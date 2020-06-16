# About This Release

WSO2 API Microgateway 3.1.0 (WSO2 MGW 310) is the successor of WSO2 API Microgateway version 3.0.2. For more information on WSO2 API Microgateway, see the [overview]({{base_path}}/getting-started/overview/) and visit our [webiste.](https://wso2.com/api-management/api-microgateway/)

-   [What's New](#whats-new)
-   [What's Improved](#whats-improved)
-   [What has changed in this release](#what-has-changed-in-this-release)
-   [Fixed issues](#fixed-issues)
-   [Known issues](#known-issues)

### What's New

-   **gRPC support**
    WSO2 API Microgateway can act as a proxy to the gRPC services providing security, rate limiting and anlytics. For more information, see [Add a gRPC API]({{base_path}}/how-tos/add-a-grpc-api/)
-   **Observability**
    WSO2 API Microgateway can be integrated with [prometheus](https://prometheus.io/) and [grafana](https://grafana.com/) to visualize the metrics. Moreover, it supports open tracing standards which can be integrated with Jaegar/zipkin. For more information, see [Observability]({{base_path}}/how-tos/observability/)

-   **Policy Hub for transformations**
    Policy hub allows developers to reuse and share the interceptors written for WSO2 API Microgateway.  For more information, see [How to add interceptors from Policy Hub]({{base_path}}/how-tos/message-transformation/how-to-add-interceptors-from-policy-hub/)

-   **Java Interceptor support**
    Interceptors for request and response transformations can now be writte in Java.  For more information, see [Message Transformation\#interceptors]({{base_path}}/how-tos/message-transformation/message-transformation-overview/#writing-an-interceptor) .

-   **Integrate with external OAuth2 servers**
    WSO2 API Microgateway can be configured with external key managers which implements OAuth2 and standard introspection. For more information, see [Configuring an External Key Manager]({{base_path}}/how-tos/security/api-authentication/configuring-an-external-key-manager/)

-   **Multiple JWT issuers**
    WSO2 API Microgateway supports configuring multiple JWT issuers to validate the self-contained JWTs. For more information, see [Secure APIs using JWT - Self Contained JWT]({{base_path}}/how-tos/security/api-authentication/secure-apis-using-oauth2.0-access-tokens/secure-apis-using-jwt-self-contained-jwt/)

-   **Combine transport-level and application-level security**
    WSO2 API Microgateway can now impose both mutual TLS and application-level securities like OAuth2, basic, and JWT using "AND" operator.  For more information, see [Mutual SSL Authentication]({{base_path}}/how-tos/security/api-authentication/mutual-ssl-authentication/) and [Making Application Security Optional]({{base_path}}/how-tos/security/api-authentication/making-application-security-optional/) .

-   **API Key authentication**
    APIs can be exposed using API key authentication. For more information, see [API Key Authentication]({{base_path}}/how-tos/security/api-authentication/api-key-authentication/)
-   **Issue API keys**
    WSO2 API Microgateway can issue self-contained JWTs as API keys. These tokens can be used to invoke APIs that are protected using API keys. For more information, see [API Key Security Token Service]({{base_path}}/how-tos/security/api-key-security-token-service/)

### What's Improved

-   **Expose open APIs without open API extensions**
    WSO2 API Microgateway supports exposing open API definitions by making open API  extensions x-wso2-basepath and x-wso2-production-endpoint optional. It can resolve the base path and endpoint address from open API [server and base path](https://swagger.io/docs/specification/api-host-and-base-path/)
-   **gRPC analytics data publishing**
    Analytics events can be published to the WSO2 Analytics server in realtime except for batch uploads. For more information, see [Analytics for Microgateway\#AnalyticsforMicrogateway-Real-TimePublishing(gRPCbased)]({{base_path}}/how-tos/analytics-for-microgateway/#real-time-publishing-events-grpc-based)
-   **Simplified docker image creation**
    Docker images containing the APIs can be built during the build process by providing additional command-line arguments. For more information, see [Quick Start Guide - Docker](_Quick_Start_Guide_-_Docker_)
-   **Download open API definitions to the project**
    "Init" command can be used to download open APIs hosted in a remote location. For more information, see [Quick Start Guide - Docker]({{base_path}}/getting-started/quick-start-guide/quick-start-guide-docker/)
-   **Template the context of APIs**
    API base path can be templated with version. "x-wso2-basePath" extension now can be templated with "{version}" variable to include the open API version to the base path of the API

### What has changed in this release

-   **Change of the project structure**
    The project structure created with init command has an additional directory called "grpc\_definitions" to include the custom protocol definitions of the gRPC services. For more information, see [Project Directory]({{base_path}}/reference/project-directory/) .

### Fixed issues

[WSO2 API Microgateway 3.1.0 - Fixed Issues in the product-microgateway GitHub repository.](https://github.com/wso2/product-microgateway/issues?q=is%3Aissue+is%3Aclosed+label%3AType%2FBug+project%3Awso2%2Fproduct-microgateway%2F3)

### Known issues

[WSO2 API Microgateway 3.1.0 - Known Issues in the product-microgateway GitHub repository](https://github.com/wso2/product-microgateway/issues?utf8=%E2%9C%93&q=is%3Aopen+is%3Aissue)
