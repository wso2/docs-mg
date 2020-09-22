# Configuration for WSO2 API Manager

Follow the instructions below to configure the WSO2 Microgateway Toolkit and the WSO2 Microgateway Runtime in order to use it with [WSO2 API Manager 3.2.0](https://apim.docs.wso2.com/en/3.2.0/). The WSO2 Microgateway 3.2.0 supports WSO2 API Manager versions, 2.6.0 onwards (2.6.0, 3.0.0, 3.1.0 and 3.2.0).

- [Configuration for WSO2 API Manager](#configuration-for-wso2-api-manager)
    - [Configuring the Microgateway 3.2.x Toolkit](#configuring-the-microgateway-32x-toolkit)
    - [Configuring the Microgateway 3.2.x Runtime](#configuring-the-microgateway-32x-runtime)
  - [Configuring Runtime for Older API Manager Versions](#configuring-runtime-for-older-api-manager-versions)
      - [Enable Legacy Key Manager](#enable-legacy-key-manager)
      - [Disable Event Hub Configuration](#disable-event-hub-configuration)
      - [Change the Throttling Server URL](#change-the-throttling-server-url)
      - [Change the Token Revocation Endpoint URL](#change-the-token-revocation-endpoint-url)
      - [Set the Analytics Stream Version](#set-the-analytics-stream-version)

### Configuring the Microgateway 3.2.x Toolkit

1.  Add the public certificate of WSO2 API Manager to the trust store.

    !!! info
        Refer the documentation on [Importing Certificates to the API Microgateway Truststore]({{base_url}}/how-tos/security/importing-certificates-to-the-api-microgateway-truststore/) for more information.

    - Export the public certificate of WSO2 API Manager.
    A sample command is given below.

    ``` java
        keytool -export -alias wso2carbon -file wso2carbon.crt -keystore <API-M_HOME>/repository/resources/security/wso2carbon.jks
    ```

    - Import the certificate to the trust store in the Microgateway Toolkit.

    ```tab="Example"
    keytool -import -trustcacerts -alias wso2carbon2 -file wso2carbon.crt -keystore <MGW_TOOLKIT_HOME>/lib/platform/bre/security/ballerinaTruststore.p12
    ```

2.  Change the REST API version and DCR version to be compatible with the WSO2 API Manager.

    | API Manager version | REST API version | DCR version |
    |---------------------|------------------|-------------|
    | 2.6.0               | v0.14            | v0.14       |
    | 3.0.0               | v1.0             | v0.15       |
    | 3.1.0               | v1.1             | v0.16       |
    | 3.2.0               | v1.2             | v0.17       |

    Open the `<MGW_TOOLKIT_HOME>/conf/toolkit-config.toml` file and change the `restVersion` and `dcrVersion` accordingly.

### Configuring the Microgateway 3.2.x Runtime
1.  Add the public certificate of WSO2 API Manager to the trust store.

    !!! info
        Refer the documentation on [Importing Certificates to the API Microgateway]({{base_url}}/how-tos/security/importing-certificates-to-the-api-microgateway-truststore/) for more information.

    - Export public certificate of WSO2 API Manager.

    A sample command is given below.

    ```tab="Example"
    keytool -export -alias wso2carbon -file wso2carbon.crt -keystore <API-M_HOME>/repository/resources/security/wso2carbon.jks
    ```

    - Import the certificate using the command given below, to the trust store in the Microgateway runtime.

    ```tab="Example"
        keytool -import -trustcacerts -alias wso2carbon2 -file wso2carbon.crt -keystore <MGW_RUNTIME_HOME>/runtime/bre/security/ballerinaTruststore.p12
    ```

2.  Open the `<MGW_RUNTIME_HOME>/conf/micro-gw.conf` file. Add the alias (e.g. wso2carbon2) to the truststore to corresponding configurations.
    
    For example, to configure the JWT authentication for API Manager JWTs, add the following JWT issuer configuration to the `micro-gw.conf` .


    ```toml tab="Format"
        [[jwtTokenConfig]]
            issuer="https://localhost:9443/oauth2/token"
            audience="http://org.wso2.apimgt/gateway"
            certificateAlias="wso2carbon2"
            validateSubscription = false
    ```

You can now successfully invoke APIs through the Microgateway using the JWT tokens issued by WSO2 API Manager.

## Configuring Runtime for Older API Manager Versions

When you use Microgateway 3.2.0 runtime with older versions of WSO2 API Manager (2.6.0, 3.0.0 or 3.1.0), please do the below configurations in the `<MGW_RUNTIME_HOME>/conf/micro-gw.conf` file.

#### Enable Legacy Key Manager

```toml tab="Example"
    [keyManager]
    enableLegacyMode = true
```

This enables using the API Manager key validation service for opaque token validation. 

#### Disable Event Hub Configuration

```toml tab="Example"
    [apim.eventHub]
    enable = false
```

Apim Event hub is used for retrieving API, Application and subscription created in API Manager. This is only available in API Manager 3.2.0.

#### Change the Throttling Server URL

```toml tab="Format"
    [throttlingConfig.dataRetriever]
    serverUrl = "https://<API-M_HOST>:<API-M_PORT>/throttle/data/v1"
```

#### Change the Token Revocation Endpoint URL

```toml tab="Format"
    [tokenRevocationConfig.persistent]
    endpointURL = "https://<API-M_HOST>:<API-M_PORT>/throttle/data/v1"
```

#### Set the Analytics Stream Version

```toml tab="Example"
    [analytics]
    streamVersion = "3.0.0"`
```

Set the analytics stream version corresponding to the API-M Analytics version that you are using.

|Analytics Version|Stream Version|
|-----------------|--------------|
|3.0.0|3.0.0|
|3.1.0|3.1.0|
