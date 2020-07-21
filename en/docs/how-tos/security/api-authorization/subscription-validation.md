# Subscription Validation

The [subscription](https://apim.docs.wso2.com/en/3.0.0/learn/consume-api/manage-subscription/subscribe-to-an-api/) validation is configurable for JWT tokens. In order to mandate the subscriptions, subscription validation can be enabled. Microgateway will validate the list under the subscribedAPIs claim and check if the user is currently invoking one of the APIs in the list. The validation If not it will send an error message with error code 900908. 

You can enable or disable subscription validation per issuer and it is disabled by default. To authorize an API request with the JWT token under an issuer with subscription validation, the API name and version should be listed under subscribedAPIs claim of the JWT token.
``
``` yml
[[jwtTokenConfig]]
issuer = "https://localhost:9443/oauth2/token"
audience = "http://org.wso2.apimgt/gateway"
certificateAlias = "wso2apim310"
# Validate subscribed APIs
validateSubscription = false
```

!!! note
    When WSO2 API Manager is used as the key manager it sends the subscribed APIs as a list in the JWT under the subscribedAPIs claim. If an external key manager is used which will not know about the subscription details then, subscription validation can be turned off for that particular JWT issuers.



