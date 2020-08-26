#Endpoint Pool Configurations
When microgateway connect with an upstream backend service it uses a default global pool configurations 
for all the upstream backend services. But this configurations can be overridden per backend service by using the `x-wso2-production-endpoints` and `x-wso2-sandbox-endpoints` 
open API extensions.

##Global Pool Configurations
Global configurations applies for all the endpoints exposes via the microgateway. Following is the set of default configurations.
This can be modified by adding the following config section to the <MGW_RUNTIME_HOME>/conf/micro-gw.conf file and editing accordingly.
``` java
[httpClients.poolConfig]
    #Max active connections per route(host:port). Default value is -1 which indicates unlimited
    maxActiveConnections = -1
    #Maximum number of idle connections allowed for the pool
    maxIdleConnections = 100
    #Maximum amount of time, the client should wait for an idle connection before it sends an error when the pool is exhausted
    waitTimeInMillis = 30000
    #Maximum active streams per connection. This only applies to HTTP/2.
    maxActiveStreamsPerConnection = 50
```

##Per Endpoint Pool Configurations
The same pool configurations can be applied per endpoint overriding the global configuration by defining it in the `x-wso2-production-endpoints` or `x-wso2-sandbox-endpoints` 
open API extension.

``` java
  x-wso2-production-endpoints:
    urls:
      - https://localhost:2380/v3
    advanceEndpointConfig:
      poolConfig: 
        maxActiveConnections: 1000
        maxIdleConnections : 50
        waitTimeInMillis : 10000
        maxActiveStreamsPerConnection : 100

```