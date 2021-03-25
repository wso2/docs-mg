# Rate Limiting

Rate limiting allows users to limit the number of incoming requests to a microgateway. The WSO2 API Microgateway provides two rate-limiting options. They are,

-   Local rate limiting
-   Distributed rate limiting

 To rate limit requests globally, the [distributed rate limiting](https://mg.docs.wso2.com/en/latest/how-tos/rate-limiting/distributed-throttling/#distributed-throttling) option can be used.

The WSO2 API Microgateway supports [resource level]({{base_path}}/how-tos/rate-limiting/adding-throttling-policies/), [subscription level](#subscription-level-throttling-api-subscriber) and [application level throttling](#application-level-throttling-application-developer).

In a lockdown environment or in offline mode where there is no connection with the central traffic management solution, the default node-level throttling(local throttling) can be used.

#### Local rate limiting
If a user wants to rate limit requests within a microgateway, the local rate limiting option can be used. When local rate limiting is enabled, WSO2 API Microgateway maintains a local counter within the microgateway and check whether the request count exceeds the throttling limit. Find information on how to enable local rate limiting from [here](https://mg.docs.wso2.com/en/latest/how-tos/rate-limiting/adding-throttling-policies/).

#### Distributed rate limiting
In a deployment with multiple microgateways, throttling becomes a challenge with node local throttling as the throttling
decision is made based on the local counter within each node. If we proceed with the node local throttling in such
environment, the API user would be allowed to send multiples of the throttling limit.I.e. if the throttling limit is set to 10,
if we have 3 gateways in a cluster, it will allow 30 requests to pass to the backend before all three gateways
throttle out requests. This will put an unexpected load on the backend. To address this requirement, the API Microgateway
supports distributed throttling where it is able to work with a central traffic management solution. In this case,
multiple microgateways can connect with WSO2 API Manager
([WSO2 Traffic Manager]({{apim_path}}/install-and-setup/setup/distributed-deployment/product-profiles/))
and perform rate-limiting precisely. Find information on how to enable distributed rate limiting from [here](https://mg.docs.wso2.com/en/latest/how-tos/rate-limiting/distributed-throttling/#distributed-throttling).

!!! note
    If you start the WSO2 API Manager without providing any profile, it runs as All in One Node (All the profiles
    are activated). For testing purposes, you can simply start the API Manager following the
    [quick start guide]({{apim_path}}/getting-started/quick-start-guide/) and test.

### Different levels of throttling

#### Subscription-level throttling (API subscriber)

Subscription-level throttling tiers are set to an API during the API implementation. When a user subscribes to the API through the developer portal, the subscription-level throttling tiers selected for the API will be listed from which one can be selected.

Based on the selected tier, a subscriber will be throttled out upon reaching the maximum number of requests specified in the tier, see [subscription-level throttling (API Publisher)]({{apim_path}}/learn/rate-limiting/setting-throttling-limits/#subscription-level-throttling-api-publisher).

#### Application-level throttling (application developer)

Application-level throttling tiers are defined at the time an application is created in the API Developer Portal as shown [here](https://apim.docs.wso2.com/en/latest/learn/rate-limiting/setting-throttling-limits/#application-level-throttling-application-developer). The limits are restricted per token for a specific application.

An application is a logical collection of one or more APIs. An API is subscribed to an application. A single access token generated for an application can be used to invoke all the APIs subscribed to that application.

An application can be used to support environment restrictions. For e.g., if there is an infrastructure limitation to serve a maximum number of requests at a given time, a throttling tier can be set to an application to avoid the system being overloaded.

For more information on application-level throttling tiers, see [application-level Throttling tiers](https://apim.docs.wso2.com/en/latest/learn/rate-limiting/adding-new-throttling-policies/#adding-a-new-application-level-throttling-tier).
