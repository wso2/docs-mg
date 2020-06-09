# API Key Authentication

API Key authentication in WSO2 API Microgateway is a simple authentication scheme that accepts a valid self-contained JWT token issued for accessing APIs. The WSO2 API Microgateway is able to authenticate requests using API Key, on an API level or resource level.

!!! note
    Please refer to [the documentation](https://docs.wso2.com/display/MG310/API+Key+Security+Token+Service) for Issuing API Key in the WSO2 API Microgateway.

The following sections explain how to work with API Key Authentication on the WSO2 API Microgateway.

-   [Enabling API Key Authentication](#APIKeyAuthentication-EnablingAPIKeyAuthentication)
    -   [Defining security schemes](#APIKeyAuthentication-Definingsecurityschemes)
    -   [Applying the API Key authentication security scheme](#APIKeyAuthentication-ApplyingtheAPIKeyauthenticationsecurityscheme)
    -   [Configuring the WSO2 API Microgateway for API Key Authentication](#APIKeyAuthentication-ConfiguringtheWSO2APIMicrogatewayforAPIKeyAuthentication)
-   [Invoking an API using API Key Authentication](#APIKeyAuthentication-InvokinganAPIusingAPIKeyAuthentication)
-   [Notes](#APIKeyAuthentication-Notes)

### Enabling API Key Authentication

###### Defining security schemes

Security schemes must be defined on the Open API definition under **securitySchemes.** One or more API key security schemes can be used (as in logical OR) at the same time. A unique name for "name", query or header for "in"  and apiKey as "type" needs to be given for the defined API Key security scheme. This name will be used to refer to the scheme on API level or resource level **.**

The below example defines an API Key security scheme named apiKeyAuth which sends X-API-Key as a request header. Here, apiKeyAuth is a name provided for the security scheme and X-API-Key is the name for the header.

!!! note
    The following example shows how to define API Key security schemes in [OAS3](https://swagger.io/docs/specification/authentication/api-keys/) . If you are using an OAS2 API definition, please refer to [this](https://swagger.io/docs/specification/2-0/authentication/api-keys/) [swagger document](https://swagger.io/docs/specification/2-0/authentication/api-keys/) on defining API Key security in OAS2.

``` yml
    # 1) Define the apikey security scheme
    components:
    securitySchemes:
    ApiKeyAuth:        # arbitrary name for the security scheme
    type: apiKey
    in: header       # can be "header" or "query" 
    name: X-API-KEY  # name of the header, query parameter or cookie
    appId:             # you can define several apikey security schemas
    type: apiKey
    in: header
    name: X-APP-ID
```

    ###### Applying the API Key authentication security scheme

    A security scheme can be specified on a resource level or to the whole API by using the section **security** as applicable. Following is an example of how to apply a security scheme to a resource. A resource-level security scheme will override an API level security scheme.  The security scheme ApiKeyAuth defined has been referred to from the resource "/pet/{petId}: by using the security section. For more information see [swagger docs - API Keys](https://swagger.io/docs/specification/authentication/api-keys/) .

    !!! note
    The following example shows how to define API Key security schemes in [OAS3](https://swagger.io/docs/specification/authentication/api-keys/) . If you are using an OAS2 API definition, please refer to [this](https://swagger.io/docs/specification/2-0/authentication/api-keys/) [swagger document](https://swagger.io/docs/specification/2-0/authentication/api-keys/) on defining API Key security in OAS2.

``` yml
    # 1) Apply API Key as operation-specific security
    "/pet/{petId}":
    get:
    tags:
    - pet
    summary: Find pet by ID
    description: Returns a single pet
    operationId: getPetById
    parameters:
    - name: petId
    in: path
    description: ID of pet to return
    required: true
    schema:
    type: integer
    format: int64
    security:
    - ApiKeyAuth: []

    # 2) Apply the API key globally to all operations
    security:
    - ApiKeyAuth: [] 
```

    ###### Configuring the WSO2 API Microgateway for API Key Authentication

    The WSO2 API Microgateway expects a self-contained JWT as an API Key.

    The following table explains the runtime configurations for API Key authentication in WSO2 API Microgateway. The configuration file ( `         micro-gw.conf        ` ) for the Microgateway runtime is located in the `         <MGW-RUNTIME-HOME>/conf        ` directory.

    **Format**

``` yml
    [apikey.tokenConfigs]
    issuer="https://localhost:9095/apikey"
    audience="http://org.wso2.apimgt/gateway"
    certificateAlias="ballerina"
    validateAllowedAPIs=false
```

    Heading
    Description
    Sub Heading
    Description
    Default value
    apikey.tokenConfigs
    These details are used by the Microgateway when it validates the API Key present in the request. The Microgateway verifies the signature verification and validates the issuer, audience and validity period as well.
    `             issuer            `
    The Secure Token Service (STS) that has issued the API Key.
    <https://localhost:9095/apikey>
    `             audience            `
    The audience claim present in the API Key is matched against the value provided in the configuration.
    <http://org.wso2.apimgt/gateway>
    `             certificateAlias            `
    The public certificate alias of the STS.
    ballerina
    validateAllowedAPIs

    When validating the API Key token, validate the allowed APIs of the API Key token

    false

    ### Invoking an API using API Key Authentication

    Use the cURL command below to invoke the API Key secured API in the Microgateway.

    Example

    **Format**

``` java
    curl -k -X GET "<API_URL>" -H  "accept: application/json" -H  "<Header name>: <API Key>"
```

    **Example**

``` erl
    curl -k -X GET "https://localhost:9095/petstore/v1/pet/3" -H  "accept: application/json" -H  "X-API-KEY: eyJhbGciOiJSUzI1NiIsICJ0eXAiOiJqd3QiLCAia2lkIjoiYmFsbGVyaW5hIn0=.eyJzdWIiOiJhZG1pbiIsICJpc3MiOiJodHRwczovL2xvY2FsaG9zdDo5MDk1L2FwaWtleSIsICJpYXQiOjE1ODAxMDUzOTAsICJqdGkiOiI3OTFiNzAyMC1kN2U2LTRmYmEtYmMyMy1lMzk5YTVlNmYzYjciLCAiYXVkIjoiaHR0cDovL29yZy53c28yLmFwaW1ndC9nYXRld2F5IiwgImtleXR5cGUiOiJQUk9EVUNUSU9OIiwgImFsbG93ZWRBUElzIjpbXX0=.f-86LfD7lLq-0oM1V1u1dLW7fWcydH4MElWVxUfRTGGRiXHhh8VrS5q18LdCtH1E1jav5pPZpdDQgQUvhVYNXVqiipydfJFOMbDysA0Jdakmh_TVmeZRHhIYgzcVHQNnXMcYXg7Ns4QPBvJVONfbmDluuiU_uFnOPBiXj2N4HL2OTLgVXkEoVTEpL0mmaO2Ab4ZHqKW5xj32aeK8sEAtU5Nd3rQOGvfEwL7xvx4JAmza8ka0eYt7c4QCPVcDSVOkdas9njlsvEdtka5GRL9PAx3xg370phSD1cji6WSRlZhEGzuq6hjLbCqsf17KvZgK1zbrEbSypjgegEe-any3EQ=="
```

    ### Notes

    !!! tip
    API Keys are simple JWTs. To enable additional security for API Key, set " `         validateAllowedAPIs        ` " to true to restrict API Key for the allowed APIs list contained inside of the JWT. Then it will check whether "allowedAPIs" or "subscribedAPIs" claim exists and only allow the API request if the API name and the version are listed.
    Example:
    API Key -
``` java
    eyJhbGciOiJSUzI1NiIsICJ0eXAiOiJqd3QiLCAia2lkIjoiYmFsbGVyaW5hIn0=.eyJzdWIiOiJhZG1pbiIsICJpc3MiOiJodHRwczovL2xvY2FsaG9zdDo5MDk1L2FwaWtleSIsICJpYXQiOjE1ODAxMTE4ODgsICJqdGkiOiIxZjQxZTM3MC0zMTRiLTQ4MmEtYTBjYS04YmU0ZDE2MGIxOTUiLCAiYXVkIjoiaHR0cDovL29yZy53c28yLmFwaW1ndC9nYXRld2F5IiwgImtleXR5cGUiOiJQUk9EVUNUSU9OIiwgImFsbG93ZWRBUElzIjpbeyJuYW1lIjoiU3dhZ2dlciBQZXRzdG9yZSBOZXciLCAidmVyc2lvbiI6IjEuMC4wIn0sIHsibmFtZSI6IlN3YWdnZXIgUGV0c3RvcmUgTmV3IiwgInZlcnNpb24iOiJ2MSJ9LCB7Im5hbWUiOiJNeUFQSSIsICJ2ZXJzaW9uIjoiKiJ9XX0=.EUP5_H7AoXgPsxL2TkDlrlnHq2F3VFwfGxQaS2CSj8lE3lP2HgfMgY1osAODegK7t0mWNZqkfkfw5xLfqYjBXikszyGabHxB-FX3GKOYNw-fFLOhSSxVD4lDOBdFpmyhGjeE8RTyrMl-HY3Apjid92sO5VlW6M-y3QTYzrmm8Gs_C3Z5z2hxpAWHXo-uNF0iVm4sdz7j4rgOxGf6HJKgXeZ47c3CDNKaNA9pRZd_ULaMvUvcmXOGJ7Xh21Pqmh1qQkT4OJm1T-2dCx8fju9YHH1Sdc3UGDDu7mK2m1_8Z14CpZOOPQK_zCp2bmjonQgJ-CTJENjfNK37dNY1a2xomw==
```
    Decoded payload -
``` yml
    {
      "sub": "admin",
      "iss": "https://localhost:9095/apikey",
      "iat": 1580111888,
      "jti": "1f41e370-314b-482a-a0ca-8be4d160b195",
      "aud": "http://org.wso2.apimgt/gateway",
      "keytype": "PRODUCTION",
      "allowedAPIs": 
        [{
          "name": "Swagger Petstore New",
          "version": "1.0.0"
        },
        {
          "name": "Swagger Petstore New",
          "version": "v1"
        },
        {
          "name": "MyAPI",
          "version": "*"
        }]
    }
```
    Above API Key will allow accessing v1, v2 and 1.0.0 versions of Swagger Petstore New API and all versions of MyAPI API.


