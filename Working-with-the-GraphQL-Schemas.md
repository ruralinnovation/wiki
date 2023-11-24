# Working With The Apollo GraphQL Schema

> The schema design and definition has been purposely decoupled from the Apollo Server specification located in the `infrastructure` (CDK) codebase.  The schema is built as part of the sequence of build steps for the entire project, and the Apollo Server (Lambda) imports the compiled `SDL` for use by your GraphQL server.  

The [Apollo GraphQL Schema](https://www.apollographql.com/docs/apollo-server/schema/schema/) is a separate package located in the `packages/schemas` directory.

## Directory Structure

```text

├── dist                                    # build output (auto-generated during compilation)
├── schema                                  # Schema definitions written in Typescript
    ├── queries                             # Query definitions written in Typescript
├── merge-schemas.ts                      # Script that imports merged schemas, converts to `SDL` and outputs to `dist` directory.
└── index.ts                                # Entry File that imports and merges queries

```

## GraphQL Tools

We leverage [GraphQL Tools](https://www.graphql-tools.com/) in order to design and spec out schemas in Typescript.  

> GraphQL Tools is a set of NPM packages and an opinionated structure for how to build a GraphQL schema and resolvers in JavaScript, following the GraphQL-first development workflow.

## GraphQL.Js

>The JavaScript reference implementation for GraphQL, a query language for APIs created by Facebook.

We import constructs from `graphql` node library and leverage them to build schemas and queries. 

## GeoJson Type

There is a custom GeoJson Type (following the [geojson spec](https://geojson.org/)) located in the root of the `schemas/schemas` directory.  This is the primary return type used by the all the resolvers in the Api.  As your application and API grows, this will obviously be supplemented with different return types.  




## Adding Queries that connect to the Python Data Service (RestAPI)

> We have organized individual service queries into their own file (e.g. `bcatQueries`).  You can follow this structure, or develop another structure that is more friendly to your organization.  

1. Create a new query file in `schemas/schemas/queries`.
2. Import your required libraries at the top of the file

For example:

```Typescript
import { GraphQLBoolean, GraphQLList, GraphQLString } from 'graphql';
import GeoJSON from '../geojson';
```
3. Create queries (You can use the `bcatQueries` as an scaffolding)

## Query Structure

```Typescript
  // Query Name
  auction_904_subsidy_awards_county_geojson: {
    // Query Return Type
    type: GeoJSON.FeatureCollectionObject,
    // Query Arguments
    args: {
      county: {
        type: GraphQLString!,
      },
      skipCache: {
        type: GraphQLBoolean,
      },
    },
    // Query Resolver
    resolve: async (
      _: any,
      { county, skipCache }: { county: string; skipCache: boolean | undefined },
      { dataSources: { pythonApi }, redisClient }: any,
      info: any
    ) => {
      return skipCache
        ? await pythonApi.getItem(`bcat/auction_904_subsidy_awards/geojson?geoid_co=${county}`)
        : await redisClient.checkCache(`auction_904_subsidy_awards-${county}`, async () => {
            return await pythonApi.getItem(`bcat/auction_904_subsidy_awards/geojson?geoid_co=${county}`);
          });
    },
  }
```

### Understanding the Resolver

[Apollo Studio Resolver Docs](https://www.apollographql.com/docs/apollo-server/data/resolvers/)

```Typescript
const resolvers = {
  Query: {
    user(parent, args, context, info) {
      return users.find(user => user.id === args.id);
    }
  }
}
```

A resolver can optionally accept four positional arguments: (parent, args, context, info)

![image](https://user-images.githubusercontent.com/47429329/183250677-9e07d6c6-3615-415a-b385-d1f97d439912.png)

The Apollo Server has a single Data Source (Python RestAPI) and the Redis Cache Adapter.  Both of these adapters are available on the `context` argument.

You will see in the BCAT query above, the context argument is `destructured` ([more info](https://simonsmith.io/destructuring-objects-as-function-parameters-in-es6))  into:

```Typescript
{ dataSources: { pythonApi }, redisClient }: any,
```

This will allow you to reference these adapters without writing something like  `context.dataSources.pythonApi`. It makes for cleaner code.

Passing these adapters in as arguments to your resolvers also allows you to decouple the schema from the server code (where these adapters are configured and instantiated).  

Both of these adapters are instantiated in your Apollo Server Lambda and added to the apollo configuration.  Then they are available in these arguments for all resolvers.  

We have included a `skipCache` optional argument in all of the `bcatQueries`.  If this argument is defined and true, the resolver will skip checking the RedisCache for an existing value for the query.  This is a good option for your testing.

Otherwise, every resolver first checks the RedisCache using the `checkCache` method on the RedisAdapter client with a key that resembles:

`{queryName}-{argument}`  e.g. `auction_904_subsidy_awards-${county}`

The `checkCache` method on the RedisClient accepts a second argument that provided the python data source query to use if there is not data located in the cache for that query.  It will make a request using this method then automatically store the return value in the cache before returning it to the resolver.  

```Typescript
  checkCache(key: string, cb: Function, maxAge: number = globalTTL): Promise<unknown> {
    return new Promise((resolve, reject) => {
      try {
        this.getCacheValue(key).then(cacheRes => {
          if (!cacheRes) {
            cb().then(dbValue => {
              if (!dbValue) {
                dbValue = null;
              }
              this.rawCache.setex(key, maxAge, JSON.stringify(dbValue));
              resolve(dbValue);
            });
          } else {
            resolve(cacheRes);
          }
        });
      } catch (err) {
        reject(err);
      }
    });
  }
```

#### Return Schema

The return value(s) from your PythonAPI calls MUST match the return schema defined in your GraphQL query.  The request will error if it does not (e.g. if a required attribute is missing).


## Understanding Apollo Server Data Source(s)

> Data sources are classes that Apollo Server can use to encapsulate fetching data from a particular source, such as a database or a REST API. These classes help handle caching, deduplication, and errors while resolving operations.  Your server can use any number of different data sources. You don't have to use data sources to fetch data, but they're strongly recommended.

> NOTE:  The following data sources are configured, instantiated and connected to the Apollo Server in:

`packages/infrastructure/src/constructs/api/ApolloGraphqlServer`

We ahve included this information here to shed more light on how your schema queries connect to the source data.  

The only data source for the Apollo Server is the existing Python Data RestAPI.  

This data source is a `RESTDataSource` and is fully supported by Apollo Studio.

[Apollo Studio Data Sources](https://www.apollographql.com/docs/apollo-server/data/data-sources/)

[Apollo Studio RESTDataSource](https://github.com/apollographql/apollo-server/tree/main/packages/apollo-datasource-rest)

### Data Source Implementation

We have created a `BaseDataSource` that implements the core `getItem` request for the RestAPI.  For extensibility we created another Data Source class that inherits from this Base class, to add custom logic to in the future.  

As of now the API is `READONLY` which means the `getItem` method should be enough for most cases.  As the API grows and other request methods are includes (POST, PUT), you can add these core methods to the Base class for all other DataSource classes to inherit from.  

```Typescript
import { RESTDataSource, RequestOptions } from 'apollo-datasource-rest';
import { EnvConfig } from '../../EnvConfig';
export class BaseDataSource extends RESTDataSource {
  constructor() {
    super();
    this.baseURL = `${EnvConfig.PYTHON_API_URL}`;
  }

  willSendRequest(request: RequestOptions) {
    request.headers.set('Authorization', this.context.headers.Authorization);
  }
  async getItem(path?: string) {
    const res = await this.get(path ? path : '', undefined);
    return res;
  }
}

```

```Typescript
import { BaseDataSource } from './BaseDataSource';
export class PythonRestApi extends BaseDataSource {
  constructor() {
    super();
  }
}

```

### Adding to the Apollo Server Config for access by resolvers.

Data Sources are added to the Apollo Server Configuration and are passed into resolvers through the the `context`.

![image](https://user-images.githubusercontent.com/47429329/183416253-dd3a093e-a271-4757-909a-ff69d55c23ab.png)




