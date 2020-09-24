# OpenAPI Extensions

WSO2 API Microgateway supports the following OpenAPI Extensions. You can use these extensions to override information with regard to API specifications.

| Extension                                                                             | Description                                                                                                            | Required/ Optional             |
|---------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| `             x-wso2-basePath            `                                            | Base path that the gateway exposes the API.                                                                            | Optional → API level only      |
| `             x-wso2-production-endpoints            `                                | Specifies the actual backend of the service.                                                                           | Optional → API/Resource level  |
 |`             x-wso2-sandbox-endpoints            `                                   | Specifies the sandbox endpoint of the service if available.                                                            | Optional → API/Resource level  |
| `                             x-wso2-request-interceptor                           `  | Specifies the interceptor used to perform transformations and mediations on the request.                               | Optional → API/Resource level  |
| `                             x-wso2-response-interceptor                           ` | Specifies the interceptor used to perform transformations and mediations on the response.                              | Optional → API/Resource level  |
| `                           x-wso2-throttling-tier                         `          | Specifies the rate limiting for the API or resource.                                                                   | Optional → API/Resource level  |
| `                           x-wso2-cors                         `                     | Specifies the Cross-Origin Resource Sharing (CORS) configuration for the API.                                          | Optional → API level only      |
| `             x-wso2-endpoints            `                                           | Defines the endpoint configuration globally which thereafter can be referred to using one of the following extensions. <li>`x-wso2-production-endpoints`</li> <li>` x-wso2-sandbox-endpoints`</li> | Optional |
| `                           x-wso2-disable-security                         `         | Enables the resource to be invoked without authentication.                                                             | Optional → API/Resource level  |
| `                           x-wso2-application-security                         `     | Set Application security (api\_key, basic\_auth, oauth2), to set optional for application level security               | Optional → API/Resource level  |
| `x-wso2-transports`                                                                   | Set transport types (http, https)                                                                                      | Optional → API level only      |
| `x-wso2-mutual-ssl`                                                                   | Enable mutual ssl                                                                                                      | Optional → API level only      |


You can find some samples on how these OpenAPI extensions are used in [Open API definitions](https://github.com/wso2/product-microgateway/blob/master/samples/).

