# Postman Workspaces and Development Environments

## Create a Workspace

1. Open Postman Application
2. Click the `Workspaces` tab.
3. Create a new Workspace (preferably a team workspace - although this will require a subscription after trial)

![image](https://user-images.githubusercontent.com/47429329/180608940-a229d8cc-86a7-4f33-ac5a-b032e20a3160.png)

![image](https://user-images.githubusercontent.com/47429329/180608962-df4c71f3-1bb8-4ba4-a9b1-12ef2aa56d62.png)

![image](https://user-images.githubusercontent.com/47429329/180608976-ff9ed78f-1d47-4bef-b80f-8f14ee40460a.png)

## Import Collection

We have provided a complete configured collection to you as an `json` file.

### Import Collection and Environment

[Download Collection File](./cori-dev.postman.collection.json)

1. Click `Import file`
2. Import File from downloaded file.
3. Check that its a Collection Import and select `import`

![image](https://user-images.githubusercontent.com/47429329/180610114-3fdca7f7-3ed9-4a03-ada9-7a323accb56f.png)

![image](https://user-images.githubusercontent.com/47429329/180609998-401e51d7-174a-44ba-86f7-97d94a734203.png)

#### Check imported Collection

All of your GraphQL and RestAPI requests are now configured for you.

![image](https://user-images.githubusercontent.com/47429329/180610325-10a5c068-9dfb-4a2e-b601-040549da377c.png)


### Configure Environment

[Download Environment File](./cori-dev.postman_environment.json)

1. Click `Import file`
2. Import downloaded environment file.
3. Check that its a environment file and select `import`

![image](https://user-images.githubusercontent.com/47429329/180610116-9e7ee095-0317-41a3-a8fa-d2e029d07c75.png)

![image](https://user-images.githubusercontent.com/47429329/180610063-64f596c0-afd2-4350-9167-18e431f05cb7.png)

#### Check Environment Configuration

Your environment variables should be setup for your DEV deployment

![image](https://user-images.githubusercontent.com/47429329/180610183-6e8c2d6d-e264-416b-bd37-32bd31f8d734.png)

> [NOTE]: As part of the Authentication deployment, we have added a Custom Postman Application Client to your UserPool.  This allows Postman to connect to Cognito to Authenticate requests from this environment.