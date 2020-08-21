# Revoked Tokens

There are two usecase where API Microgateway needs to know when a token is revoked by the Security Token Service (STS).

1. When the API Microgateway is working with JWT formatted self-contained access tokens, it does not communicate with the STS for checking the validity of the token. It considers any token with a trusted signature as valid as long as the token is not expired. However, this model becomes a problem when the respective token is revoked by the STS. As a result, there needs to be a mechanism where the API Microgateway gets notified when a token is revoked before its expiry.
2. When the API Microgateway is authenticating requests using opaque (reference) tokens, it communicates with STS to validate the tokens. However if a token is revoked in the STS, API Microgateway doesn't get to know that token is revoked until token cache is expired. In other words, API Microgateway will allow to use revoked token until the token cache is expired. To avoid this, API Microgateway needs to get notified when a token is revoked by the STS.

WSO2 API Microgateway uses Real-time and Persistent Notifications to identify tokens that are revoked before their expiry. Real-time Notifications help you identify such revoked tokens in real-time after a the WSO2 API Microgateway server has spun up. In contrast, when using Persistent notifications, the persistent storage maintains a current list of the revoked tokens, so this will help new WSO2 API Microgateway servers that spin up to obtain information with regard to revoke tokens that were revoked previously, which still have not expired. You can configure WSO2 API Microgateway to use both Real-time and Persistent Notifications together or separately. However, WSO2 recommends that you enable both Real-time and Persistent Notifications so that WSO2 API Microgateway can have a holist view of all the tokens that have been revoked before their expiry period.

### Methods to detect JWT token revocation

- [Real-time Notifier](#real-time-notifier)
- [Persistent Notifier](#persistent-notifier)

![JWT revocation]({{base_path}}/assets/img/how-tos/jwt-revocation.png)

#### Real-time Notifier

When working with Real-time Notifications, WSO2 API Microgateway uses a Publisher- Subscriber model (pub-sub model) where the Security Token Service (STS) and WSO2 API Microgateways are linked using a Message Broker (MB). Whenever a revoke token request is received, the STS publishes a message to the JMS Message Broker. WSO2 API Microgateway has subscribed to the `tokenRevocation` topic, which is the JMS connection topic. When the JMS connection topic receives a message, the Message Broker propagates the message to the WSO2 API Microgateway servers. When the WSO2 API Microgateway servers receive this message, it will store the revoked tokens in-memory and treat them as revoked tokens. You can't extend the Real-time Notifier to add your own implementation.

#### Persistent Notifier

When using Persistent Notifications, WSO2 API Microgateway uses a persistent storage mechanism to link the Security Token Service (STS) and the WSO2 API Microgateway servers. Whenever a token revoke request is received, the STS publishes a message to the persistent storage. When a new WSO2 API Microgateway server spins up, it pulls the list of revoked tokens from the persistent storage, and stores them in the revoked jti (JWT ID) cache. The latter mentioned process only takes place once, and the state of the token is preserved. The state of the revoked token is used at restarts and when new API Microgateways join the cluster of Microgateway servers. By default, WSO2 API Microgateway can use WSO2 API Manager or [etcd](https://github.com/etcd-io/etcd) servers as its persistent storage when working with persistent notifications. However, unlike when using the real-time notifications, you can use any persistent storage and a custom implementation.

For more information on how to enable and work with this feature, check [How to revoke tokens](/how-tos/security/rejecting-revoked-tokens)
