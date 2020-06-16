# Message Transformation

You can use interceptors in the API Microgateway to carry out transformations and mediations on the requests and responses. Request interceptors get triggered before sending the request to the backend. The response interceptors get triggered before responding to the client.  If you are an API developer, you can use [Ballerina](https://ballerina.io) or **Java** to write custom request/response interceptors. Thereafter, in order for the interceptor to be engaged in WSO2 API Microgateway, define the corresponding interceptors using the following OpenAPI extensions in the OpenAPI definition.

| Interceptor          | Corresponding OpenAPI Extension                              |
|----------------------|--------------------------------------------------------------|
| Request interceptor  | **`              x-wso2-request-interceptor             `**  |
| Response interceptor | **`              x-wso2-response-interceptor             `** |

You can define interceptors on an API level (per API) and on a resource level (per resource). If you define a request interceptor on an API level **and** a resource level basis, the API level interceptor will act on the request first before passing the request to the resource level interceptor. However, if you define a response interceptor on an API level **and** a resource level, the resource level interceptor will act on the response first before passing the request to the API level interceptor.

The following sub sections explains how you can work with OpenAPI extensions to add interceptors to an API and/or resource level requests and responses.

### Writing an interceptor

Microgateway defines java interfaces to write the request and response interceptors. The interfaces for request and response interceptors are defined as below. Developers can implement these interfaces to achieve the custom transformation logic. The java docs for the supported java objects, methods and etc can be found [here](http://product-dist.wso2.com/javadocs/api-microgateway/3.1.0/) .

#### Interceptor Interface

``` java
package org.wso2.micro.gateway.interceptor;
/**
* Defines the interface for writing the gateway interceptors.
*/
public interface Interceptor {

/**
* Intercepts the request before forwarding the request to the back end.
*
* @param caller {@link Caller} The caller object.
* @param request {@link Request} The request object.
* @return Returns whether the interceptor has completed the task completely. In order to stop the request
* flow from the interceptor return false.
*/
boolean interceptRequest(Caller caller, Request request);

/**
* Intercepts the response before forwarding the response to the client.
*
* @param caller {@link Caller} The caller object.
* @param response {@link Response} The response object.
* @return Returns whether the interceptor has completed the task completely. In order to stop the request
* flow from the interceptor return false.
*/
boolean interceptResponse(Caller caller, Response response);
}
```

#### Writing an Interceptor

``` java
package org.mgw.interceptor;
import org.json.JSONObject;
import org.wso2.micro.gateway.interceptor.Caller;
import org.wso2.micro.gateway.interceptor.Interceptor;
import org.wso2.micro.gateway.interceptor.InterceptorException;
import org.wso2.micro.gateway.interceptor.Request;
import org.wso2.micro.gateway.interceptor.Response;

public class SampleInterceptor implements Interceptor {
public static String value="json";
public boolean interceptRequest(Caller caller, Request request) {
boolean hasApiKey = request.hasHeader("X-API-KEY");
if (!hasApiKey) {
Response response = new Response();
JSONObject responsePayload = new JSONObject().put("error", "Missing required header");
response.setResponseCode(400);
response.setJsonPayload(responsePayload);
caller.respond(response);
// If we respond from the interceptor it is necessary to set the return value as false. 
// Otherwise the usual request flow will continue.
return false; 
}
return true;
}
    public boolean interceptResponse(Caller caller, Response response) {
// Send a custom json message if the response contains the key "error"
try {
JSONObject responseObject = response.getJsonPayload();
if(responseObject.has("error")){
JSONObject customErrorMessage = new JSONObject().put("error", "Invalid json payload returned");
response.setJsonPayload(customErrorMessage);
}
} catch (InterceptorException e) {
System.out.println("Error while getting the response payload");
}
return true;
} 
}
```

!!! info
    Add the microgateway interceptor dependency
    The java interceptor project will require the following dependency
    **Interceptor dependency**
    ``` java
    <dependency>
    <groupId>org.wso2.am.microgw</groupId>
    <artifactId>mgw-interceptor</artifactId>
    <version>3.1.0</version>
    </dependency>
    ```
### Adding interceptors to the project

Once java interceptors are written, then the interceptor project should be built and the output jar should be placed in the ` <MGW-project>/lib` directory. If third-party libraries are used when writing interceptors, these custom jars should also be placed in the same directory.

#### Adding interceptors using OpenAPI extensions

Java Interceptors can be added to a particular resource as opposed to the whole API if needed.  The following is a sample on how to use an extension to add an interceptor.  The java interceptors are specified using the prefix "java:"

##### Interceptor at resource level

``` java
paths:
"/pet/findByStatus":
get:
tags:
- pet
summary: Finds Pets by status
description: Multiple status values can be provided with comma separated strings
operationId: findPetsByStatus
x-wso2-request-interceptor: java:org.mgw.interceptor.SampleInterceptor
x-wso2-response-interceptor: java:org.mgw.interceptor.SampleInterceptor
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

#### Add interceptors globally at the API level

You can add an interceptor so that it gets applied at the API level, which is global to all resources defined in the OpenAPI definition. The following is a sample on how to use the extension to add an interceptor to the API.

##### Interceptor at API level

``` java
openapi: 3.0.0
version: 1.0.0
title: Swagger Petstore New
termsOfService: http://swagger.io/terms/
x-wso2-basePath: /petstore/v1
x-wso2-request-interceptor: java:org.mgw.interceptor.SampleInterceptor
x-wso2-response-interceptor: java:org.mgw.interceptor.SampleInterceptor
x-wso2-production-endpoints:
urls:
- https://petstore.swagger.io/v
```

### Writing an interceptor

This section explains writing interceptors  in [Ballerina](https://ballerina.io/) . For information on Ballerina syntax, see [Learn Ballerina](https://ballerina.io/learn/) in the Ballerina documentation. The following is a sample request interceptor written in Ballerina ( `             interceptor.bal            ` ).

#### Writing an Interceptor

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

#### Adding interceptors to the project

Once interceptors are written, the bal files containing the interceptor logic should be added to the `<MGW-project>/interceptors` directory

#### Adding interceptors using OpenAPI extensions

Interceptors can be added to a particular resource as opposed to the whole API if needed.  The following is a sample on how to use an extension to add an interceptor. For an end-to-end sample, see the [sample OpenAPI definition with interceptors](https://github.com/wso2/product-microgateway/blob/master/samples/interceptors_sample.yaml) .

##### Interceptor at resource level

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

#### Add interceptors globally at the API level

You can add an interceptor so that it gets applied at the API level, which is global to all resources defined in the OpenAPI definition. The following is a sample on how to use the extension to add an interceptor to the API.

##### Interceptor at API level

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


