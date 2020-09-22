# About This Release

WSO2 API Microgateway 3.2.0 is the successor of WSO2 API Microgateway version 3.1.0. For more information on WSO2 API Microgateway, see the [overview](/getting-started/overview/) and visit our [webiste.](https://wso2.com/api-management/api-microgateway/)

-   [What's New](#whats-new)
-   [What's Improved](#whats-improved)
-   [Fixed issues](#fixed-issues)
-   [Known issues](#known-issues)

### What's New

-   **Circuit Breaker, Retry, and Timeout Capabilities**        
    To handle situations where the backend is not reachable due to issues like heavy load, slow response time etc, 
    the WSO2 API Microgateway can be configured with advanced endpoint configurations including circuit breakers, retries 
    and endpoint timeouts. For more information, visit 
    [Endpoint Retry and Timeouts](/how-tos/endpoints/resiliency/retry-and-timeout-configs) and 
    [Circuit Breaker](/how-tos/endpoints/resiliency/circuit-breaker/).
    
-   **Custom Claims Mapping for JWTs**      
    WSO2 API Microgateway 3.2.0 facilitates claim mapping for JWTs, particularly useful when the claims of the incoming JWT cannot be directly validated by the backend. Also, it brings in the flexibility to generate JWTs with preferred custom claims.
    More information can be found from [Custom Claims Mapping](/how-tos/support-custom-claims-mapping).
    
-   **Custom Siddhi Policies and Deny Policies with WSO2 API Manager**      
    The WSO2 API Manager allows users to define and 
    [deploy custom Siddhi-based policies](https://apim.docs.wso2.com/en/3.2.0/learn/rate-limiting/advanced-topics/custom-throttling/) 
    and do the rate-limiting based on those queries. The API Manager has ability to block the requests when 
    [Deny Policies](https://apim.docs.wso2.com/en/3.2.0/learn/rate-limiting/access-control/#denying-requests) are enabled.
    The WSO2 API Microgateway also has ability to enforce these policies when it is integrated with WS02 API Manager. 
    <!---TODO: add the documentation links for custom throttling and deny policies--->

-   **JWKS Support for JWT Verification**   
    The WSO2 API Microgateway can be configured such that JWT Token validation happens via connecting to
    JSON Web Key Set endpoint.  
    For more information, visit [Secure APIs using JWT - Self Contained JWT](/how-tos/security/api-authentication/secure-apis-using-oauth2.0-access-tokens/secure-apis-using-jwt-self-contained-jwt/)

### What's Improved

-   **Backend JWT Generation**      
    The WSO2 API Microgateway has ability to generate the backend JWT Token. In addition, the JWT generation logic can be 
    customized based on user preference as well. For more information, visit 
    [Generating a JWT in Microgateway](/how-tos/passing-enduser-attributes-to-the-backend-using-jwt/).
    
-   **Publish Events to Multiple Traffic Managers**     
    The WSO2 API Microgateway can publish events to multiple Traffic Managers to preserve high availability. 
    For more information, see [Distributed Throttling](/how-tos/rate-limiting/distributed-throttling/).
    
-   **Improved Subscription Validation**    
    The WSO2 API Microgateway supports real time [subscription blocking](https://apim.docs.wso2.com/en/3.2.0/learn/design-api/advanced-topics/block-subscription-to-an-api/#block-subscription-to-an-api) 
    feature, when it is integrated with WSO2 API Manager. 
    And the subscriptions, subscription tiers can be changed in the real time as well.
    <!---TODO: Link event hub documentation--->
    
-   **Multiple Extension Filters**      
    The WSO2 API Microgateway has ability to engage multiple Extension Filters and define their positioning in 
    the Filter chain. For more information, visit [Add Custom Filters](/how-tos/extensions/custom-filters). 
    
-   **Improved Rate Limiting with WSO2 API MANAGER**    
    In WSO2 API Manager, the users can define throttle policies which perform 
    [rate limiting based on various conditions](https://apim.docs.wso2.com/en/3.2.0/learn/rate-limiting/access-control/#creating-the-advanced-rate-limit-policy)
    including headers, JWT claims, query params and IP address. The same policies can be enforced within 
    WSO2 API Microgateway when it is integrated with WSO2 API Manager.   
    <!---TODO: add documentation and point from here--->

-   **Integrate with Third Party Key Managers**     
    WSO2 API Microgateway 3.2.0,  is able to integrate with other key manager including the WSO2 API Manager Key Manager.
    
-   **JWT Revocation with WSO2 API Manager**    
    WSO2 API Microgateway has ability to persist revoked access tokens inside WSO2 API Manager (Traffic Manager). 
    For more information, visit [Rejecting Revoked tokens](/how-tos/security/rejecting-revoked-tokens/).
    
    
-   [More in 3.2.0 Improvements](https://github.com/wso2/product-microgateway/issues?q=is%3Aissue+project%3Awso2%2Fproduct-microgateway%2F9+is%3Aclosed+label%3AType%2FImprovement)

### Fixed issues

WSO2 API Microgateway 3.2.0 - [Fixed Issues](https://github.com/wso2/product-microgateway/issues?q=is%3Aissue+project%3Awso2%2Fproduct-microgateway%2F9+is%3Aclosed+label%3AType%2FBug+)

### Known issues

WSO2 API Microgateway 3.2.0 - [Known Issues](https://github.com/wso2/product-microgateway/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue)
