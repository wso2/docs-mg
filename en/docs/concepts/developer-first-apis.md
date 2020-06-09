# Developer First APIs

This is the most common way of creating/defining APIs in a Microgateway environment. This method allows developers to define Microgateway API endpoints using standard OpenAPI definitions. They only have to copy an already defined OpenAPI definition of a valid REST endpoint to the Microgateway project. Microgateway will parse the resources and different attributes defined in the OpenAPIs definition and generate the required gateway services. These services can then be built and deployed into different gateway environments.

## Defining gateway specific properties

OpenAPI specification doesn't define all required properties for a API gateway. It's meant to be a descriptor of an REST endpoint. In this case that'd be the OpenAPI definition of backend endpoint. However a developer of an API gateway should be able to define,

-   Different types of endpoints
-   Gateway security
-   Rate limiting...

and many other gateway specific features per API or per Resource. Since Microgateway is using the OpenAPI definition as the SSOT (Single Source Of Truth), These additional properties should somehow go into OpenAPI definition maintained inside the Microgateway project. Microgateway utilizes OpenAPI Specification's vendor extensions for this purpose.

So to define above metioned additional API/Resource specific gateway attributes, Microgateway introduces set of vendor extensions for developers. List of available extensions are documented [here](_OpenAPI_Extensions_) .

## How to work with standard open API definition

In microgateway version 3.0.x series the open API definition required 2 mandatory wso2 specific extensions at leaset. Those were

1.  x-wso2-basePath
2.  x-wso2-production-endpoints

The x-wso2-basePath is used to group the all http resources of the open API under a single context so, it will be exposed as a sub resources of a singel API using microgateway. This is how microgateway logically seperat one API from the other. The x-wso2-production-endpoints refers to the actual backend service implementation of the API. So these two details were mandatory to expose an API using microgateway.

**But with 3.1.0 onwards none of the wso2 specific open API extensions are mandatory.** Microgateway resolves base path and back end url as below

1.  **For swagger 2.0**
    The swagger 2.0 specification  defines a way of providing the [base path and the host](https://swagger.io/docs/specification/2-0/api-host-and-base-path/) of the service. If the swagger specifies the combination of **schemes, basePath and host** attributes, microgateway will drive the back end service url using host and schemes, and derive the base path using the base path attribute
2.  **For Open API 3.0
    ** The open API 3.0 specification defines a way of providing [server](https://swagger.io/docs/specification/api-host-and-base-path/) url of the API defines by the open API definition. Microgateway derive the base path and the back end service url using the **servers** object defined in the open API definition.
    For if the server url is defined as ex: <http://test.com/v2> then microgateway will resolve backend service url as the given URL and it will also expose the API using the base path(context) /v2.

## Updating the API

Updating of the API is done by editing the existing open API definition. In order to update the API, the project must be built and microgateway should be re deployed again as API updates are not hot deployable.

Following properties of the open API is not recommended to be editied, as it will behave as a new API if those were edited.

-   Title
-   Version
-   basePath - If swagger 2.0. version is used
-   x-wso2-basePath
-   [servers](https://swagger.io/docs/specification/api-host-and-base-path/) objects basePath section if x-wso2-basePath is not present in the open API (this is explained in the above section **How to work with standard open API definition** ).
    For ex if servers url is defined as below.

    **Open API servers object**

    ``` yml
        servers:
        - url: https://api.example.com/v1 
    ```

        Then microgateway will expose the API using the base path(or context) /v1 if the x-wso2-basePath extension is not present. Then editing the last part of the servers url(/v1) will expose API as a different context. If editing the servers url is a must due to the changes in the actual back end service, then we can use the x-wso2-basePath extension as a fixed context and edit the servers url. So API will be exposed with the same base bath as it was before.


