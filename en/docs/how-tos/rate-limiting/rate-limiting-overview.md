# Rate Limiting

The WSO2 API Microgateway by default has an inbuilt mechanism to handle rate limiting. When a Microgateway project is initialized, an execution plan, which is related to the subscription-level throttling policy defined in the API, is created in-memory. The API Microgateway monitors the incoming requests and publishes the events to a stream defined in the inbuilt stream processing engine. This stream is then read and fed into the relevant pre-defined execution plan. The execution plan detects the throttling point and publishes an event to a stream (globalThrottleStream). When the globalThrottleStream is updated, the API Microgateway updates its local counteis in background as gateway is subscribed to the global throttle stream.

The WSO2 API Microgateway supports [resource level]({{base_path}}/how-tos/rate-limiting/adding-throttling-policies/) , [subscription level](#subscription-level-throttling-api-subscriber) and [application level throttling](#application-level-throttling-application-developer) .

In case of a lock down environment or offline mode where there is no connection with a central traffic management solution, the default node-level throttling can be used.

![rate limiting-overview]({{base_path}}/assets/img/how-tos/rate-limiting-overview.jpg)

### Different levels of throttling

#### Subscription-level throttling (API subscriber)

After subscription-level throttling tiers are set and the API is published, at subscription time, the consumers of the API can sign in to the **API Store** and select the tier, out of the tiers enabled for that specific subscriber, that they are interested in as shown below:

According to the selected tiers, the subscribers are granted a maximum number of requests to the API. For more information on how tiers are set, see [Subscription-level throttling (API Publisher)](https://docs.wso2.com/display/AM260/Setting+Throttling+Limits#SettingThrottlingLimits-Subscription-levelthrottling(APIpublisher)) .

#### Application-level throttling (application developer)

Application-level throttling tiers are defined at the time an application is created in the API Store as shown below. The limits are restricted per token for a specific application.

An application is a logical collection of one or more APIs and is required to subscribe to an API. Applications allow you to use a single access token to invoke a collection of APIs and to subscribe to one API multiple times with different SLA levels.

An application is available to a consumer at different levels of service. For example, if you have infrastructure limitations in facilitating more than a certain number of requests to an application at a time, the throttling tiers can be set accordingly so that the application can have a maximum number of requests within a defined time.

For more information on application-level throttling tiers, see [Application-level Throttling tiers](https://docs.wso2.com/display/AM260/Setting+Throttling+Limits#SettingThrottlingLimits-Application-levelThrottlingtiers) .

### Defining a throttling policy

You need to define the throttling policy (or policies) in the `         policies.yaml        ` file of the project. The following is a sample on how to you can define a throttling policy "10kPerMin" in the `         policies.yaml        ` file.

**Defining a throttling policy**

``` java
    - 10kPerMin:
    count: 10000
    unitTime: 1
    timeUnit: min
```
