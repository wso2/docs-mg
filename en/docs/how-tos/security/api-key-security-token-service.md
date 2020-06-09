# API Key Security Token Service

The WSO2 API Microgateway provides a simple Security Token Service(STS) for API Key. The STS issues a self-contained token as an API Key for API access.

-   [Quick start](#APIKeySecurityTokenService-Quickstart)
-   [Configuring WSO2 API Microgateway for API Key STS](#APIKeySecurityTokenService-ConfiguringWSO2APIMicrogatewayforAPIKeySTS)
-   [Getting an API Key](#APIKeySecurityTokenService-GettinganAPIKey)
-   [Invoke an API using API Key](#APIKeySecurityTokenService-InvokeanAPIusingAPIKey)

### Quick start

You can obtain an API Key with the default configuration using a request similar to the following.

**Example**

``` java
    curl -k -X GET "https://localhost:9095/apikey" -H  "Authorization:Basic YWRtaW46YWRtaW4="
```

    !!! info
    Basic authentication header
    Here the basic authentication header is the base64 encoded username and password. For example Base64\_Encoded(admin:admin). This is required because API key service (/apikey) is protected with basic authentication which is explained below.

### Configuring WSO2 API Microgateway for API Key STS

###### 1. Configuring the WSO2 API Microgateway for Basic Authentication

API Key STS is secured with basic authentication. Therefore it is required to configure for basic authentication.

!!! note
    Please follow the documentation on [Configuring the WSO2 API Microgateway for Basic Authentication](https://docs.wso2.com/display/MG310/Basic+Authentication#BasicAuthentication-ConfiguringtheWSO2APIMicrogatewayforBasicAuthentication)

###### 2. API Key STS configuration

To configure the API Key STS, open &lt;MGW-RUNTIME\_HOME&gt;/conf/micro-gw.conf file in a text editor and configure the parameters as described below.

``` yml
    [apikey.issuer]
    # API Key STS token configurations
    [apikey.issuer.tokenConfig]
    enabled=true
    keyStorePath="${mgw-runtime.home}/runtime/bre/security/ballerinaKeystore.p12"
    keyStorePassword="ballerina"
    issuer="https://localhost:9095/apikey"
    certificateAlias="ballerina"
    # validity time in seconds for the API Key. -1 is to indicate unlimited time 
    validityTime=-1
    # API Key STS Allowed APIs
    # provide the list of allowed APIs by the generated API Key
    # [[apikey.issuer.api]] 
    # name="API name given in the API Definition"
    # versions="Allowed versions" or "*" to allow all versions
    # Examples :
    # API 1
    [[apikey.issuer.api]]
    name="Swagger Petstore New"
    versions="1.0.0, v1, v2"
    # API 2
    [[apikey.issuer.api]]
    name="MyAPI"
    versions="*" 
```

    ### Getting an API Key

    Use the cURL command below to get an API Key.

    -   [**Format**](#2ab3114b1a3c400c9afda6d7f3be687a)
    -   [**Example**](#57ae2f8d0a8244b09f6b0a6e3bc5ed77)

``` java
    curl -k -X GET "<host>:<port>/apikey" -H  "Authorization:Basic base64(username:password)"
```

``` java
    curl -k -X GET "https://localhost:9095/apikey" -H  "Authorization:Basic YWRtaW46YWRtaW4="
```

    You can request an API Key valid for a specific time period in invoke time as follows :

    -   [**Format**](#ad11c627cd4742bc86e06aec16e79790)
    -   [**Example**](#24b3dc8a3dd3449a8708a5946d9661a2)

``` java
    curl -k -X GET "<host>:<port>/apikey" -H "Content-Type: application/json" -H  "Authorization:Basic base64(username:password)" -d '{"validityTime":<time in seconds>}'
```

``` java
    curl -k -X GET "https://localhost:9095/apikey" -H "Content-Type: application/json" -H  "Authorization:Basic YWRtaW46YWRtaW4=" -d '{"validityTime":600}'
```

    ### Invoke an API using API Key

    Refer [API Key Authentication](https://docs.wso2.com/display/MG310/API+Key+Authentication) for information.
