# Writing an interceptor in Java

Microgateway defines java interfaces to write the request and response interceptors. The interfaces for request and response interceptors are defined as below. Developers can implement these interfaces to achieve the custom transformation logic. The java docs for the supported java objects, methods and etc can be found [here](http://product-dist.wso2.com/javadocs/api-microgateway/3.1.0/).

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

#### Step 1 - Implement the interface

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
      <version>3.2.0</version>
    </dependency>
    ```
#### Step 2 - Adding interceptors to the project

Once java interceptors are written, then the interceptor project should be built and the output jar should be placed in the ` <MGW-project>/lib` directory. If third-party libraries are used when writing interceptors, these custom jars should also be placed in the same directory.

#### Step 3 - Refer interceptor in the API

The java interceptors are specified using the prefix "java:" in the open API extension `x-wso2-request-interceptor` or `x-wso2-response-interceptor`.
The fully qualified class name should be given with prefix "java:". Ex : `x-wso2-request-interceptor: java:org.mgw.interceptor.SampleInterceptor`

##### Interceptor at resource level

Java Interceptors can be added to a particular resource as opposed to the whole API if needed

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

##### Interceptor at API level

You can add an interceptor so that it gets applied at the API level, which is global to all resources defined in the OpenAPI definition. The following is a sample on how to use the extension to add an interceptor to the API.

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

!!! info
    If both resource level and API level request and response interceptors are added then following will be the order in which they are applied
    
    1. API level request interceptor
    2. Resource level request interceptor
    3. Resource level response interceptor
    4. API level response interceptor