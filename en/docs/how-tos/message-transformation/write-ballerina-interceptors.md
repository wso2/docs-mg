# Writing an interceptor in Ballerina

This section explains writing interceptors in [Ballerina](https://ballerina.io/). For information on Ballerina 1.2.x syntax, see [API docs](https://ballerina.io/learn/api-docs/ballerina/) in the Ballerina documentation. The following is a sample request interceptor written in Ballerina (`interceptor.bal`).

#### Step 1 - Implement the interceptor object

``` java
import ballerina/http;
    
public function validateRequest (http:Caller outboundEp, http:Request req) {
    // Backend requires X-API-KEY header. No point in passing the request to the backend 
    // if the header is not present in the request.
boolean hasApiKey = req.hasHeader("X-API-KEY");
if (!hasApiKey) {
    http:Response res = new;
    res.statusCode = 400;
    json message = {"error": "Missing required header"};
    res.setPayload(message);
    var status = outboundEp->respond(res);
   }
}

public function validateResponse (http:Caller outboundEp, http:Response res) {
    // Client only supports json. Therefore we need to make sure only json responses are returned
    var payload = res.getJsonPayload();
    if (payload is error) {
        res.statusCode = 500;
        json message = {"error": "Error while generating response"};
        res.setPayload(message);
    }
} 
```

!!! info
    Interceptor method names
    Request and response interceptor names(validateResponse, validateRequest) can be any valid method name in ballerina, and  needs to be referred in the open API definition as in below

#### Step 2 - Adding interceptors to the project

Once interceptors are written, the bal files containing the interceptor logic should be added to the `<MGW-project>/interceptors` directory

#### Step 3 - Refer interceptor in the API
The ballerina interceptors are referred in the open API by mentioning the ballerina method name which used to intercept 
the request and response using `x-wso2-request-interceptor` and `x-wso2-response-interceptor` open API extensions respectively.
Ex: `x-wso2-request-interceptor: validateRequest`

##### Interceptor at resource level

Interceptors can be added to a particular resource as opposed to the whole API if needed.  The following is a sample on how to use an extension to add an interceptor. For an end-to-end sample, see the [sample OpenAPI definition with interceptors](https://github.com/wso2/product-microgateway/blob/master/samples/interceptors_sample.yaml) .

``` java
paths:
  "/pet/findByStatus":
  get:
    tags:
      - pet
    summary: Finds Pets by status
    description: Multiple status values can be provided with comma separated strings
    operationId: findPetsByStatus
    x-wso2-request-interceptor: validateRequest
    x-wso2-response-interceptor: validateResponse
    parameters:
    - name: status
      in: query
      description: Status values that need to be considered for filter
      required: true
      explode: true
      schema:
        type: array
        items:
          type: string
          enum:
            - available
            - pending
            - sold
            default: available
```

##### Interceptor at API level
You can add an interceptor so that it gets applied at the API level, which is global to all resources defined in the 
OpenAPI definition. The following is a sample on how to use the extension to add an interceptor to the API.

``` java
openapi: 3.0.0
    version: 1.0.0
    title: Swagger Petstore New
    termsOfService: http://swagger.io/terms/
x-wso2-basePath: /petstore/v1
x-wso2-request-interceptor: validateRequest
x-wso2-response-interceptor: validateResponse
x-wso2-production-endpoints:
    urls: 
       - https://petstore.swagger.io/v
```
