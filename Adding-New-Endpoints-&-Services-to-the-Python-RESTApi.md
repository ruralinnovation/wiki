# Copy Python Scaffolding

We have included a Scaffolding Lambda in the `python-lambdas` directory for easy setup.  

![image](https://user-images.githubusercontent.com/47429329/181375454-9861efa5-e4e5-4856-b93d-032fb5977278.png)

1. Copy this entire directory
2. Paste it in the root of the `python-lambdas` directory
3. rename the new directory the name of your new microservice (e.g. `bcat`)
4. rename the project in the `pyproject.toml` file

![image](https://user-images.githubusercontent.com/47429329/181375857-837d2b9b-416b-491e-af21-0bf4a6231727.png)

# Update your New Service

#### Your New Service scaffold is the following:

```python
import os
from aws_lambda_powertools import Logger, Tracer
from aws_lambda_powertools.logging import correlation_paths
from aws_lambda_powertools.event_handler.api_gateway import APIGatewayRestResolver, Response
from aws_lambda_powertools.event_handler.exceptions import BadRequestError


logger = Logger(service="Scaffolding")
tracer = Tracer(service="Scaffolding")
app = APIGatewayRestResolver(strip_prefixes=["/scaffolding"])


@app.get(rule="/bad-request-error")
def bad_request_error(msg):
    # HTTP  400
    raise BadRequestError(msg)


@app.get("/<table>/geojson", compress=False)
def get(table):
    print(table)

# You can continue to use other utilities just as before
@tracer.capture_lambda_handler
@logger.inject_lambda_context(correlation_id_path=correlation_paths.API_GATEWAY_REST, log_event=True)
def handler(event, context):
    return app.resolve(event, context)
```

5. In the `index.py` file rename your service in:
* `Logger(service="NEW_SERVICE_NAME")`
* `Tracer(service="NEW_SERVICE_NAME")`

6. Update the `ApiGatewayResolver` strip_prefixes value with the `corePath` for this service.  

#### Understanding the Core Path

The core path is the path immediately following the Cloudfront URL and this path is how Api Gateway determines which lambda service to trigger when a request is made.  The remaining params/path in the URL will be passed to the lambda to parse and act upon. 

Example:  `cloudfront_url.net/{corePath}/{customParam1}/customPath`

> The BCAT service the `corePath` is `/bcat` and all requests that start with this path will trigger the bcat microservice lambda in the corresponding directory (`bcat`).  The lambda itself will parse the rest of the path and you can create custom endpoint methods to handle this within python (leveraging the `Python Lambda Powertools` library with similar patterns to `Flask`).

We suggest stripping the prefix (aka corePath) in the ApiGatewayResolver to reduce code smell in your custom lambda services.  If you dont strip the prefix all your request methods in your lambda will have to include the corePath.

7. Include new request methods and logic.

# Update Microservices Config in the `config/config.ts` file.

We have created a configuration driven deployment for new services and endpoints.  What this means is that you WILL NOT have to touch any of the `Typescript/CDK` code or constructs in order to deploy new service endpoints to the Python RestApi.  This was always the goal, to ease the transition into using these new technologies while not inhibiting your ability to scale and add to the API.  

Your Deployment configuration accepts a `microservicesConfig` attribute which has the following type:


```Typescript

interface ServiceConfig {
  /**
   * The Logical Name of the service (NO SPACES) e.g. BCATService
   */
  logicalName: string;
  /**
   * The Core path to trigger the Microservice e.g. /bcat
   */
  corePath: string;
  /**
   * The name of the directory this service is located.  e.g. bcat
   */
  directoryName: string;
}


microservicesConfig: ServiceConfig[]

```

As of now we have a single BCAT Microservice so you configuration is the following:

```Typescript
const microservicesConfiguration: ServiceConfig[] = [
  {
    logicalName: 'BCATService',
    corePath: '/bcat',
    directoryName: 'bcat',
  },
];
```

8. When you want to add a new service you add a new `ServiceConfig` to the list using the following attributes.

**logicalName**: Used by Cloudformation for the deployment (cannot have spaces)  
**corePath**:  As described above the core path is the path that when requested will trigger the corresponding lambda in the `python-lambdas` directory.  This is how Api Gateway determines which lambda to call during requests.  
**directoryName**:  This is the name of the directory that the lambda/service code is in.  The Python Data Service construct needs to know where the lambda is in order build the code and link up the Api Gateway to the lambda.

8. Once you add a new service your config should resemble this:

```Typescript
const microservicesConfiguration: ServiceConfig[] = [
  {
    logicalName: 'BCATService',
    corePath: '/bcat',
    directoryName: 'bcat',
  },
  {
    logicalName: 'CustomNewService',
    corePath: '/custom-new-service',
    directoryName: 'custom-new-service',
  }
];
```

# Test your code by running a local build:  

```bash
npm run build
```

If there are no errors you can now:

9. commit your changes
10. push to the `DEV` branch to trigger the pipeline to rebuild and deploy the changes.




