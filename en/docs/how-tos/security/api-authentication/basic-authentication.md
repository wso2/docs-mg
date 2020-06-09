# Basic Authentication

Basic authentication is a simple HTTP authentication scheme in which the request will contain an authorization header with a valid  base64 encoded username and password.  The WSO2 API Microgateway is able to authenticate requests using basic, and OAuth2 authentication schemes, on an API level or resource level.  In addition to using these schemes individually, it is also possible to use the OAuth2 and Basic schemes at the same time.

The following sections explain how to work with Basic Authentication on the WSO2 API Microgateway.

-   [Enabling Basic Authentication](#BasicAuthentication-EnablingBasicAuthentication)
    -   [Configuring the WSO2 API Microgateway for Basic Authentication](#BasicAuthentication-ConfiguringtheWSO2APIMicrogatewayforBasicAuthentication)
    -   [Defining security schemes](#BasicAuthentication-Definingsecurityschemes)
    -   [Applying the basic authentication security scheme](#BasicAuthentication-Applyingthebasicauthenticationsecurityscheme)
    -   [Validating scopes through basic authentication](#BasicAuthentication-Validatingscopesthroughbasicauthentication)
    -   [Invoking an API using Basic Authentication](#BasicAuthentication-InvokinganAPIusingBasicAuthentication)

### Enabling Basic Authentication

###### Configuring the WSO2 API Microgateway for Basic Authentication

Add the username and password to the WSO2 API Micrgateway configuration file. Navigate to the `         <MGW_HOME>        ` /conf folder and open the “micro-gw.conf" file. Under `         [b7a.users]        ` section add the username and password as follows. This will be used to validate the incoming requests with the username and password.

!!! note
    The password needs to be converted to the equivalent **SHA1 hashed value in lowercase** before adding it to the micro-gw.conf file. The [SHA1 hash generator](https://passwordsgenerator.net/sha1-hash-generator/) can be used to generate an uppercase SHA1 hashed value then convert it to lowercase.

**Format**

``` java
    [b7a.users.<username>]
    password="<SHA1(password)>"
```

    **Example**

``` java
    [b7a.users]
    [b7a.users.shani]
    password = "083c86287c6b92aafe06b11d71ad2bb770942fc7"
```

    !!! note
    Note
    If you want to use a different hashing algorithm for the password such as SHA256 or SHA512, @ prefix should be added before the hashed value.

**Format**

``` java
    [b7a.users.<username>]
    password="@<HASH_ALGO>:{<HASH_ALGO(password)>}"
```

    **Example**

``` java
    [b7a.users.shani]
    password="@sha256:{5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8}" 
```

    ###### Defining security schemes

    Security schemes must be defined on the Open API definition under **securitySchemes.** One or more schemes can be defined at the same time. A name needs to be given for the defined security scheme. This name will be used to refer to the scheme on API level or resource level **.**

    Below is a security scheme defined by the name of "mybasic". This name can be any arbitrary name, which will be used to identify the defined security scheme. The **type:http** and **scheme:basic** must be defined for Basic Authentication.

    !!! note
    The following example shows how to define Basic authentication security schemes in [OAS3](https://swagger.io/docs/specification/authentication/basic-authentication/) . If you are using an OAS2 API definition, please refer to [this](https://swagger.io/docs/specification/2-0/authentication/api-keys/) [swagger document](https://swagger.io/docs/specification/2-0/authentication/basic-authentication/) on defining Basic authentication in OAS2.

``` yml
    components:
    securitySchemes:
    mybasic:
    type: http
    scheme: basic
```

    ###### Applying the basic authentication security scheme

    A security scheme can be specified on a resource level or to the whole API by using the section **security** as applicable. Following is an example of how to apply a security scheme to a resource. A resource level security scheme will override an API level security scheme.  The security scheme "mybasic" defined has been referred to from the resource "/pet/{petId}: by using the security section. The square brackets denote the scopes used. It is empty in this case because the basic authentication security scheme does not use scopes for authorization. For more information see [swagger docs - Basic Authentication](https://swagger.io/docs/specification/authentication/basic-authentication/) .

    !!! note
    The following example shows how to define Basic authentication security schemes in [OAS3](https://swagger.io/docs/specification/authentication/basic-authentication/) . If you are using an OAS2 API definition, please refer to [this](https://swagger.io/docs/specification/2-0/authentication/api-keys/) [swagger document](https://swagger.io/docs/specification/2-0/authentication/basic-authentication/) on defining Basic authentication in OAS2.

``` yml
    "/pet/{petId}":
    get:
    security:
    - mybasic: []
```

    ###### Validating scopes through basic authentication

    Scopes in security schemes (typically oauth schemes) provide the ability to restrict the usage of certain endpoints to the users by limiting the access of that endpoint to the users with the corresponding scopes. In micro-gateway it is possible to validate the users (basic authentication) with scopes.

    First in the resource level or the API level, basic authentication security should be declared alongside oauth2 security with scopes.

    !!! note
    The following example shows how to define Basic authentication security schemes in [OAS3](https://swagger.io/docs/specification/authentication/basic-authentication/) . If you are using an OAS2 API definition, please refer to [this](https://swagger.io/docs/specification/2-0/authentication/api-keys/) [swagger document](https://swagger.io/docs/specification/2-0/authentication/basic-authentication/) on defining Basic authentication in OAS2.

``` yml
    paths:
    "/pet/{petId}":
    get:
    security:
    - mybasic: []
    - OAuth2:
    - read
    - write

    components:
    securitySchemes:
    mybasic:
    type: http
    scheme: basic
    OAuth2:
    type: oauth2
    flows:
    authorizationCode:
    authorizationUrl: https://example.com/oauth/authorize
    tokenUrl: https://example.com/oauth/token
    scopes:
    read: Grants read access
    write: Grants write access
    admin: Grants access to admin operations
```

    Then, the required scopes should be added to the "micro-gw.conf" file (comma separated) under the users which are provided access to certain endpoints.

    **Format**

``` java
    [b7a.users.<username>]
    password="@<HASH_ALGO>:{<HASH_ALGO(password)>}"
    scopes="scope1,scope2"
```

    **Example**

``` java
    ["b7a.users.shani"]
    password="@sha256:{5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8}"
    scopes="read,write"
```

    The users with the scopes in the configuration can access the endpoints with certain scopes included in the oauth2 scheme using basic authentication.

    ###### Invoking an API using Basic Authentication

    Use the cURL command below to invoke the API via the microgateway.

    **Format**

``` java
    curl -k -X GET "<API_URL>" -H  "accept: application/json" -H  "Authorization: Basic base64(username:password)"
```

    **Example**

``` erl
    curl -k -X GET "https://localhost:9095/petstore/v1/pet/3" -H  "accept: application/json" -H  "Authorization: Basic YWRtaW46YWRtaW4="
```


