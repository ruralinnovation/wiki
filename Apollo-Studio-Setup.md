[Getting started with Apollo Studio](https://www.apollographql.com/docs/studio/getting-started)

# Create an Apollo Studio Account

You can sign up for Studio with either your GitHub identity or a username and password.

1. Go to [studio.apollographql.com](https://studio.apollographql.com/) and click Let's get started.

2. Complete the signup flow, which includes:

3. Creating an [organization](https://www.apollographql.com/docs/studio/org/organizations) that you can invite teammates to

4. [Selecting a plan](https://www.apollographql.com/pricing/) for your organization (the Free plan is always free, and the Team plan provides a free trial of paid features)

# Integrate Existing Api
> Note: The Apollo Docs suggest you import/link an existing schema.  We have found that using the explorer to dynamically access this information is the easiest way to get started.

1. Click the Apollo Explorer Button on the top left of the screen.

![image](https://user-images.githubusercontent.com/47429329/180656288-f30cb10c-90eb-4ae6-8429-6d4b0c0f6fa4.png)

2. On the top right of the screen is a sandbox url input, click the settings icon in this input.

![image](https://user-images.githubusercontent.com/47429329/180656312-c9910961-a4aa-4841-81cc-6d23a144d4d5.png)

![image](https://user-images.githubusercontent.com/47429329/180656429-43599a20-7b22-473e-bc6e-b9b51d8f5aa2.png)


3.  Enter in the Cloudfront GraphQL API URL and click `save`

![image](https://user-images.githubusercontent.com/47429329/180656361-05dc6e40-a59d-4608-ae26-b9cb480e1943.png)


4. Click the headers tab on the bottom of the screen

![image](https://user-images.githubusercontent.com/47429329/180656568-73420437-5c26-4ca1-a61e-c39599a66510.png)

5. Copy the generated id token from your Postman Environment and enter it into this header field.

![image](https://user-images.githubusercontent.com/47429329/180656594-1bface6e-f436-4146-9b6e-73bce4b3f247.png)

6. Apollo Studio will use introspection to access the schema of your API. If it has worked you should see your schema on the left side of the screen.

![image](https://user-images.githubusercontent.com/47429329/180656714-4a83b1a5-5289-4b9a-9f66-9fb1c4c83b6e.png)

7. Publish your schema by pressing the `publish` button to the right of the sandbox url input.  Because this schema is connected to your DEV APi, using an appropriate naming convention.

![image](https://user-images.githubusercontent.com/47429329/180656749-b6143703-9831-4aae-b74a-eef5e6efdc77.png)

![image](https://user-images.githubusercontent.com/47429329/180656788-b34b61b1-c2b5-40d8-a67d-c91d3c67d1bb.png)


# Setup Automated Authentication in a Preflight Script

1. Once your graph is published , you will br automatically navigated to that graphs screen.

![image](https://user-images.githubusercontent.com/47429329/180656870-34e9ed69-94ca-4d03-8544-d8c38ba7db99.png)

2. Click the `Explorer` navigation element.

![image](https://user-images.githubusercontent.com/47429329/180656896-19fb7b61-fdab-4c4f-84b9-443aa70330d5.png)

3. There is a section `Preflight script`, click the `add script` button.

![image](https://user-images.githubusercontent.com/47429329/180656935-716a5e00-c613-478b-9189-2b37ed6c4421.png)

4. Add the following script:

```javascript
var clientId = explorer.environment.get("cognitoClientId");
var username = explorer.environment.get("cognitoUserName");
var password = explorer.environment.get("cognitoUserPassword");
try {
       const response = await explorer.fetch("https://cognito-idp.us-east-1.amazonaws.com/", {
              method: 'POST',
              headers: {
                     'X-Amz-Target': 'AWSCognitoIdentityProviderService.InitiateAuth',
                     'Content-Type': 'application/x-amz-json-1.1'
              },
              body: JSON.stringify({
                     "AuthParameters": {
                            "USERNAME": username,
                            "PASSWORD": password
                     },
                     "AuthFlow": "USER_PASSWORD_AUTH",
                     "ClientId": clientId
              })
       });

       const body = JSON.parse(response.body);
       explorer.environment.set("cognitoClientId", clientId);
       explorer.environment.set("cognitoUserName", username);
       explorer.environment.set("cognitoUserPassword", password);
       explorer.environment.set("cognitoIdToken", body.AuthenticationResult.IdToken);
}
catch (err) {
       console.log('ERROR AUTHENTICATING', err);
       explorer.environment.set("cognitoClientId", clientId);
       explorer.environment.set("cognitoUserName", username);
       explorer.environment.set("cognitoUserPassword", password);
}

```

5. Add an env variables json in the `Environment variables` section with values from your Postman/Cognito environment

```json
{
  "cognitoClientId": "6um99fv2qtb6f7ise3i037vna",
  "cognitoUserName": "mf-int-test@yopmail.com",
  "cognitoUserPassword": "PASSWORD"
}
```

![image](https://user-images.githubusercontent.com/47429329/180657014-c525de8a-a97c-499b-8e50-8f4869fdff04.png)

6. Click the Preflight script on

![image](https://user-images.githubusercontent.com/47429329/180657125-7e8eb2c6-b78e-4df9-8470-b33dffca8bc2.png)

7. Update the shared headers to reference the new id token variable.

![image](https://user-images.githubusercontent.com/47429329/180657154-6b4f7bc1-9557-4ed6-b6a1-a64b455ad2a4.png)


# Send Requests

Select queries from your schema and add graphql variables.  Then send requests!
```
query auction_904_subsidy_awards_geojson ($counties: [String]!, $skipCache: Boolean) {
    auction_904_subsidy_awards_geojson (counties: $counties, skipCache: $skipCache) {
        type
        features {
            type
            id
            properties
            geometry {
                coordinates
                type
            }
        }
    }
}
```
![image](https://user-images.githubusercontent.com/47429329/180657251-644638d4-f0ec-4926-8000-625b501a73c1.png)

