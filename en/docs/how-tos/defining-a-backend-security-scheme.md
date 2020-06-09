# Defining a Backend Security Scheme

-   [Defining a security scheme](#DefiningaBackendSecurityScheme-Definingasecurityscheme)
-   [Specifying basic authentication for API / resource level backend endpoints](#DefiningaBackendSecurityScheme-SpecifyingbasicauthenticationforAPI/resourcelevelbackendendpoints)
-   [Invoking an API using basic authentication](#DefiningaBackendSecurityScheme-InvokinganAPIusingbasicauthentication)

###### Defining a security scheme

The security scheme used for the API must be defined via the `                     securityConfig                   ` section in the API definition. By default, WSO2 API Microgateway supports OAuth2. The following is an example on how you can define the security scheme as "basic". Multiple security schemes can be defined. For more information on security schemes see [Defining securitySchemes](https://swagger.io/docs/specification/authentication/) .

**Define security scheme**

``` java
    securityConfig:
    type: basic
    username: kim
```

    ###### Specifying basic authentication for API / resource level backend endpoints

    When an actual backend service of the API is protected using basic authentication, the basic authentication parameters (username and password) must be sent to the backend. Due to the latter mentioned reason, you need to define the endpoint security parameter in the OpenAPI using extensions. After you have defined the endpoint(s) using the `                     x-wso2-endpoints                   ` extension, an API level or resource level endpoint will be able to refer to the defined endpoint. You need to define a name for the extension when defining an endpoint using the `                     x-wso2-endpoints                   ` extension. The extension name that you specify will be use to pass the password when running WSO2 API Microgateway. The following is a sample on how to define and use endpoints by reference.

    **Define and refer endpoints**

``` java
    x-wso2-basePath: /petstore/v1
    x-wso2-production-endpoints: "#/x-wso2-endpoints/myEndpoint"
    paths:
    "/pet/findByStatus":
    get:
    tags:
    - pet
    summary: Finds Pets by status
    description: Multiple status values can be provided with comma separated strings
    operationId: findPetsByStatus
    x-wso2-production-endpoints: "#/x-wso2-endpoints/myEndpoint3"
    
    x-wso2-endpoints:
    - myEndpoint:
    urls:
    - https://petstore.swagger.io/v2
    - https://petstore.swagger.io/v5
    securityConfig:
    type: basic
    username: roshan
    - myEndpoint3:
    urls:
    - https://petstore.swagger.io/v3
    - https://petstore.swagger.io/v4
    securityConfig:
    type: basic
    username: kim
```

    A complete sample can be found [here.](https://github.com/wso2/product-microgateway/blob/master/samples/endpoint_by_reference_sample.yaml)

    ###### Invoking an API using basic authentication

    When running the micro gateway, you can provide the password as an environment variable.

    **Format**

``` java
    bash gateway <path_to_the_excutable_file> --<Endpoint Name>_<Endpoint Type>_basic_password=<passowrd>
```

    -   endpoint Name: Name specified in the open API definition under `           x-wso2-endpoints          `
    -   Endpoint Type: prod or sand.

    **sample**

``` java
    bash gateway <path_to_the_excutable_file> --myEndpoint3_prod_basic_password=123456
```


