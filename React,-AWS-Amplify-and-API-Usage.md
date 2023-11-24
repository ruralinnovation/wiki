# Configuring Amplify for different envs

## Config.json

You will need to create a config file in the public directory of your hosted application.  This file will house attributes from your deployed API (e.g. UserPoolId and the UserPool domain). When a user navigates to your application the Configuration Service will read this config as a static file and configure the Amplify Library to authenticate your application

```json
{
  "environment": "dev",
  "region": "us-east-1",
  "apiUrl": "https://d25ssrwsq4u9bu.cloudfront.net",
  "cognito": {
    "clientId": "6um99fv2qtb6f7ise3i037vna",
    "domain": "mf-data-api-dev.auth.us-east-1.amazoncognito.com",
    "userPoolId": "us-east-1_NE91zaapX"
  }
}

```
## Sample Config Service

You can create a ConfigurationService, which is responsible for reading the config file, storing attributes in state, and setting up Amplify Auth Library.  You can import this service in an ApiContext.

```Typescript
import { Auth, Logger } from 'aws-amplify';

interface ICognitoConfig {
  userPoolId: string;
  clientId: string;
  identityPoolId: string;
  domain: string;
  hostedAuthenticationUrl: string;
  logoutUrl: string;
}

export default class ConfigurationService {
  private logger: Logger;
  loaded = false;

  // These properties are assigned from config.json
  environment: string = '';
  region: string = '';
  apiUrl: string = '';
  cognito: ICognitoConfig = {
    userPoolId: '',
    clientId: '',
    identityPoolId: '',
    domain: '',
    hostedAuthenticationUrl: '',
    logoutUrl: '',
  };
  version: string = '';

  constructor() {
    this.logger = new Logger('ConfigurationService');
  }

  public async load() {
    const response = await fetch('/config.json');
    const cfg = await response.json();
    Object.assign(this, cfg);
    console.log('Config Loaded');
    this.configureAmplify();
    this.loaded = true;
  }

  get loginUrl(): string {
    return window.location.origin;
  }

  isLocal(): boolean {
    return this.environment === 'local';
  }
  isDev(): boolean {
    return this.environment === 'dev';
  }

  private configureAmplify(): void {
    Logger.LOG_LEVEL = this.isLocal() || this.isDev() ? 'DEBUG' : 'INFO';
    const options = {
      Analytics: {
        disabled: true,
      },
      Auth: {
        region: this.region,
        userPoolId: this.cognito.userPoolId,
        userPoolWebClientId: this.cognito.clientId,
        oauth: {
          domain: this.cognito.domain,
          scope: ['email', 'openid', 'profile'],
          redirectSignIn: this.loginUrl,
          redirectSignOut: this.cognito.logoutUrl ? this.cognito.logoutUrl : this.loginUrl + '/logout/',
          responseType: 'code',
          mandatorySignIn: true,
        },
      },
    };

    Auth.configure(options);
  }
}

```
# Sample API Context Component

The ApiContext is the bread and butter for Authentication and Apollo Setup.

1. Imports ConfigurationService
2. Initializes configuration
3. Leverages Amplify to check if there is a logged in User, if not will redirect to the hosted UI
4.  Once there is a logged in user in state, we configure the ApolloClient with the token from that User
5. We leverage the ApolloProvider with the apolloClient so you can use apollo client to query the GraphQL API in any component. 

```Typescript
import {
  ApolloClient,
  ApolloLink,
  ApolloProvider,
  concat,
  HttpLink,
  InMemoryCache,
  NormalizedCacheObject,
} from '@apollo/client';

export interface AuthUser {
  email?: string;
  username: string;
  userType: string;
  groups: string[];
}
function ApiContextProvider(props: { children: any }) {
  // Import the Configuration Service
  const configService: ConfigurationService = new ConfigurationService();
  const [config, setConfig] = useState<ConfigurationService>(null);
  // Create user state hook
  const [user, setUser] = useState<AuthUser>(null);
  // Create ready state hook (will be true once ALL is setup and configured)
  const [ready, setReady] = useState<boolean>(false);
  // Create Apollo Client hook
  const [apolloClient, setApolloClient] = useState<ApolloClient<NormalizedCacheObject> | null>(null);
  
  const initializeConfig = useCallback(async () => {
    if (configService.loaded) return;
    await configService.load();
    setConfig(configService);
  }, []);

  useEffect(() => {
    initializeConfig();
  }, [initializeConfig]);

  useEffect(() => {
    if (user) {
      createApolloClient();
    }
  }, [user]);
  useEffect(() => {
    if (config) {
      AmplifyService.isAuthenticated()
        .then(bool => {
          console.log('Authenticationed ', bool);
          if (bool) {
            AmplifyService.getClaims()
              .then(claims => {
                if (!claims) {
                  console.log('not auth');
                  AmplifyService.federatedLogin();
                } else {
                  setUser({
                    username: claims.username,
                    userType: 'admin',
                    groups: claims.groups,
                    email: claims.email,
                  });
                }
              })
              .catch(err => {
                console.log(err);
                updateAuthUser(null);
              });
          } else {
            AmplifyService.federatedLogin();
          }
        })
        .catch(err => {
          console.log('ERROR', err);
        });
    }
  }, [config]);

  const createApolloClient = async () => {
    const token = (await Auth.currentSession()).getIdToken().getJwtToken();
    const link = new HttpLink({
      uri: `${config.apiUrl}/graphql`,
    });
    const authMiddleware = new ApolloLink((operation, forward) => {
      operation.setContext({
        headers: {
          authorization: `Bearer ${token}`,
        },
      });
      return forward(operation);
    });
    const client = new ApolloClient({
      link: concat(authMiddleware, link),
      cache: new InMemoryCache({
        dataIdFromObject: o => (o.id ? `${o.__typename}-${o.id}` : `${o.__typename}-${o.cursor}`),
      }),
      credentials: 'include',
    });

    setApolloClient(client);
    setReady(true);
  };

  return ready && apolloClient ? (
        <ApolloProvider client={apolloClient}>{props.children}</ApolloProvider>
      ) : (
        <div>LOADING</div>
  )
}


function App() {
  return (
      <ApiContextProvider>
        <Application />
      </ApiContextProvider>
  );
}

export default App;

```


