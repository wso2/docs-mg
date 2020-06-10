# JWT Token Revocation

When the API Microgateway is working with JWT formatted self-contained access tokens, it does not communicate with the Security Token Service (STS) for checking the validity of the token. It considers any token with a trusted signature as valid as long as the token is not expired. However, this model becomes a problem when the respective token is revoked by the STS. As a result, there needs to be a mechanism where the API Microgateway gets notified when a token is revoked before its expiry.

WSO2 API Microgateway uses Real-time and Persistent Notifications to identify tokens that are revoked before their expiry. Real-time Notifications help you identify such revoked tokens that are revoked in real-time after a the WSO2 API Microgateway server has spun up. In contrast, when using Persistent notifications, the persistent storage maintains a current list of the revoked tokens, so this will help new WSO2 API Microgateway servers that spin up to obtain information with regard to revoke tokens that were revoked previously, which still have not expired. You can configure WSO2 API Microgateway to use both Real-time and Persistent Notifications together or separately. However, WSO2 recommends that you enable both Real-time and Persistent Notifications so that WSO2 API Microgateway can have a holist view of all the tokens that have been revoked before their expiry period.

-   [Methods to detect JWT token revocation](#JWTTokenRevocation-MethodstodetectJWTtokenrevocation)
-   [Enabling notifications to identify revoked tokens](#JWTTokenRevocation-Enablingnotificationstoidentifyrevokedtokens)

### Methods to detect JWT token revocation

-   [Real-time Notifier](#JWTTokenRevocation-Real-timeNotifier)
-   [Persistent Notifier](#JWTTokenRevocation-PersistentNotifier)

![JWT revocation]({{base_path}}/assets/img/how-tos/jwt-revocation.png){width="900"}

#### Real-time Notifier

When working with Real-time Notifications, WSO2 API Microgateway uses a Publisher- Subscriber model (pub-sub model) where the Security Token Service (STS) and WSO2 API Microgateways are linked using a Message Broker (MB). Whenever a revoke token request is received, the STS publishes a message to the JMS Message Broker. WSO2 API Microgateway has subscribed to the `         tokenRevocation        ` topic, which is the JMS connection topic. When the JMS connection topic receives a message, the Message Broker propagates the message to the WSO2 API Microgateway servers. When the WSO2 API Microgateway servers receive this message, they will store the revoked tokens in-memory and treat them as revoked tokens. You can not extend the Real-time Notifier to add your own implementation.

#### Persistent Notifier

When using Persistent Notifications, WSO2 API Microgateway uses a persistent storage mechanism to link the Security Token Service (STS) and the WSO2 API Microgateway servers. Whenever a token revoke request is received, the STS publishes a message to the persistent storage. When a new WSO2 API Microgateway server spins up, it pulls the list of revoked tokens from the persistent storage, and stores them in the revoked jti (JWT ID) cache. The latter mentioned process only takes place once, and the state of the token is preserved. The state of the revoked token is used at restarts and when new API Microgateways join the cluster of Microgateway servers. By default, WSO2 API Microgateway uses a [etcd](https://github.com/etcd-io/etcd) server as its persistent storage when working with persistent notifications. However, unlike when using the real-time notifications, when using the persistent notifications, you can use any persistent storage and a custom implementation.

The following code snippet shows how the revoked token map object is passed and how you can add your own revoked token to the map.

``` java
    [tokenRevocationConfig.persistent]
    enablePersistentStorageRetrieval = true
    useDefault = false
    hostname = "https://127.0.0.1:2379/v2/keys/jti/"
    username = "root"
    password = "root"
```

    ### 
    Enabling notifications to identify revoked tokens

    Let's use the following sample scenario.

    -   Let's enable both Real-time Notifications and Persistent Notifications.

    -   Let's work with the default implementation, which uses etcd as the persistent storage, for Persistent Notifications.

    Follow the instructions below to configure and enable Real-time Notifications and Persistent Notifications.

    !!! info
    **Before you begin** make sure to configure your Security Token Service (STS) and start it. In addition, when working with real-time notifications, you need to define the JMS map message payload in the following format.
``` java
    "payloadData": [
    {
    "name": "revokedToken",
    "type": "STRING"
    },
    {
    "name": "ttl",
    "type": "STRING"
    }
    ]
```

1.  Navigate to the `           <MGWTK_HOME>/resources/conf/micro-gw.conf          ` file and add the following configuration.

    -   [**Real-time and Persistent Notifications**](#both-notifiers)
    -   [**Only Real-time Notifications**](#realtime-notifier)
    -   [**Only Persistent Notifications**](#realtime-persistent)

    ``` java
        [tokenRevocationConfig]
        [tokenRevocationConfig.realtime]
        enableRealtimeMessageRetrieval = true
        jmsConnectionTopic = "tokenRevocation"
        jmsConnectioninitialContextFactory = "bmbInitialContextFactory"
        jmsConnectionProviderUrl= "amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"
        jmsConnectionUsername = ""
        jmsConnectionPassword = ""
        [tokenRevocationConfig.persistent]
        enablePersistentStorageRetrieval = true
        useDefault = true
        hostname = "https://127.0.0.1:2379/v2/keys/jti/"
        username = "root"
        password = "root"
    ```

    ``` java
        [tokenRevocationConfig]
        [tokenRevocationConfig.realtime]
        enableRealtimeMessageRetrieval = true
        jmsConnectionTopic = "tokenRevocation"
        jmsConnectioninitialContextFactory = "bmbInitialContextFactory"
        jmsConnectionProviderUrl= "amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"
        jmsConnectionUsername = ""
        jmsConnectionPassword = ""
        [tokenRevocationConfig.persistent]
        enablePersistentStorageRetrieval = false
        useDefault = true
        hostname = "https://127.0.0.1:2379/v2/keys/jti/"
        username = "root"
        password = "root"
    ```

    ``` java
        [tokenRevocationConfig]
        [tokenRevocationConfig.realtime]
        enableRealtimeMessageRetrieval = false
        jmsConnectionTopic = "tokenRevocation"
        jmsConnectioninitialContextFactory = "bmbInitialContextFactory"
        jmsConnectionProviderUrl= "amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"
        jmsConnectionUsername = ""
        jmsConnectionPassword = ""
        [tokenRevocationConfig.persistent]
        enablePersistentStorageRetrieval = true
        useDefault = true
        hostname = "https://127.0.0.1:2379/v2/keys/jti/"
        username = "root"
        password = "root"
    ```

        [tokenRevocationConfig.realtime]

        Element
        Description
        enableRealtimeMessageRetrieval

        This element is disabled by default. Set this element to `               true              ` if you want to enable Real-time Notifications.
        jmsConnectionTopic

        This is the name of the topic in the JMS Message Broker that WSO2 API Microgateway listens to in order to identify messages related to revoked tokens.
        jmsConnectioninitialContextFactory
        jmsConnectionProviderUrl
        jmsConnectionUsername
        jmsConnectionPassword

        Similar to `               jmsConnectionTopic              ` these too are JMS related configuration. As you can not use a custom implementation when using Real-time Notifications, you will not need to change these configurations.
        [tokenRevocationConfig.persistent]

        Element
        Description
        enablePersistentStorageRetrieval

        Set this element to true if you want enable Persistent Notifications.
        useDefault

        This element is enabled by default, so that you can use etcd as the default persistent storage. If you are using any other persistent storage, other than etcd, you need to define your custom implementation in the `               <MGW_HOME>/distribution/resources/filters/token_revocation_extension.bal              ` file. Thereafter, you need to set this element to `               false              ` in order to enable your custom implementation.
        hostname

        The format of this element is " `               <etcd-server-access-URL>/<service>/keys/jti/              ` ". The default value is `               https://127.0.0.1:2379/v2/keys/jti/              ` based on etcd version 2. However, you can use etcd version 3 ( `               <etcd-server-access-URL>/v3/keys/jti/              ` ) or even another persistent storage of your choice.
        username

        Enter the username of your persistent storage server (e.g., etcd server). The default value for the etcd server is "root".
        password

        Enter the password of your persistent storage server (e.g., etcd server). The default value for the etcd server is "root".

        !!! note
        When working with persistent notifications, if you introduced new elements in your custom implementation, then you have to provide the corresponding values for those elements in the `           <MGWTK_HOME>/resources/conf/micro-gw.conf          ` file under the `           [tokenRevocationConfig.persistent]          ` section.

2.  Generate a JWT token.
    Use one of the following methods to generate your JWT token.

    -   **Use any third party secure token service (STS)**
        You need to add the public certificate of the token service that you used to sign the JWT token to the trust store of the WSO2 API Microgateway. The JWT should have the sub, aud, exp, and jti claims in order to be successfully validated with WSO2 API Microgateway.
    -   **Use WSO2 API Manager.
        ** [Obtain a JWT token using the WSO2 API Store](https://docs.wso2.com/display/AM260/Generating%20a%20JWT%20token%20from%20the%20API%20Store) .

3.  Extract the jti from the JWT token via the [https://jwt.io](https://jwt.io/) site.

4.  Configure the persistent storage.

    !!! note
        This is only required if you have enabled Persistent Notifications.

    Click here for instructions to configure the etcd server.

    1.  Download the [etcd distribution](https://github.com/etcd-io/etcd/releases) based on your OS and unzip it.
        In this example, let's work with [etcd v3.3.13](https://github.com/etcd-io/etcd/releases/tag/v3.3.13) .

        !!! tip
            If you are using a Mac OS, download the Darwin files.

    2.  Navigate to the unzipped etcd distribution and start the etcd server.

        ``` java
            cd <etcd_HOME>
            ./etcd
        ```

            The following is a sample message that appear.

        ``` java
            2019-05-23 19:09:18.921356 I | embed: ready to serve client requests
        ```

            !!! note
            If you are working in a production environment, start the etcd server with HTTPS enabled.
        ``` java
            ./etcd --name infra0 --data-dir infra0 --cert-file=<PATH_TO_CERT>/server.crt --key-file=<PATH_TO_CERT>/server.key --advertise-client-urls=https://localhost:2379 --listen-client-urls=https://localhost:2379
        ```

    3.  Setup the required authentication with the service as follows:

        1.  Create a user.

            -   [**Format**](#format-create-user)
            -   [**Example**](#example-create-user)
            -   [**Response**](#response-create-user)

            ``` java
                curl -L <etcd-client-URL>/<service>/user/add -X POST -d '{"name": "<username>", "password": "<password>"}'
            ```

                -   `                     <etcd-client-URL>                    ` - You can find your client URL in the terminal that you used to start the etcd server.
                -   `                     <service>                    ` - The service changes based on your etcd version. `                     /v3alpha/auth                    ` is the service that corresponds to etcd v3.3.13.

            ``` java
                curl -L http://localhost:2379/v3alpha/auth/user/add -X POST -d '{"name": "root", "password": "pass"}'
            ```

            ``` java
                {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"1","raft_term":"2"}}
            ```

                2.  Create a user role.

                -   [**Format**](#format-create-role)
                -   [**Example**](#example-create-role)
                -   [**Response**](#response-create-role)

            ``` java
                curl -L <etcd-client-URL>/<service>/role/add -X POST -d '{"name": "<user-role>"}'
            ```

                -   `                     <etcd-client-URL>                    ` - You can find your client URL in the terminal that you used to start the etcd server.
                -   `                      <service>                     ` - The service changes based on your etcd version. `                      /v3alpha/auth                     ` is the service that corresponds to etcd v3.3.13.

            ``` java
                curl -L http://localhost:2379/v3alpha/auth/role/add -X POST -d '{"name": "root"}'
            ```

            ``` java
                {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"1","raft_term":"2"}}
            ```

                3.  Assign the user to the user role.

                -   [**Format**](#format-assign-perm)
                -   [**Example**](#example-assign-perm)
                -   [**Response**](#response-assign-perm)

            ``` java
                curl -L <etcd-client-URL>/<service>/user/grant -X POST -d '{"user": "<username>", "role": "<user-role>"}'
            ```

                -   `                     <etcd-client-URL>                    ` - You can find your client URL in the terminal that you used to start the etcd server.
                -   `                     <service>                    ` - The service changes based on your etcd version. `                     /v3alpha/auth                    ` is the service that corresponds to etcd v3.3.13.

            ``` java
                curl -L http://localhost:2379/v3alpha/auth/user/grant -X POST -d '{"user": "root", "role": "root"}'
            ```

            ``` java
                {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"1","raft_term":"2"}}
            ```

                4.  Enable user authentication.

                -   [**Format**](#format-enable-auth)
                -   [**Example**](#example-enable-auth)
                -   [**Response**](#response-enable-auth)

            ``` java
                curl -L <etcd-client-URL>/<service>/enable -X POST -d '{}'
            ```

                -   `                     <etcd-client-URL>                    ` - You can find your client URL in the terminal that you used to start the etcd server.
                -   `                     <service>                    ` - The service changes based on your etcd version. `                     /v3alpha/auth                    ` is the service that corresponds to etcd v3.3.13.

            ``` java
                curl -L http://localhost:2379/v3alpha/auth/enable -X POST -d '{}'
            ```

            ``` java
                {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"1","raft_term":"2"}}
            ```

                5.  Authenticate the user with etcd and obtain an authentication token.

                -   [**Format**](#format-auth-token)
                -   [**Example**](#example-auth-token)
                -   [**Response**](#response-auth-token)

            ``` java
                curl -L <etcd-client-URL>/<service>/authenticate -X POST -d '{"name": "<username>", "password": "<password>"}'
            ```

                -   `                     <etcd-client-URL>                    ` - You can find your client URL in the terminal that you used to start the etcd server.
                -   `                     <service>                    ` - The service changes based on your etcd version. `                     /v3alpha/auth                    ` is the service that corresponds to etcd v3.3.13.

            ``` java
                curl -L http://localhost:2379/v3alpha/auth/authenticate -X POST -d '{"name": "root", "password": "pass"}'
            ```

            ``` java
                {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"1","raft_term":"2"},"token":"hjdNkYCLWVBgNBbL.9"}
            ```

                !!! info
                Troubleshooting
            ``` java
                {"error":"etcdserver: invalid auth token","code":16}
            ```
                If you get the above error, regenerate your Auth token by repeating this step.

    4.  Create a directory in the etcd server to store the revoked JWT IDs (jti).
        This directory can have any name (e.g., jti, jwt).

        -   [**Format**](#format-chk-key-value)
        -   [**Example**](#example-chk-key-value)

        ``` java
            curl --cacert <etcd-client-URL>/server.crt https://127.0.0.1:2379/<etcd-cli-version>/keys/jti -XPUT -d dir=true
        ```

            -   `                   <etcd-client-URL>                  ` - You can find your client URL in the terminal that you used to start the etcd server.
            -   `                    <etcd-cli-version>                   ` - The CLI version that corresponds to your etcd version. For example, `                    /v3alpha                   ` is the CLI version that corresponds to etcd v3.3.13.

        ``` java
            curl --cacert http://localhost:2379/server.crt https://127.0.0.1:2379/v3alpha/keys/jti -XPUT -d dir=true
            1e41eb69-b134-4158-ba16-9449ecaa2b3b
        ```

            5.  Store the key value pair in the etcd server.

            -   [**Format**](#format-store-key-value)
            -   [**Example**](#example-store-key-value)

        ``` java
            curl -L <etcd-client-URL>/<etcd-cli-version>/kv/put -X POST -d '{"key": "<jti>", "value": "<ttl>"}' -H 'Authorization : <auth-token>'
        ```

            -   `                   <etcd-client-URL>                  ` - You can find your client URL in the terminal that you used to start the etcd server.
            -   `                   <etcd-cli-version>                  ` - The CLI version that corresponds to your etcd version. For example, `                   /v3alpha                  ` is the CLI version that corresponds to etcd v3.3.13.
            -   `                    <jti>                   ` - Enter the JWT ID (jti) that corresponds to the JWT token that you obtained in [step 3](#JWTTokenRevocation-step3) .

            -   `                    <ttl>                   ` - Enter the time-to-live (TTL) respective to the revoke token in milliseconds.

            -   `                                         <auth-token>                    ` - Enter the Auth token that you obtained in [step 4-(c)-(5)](#JWTTokenRevocation-AuthToken) .

        ``` java
            curl -L http://localhost:2379/v3alpha/kv/put -X POST -d '{"key": "3ad5672a-afca-4387-9ec5-fcf28785f803", "value": "3600"}' -H 'Authorization : hjdNkYCLWVBgNBbL.9'
        ```

            !!! info
            If you want to check whether you have stored your key-value pair correctly, run the following command.
            -   [**Format**](#format-chk-key-value)
            -   [**Example**](#example-chk-key-value)
        ``` java
            curl -L <etcd-client-URL>/<etcd-cli-version>/kv/range -X POST -d '{"key": "<jti>"}' -H 'Authorization : <auth-token>'
        ```
            -   `                   <etcd-client-URL>                  ` - You can find your client URL in the terminal that you used to start the etcd server.
            -   `                   <etcd-cli-version>                  ` - The CLI version that corresponds to your etcd version. For example, `                   /v3alpha                  ` is the CLI version that corresponds to etcd v3.3.13.
            -   `                    <jti>                   ` - Enter the JWT ID (jti) that corresponds to the JWT token that you obtained in [step 3](#JWTTokenRevocation-step3) .
            -   `                                                               <auth-token>                     ` - Enter the Auth token that you obtained in [step 4-(c)-(5)](#JWTTokenRevocation-AuthToken) . `                   `
        ``` java
            curl -L <etcd-client-URL>/<etcd-cli-version>/kv/range -X POST -d '{"key": "3ad5672a-afca-4387-9ec5-fcf28785f803"}' -H 'Authorization : hjdNkYCLWVBgNBbL.9'
        ```

5.  Establish security between the persistent storage (etcd server), WSO2 API Microgateway, and the STS.

    !!! note
        This is only required if you are working in a production environment.

    1.  Create a self-signed certificate for the persistent storage.
    2.  Add the self-signed certificate to the Ballerina Trust Store ( `             ballerinaTruststore.p12            ` ) in the WSO2 API Microgateway project and to the Client Trust Store ( `             client-truststore.jks            ` ) in the STS (e.g., WSO2 API Manager).

6.  Create and publish and API.

    !!! note
        This step is only required if you do not already have an existing API.

    For this example, let's skip this step and use the [petstore open API](https://github.com/wso2/product-microgateway/blob/master/samples/petstore_basic.yaml) .

7.  Initialize an API Microgateway project (e.g., `           petstore          ` ).
    Navigate to a preferred folder where you want to create the Microgateway project. Thereafter, run the following command.

    -   [**Format**](#format-create-proj)
    -   [**Example**](#eg-create-proj)
    -   [**Sample Response**](#res-init)

    ``` java
        micro-gw init <project-name>
    ```

    ``` java
        micro-gw init petstore
    ```

    ``` java
        Project 'petstore' is initialized successfully.
    ```

        8.  Build the WSO2 API Microgateway project (e.g., petstore).

        1.  Add an API or multiple APIs to the project.
        Navigate to the `            /petstore/api_definitions           ` directory. Add the API definition(s) to this  directory. For this example, let's add the [petstore](https://github.com/wso2/product-microgateway/blob/master/samples/petstore_basic.yaml) open API definition.
        2.  Navigate to the `            <MGW_HOME>/bin           ` directory and run the following command.

        -   [**Format**](#format-build-proj)
        -   [**Example**](#eg-build-proj)
        -   [**Response**](#res-build)

    ``` java
        micro-gw build <project-name>
    ```

    ``` java
        micro-gw build petstore
    ```

    ``` java
        Compiling source
        src:0.0.0

        Running tests
        src:0.0.0
        No tests found

        Generating executable
        ./target//Users/kim/Downloads/TestProj/petstore/target/petstore.balx
    ```

        This creates an executable file ( `           /petstore/target/petstore.balx          ` ) that you can use to expose the API via WSO2 API Microgateway.

        9.  Start WSO2 API Microgateway.

        -   [**Format**](#format-store-key-value)
        -   [**Example**](#example-store-key-value)
        -   [**Response**](#response-store-key-value)

        **Format**

    ``` java
        gateway <path-to-MGW-executable-file>
    ```

        **Example**

    ``` java
        gateway /Users/kim/Downloads/TestProj/petstore/target/petstore.balx
    ```

        WSO2 API Microgateway starts for the API.

    ``` java
        [ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9096
        [ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9095
        [ballerina/http] started HTTP/WS endpoint 0.0.0.0:9090
        2019-06-14 17:23:00,540 INFO  [wso2/gateway] - HTTPS listener is active on port 9095 
        2019-06-14 17:23:00,541 INFO  [wso2/gateway] - HTTP listener is active on port 9090 
    ```

        When starting the project, if you enable the Ballerina logging, the console output will be as follows:

        Click here to see the command.

        -   [**Format**](#Bal-log)
        -   [**Output**](#Bal-log-output)

    ``` java
        gateway -e b7a.log.level=TRACE <path-to-MGW-executable-file>
    ```

    ``` java
        [ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9096
        [ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9095
        [ballerina/http] started HTTP/WS endpoint 0.0.0.0:9090
        2019-06-14 17:23:19,196 INFO  [wso2/gateway] - HTTPS listener is active on port 9095
        2019-06-14 17:23:19,196 INFO  [wso2/gateway] - HTTP listener is active on port 9090
        2019-06-14 17:23:19,197 DEBUG [src] - Throttle streams initialized.
        2019-06-14 17:23:19,200 DEBUG [src] - Throttle policies initialized.
        2019-06-14 17:23:19,613 INFO  [wso2/gateway] - [TokenRevocationJMS] [-] subscriber service for token revocation is started
        2019-06-14 17:23:19,614 INFO  [wso2/gateway] - [EtcdUtil] [-] One time ETCD revoked token retriever task initiated
        2019-06-14 17:23:19,642 DEBUG [wso2/gateway] - [TokenRevocationETCDUtil] [-] Setting authorization header for etcd requests
        2019-06-14 17:23:19,650 DEBUG [ballerina/http] - Cached response not found for: 'GET '
        2019-06-14 17:23:19,651 DEBUG [ballerina/http] - Sending new request to:
        2019-06-14 17:23:19,958 DEBUG [wso2/gateway] - [TokenRevocationETCDUtil] [-] Http Response object obtained
        2019-06-14 17:23:19,966 DEBUG [wso2/gateway] - [TokenRevocationETCDUtil] [-] etcd responded with a payload
        2019-06-14 17:23:19,967 DEBUG [wso2/gateway] - [TokenRevocationETCDUtil] [-] Revoked tokens are successfully added to cache
    ```

        10. Optionally, **test the synchronization process of token revocation** .

        1.  Send the revoke request using the extracted jti as the token.

        -   [**Format**](#format-send-req)
        -   [**Example**](#example-send-req)
        -   [**Response**](#response-send-req)

        ``` java
            curl -k -v -d "token=<jti>" -H "Authorization: Basic <base64-encoded-string>" -H "Content-Type: application/x-www-form-urlencoded" https://localhost:8243/revoke 
        ```

            -   `                  <jti>                 ` - Enter the JWT ID (jti) that corresponds to the JWT token, which you obtained in [step 3](#JWTTokenRevocation-step3) , that you want to revoke.

            -   `                  <base64-encoded-string>                 ` - Use a base64 encoder (e.g., <https://www.base64encode.org/> ) to encode your client ID and client secret using the following format: `                  <clientId>:<clientSecret>                 ` Thereafter, enter the encoded value for this parameter.

        ``` java
            curl -k -v -d "token=1e41eb69-b134-4158-ba16-9449ecaa2b3b" -H "Authorization: Basic U01OYVM0Tkg1UlJKSFJONDVoSzcxWElVbXdNYTo4ajRqTHFBUDZDNjNSTkFTVTdMZDEyeVUxbHNh" -H "Content-Type: application/x-www-form-urlencoded" https://localhost:8243/revoke 
        ```

            The following response can be seen via the WSO2 API Microgateway console.

        ``` java
            2019-06-14 17:20:45,636 DEBUG [wso2/gateway] - [TokenRevocationJMS] [-] token revoked jms Message Received
            2019-06-14 17:20:45,638 DEBUG [wso2/gateway] - [TokenRevocationJMS] [-] Successfully added to revoked token map
        ```

            2.  Use the revoked JWT token to send a request to WSO2 API Microgateway.

            -   [**Format**](#format-send-revoked-tk)
            -   [**Example**](#example-send-revoked-tk)
            -   [**Response**](#response-send-revoked-tk)

        ``` java
            curl -k -i -H "Authorization: Bearer <JWT-token>" https://localhost:9095/petstore/v1/pet/findByStatus?status=available
        ```

        ``` java
            curl -k -i -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5UQXhabU14TkRNeVpEZzNNVFUxWkdNME16RXpPREpoWldJNE5ETmxaRFUxT0dGa05qRmlNUT09In0=.eyJhdWQiOiJodHRwOlwvXC9vcmcud3NvMi5hcGltZ3RcL2dhdGV3YXkiLCJzdWIiOiJhZG1pbiIsImFwcGxpY2F0aW9uIjp7Im93bmVyIjoiYWRtaW4iLCJ0aWVyIjoiVW5saW1pdGVkIiwibmFtZSI6Ikp3dFRlc3QiLCJpZCI6Mn0sInNjb3BlIjoiYW1fYXBwbGljYXRpb25fc2NvcGUgZGVmYXVsdCIsImlzcyI6Imh0dHBzOlwvXC9sb2NhbGhvc3Q6OTQ0M1wvb2F1dGgyXC90b2tlbiIsImtleXR5cGUiOiJQUk9EVUNUSU9OIiwic3Vic2NyaWJlZEFQSXMiOltdLCJjb25zdW1lcktleSI6IkpGTjJiRkJyVzdJR2NBaVoydWVkUzM2UjdBY2EiLCJleHAiOjE1NjA1MTY1ODUsImlhdCI6MTU2MDUxMjk4NSwianRpIjoiM2FkNTY3MmEtYWZjYS00Mzg3LTllYzUtZmNmMjg3ODVmODAzIn0=.NVez5rLf2H05AeuAnelDSiDSqj0VwD8A-KtOKr3GxxnelpTM14iyk_k9uyGdsHQ50t9uemowwTCTR2FRo6aOVg3o-RBHzQx2BQEa0VH6JNN83KS0ySkIxjTbNVyCHbFMgFpK0xnhoJyZwnGYYbozwHv2MTJXKdMBose-PclIAgoqNFWDJfYD1YWkdKzeH7QSYnVl6cJWo562PER9a141ydL1jh0snz8QEGKA25PmuvkZ33ydnXSlV1PIBNiHSWL-gTlCmapcPpRqJ8gG8Ld_BGg5QHvqx8YQRTT9p_AHOrhXm-i02IKxYT0zBs6f6y9YDo3F6OeWdmDzItJu14xeqA==" https://localhost:9095/petstore/v1/pet/findByStatus?status=available
        ```

            Response in the Microgateway Console.

        ``` java
            2019-06-14 17:25:27,711 DEBUG [wso2/gateway] - [AuthnFilter] [-] Non-OAuth token found. Calling the auth scheme : jwt
            2019-06-14 17:25:27,711 DEBUG [wso2/gateway] - [AuthnFilter] [-] Processing request with the Authentication handler chain
            2019-06-14 17:25:27,712 DEBUG [ballerina/http] - Trying to authenticate with the auth provider: jwt
            2019-06-14 17:25:27,780 DEBUG [ballerina/auth] - Add authenticated user :admin to the cache
            2019-06-14 17:25:27,781 DEBUG [wso2/gateway] - [JWTAuthProvider] [-] jwt found from the jwt cache
            2019-06-14 17:25:27,781 DEBUG [wso2/gateway] - [JWTAuthProvider] [-] jti claim found in the jwt
            2019-06-14 17:25:27,782 DEBUG [wso2/gateway] - [JWTAuthProvider] [-] Checking for the JTI in the gateway invalid revoked token map.
            2019-06-14 17:25:27,782 DEBUG [wso2/gateway] - [JWTAuthProvider] [-] JTI token found in the invalid token map.
            2019-06-14 17:25:27,782 DEBUG [wso2/gateway] - [JWTAuthProvider] [-] JWT Authentication Handler returned with value : false
            2019-06-14 17:25:27,783 DEBUG [wso2/gateway] - [JWTAuthProvider] [-] JWT Token is revoked
            2019-06-14 17:25:27,783 DEBUG [wso2/gateway] - [AuthnFilter] [-] Authentication handler chain returned with value : false
            2019-06-14 17:25:27,784 DEBUG [wso2/gateway] - [AuthnFilter] [-] Removed header : Authorization from the request
        ```

            Response in the STS (e.g., WSO2 API Manager).

        ``` java
            HTTP/1.1 401 Unauthorized
            content-type: application/json
            content-length: 146
            server: ballerina/0.990.5
            date: Fri, 14 Jun 2019 17:25:27 +0530

            {"fault":{"code":900901, "message":"Invalid Credentials", "description":"Invalid Credentials. Make sure you have given the correct access token"}}
        ```


