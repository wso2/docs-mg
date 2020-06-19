# Overriding Endpoints for Developer-first Approach APIs

The following sections describe how you can override endpoint information for APIs that have been added to WSO2 API Microgateway using an OpenAPI definition.

### Overriding a single endpoint at runtime

Use the following command to start WSO2 Microgateway. Here the system variables are provided at server start-up to override the endpoint of the API, which has been added to WSO2 API Microgateway using an OpenAPI definition.

``` tab="Format"
bash gateway <endpoint-name>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL>" -e <endpoint-name>_<endpoint-type>_basic_password=<password> <path-to-MGW-executable-file>

- <endpoint-name> - Name specified in the OpenAPI definition under the `x-wso2-endpoints` OpenAPI extension.
- <endpoint-type> -  Use one of the following values based on the type of the endpoint.
- prod - Use this for a production endpoint.
- sand - Use this for a sandbox endpoint.
- <endpoint-index>- The endpoint index starts from 0. Therefore, when overriding a single endpoint this value is 0.
- <path-to-MGW-executable-file> - Enter the path to the WSO2 API Microgateway project's executable file ( `.balx` ).
- Basic Auth parameters

note:
    The following parameters are only required if your endpoint is **protected by Basic Auth credentials** 
    
   `<endpoint-name>_<endpoint-type>_basic_password=<password>`
```

``` java tab="Example"
bash gateway -e myEndpoint_prod_endpoint_0="http://wso2.com" -e myEndpoint_prod_basic_password=123456 /home/user/petstore-project/target/petstore-project.balx
```

``` java tab="Example Code Snipper"
    x-wso2-basePath: /petstore/v1
    x-wso2-production-endpoints:"#/x-wso2-endpoints/myEndpoint"
    paths:
      "/pet/findByStatus":
      get:
        tags:
        - pet
      summary: Finds Pets by status
      description: Multiple status values can be provided with comma separated strings
      operationId: findPetsByStatus
      x-wso2-production-endpoints:"#/x-wso2-endpoints/myEndpoint3"
      x-wso2-sandbox-endpoints: "#/x-wso2-endpoints/myendpoint4"

      .
      .
      . 
    x-wso2-endpoints:
      - myEndpoint:
        urls:
          - https://petstore.swagger.io/v2
          - https://petstore.swagger.io/v5
        securityConfig:
          type: basic
          username: john
      - myEndpoint3:
        urls:
          - https://petstore.swagger.io/v3
          - https://petstore.swagger.io/v4
        securityConfig:
          type: basic
          username: john
      - myEndpoint4:
        urls:
          - https://www.mocky.io/v2/5d009a293200005f00f9d5ca
          - https://www.mocky.io/v2/5d009a7c3200005f00f9d5cc
        securityConfig:
          type: basic
          username: john
```

### Overriding multiple endpoints at runtime

When using a developer-first approach , use the following command to start WSO2 Microgateway. Here the system variables are provided at server start-up to override the endpoints that correspond to the API.

``` tab="Format"
bash gateway <endpoint-name>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL_1>" <endpoint-name>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL_2>" -e <endpoint-name>_<endpoint-type>_basic_password=<password> <path-to-MGW-executable-file

- <endpoint-name> - Name specified in the OpenAPI definition under the `x-wso2-endpoints` OpenAPI extension.
- <endpoint-type>-  Use one of the following values based on the type of the endpoint
- prod - Use this for a production endpoint.
- sand - Use this for a sandbox endpoint.
- <endpoint-index> - The endpoint index starts from 0. If there are many URLs (e.g., load balanced or fail over) , you can override them using endpoint indexes (e.g., 1, 2, 3).
- <path-to-MGW-executable-file>- Enter the path to the WSO2 API Microgateway project's executable file ( `.balx` ).

- Basic Auth parameters

  Note:  
    The following parameters are only required if your endpoint is **protected by Basic Auth credentials** .

    - <endpoint-name>_<endpoint-type>_basic_password=<password>
```

``` java tab="Example"
    bash gateway -e myEndpoint_prod_endpoint_0="http://wso2.com" -e myEndpoint_prod_endpoint_1="http://wso2.support.com" -e myEndpoint_prod_basic_password=123456 /home/user/petstore-project/target/petstore-project.balx
```

``` tab="Example code snippet"
    
    Let's base this example on the following OpenAPI definition.

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
    x-wso2-sandbox-endpoints: "#/x-wso2-endpoints/myendpoint4"
    .
    .
    .
    .

    x-wso2-endpoints:
      - myEndpoint:
      urls:
        - https://petstore.swagger.io/v2
        - https://petstore.swagger.io/v5
      securityConfig:
        type: basic
        username: john
      - myEndpoint3:
        urls:
          - https://petstore.swagger.io/v3
          - https://petstore.swagger.io/v4
      securityConfig:
        type: basic
        username: john
      - myEndpoint4:
      urls:
        - https://www.mocky.io/v2/5d009a293200005f00f9d5ca
        - https://www.mocky.io/v2/5d009a7c3200005f00f9d5cc
      securityConfig:
        type: basic
        username: john
```


