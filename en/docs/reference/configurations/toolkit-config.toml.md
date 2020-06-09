# toolkit-config.toml

The following are the configurations with regard to WSO2 API Microgateway Toolkit. The configuration file ( `         toolkit-config.toml        ` ) for the Microgateway toolkit is located in the `         <MGW-TK_HOME>/conf        ` directory.

Heading
Description
Sub Heading
Description
Default value
`             client            `
The configurations of the Microgateway toolkit client when connecting with the WSO2 API Manager (WSO2 API-M) server to fetch API data.
`             httpRequestTimeout            `
The client request timeout in seconds
1000000
`             token            `
The configuration required to get a valid token from WSO2 API-M in order to communicate with its secured REST APIs.
    baseURL

The base REST API URL of the WSO2 API-M server. When you update this parameter, this will be applied as the baseURL in the subsequent REST API URLs.
``` java
    https://localhost:9443
```

    restVersion

    The API Manager version and its respective REST version. When you update this parameter, this will be applied as the REST version in the subsequent REST API URLs.
    `             v1.1            `
    `             dcrVersion            `
    The API Manager version and its respective DCR (Dynamic Client Registration) version. When you update this parameter, this will be applied as the DCR version in the subsequent Client Registration URL.
    `             v0.16            `
    `             publisherEndpoint            `
    The Publisher REST API URL of the WSO2 API-M server.
``` java
    {baseURL}/api/am/publisher/{restVersion}
```

    In special scenarios the user can directly enter the complete URL for endpoints.

``` java
    https://gateway.api.cloud.wso2.com/api/am/publisher
```

    `             adminEndpoint            `
    The admin REST API URL of the WSO2 API-M server.
``` java
    {baseURL}/api/am/admin/{restVersion}
```

    In special scenarios the user can directly enter the complete URL for endpoints.

``` java
    https://gateway.api.cloud.wso2.com/api/am/admin
```

    `             registrationEndpoint            `
    The dynamic client registration endpoint URL of the the WSO2 API-M server. This is used to create an OAuth app in the WSO2 API-M server in order to request tokens.
``` java
    {baseURL}/client-registration/{dcrVersion}/register
```

    In special scenarios the user can directly enter the complete URL for endpoints.

``` java
    https://gateway.api.cloud.wso2.com/client-registration/register
```

    `             tokenEndpoint            `
    The token endpoint of the WSO2 API-M server that is used to request tokens in order to communicate with REST APIs.
``` java
    {baseURL}/oauth2/token
```

    In special scenarios the user can directly enter the complete URL for endpoints.

``` java
    https://gateway.api.cloud.wso2.com/oauth2/token
```

    `             username            `
    The username of the user that is authenticated in the WSO2 API-M server in order request details from REST APIs.
    This value gets automatically filled when the user enters data via the CLI. You can specify a username as well, so that then the CLI only asks the user for a password.
    `             clientId            `
    The client ID of the application that is created on the WSO2 API-M server. This value will also be automatically created when the user adds values via the CLI. You can override the value here in order to be able to use an already existing application rather than creating a new OAuth Application.
    `             clientSecret            `
    The client secret of the application. his value will also be automatically created when the user adds values via the CLI. You can override the value of the client secret here.
    `             trustStoreLocation            `
    The trust store location of the Microgateway toolkit.
    `             trustStorePassword            `
    Encoded value of the trust store password.
    `             corsConfiguration            `
    The Cross Origin Resource Sharing (CORS) related configuration used by the Microgateway runtime
    `             accessControlAllowOrigins            `
    Specifies the allowed origins
    \*
    `             accessControlAllowCredentials            `
    Includes cookies on cross-origin requests.
    false
    `             corsConfigurationEnabled            `
    Defined whether CORS support is enabled or not for the Microgateway.
    true
    `             accessControlAllowHeaders            `
    Allowed headers.
``` java
    ["authorization", "Access-Control-Allow-Origin", "Content-Type", "SOAPAction"]
```

    `             accessControlAllowMethods            `
    Allowed HTTP methods.
``` java
    ["GET", "PUT", "POST", "DELETE", "PATCH", "OPTIONS"]
```

    ### Sample

    The following is a sample Microgateway toolkit configuration file.

``` java
    [client]
    httpRequestTimeout = 1000000

    [token]
    baseURL = ""
    restVersion = "v1.1"
    dcrVersion = "v0.16"
    publisherEndpoint = "{baseURL}/api/am/publisher/{restVersion}"
    adminEndpoint = "{baseURL}/api/am/admin/{restVersion}"
    registrationEndpoint = "{baseURL}/client-registration/{dcrVersion}/register"
    tokenEndpoint = "{baseURL}/oauth2/token"
    username = ""
    clientId = ""
    clientSecret = ""
    trustStoreLocation = ""
    trustStorePassword = ""

    [corsConfiguration]
    corsConfigurationEnabled = true
    accessControlAllowCredentials = false
    accessControlAllowOrigins = ["*"]
    accessControlAllowHeaders = ["authorization", "Access-Control-Allow-Origin", "Content-Type", "SOAPAction"]
    accessControlAllowMethods = ["GET", "PUT", "POST", "DELETE", "PATCH", "OPTIONS"]
```
