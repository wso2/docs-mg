# Overriding Endpoints for Developer-first Approach APIs

The following sections describe how you can override endpoint information for APIs that have been added to WSO2 API Microgateway using an OpenAPI definition.

You need to define endpoints as endpoint objects in the OpenAPI definition in order to override endpoints at runtime. See [defining endpoint object]({{base_path}}/how-tos/endpoints/defining-endpoints-in-an-openapi-definition/#endpoint-object).

## Overriding a single endpoint

#### Overriding a single endpoint at runtime

Use the following command to start WSO2 Microgateway. Here the system variables are provided at server start-up to override the endpoint of the API, which has been added to WSO2 API Microgateway using an OpenAPI definition.

``` tab="Format - binary" 
gateway <path-to-MGW-executable-file> --<endpoint-name>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL>"

- <path-to-MGW-executable-file> - Enter the path to the WSO2 API Microgateway project's executable file ( `.jar` ).
- <endpoint-name> - Name specified in the OpenAPI definition under the `x-wso2-endpoints` OpenAPI extension.
- <endpoint-type> -  Use one of the following values based on the type of the endpoint.
    - prod - Use this for a production endpoint.
    - sand - Use this for a sandbox endpoint.
- <endpoint-index>- The endpoint index starts from 0. Therefore, when overriding a single endpoint this value is 0.
```

``` java tab="Example - binary"
gateway /home/user/petstore-project/target/petstore-project.jar --myEndpoint_prod_endpoint_0="http://wso2.com" 
```

``` tab="Example - docker" 
docker run -d -p 9090:9090 -p 9095:9095 -e myEndpoint_prod_endpoint_0="http://wso2.com" docker.wso2.com/petstore:v1
```

#### Overriding a single endpoint at runtime which is protected by basic authentication

Use the following command to start WSO2 Microgateway. Here the system variables are provided at server start-up to override the endpoint of the API, which has been added to WSO2 API Microgateway using an OpenAPI definition.

``` tab="Format"
gateway <path-to-MGW-executable-file> --<endpoint-name>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL>" --<endpoint-name>_<endpoint-type>_basic_password=<password> 

- <path-to-MGW-executable-file> - Enter the path to the WSO2 API Microgateway project's executable file ( `.jar` ).
- <endpoint-name> - Name specified in the OpenAPI definition under the `x-wso2-endpoints` OpenAPI extension.
- <endpoint-type> -  Use one of the following values based on the type of the endpoint.
    - prod - Use this for a production endpoint.
    - sand - Use this for a sandbox endpoint.
- <endpoint-index>- The endpoint index starts from 0. Therefore, when overriding a single endpoint this value is 0.
- Basic Auth parameters

note:
    The following parameters are only required if your endpoint is **protected by Basic Auth credentials** 
    
   `<endpoint-name>_<endpoint-type>_basic_password=<password>`
```

``` java tab="Example - binary"
gateway /home/user/petstore-project/target/petstore-project.jar --myEndpoint_prod_endpoint_0="http://wso2.com" --myEndpoint_prod_basic_password=123456 
```

``` java tab="Example - docker"
docker run -d -p 9090:9090 -p 9095:9095 -e myEndpoint_prod_endpoint_0="http://wso2.com" -e myEndpoint_prod_basic_password=123456 docker.wso2.com/petstore:v1
```


## Overriding multiple endpoints


#### Overriding multiple endpoints at runtime

When using a developer-first approach , use the following command to start WSO2 Microgateway. Here the system variables are provided at server start-up to override the endpoints that correspond to the API.

``` tab="Format"
gateway <path-to-MGW-executable-file> --<endpoint-name>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL_1>" --<endpoint-name>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL_2>"

- <path-to-MGW-executable-file>- Enter the path to the WSO2 API Microgateway project's executable file ( `.jar` ).
- <endpoint-name> - Name specified in the OpenAPI definition under the `x-wso2-endpoints` OpenAPI extension.
- <endpoint-type>-  Use one of the following values based on the type of the endpoint
    - prod - Use this for a production endpoint.
    - sand - Use this for a sandbox endpoint.
- <endpoint-index> - The endpoint index starts from 0. If there are many URLs (e.g., load balanced or fail over) , you can override them using endpoint indexes (e.g., 1, 2, 3).

```

``` java tab="Example - binary"
gateway /home/user/petstore-project/target/petstore-project.jar --myEndpoint_prod_endpoint_0="http://wso2.com" --myEndpoint_prod_endpoint_1="http://wso2.support.com" 
```

``` java tab="Example - docker"
docker run -d -p 9090:9090 -p 9095:9095 -e myEndpoint_prod_endpoint_0="http://wso2.com" -e myEndpoint_prod_endpoint_1="http://wso2.support.com" docker.wso2.com/petstore:v1
```

#### Overriding multiple endpoints at runtime which are protected by basic authentication

When using a developer-first approach , use the following command to start WSO2 Microgateway. Here the system variables are provided at server start-up to override the endpoints that correspond to the API.

``` tab="Format"
gateway <path-to-MGW-executable-file> --<endpoint-name>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL_1>" --<endpoint-name>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL_2>" --<endpoint-name>_<endpoint-type>_basic_password=<password> 

- <path-to-MGW-executable-file>- Enter the path to the WSO2 API Microgateway project's executable file ( `.jar` ).
- <endpoint-name> - Name specified in the OpenAPI definition under the `x-wso2-endpoints` OpenAPI extension.
- <endpoint-type>-  Use one of the following values based on the type of the endpoint
    - prod - Use this for a production endpoint.
    - sand - Use this for a sandbox endpoint.
- <endpoint-index> - The endpoint index starts from 0. If there are many URLs (e.g., load balanced or fail over) , you can override them using endpoint indexes (e.g., 1, 2, 3).


- Basic Auth parameters

  Note:  
    The following parameters are only required if your endpoint is **protected by Basic Auth credentials** .

    - <endpoint-name>_<endpoint-type>_basic_password=<password>
```

``` java tab="Example - binary"
gateway /home/user/petstore-project/target/petstore-project.jar --myEndpoint_prod_endpoint_0="http://wso2.com" --myEndpoint_prod_endpoint_1="http://wso2.support.com" --myEndpoint_prod_basic_password=123456 
```

``` java tab="Example - docker"
docker run -d -p 9090:9090 -p 9095:9095 -e myEndpoint_prod_endpoint_0="http://wso2.com" -e myEndpoint_prod_endpoint_1="http://wso2.support.com" -e myEndpoint_prod_basic_password=123456 docker.wso2.com/petstore:v1
```