# Subscription Validation

The [subscription](https://apim.docs.wso2.com/en/latest/learn/consume-api/manage-subscription/subscribe-to-an-api/) validation is configurable for JWT and reference tokens. In order to mandate the subscriptions, subscription validation can be enabled. Microgateway will validate the list under the subscribedAPIs claim and check if the user is currently invoking one of the APIs in the list. The validation If not it will send an error message with error code 900908.
To authorize an API request with the JWT token under an issuer with subscription validation, the API name and version should be listed under subscribedAPIs claim of the JWT token.

!!! note
    When older version of WSO2 API Manager is used as the key manager it sends the subscribed APIs as a list in the JWT under the subscribedAPIs claim. If an external key manager is used which will not know about the subscription details then, subscription validation can be turned off for that particular JWT issuers.

## Subscription Validation Model

  In Microgateway, the subscriptions are validated in the gateway it self by using a set of internal data stores.

  1. Application-key mapping
  2. Application data
  3. API data
  4. Subscription data
  
  WSO2 API Manager exposes the above data via an REST endpoint (/internal/data/v1). And also, publishes JMS events when a new Application, API or Subscription is created.

  Microgateway fetches the existing data during the startup using the REST API and put into a set of internal data maps. In the same time, subscribes to the JMS topic which is then used to fetch new events.

### Subscription Validation Steps

1. The JWT token or Opaque (reference) token is validated using introspection or JWKs
2. Get the consumer key related to the token from the jwt payload or the introspection response.
3. From the application-key mapping data store, get the application id related to the consumer key.
4. If no data is found, invoke the internal data api and fetch the data from API Manager.
5. Using the application id, get the subscription data (api ids) from the subscriptions data store.
6. Get the API data related to the subscribed apis
7. Check whether the API which is being invoked is in the subscribed APIs.
  
### Configuration
  
  This can be configured as below in {MGW_RUNTIME_HOME/conf/mgw-conf.toml} file.

```yml
# Configurations for retrieving API and subscription data from API Manager.
[apim.eventHub]
  # Enable/ Disable the feature
  enable = true
  # The API Manager URL
  serviceUrl = "https://localhost:9443"
  # The internal data REST API context.
  internalDataContext="/internal/data/v1/"
  # User name and password of the internal data api.
  username="admin"
  password="admin"
  # The message broker connection URL.
  eventListeningEndpoints = "amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"
```

!!! note
    Older versions of WSO2 API Manager (2.x series and 3.0.0, 3.1.0) does not have this capability. Therefore, when using Microgateway with those APIM versions, disable the Event Hub and set enableLegacyKM to true. This will use the older key validation service to validate the token and subscriptions for reference/ opaque tokens.

    ```yml
    # Key manager configurations
    [keyManager]
    # Connection URL of the Key Manager server
    serverUrl = "https://localhost:9443"
    # The token endpoint context of the Key Manager server
    tokenContext = "oauth2"
    # When Microgateway is used with older APIM versions for subscription validation by using KeyValidation service.
    enableLegacyMode = true
    ...
    [apim.eventHub]
    # Enable/ Disable the feature
    enable = false
    ```

### Enable Subscription Validation
You can enable or disable subscription validation using the following configuration and it is disabled by default. Add the following to the `<MGW-RUNTIME_HOME>`/conf/micro-gw.conf.

!!! note
    In order to subscription validation, the ``` [eventHub] ``` must be enabled.

#### Enable Subscription Validation for JWT tokens.
``` yaml 
  [[jwtTokenConfig]]
    issuer = "https://localhost:9443/oauth2/token"
    audience = "http://org.wso2.apimgt/gateway"
    certificateAlias = "wso2apim310"
    # Validate subscribed APIs
    validateSubscription = true
```

#### Enable Subscription Validation for Opaque/ Reference tokens.
``` yaml
  [security]
    # Enable/ Disable subscription validation for tokens.
    validateSubscriptions = true
```
