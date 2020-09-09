# About This Release

WSO2 API Microgateway 3.2.0 is the successor of WSO2 API Microgateway version 3.1.0. For more information on WSO2 API Microgateway, see the [overview]({{base_path}}/getting-started/overview/) and visit our [webiste.](https://wso2.com/api-management/api-microgateway/)

-   [What's New](#whats-new)
-   [What's Improved](#whats-improved)
-   [What has changed in this release](#what-has-changed-in-this-release)
-   [Fixed issues](#fixed-issues)
-   [Known issues](#known-issues)

### What's New

-  **Circuit Breaker, Retry, and Timeout Capabilities**   
   WSO2 API Microgateway 3.2.0 is capable of applying circuit breakers to services in order to avoid any malfunctioning that can occur due to excess requests reaching a service from an overload. It also supports retries and timeouts to a service.

- **JWKS support for JWT verification**
  JWKS endpoint can now be configured for a JWT issuer. This brings in the ability to perform JWT verification using different issuers.

- **Custom Claims Mapping for JWTs**
 WSO2 API Microgateway 3.2.0 facilitates claim mapping for JWTs, particularly useful when the claims of the incoming JWT cannot be directly validated by the backend. Also, it brings in the flexibility to generate JWTs with preferred custom claims.
 
- **Support Custom Siddhi Policies and Deny Policies**
  WSO2 Microgateway 3.2.0 supports custom Siddhi based throttling policies making it easier to enforce complex customized throttling policies for an API.


### What's Improved

- **Backend JWT generation of Microgateway**
   The new WSO2 API Micorgateway allows to send the JWT in any header, in oppose to a pre-defined request header, as required.

- **Integrate with multiple Key Managers**
   WSO2 API Microgateway 3.2.0,  is able to integrate with other key managers in addition to the WSO2 API Manager Key Manager.
   
- **Support reading open API definition in interceptors**
   
- **Support reading configurations from java interceptors**

- **Mutual SSL support per API**

- **Binary publisher to support publishing to multiple TMs** 


### Fixed issues

[WSO2 API Microgateway 3.2.0 - Fixed Issues in the product-microgateway GitHub repository.](https://github.com/wso2/product-microgateway/issues?q=is%3Aissue+project%3Awso2%2Fproduct-microgateway%2F9+is%3Aclosed+label%3AType%2FBug+)

### Known issues

[WSO2 API Microgateway 3.2.0 - Known Issues in the product-microgateway GitHub repository](https://github.com/wso2/product-microgateway/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue)
