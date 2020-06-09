# Making Application Security Optional

If application security(oauth2, basic and API key) is not defined for API/resource, it will be oauth2(JWT, Opaque) protected by default. This default behavior can be overridden by using OpenAPI extension `                   x-wso2-application-security                 ` . This extension is supported at the resource and the API level. The following is an example of how you can do it at the resource level of an API so oauth2 security will not be added if the resource is not protected with any application security.

!!! note
    Please note that application security being optional is valid only **when mutual SSL is mandatory for the API.** For more information on enabling Mutual SSL, please refer to [this document](https://docs.wso2.com/display/MG310/Certificate+Based+Authentication+-+Mutual+SSL) . If you want to disable all security for a resource, use [x-wso2-disable-security OpenAPI extension](https://docs.wso2.com/display/MG310/Disabling+Security) .

``` yml
    paths:
    "/pet/findByStatus":
    get:
    tags:
    - pet
    summary: Finds Pets by status
    description: Multiple status values can be provided with comma separated strings
    operationId: findPetsByStatus
    x-wso2-application-security: 
    optional: true
```
