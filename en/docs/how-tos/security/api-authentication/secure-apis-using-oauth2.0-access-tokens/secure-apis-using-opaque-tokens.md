# Secure APIs Using Opaque Tokens

-   [Configure API Microgateway to validate OAuth2 Opaque tokens with WSO2 API Manager](#SecureAPIsUsingOpaqueTokens-ConfigureAPIMicrogatewaytovalidateOAuth2OpaquetokenswithWSO2APIManager)
-   [Configure API Microgateway to validate OAuth2 Opaque tokens with External key manager](#SecureAPIsUsingOpaqueTokens-ConfigureAPIMicrogatewaytovalidateOAuth2OpaquetokenswithExternalkeymanager)
    -   [Securing the Introspect Endpoint](#SecureAPIsUsingOpaqueTokens-SecuringtheIntrospectEndpoint)
        -   [Basic Authentication](#SecureAPIsUsingOpaqueTokens-BasicAuthentication)
        -   [OAuth2](#SecureAPIsUsingOpaqueTokens-OAuth2)
-   [Subscription Validation](#SecureAPIsUsingOpaqueTokens-SubscriptionValidation)

WSO2 API Microgateway can be configured to connect to a key manager (external or to the WSO2 API Manager). Token introspection is one of the main tasks carried out by the key manager in addition to token generation, revocation, and invalidation.

[OAuth 2.0 Token Introspection](https://tools.ietf.org/html/rfc7662) is a specification that defines how a protected resource can validate and obtain meta-information of OAuth2.0 tokens issued by an authorization server. This specification defines an introspect endpoint that provides meta-information including the token validity of a given OAuth2.0 token.  The introspection endpoint is implemented on the authorization server-side.

WSO2 Microgateway can be configured to connect with an introspect endpoint (/introspect) of a key manager.  If the API Microgateway receives an opaque (non-JWT) token, then the external key manager's introspect endpoint will be invoked to validate the token. Upon validation of the token,  API  Microgateway will cache the token. A subsequent request with the same token will first check the cache before connecting with the key manager.

#### **Configure API Microgateway to validate OAuth2 Opaque tokens with WSO2 API Manager**

Microgateway by default connects with [WSO2 API Manager](https://apim.docs.wso2.com/en/latest/) key validation service. This service is responsible to validate the tokens Issued by API Manager's key manager component.  WSO2 key manager component validates the token, scopes, subscription and generates the jwt to be sent to backend as well if configured. When use with WSO2 API manager the microgateway full features like subscription validation, application and subscription level throttling, complete anlytics are enabled.

**WSO2 APIM as the key manager**

``` java
    [keyManager]
    serverUrl = "https://localhost:9443"
    tokenContext = "oauth2"
    external = false
    [keymanager.security.basic]
    enabled = true
    username = "admin"
    password = "admin"
```

    !!! info
    Authentication for key validation service of APIM
    When using APIM key validations service, that service is secured using basic authentication. This is a soap service in APIM server and requires admin priviledges to access it. Hence one of  admin user's credentials, available on APIM server  are required

#### **Configure API Microgateway to validate OAuth2 Opaque tokens with External key manager
**

Microgateway can be configured with external key manager that does supports standard introspection capabilities. In order to enable introspection with an external key manager, " **external** " parameter should be set under the key manager configurations.

**Enable external key manager**

``` yml
    # Key manager configurations
    [keyManager]
    # Connection URL of the Key Manager server
    serverUrl = "https://localhost:9443"
    # The token endpoint context of the Key Manager server
    tokenContext = "oauth2"
    # timestamp skew in seconds which added when checking the token validity period
    timestampSkew = 5000
    # External Key Manager
    external = true
```

    ##### Securing the Introspect Endpoint

    Different key managers might use different mechanisms to **secure the introspect endpoint.** Microgateway supports following authentication schemes with introspection endpoint

    ###### **Basic** Authentication

    The following configuration can be used to provide the basic authentication details.

    **Basic authentication configs**

``` yml
    # Basic security configurations
    [keymanager.security.basic]
    enabled = true
    username = "admin"
    password = "admin"
```

    These values can be set as environment variables as well.

    ###### OAuth2

    If OAuth2 is used in key manager to protect the introspect endpoint then oauth2 authentication should be enabled for introspection in Microgateway.

    **OAuth2 authentication**

``` yml
    keymanager.security.oauth2]
    enabled = true
    # Authentication credentials should be sent via (AUTH_HEADER_BEARER/POST_BODY_BEARER/NO_BEARER)?
    credentialBearer = "AUTH_HEADER_BEARER"
    # Token URL for the authorization endpoint
    tokenUrl = ""
```

    Microgateway supports different grants when introspect endpoint is secured with the OAuth2.

    -   **Client Credentials**

    **Client Credentials Grant config**

    ``` yml
        # Oauth2 security grants
        [keymanager.security.oauth2.clientCredential]
        enabled = true
        clientId = ""
        clientSecret = ""
        scopes = ""
    ```

        This will use the client credential grant type to get a valid token in order to call the introspect endpoint. The gateway will reuse this token until its expiry time and renew the token only when token gets expired.

        -   **Password grant**

        **Password grant**

    ``` yml
        [keymanager.security.oauth2.password]
        enabled = true
        clientId = ""
        clientSecret = ""
        scopes = ""
        username = ""
        password = ""
    ```

        -   **Direct token**

        **Direct Access Token Method**

    ``` yml
        [keymanager.security.oauth2.directToken]
        enabled = true
        accessToken = ""
    ```

        In this method, the access token can be directly configured in the configuration, so that gateway will send that token when calling the secured introspection endpoint.

        -   **Refresh Grant**

        **Refresh Grant**

    ``` yml
        [keymanager.security.oauth2.refresh]
        enabled = true
        refreshUrl = ""
        scopes = ""
        refreshToken = ""
        clientId = ""
        clientSecret = ""
    ```

        The refresh grant can be used with "Password Grant" or "Direct Token" method in order to renew the token in case the access token expires. When refresh config is enabled, if password grant is used then gateway will automatically renews the access token if the existing token expires. And also it will renew the token when the direct token method is used.

        #### Subscription Validation

        WSO2 Microgateway can be configured to validate the API subscriptions when using opaque tokens. This can only be used for APIs which are published in API Manager.

        For subscription validation, WSO2 API Manager should be used as the key manager in **internal** mode. In this mode, the token is validated by calling the KeyValidation admin service in the kay manager in contrast to using the introspection API in external mode.

        **Enable external key manager**

``` yml
    # Key manager configurations
    [keyManager]
    # Connection URL of the Key Manager server
    serverUrl = "https://localhost:9443"
    # The token endpoint context of the Key Manager server
    tokenContext = "oauth2"
    # timestamp skew in seconds which added when checking the token validity period
    timestampSkew = 5000
    # Internal Key Manager
    external = false
    [keymanager.security.basic]
    enabled = true
    username = "admin"    
    password = "admin"
```
