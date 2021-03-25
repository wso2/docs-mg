# Message Transformation

You can use interceptors in the API Microgateway to carry out transformations and mediations on the requests and responses. 
Request interceptors get triggered before sending the request to the backend. The response interceptors get triggered before responding to the client.  
If you are an API developer, you can use [Ballerina](https://ballerina.io) or **Java** to write custom request/response interceptors. 
Thereafter, in order for the interceptor to be engaged in WSO2 API Microgateway, define the corresponding interceptors using the following OpenAPI extensions in the OpenAPI definition.

| Interceptor          | Corresponding OpenAPI Extension                              |
|----------------------|--------------------------------------------------------------|
| Request interceptor  | **`              x-wso2-request-interceptor             `**  |
| Response interceptor | **`              x-wso2-response-interceptor             `** |

You can define interceptors on an API level (per API) and on a resource level (per resource). 
If you define a request interceptor on an API level **and** a resource level basis, the API level interceptor will act 
on the request first before passing the request to the resource level interceptor. 
However, if you define a response interceptor on an API level **and** a resource level, 
the resource level interceptor will act on the response first before passing the request to the API level interceptor.

Adding an interceptor requires following 3 steps. </br>

###### 1. Implement the interceptor with logic
###### 2. Add the interceptor to the microgateway project.
###### 3. Refer the interceptor in the API to engage it in the request flow.

The following sub sections explains how to achieve the above mentioned three steps when writing interceptors using different programming languages.

#### [1. Writing Interceptors in Java]({{base_path}}/how-tos/message-transformation/write-java-interceptors/)

#### [2. Writing Interceptors in Ballerina]({{base_path}}/how-tos/message-transformation/write-ballerina-interceptors/)



