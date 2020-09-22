# micro-gw.conf

The following table explains the runtime configurations that are available in WSO2 API Mircogateway. The configuration file ( `         micro-gw.conf        ` ) for the Microgateway runtime is located in the `         <MGW-RUNTIME-HOME>/conf        ` directory. However, you can refer `         default-micro-gw.conf.template        ` located in `         <MGW-RUNTIME-HOME>/conf        ` on default configuration values in the Microgateway runtime. If you want to change any default configurations, you have to edit configurations in `         micro-gw.conf        ` after copying these configurations to `         micro-gw.conf.        `

!!! note
    `         The default-micro-gw.conf.template        ` is a read-only file. The changes you made in `         default-micro-gw.conf.template        ` will not be considered by the Microgateway. You have to do configurations instead in `         micro-gw.conf        ` .

|Heading|Description|Sub Heading|Description|Default value|
|-------|-----------|-----------|-----------|-------------|
|`listenerConfig`|The transport listener of the Microgateway (MGW) that receives the incoming requests.|`host`|Host or IP of the Microgateway that is exposed to the outside.|0.0.0.0|
|||`httpPort`|The port used for HTTP connections|9090|
|||`httpsPort`|The port used for HTTPS connections|9095|
|||`keyStore.path`|Internal Key Store path of the Microgateway|${ballerina.home}/bre/security/ballerinaKeystore.p12|
|||`keyStore.password`|Internal Key Store password|ballerina|
|||`tokenListenerPort`|The port where the endpoints (e.g., `/token` , `/authorize` etc.) are exposed.|9096|
|||`trustStore.path`|When validating the JWT token, the path of the client trust store where the Microgateway looks for the public certificate of the STS.|${ballerina.home}/bre/security/ballerinaTruststore.p12|
|||`trustStore.password`|The password of the client trust store.|ballerina|
|`authConfig`|The authorization details that the Microgateway uses when enforcing security to the APIs that are exposed by it.|`authorizationHeader`|The header Microgateway will look into this header to retrieve security-related details (e.g., If the API is protected by OAuth or JWT it looks for OAuth or JWT token in the specified header.|Authorization|
|||`removeAuthHeaderFromOutMessage`|This specifies whether to send the above-mentioned authorization header to the actual back end or not.|true|
|`keyManager`|The Key Manager related information. This information is required when the Microgateway connects with the Key Manager in order to validate the tokens. (for configurations, please check [the documentation on Securing APIs Using Opaque Tokens](/how-tos/security/api-authentication/secure-apis-using-oauth2.0-access-tokens/secure-apis-using-opaque-tokens/) )|`serverUrl`|Connection URL of the Key Manager server. By default, this is WSO2 Identity Server (WSO2 IS).|<https://localhost:9443>|
|||`tokenContext`|The token endpoint context of the Key Manager server.|oauth2|
|||`timestampSkew`|The timestamp skew that is added when checking the token validity period for the tokens that are retrieved from the gateway cache. Value is in seconds.|5000|
|||`external`|(Deprecated)To set an external key manager, enable this.|false|
|||`enableLegacyKM`|Enable this when Microgateway is configured with older versions of API Manager (3.1.0 or below) to use the key validation service.|false|
|||`jwksURL`|The endpoint of the JWT key set url of the key manager server.|`empty`|
|`jwtTokenConfig`|These details are used by the Microgateway when it validates the JWT present in the request. The Microgateway does the JWT validation itself. It does the signature verification and the validation of the issuer, audience, and validity period as well.|`issuer`|The Secure Token Service (STS) that has issued the JWT. If the issuer claim has JWT present in it and the request matches the given value here, then the issuer validation will be successful.|<https://localhost:9443/oauth2/token>|
|||`audience`|The audience claim present in the JWT is matched against the value provided in the configuration.|<http://org.wso2.apimgt/gateway>|
|||`certificateAlias`|The public certificate alias of the STS.|wso2apim310|
|||`validateSubscription`|Validate subscribed APIs|false|
|`jwtConfig`|Details related to the JWT that the Microgateway sends to the backend.|`header`|The header that the Microgateway uses to include the JWT when forwarding the request to the backend.|X-JWT-Assertion|
|`caching`|The caching config used for OAuth2 token validation. Note that this cache is only for OAuth2 tokens.|`tokenCache.expiryTime`|Expiry time of the cache in seconds.|900000|
|||`tokenCache.capacity`|The size of the cache in MB.|100|
|||`tokenCache.evictionFactor`|The factor of the cache that will be cleared when the cache is full. By default 0.25 @5MB of cache will be cleared when the cache is full (i.e. 100 MB).|0.25|
|`analytics`|Defines the analytics data publishing related configuration|`streamVersion`|The analytics stream version to which the events should be published. This should match with the API-M analytics version which is being used.|3.2.0|
|`analytics.fileUpload`|Configurations for file upload analytics|`enable`|This defines whether publishing from the Microgateway to analytics is enabled or not.|false|
|||`uploadingTimeSpanInMillis`|The time interval in which the uploading task is run.|600000|
|||`uploadingEndpoint`|The endpoint URL of the web application, to which the file has to be uploaded. This web app is deployed in the Analytics server to retrieve files containing analytics data.|<https://localhost:9444/analytics/v1.0/usage/upload-file>|
|||`rotatingPeriod`|The time interval, after which the file is rotated and compressed. This depends on the Transactions Per Second (TPS) capacity of the environment.|600000|
|||`task.uploadFiles`|This determines whether to enable or disable the file upload task. If this property is disabled, the analytics files are not uploaded to the analytics server, although the files are persisted in the Microgateway system.|true|
|||`username`|The username that the analytics server uses.|admin|
|||`password`|The password that the analytics server uses.|admin|
|`analytics.gRPCAnalytics`|Analytics configurations for GRPC|`enable`|Enable GRPC Analytics|false|
|||`endpointURL`|The APIM Analytics endpoint configured to accept gRPC analytics.|<https://localhost:9806>|
|||`reconnectTimeInMillies`|The time interval in milliseconds for gRPC connection recovery task|6000|
|`http2`|Http configurations|`enable`|Enable HTTP2|true|
|`httpClients`|HTTP client configuration|`verifyHostname`|Hostname verification|true|
|`validationConfig`|Request and response validation configurations|`enableRequestValidation`|Enable validating the request|false|
|||`enableResponseValidation`|Enable validating the response|false|
|`apim.eventHub`|Configurations for retrieving API and subscription data from API Manager. (This works from API Manager 3.2.0 onwards)|`enable`|Enable the data retrieval from API Manager|false|
|||`serviceUrl`|The API Manager url|`https://localhost:9443`|
|||`internalDataContext`|The context of the API which is used to retrieve the data during the Microgateway startup|`/internal/data/v1/`|
|||`eventListeningEndpoints`|The message broker endpoint which is used to retrieve the data from subsequent API, Application and Subscription creations.|`amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672`|
|`security`|The opaque token validation configuration|`validateSubscriptions`|Validate subscriptions when using opaque tokens. (valid only when the `event hub` configuration is enabled and using API Manager 3.2.0)|`false`|

### Template configuration file

The following are the default configurations and default template of configurations in Microgateway runtime. You can also find this file in `         <MGW-RUNTIME-HOME>/conf/default-micro-gw.conf.template.        `

``` yml
# Transport listener Configurations
[listenerConfig]
  # Microgateway exposed IP / Host
  host = "0.0.0.0"
  # HTTP port that is used to make APIs available to the outside.
  httpPort = 9090
  # HTTPs port that is used to make APIs available to the outside and for endpoints(/token, /authorize, /revoke/, userinfo) of Key Manager.
  httpsPort = 9095
  # HTTP port for endpoints(/token, /authorize, /revoke/, userinfo) of Key Manager
  tokenListenerPort = 9096
  # Internal keystore
  keyStorePath = "${mgw-runtime.home}/runtime/bre/security/ballerinaKeystore.p12"
  keyStorePassword = "ballerina"
  # Truststore
  trustStorePath = "${mgw-runtime.home}/runtime/bre/security/ballerinaTruststore.p12"
  trustStorePassword = "ballerina"
  # http1 Settings
  # Can be set to either `AUTO`, which respects the `connection` header, or `ALWAYS`,
  # which always keeps the connection alive, or `NEVER`, which always closes the connection
  keepAlive = "AUTO"
  # Defines the maximum number of requests that can be processed at a given time on a single connection.
  # By default 10 requests can be pipelined on a single connection and user can change this limit appropriately.
  maxPipelinedRequests = 10
  # Maximum allowed length for a URI. Exceeding this limit will result in a `414 - URI Too Long` response.
  maxUriLength = 4096
  # Maximum allowed size for headers. Exceeding this limit will result in a `413 - Payload Too Large` response.
  maxHeaderSize = 8192
  # Maximum allowed size for the entity body. By default it is set to -1 which means there is no
  # restriction `maxEntityBodySize`, On the Exceeding this limit will result in a`413 - Payload Too Large` response.
  maxEntityBodySize = -1


# API Authorization security for the gateway and the backend
[authConfig]
  # Authorization header expected by the Microgateway. Can be overridden at API level using the extension
  authorizationHeader = "Authorization"
  # Remove authorization header from the backend request
  removeAuthHeaderFromOutMessage = true

# API JWT Authorization security for backend
[jwtConfig]
  # JWT header when forwarding the request to the backend
  header = "X-JWT-Assertion"

# Key manager configurations
[keyManager]
  # Connection URL of the Key Manager server
  serverUrl = "https://localhost:9443"
  # The token endpoint context of the Key Manager server
  tokenContext = "oauth2"
  # When Microgateway is used with older APIM versions for subscription validation by using KeyValidation service.
  enableLegacyMode = false
  # Remote User Claim Retrieval Enabled
  remoteUserClaimRetrievalEnabled = false
  # Basic security configurations
  # Issuer
  issuer = "https://localhost:9443/oauth2/token"
  [keymanager.security.basic]
    enabled = true
    username = "admin"
    password = "admin"
  # Oauth2 security configurations
  [keymanager.security.oauth2]
    enabled = false
    # Authentication credentials should be sent via (AUTH_HEADER_BEARER/POST_BODY_BEARER/NO_BEARER)?
    credentialBearer = "AUTH_HEADER_BEARER"
    # Token URL for the authorization endpoint
    tokenUrl = ""
    # Oauth2 security grants
    [keymanager.security.oauth2.clientCredential]
      enabled = false
      clientId = ""
      clientSecret = ""
      scopes = ""
    [keymanager.security.oauth2.password]
      enabled = false
      clientId = ""
      clientSecret = ""
      scopes = ""
      username = ""
      password = ""
    [keymanager.security.oauth2.directToken]
      enabled = false
      accessToken = ""
    [keymanager.security.oauth2.refresh]
      enabled = false
      refreshUrl = ""
      scopes = ""
      refreshToken = ""
      clientId = ""
      clientSecret = ""

# JWT token authorization configurations. You can provide multiple JWT issuers
# Issuer 1
[[jwtTokenConfig]]
  issuer = "https://localhost:9443/oauth2/token"
  certificateAlias = "wso2apim310"
  URL of the JWKs endpoint
  jwksURL = ""
  # Validate subscribed APIs
  validateSubscription = false
  # The claim in which the consumer key of the application is coming
  consumerKeyClaim = "aud"
  #class name of JWT claims value mapper transformer if you need claims mapping.
  claimMapperClassName = "org.wso2.micro.gateway.jwtTransformer.DefaultJwtTransformer"
  # Remote User Claim Retrieval Enabled
  remoteUserClaimRetrievalEnabled = false
  # The key of remote claims and the local claims if you need claims mapping.
  [[jwtTokenConfig.claims]]
    remoteClaim = "scp"
    localClaim = "scope"
  [[jwtTokenConfig.claims]]
    remoteClaim = "FName"
    localClaim = "firstName"
# Issuer 2
[[jwtTokenConfig]]
  issuer = "https://host:port/issuer"
  audience = "http://org.wso2.apimgt/gateway"
  certificateAlias = "alias"
  # Validate subscribed APIs
  validateSubscription = false
  #class name of JWT claims value mapper transformer if you need claims mapping.
  claimMapperClassName = "org.wso2.micro.gateway.jwtTransformer.customJwtTransformer"
  # Remote User Claim Retrieval Enabled
  remoteUserClaimRetrievalEnabled = false
  # The key of remote claims and the local claims if you need claims mapping.
  [[jwtTokenConfig.claims]]
    remoteClaim = "scps"
    localClaim = "scope"
  [[jwtTokenConfig.claims]]
    remoteClaim = "FN"
    localClaim = "firstName"

# JWT token revocation configurations
[tokenRevocationConfig]
# Real time revocation configurations
  [tokenRevocationConfig.realtime]
    enableRealtimeMessageRetrieval = false
    # The JMS Message Broker that identify messages related to revoked tokens
    jmsConnectionTopic = "tokenRevocation"
    # The message broker context factory
    jmsConnectioninitialContextFactory = "wso2mbInitialContextFactory"
    # The message broker connection URL
    jmsConnectionProviderUrl= "amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"
    # The username used to establish  the message broker connection
    jmsConnectionUsername = ""
    # The password used to establish  the message broker connection
    jmsConnectionPassword = ""
  # Persistent revocation configurations
  [tokenRevocationConfig.persistent]
    enablePersistentStorageRetrieval = false
    # Use APIM as the default persistent storage. Other types are "etcd" and "custom"
    type = "default"
    # The endpoint url of your persistent storage server (e.g.: <etcd-server-access-URL>/<service>/keys/jti/)
    endpointURL = "https://localhost:9443/internal/data/v1"
    # The username of your persistent storage server
    username = "admin"
    # The password of your persistent storage server
    password = "admin"

# token cache configurations
[caching]
  # Expiry time of the cache in seconds
  tokenCacheExpiryTime = 900000
  # The size of the cache in MB
  tokenCacheCapacity = 10000
  # The factor of the cache that will be cleared when the cache is full.
  tokenCacheEvictionFactor = 0.25

# Analytics configurations
[analytics]
  # The configured API Manager analytics stream version
  streamVersion = "3.2.0"
  # Configurations for file upload analytics
  [analytics.fileUpload]
    enable = false
    # Time interval in milliseconds for file uploading task 
    uploadingTimeSpanInMillis = 600000
    # Initial time delay in milliseconds for file upload analytics
    initialDelayInMillis = 5000
    # Endpoint configured to accept file upload analytics
    uploadingEndpoint = "https://localhost:9444/analytics/v1.0/usage/upload-file"
    # File rotating period in milliseconds
    rotatingPeriod = 600000
    # To enable file upload task
    taskUploadFiles = true
    # Username used in analytics server
    username = "admin"
    # Password used in in analytics server
    password = "admin"
  [analytics.gRPCAnalytics]
    enable = false
    # APIM Analytics endpoint configured to accept gRPC analytics
    endpointURL = "https://localhost:9806"
    # Time interval in milliseconds for gRPC connection recovery task
    reconnectTimeInMillies = 6000

# User configuration for Basic auth
[b7a.users]
  # [b7a.users.<username>]
  #   password = <sha1 encrypted password> / <prefix>:<encrypted password>
  #   scopes = <comma_separated_scopes> if scopes are needed
  # Example 1:
  [b7a.users.admin]
    # password should be sha1 encrypted by default
    password = "d033e22ae348aeb5660fc2140aec35850c4da997"
  # Example 2:
  [b7a.users.prometheus]
    # Password. @prefix used to denote that password is hashed with the algorithm (e.g.: @sha256, @sha512)
    password = "@sha256:{5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8}"
    # scopes (e.g. "observability" to enable observability)
    scopes = "observability"

# Request and response validation configurations
[validationConfig]
  enableRequestValidation = false
  enableResponseValidation = false

# Enable http2 for the microgateway listeners.
[http2]
  enable = true

# HTTP client configuration. These are used when gateway connects with the upstream backend endpoints
[httpClients]
  # Hostname verification
  verifyHostname=true
  # Skip validating certificates when ssl is used
  disableSslVerification = false
  # Enable http2 when connecting with upstream backend endpoints.
  enableHttp2 = true
  #Proxy configurations required when microgateway connects to an upstream back end via a corporate proxy.
  [httpClients.proxy]
    #Enable proxy when connecting with backend services.
    enable = false
    #Enable proxy when microgateway communicating with internal components like key manager, analytics and etc
    enableInternalServices = false
    # Host or IP address of the proxy server
    host = ""
    # Port on which proxy server is listening
    port = 0
    # Security credentials if the proxy server is protected.
    username = ""
    password = ""
  # Configurations for managing HTTP client(sender) connection pool
  [httpClients.poolConfig]
    #Max active connections per route(host:port). Default value is -1 which indicates unlimited
    maxActiveConnections = -1
    #Maximum number of idle connections allowed for the pool
    maxIdleConnections = 100
    #Maximum amount of time, the client should wait for an idle connection before it sends an error when the pool is exhausted
    waitTimeInMillis = 30000
    #Maximum active streams per connection. This only applies to HTTP/2.
    maxActiveStreamsPerConnection = 50

# Mutual SSL configuration
[mutualSSLConfig]
  # SSL Protocol to be used
  protocolName = "TLS"
  # SSL/TLS protocols to be enabled
  protocolVersions = "TLSv1.2,TLSv1.1"
  # List of ciphers to be used
  ciphers="TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256, TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA256, TLS_ECDH_RSA_WITH_AES_128_CBC_SHA256,TLS_DHE_RSA_WITH_AES_128_CBC_SHA256,TLS_DHE_DSS_WITH_AES_128_CBC_SHA256, TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA, TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA,TLS_ECDH_RSA_WITH_AES_128_CBC_SHA,TLS_DHE_RSA_WITH_AES_128_CBC_SHA, TLS_DHE_DSS_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256  ,TLS_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDH_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDH_RSA_WITH_AES_128_GCM_SHA256, TLS_DHE_RSA_WITH_AES_128_GCM_SHA256,TLS_DHE_RSA_WITH_AES_128_GCM_SHA256,TLS_DHE_DSS_WITH_AES_128_GCM_SHA256  ,TLS_ECDHE_ECDSA_WITH_3DES_EDE_CBC_SHA,TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA,SSL_RSA_WITH_3DES_EDE_CBC_SHA, TLS_ECDH_ECDSA_WITH_3DES_EDE_CBC_SHA,TLS_ECDH_RSA_WITH_3DES_EDE_CBC_SHA,SSL_DHE_RSA_WITH_3DES_EDE_CBC_SHA, SSL_DHE_DSS_WITH_3DES_EDE_CBC_SHA,TLS_EMPTY_RENEGOTIATION_INFO_SCSV"
  # The type of client certificate verification. (e.g.: "require" or "optional")
  sslVerifyClient = "optional"
  # Header name append by the load balancer including the encoded certificate of client after mtls handshake with client.
  certificateHeaderName = "X-WSO2-CLIENT-CERTIFICATE"
  # Is client certificate validation enable. This applies only if the certificate is present in the header. That is
  # in load balancer fronted scenarios. If there is no load balancer in front of gateway, then if mutual ssl is enabled
  # for the API, then irrespective of this config value, certificate will be validated.
  isClientCertificateValidationEnabled = true
    # [[mutualSSLConfig.api.certificates]]
      # name = "API name given in the API Definition"
      # version = "API version given in the API definition"
      # aliasList = "list of aliases for a given API"
    # Examples :
    # API 1
    [[mutualSSLConfig.api.certificates]]
      name = "Swagger Petstore"
      version = "1.0.5"
      aliasList = ["ballerina", "wso2apim310"]
    # API 2
    [[mutualSSLConfig.api.certificates]]
      name = "MyAPI"
      version = "2.0.0"
      aliasList = ["myAlias1", "myAlias2"]


# Throttling configurations
[throttlingConfig]
  # Connect with the central traffic manager
  enabledGlobalTMEventPublishing = false
  # Enable global advanced throttling based on request header conditions
  enableHeaderConditions = false
  # Enable global advanced throttling based on request query parameter conditions
  enableQueryParamConditions = false
  # Enable global advanced throttling based on jwt claim conditions
  enableJwtClaimConditions = false
  # The message broker context factory
  jmsConnectioninitialContextFactory = "wso2mbInitialContextFactory"
  # The message broker connection URL
  jmsConnectionProviderUrl = "amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"
  # The username used to establish  the message broker connection
  jmsConnectionUsername = ""
  # The password used to establish  the message broker connection
  jmsConnectionPassword = ""
  # The central traffic management solution URL
  throttleEndpointUrl = "https://localhost:9443/endpoints"
  # username:password to create the connection to the central traffic manager
  throttleEndpointbase64Header = "admin:admin"
  #Configurations related to retrieve custom throttle policy related key templates from traffic manager or key manager.
  [throttlingConfig.dataRetriever]
    serverUrl = "https://localhost:9443/internal/data/v1"
    username = "admin"
    password = "admin"
  # Configurations related to node local throttling.
  [throttlingConfig.nodeLocal]
    # Core number of threads in the thread pool.
    processThreadPoolCoreSize = 200
    # Maximum number of threads in the thread pool.
    processThreadPoolMaximumSize = 1000
    # Keep alive time of the threads in seconds
    processThreadPoolKeepAliveTime = 200
    # Throttle data cleanup task frequency in seconds.
    cleanUpFrequency = 3600


  # Throttling configurations related to event publishing using a binary connection
  [throttlingConfig.binary]
    enabled = true
    # Credentials required to establish connection between Traffic Manager
    username = "admin"
    password = "admin"
    # Receiver URL and the authentication URL of the Traffic manager node/nodes
    [[throttlingConfig.binary.URLGroup]]
      receiverURL = "tcp://localhost:9611"
      authURL = "ssl://localhost:9711"
    # Data publisher object pool configurations
    [throttlingConfig.binary.publisherPool]
      maxIdle = 1000
      initIdleCapacity = 200
    # Data publisher thread pool configurations
    [throttlingConfig.binary.publisherThreadPool]
      corePoolSize = 200
      maxPoolSize = 1000
      keepAliveTime = 200
    [throttlingConfig.binary.agent]
      # SSL Protocols
      sslEnabledProtocols = "TLSv1,TLSv1.1,TLSv1.2"
      # ciphers
      ciphers="TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256, TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA256, TLS_ECDH_RSA_WITH_AES_128_CBC_SHA256,TLS_DHE_RSA_WITH_AES_128_CBC_SHA256,TLS_DHE_DSS_WITH_AES_128_CBC_SHA256, TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA, TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA,TLS_ECDH_RSA_WITH_AES_128_CBC_SHA,TLS_DHE_RSA_WITH_AES_128_CBC_SHA, TLS_DHE_DSS_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256  ,TLS_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDH_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDH_RSA_WITH_AES_128_GCM_SHA256, TLS_DHE_RSA_WITH_AES_128_GCM_SHA256,TLS_DHE_RSA_WITH_AES_128_GCM_SHA256,TLS_DHE_DSS_WITH_AES_128_GCM_SHA256  ,TLS_ECDHE_ECDSA_WITH_3DES_EDE_CBC_SHA,TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA,SSL_RSA_WITH_3DES_EDE_CBC_SHA, TLS_ECDH_ECDSA_WITH_3DES_EDE_CBC_SHA,TLS_ECDH_RSA_WITH_3DES_EDE_CBC_SHA,SSL_DHE_RSA_WITH_3DES_EDE_CBC_SHA, SSL_DHE_DSS_WITH_3DES_EDE_CBC_SHA,TLS_EMPTY_RENEGOTIATION_INFO_SCSV"
      # The size of the queue event disruptor which handles events before they are published.
      # The value specified should always be the result of an exponent with 2 as the base.
      queueSize = 32768
      # The maximum number of events in a batch sent to the queue event disruptor at a given time
      batchSize = 200
      # The number of threads that will be reserved to handle events at the time you start
      corePoolSize = 1
      # Socket timeout
      socketTimeoutMS = 30000
      # The maximum number of threads that should be reserved at any given time to handle events
      maxPoolSize = 1
      # The amount of time which threads in excess of the core pool size may remain idle before being terminated.
      keepAliveTimeInPool = 20
      # The time interval between reconnection
      reconnectionInterval = 30
      # TCP connection pool configurations (for data publishing)
      maxTransportPoolSize = 250
      maxIdleConnections = 250
      evictionTimePeriod = 5500
      minIdleTimeInPool = 5000
      # SSL connection pool configurations (for authentication)
      secureMaxIdleTransportPoolSize = 250
      secureMaxIdleConnections = 250
      secureEvictionTimePeriod = 5500
      secureMinIdleTimeInPool = 5000

# Observability configurations
[b7a.observability]
  # Observability metrics monitoring
  [b7a.observability.metrics]
    enabled = false
    # Reporter name that reports the collected Metrics to the remote metrics server
    reporter = "prometheus"
    # Prometheus to scrape the information
    [b7a.observability.metrics.prometheus]
      # port exposing api level metrics
      port=9797
      # port exposing vm level metrics
      jmx_port = 8080
      # secure port for api level and vm level metrics
      secure_port = 9000
  # Observability distributed tracing
  [b7a.observability.tracing]
    enabled = false
    # Tracer name
    name = "jaeger"
    # Jaeger for tracing
    [b7a.observability.tracing.jaeger.reporter]
      # The port which the Jaeger server is listening to
      port = 5775

# API Key authentication configurations
[apikey.tokenConfigs]
  issuer="https://localhost:9095/apikey"
  certificateAlias="ballerina"
  # Validate Allowed/subscribed APIs
  validateAllowedAPIs=false

# API Key Security Token Service(STS) configurations
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

# JWT Generator configurations
[jwtGeneratorConfig]
  # Enable jwt generator
  jwtGeneratorEnabled=false
  # Dialect prefix that can be added to the claims
  claimDialect="http://wso2.org/claims"
  # Signature algorithm used to sign the JWT token (only SHA256withRSA and NONE is supported)
  signingAlgorithm="SHA256withRSA"
  # Certificate alias from the keystore
  certificateAlias="ballerina"
  # Private key alias from the keystore
  privateKeyAlias="ballerina"
  # JWT token expiry time - ms (valid only if the jwt generator caching mechanism is disabled)
  tokenExpiry=900000
  # Restricted claims as an array that should not be included in the backend JWT token
  # Example: restrictedClaims=["claim1","claim2","claim3"]
  restrictedClaims=[]
  # Token issuer standard claim
  issuer="wso2.org/products/am"
  # Token audience standard claim
  audience=["http://org.wso2.apimgt/gateway"]
  # JWT token generator implementation
  generatorImpl="org.wso2.micro.gateway.jwt.generator.MGWJWTGeneratorImpl"
  [jwtGeneratorConfig.claimRetrieval]
      # User claim retriever implementation. This configuration is not applied by default.
      # User needs to mention the class explicitly
      retrieverImpl=""
      [jwtGeneratorConfig.claimRetrieval.configuration]
          custom_config_key = "custom_config_property"
  # JWT Generator cache configurations
  [jwtGeneratorConfig.jwtGeneratorCaching]
    # Enable jwt generator token caching
    tokenCacheEnable=true
    # Token cache expiry time (ms)
    tokenCacheExpiryTime=900000
    # Token cache capacity
    tokenCacheCapacity=10000
    # Token cache eviction factor
    tokenCacheEvictionFactor=0.25

# server configuration
[server]
  # timestamp skew in milliseconds which added when checking the token validity period
  timestampSkew = 5000

# Configurations for retrieving API and subscription data from API Manager.
[apim.eventHub]
  # Enable/ Disable the feature
  enable = false
  # The API Manager URL
  serviceUrl = "https://localhost:9443"
  # The internal data REST API context.
  internalDataContext="/internal/data/v1/"
  # User name and password of the internal data api.
  username="admin"
  password="admin"
  # The message broker connection URL.
  eventListeningEndpoints = "amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"

# API Manager default credentials
[apim.credentials]
username = "admin"
password = "admin"

# Token validation configuration
[security]
  # Enable/ Disable subscription validation for opaque (reference) tokens.
  validateSubscriptions = false

```
