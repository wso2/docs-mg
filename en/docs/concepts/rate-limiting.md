# Rate Limiting

The WSO2 API Microgateway by default has an inbuilt mechanism to handle rate limiting. When a Microgateway project is initialized, an execution plan related to the subscription-level throttling policy defined in the API is created in-memory. The API Microgateway monitors the incoming requests and publishes the events to a stream defined in the inbuilt stream processing engine. This stream is then read and fed into the relevant pre-defined execution plan. The execution plan detects the throttling point and publishes an event to a stream (globalThrottleStream). When the globalThrottleStream is updated, the API Microgateway updates its local counters in the background as the gateway is subscribed to the global throttle stream.

The WSO2 API Microgateway supports [resource level](/how-tos/rate-limiting/adding-throttling-policies/), [subscription level](#subscription-level-throttling-api-subscriber) and [application level throttling](#application-level-throttling-application-developer).

In a lockdown environment or in offline mode where there is no connection with the central traffic management solution, the default node-level throttling can be used.

![rate limiting-overview](/assets/img/how-tos/rate-limiting-overview.jpg)

### Different levels of throttling

#### Subscription-level throttling (API subscriber)

Subscription-level throttling tiers are set to an API during the API implementation. When a user subscribes to the API through the developer portal, the subscription-level throttling tiers selected for the API will be listed from which one can be selected.

Based on the selected tier, a subscriber will be throttled out upon reaching the maximum number of requests specified in the tier, see [Subscription-level throttling (API Publisher)](https://apim.docs.wso2.com/en/latest/learn/rate-limiting/setting-throttling-limits/#subscription-level-throttling-api-publisher).

#### Application-level throttling (application developer)

Application-level throttling tiers are defined at the time an application is created in the API Developer Portal as shown below. The limits are restricted per token for a specific application.

An application is a logical collection of one or more APIs. An API is subscribed to an application. A single access token generated for an application can be used to invoke all the APIs subscribed to that application.

An application can be used to support environment restrictions. For e.g., if there is an infrastructure limitation to serve a maximum number of requests at a given time, a throttling tier can be set to an application to avoid the system being overloaded.

For more information on application-level throttling tiers, see [Application-level Throttling tiers](https://apim.docs.wso2.com/en/latest/learn/rate-limiting/setting-throttling-limits/#application-level-throttling-application-developer).

Check [How to define API/Resource level rate limiting](/how-tos/rate-limiting/adding-throttling-policies) and [How to enabled distributed rate limiting](/how-tos/rate-limiting/distributed-throttling) for more information.
