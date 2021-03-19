# Rejecting Revoked Tokens
Follow the below steps to enable Real-time and Persistent token revocation. 

To understand how WSO2 Microgateway handles revoked tokens happens, refer the documentation on [Revoked Tokens]({{base_path}}/concepts/revoked-tokens/). 

The following sections explains the steps required to configure to handle revoked JWTs. The same steps can be used for handling revoked opaque tokens. However, for opaque tokens, you only need to enable real-time notifications.
Follow one of the below sections based on your secure token service (STS), 

1. [Configure WSO2 API Manager as the secure token service (STS) for token revocation](#configure-wso2-api-manager-as-the-secure-token-service-sts-for-token-revocation)
2. [Configure third party Security Token Service (STS) for token revocation](#configure-third-party-security-token-service-sts-for-token-revocation)

### Configure WSO2 API Manager as the secure token service (STS) for token revocation

!!! note
    The API Manager is supporting [token revocation]({{apim_path}}/learn/api-security/oauth2/token-revocation/#token-revocation) from 3.0.0 onwards.

1. Navigate to the `<MGW_HOME>/conf/micro-gw.conf` file and add the `tokenRevocationConfig` configuration similar to the following.

    ``` java tab="Real-time and Persistent Notifications"
    [tokenRevocationConfig]
      [tokenRevocationConfig.realtime]
        enableRealtimeMessageRetrieval = true
        jmsConnectionTopic = "tokenRevocation"
        jmsConnectioninitialContextFactory = "wso2mbInitialContextFactory"
        jmsConnectionProviderUrl= "amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"
        jmsConnectionUsername = "admin"
        jmsConnectionPassword = "admin"
      [tokenRevocationConfig.persistent]
        enablePersistentStorageRetrieval = true
        type = "default"
        endpointURL = "https://localhost:9443/internal/data/v1"
        username = "admin"
        password = "admin"
    ```

    ``` java tab="Only Real-time Notifications"
    [tokenRevocationConfig]
      [tokenRevocationConfig.realtime]
        enableRealtimeMessageRetrieval = true
        jmsConnectionTopic = "tokenRevocation"
        jmsConnectioninitialContextFactory = "wso2mbInitialContextFactory"
        jmsConnectionProviderUrl= "amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"
        jmsConnectionUsername = "admin"
        jmsConnectionPassword = "admin"
      [tokenRevocationConfig.persistent]
        enablePersistentStorageRetrieval = false
        type = "default"
        endpointURL = "https://localhost:9443/internal/data/v1"
        username = "admin"
        password = "admin"
    ```

    ``` java tab="Only Persistent Notifications"
    [tokenRevocationConfig]
      [tokenRevocationConfig.realtime]
        enableRealtimeMessageRetrieval = false
        jmsConnectionTopic = "tokenRevocation"
        jmsConnectioninitialContextFactory = "wso2mbInitialContextFactory"
        jmsConnectionProviderUrl= "amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"
        jmsConnectionUsername = ""
        jmsConnectionPassword = ""
      [tokenRevocationConfig.persistent]
        enablePersistentStorageRetrieval = true
        type = "default"
        endpointURL = "https://localhost:9443/internal/data/v1"
        username = "admin"
        password = "admin"
    ```

2. Generate a JWT token.
      Follow [Obtain a JWT token using the WSO2 API Manager]({{apim_path}}/learn/consume-api/manage-application/generate-keys/generate-api-keys/#generate-application-keys).

3.  Start WSO2 API Manager.

4. Test the synchronization process of token revocation.

    1. Send the revoke request.

        ```sh tab="Format"
        curl -k -v -d "token=<token>" -H "Authorization: Basic <base64-encoded-string>" -H "Content-Type: application/x-www-form-urlencoded" https://localhost:8243/revoke 
        - `<jwt>` - Enter the opaque(reference)/JWT access token, that you want to revoke.
        - `<base64-encoded-string>` - Use a base64 encoder (e.g., <https://www.base64encode.org/> ) to encode your client ID and client secret using the following format: `<clientId>:<clientSecret>` Thereafter, enter the encoded value for this parameter.
        ```

        ```sh tab="Example"
        token=eyJ4NXQiOiJNell4TW1Ga09HWXdNV0kwWldObU5EY3hOR1l3WW1NNFpUQTNNV0kyTkRBelpHUXpOR00wWkdSbE5qSmtPREZrWkRSaU9URmtNV0ZoTXpVMlpHVmxOZyIsImtpZCI6Ik16WXhNbUZrT0dZd01XSTBaV05tTkRjeE5HWXdZbU00WlRBM01XSTJOREF6WkdRek5HTTBaR1JsTmpKa09ERmtaRFJpT1RGa01XRmhNelUyWkdWbE5nX1JTMjU2IiwiYWxnIjoiUlMyNTYifQ.eyJzdWIiOiJhZG1pbkBjYXJib24uc3VwZXIiLCJhdWQiOiJLOHdqaGc5NWY4R1FEOWhzMF9jWnpWSHF1ek1hIiwibmJmIjoxNjA4NzA1MzA5LCJhenAiOiJLOHdqaGc5NWY4R1FEOWhzMF9jWnpWSHF1ek1hIiwic2NvcGUiOiJhbV9hcHBsaWNhdGlvbl9zY29wZSBkZWZhdWx0IiwiaXNzIjoiaHR0cHM6XC9cL2xvY2FsaG9zdDo5NDQzXC9vYXV0aDJcL3Rva2VuIiwiZXhwIjoxNjA4NzA4OTA5LCJpYXQiOjE2MDg3MDUzMDksImp0aSI6ImJhMjdkMmQyLTBlMzAtNGZhMC1hY2VjLTJmOWYxYWQ4ODkwMCJ9.xdQvckDYVVkPsh_fXyMBE9DoZq0hXloIx5Pb1hQ-026U6P4QDjr66maPpeeprpLb_X0T_QwvD_aKx7YXnTWXONrPBxeZAeeuO1jo8mqtllwLCMvXKTlTZIQRzccNZaV7i_seiBGaiiPucKXI87bq01Fq_oNPWi8OP67UfxR97qNMNYgt1IrojdxvEBM8r5LVFbvKCy_e2JRumSmi1edBgQnaTnevFc5cWh1DHkOQWsuHhXvDtfgtsKV28Uv5B6rM2ttOgM3ErS2nFHutorbv_1BW6w1l6hVLouwqrjzKRx7CdKf1UI_HMOimR3Ghv6_OFsS0ONoeIIy47mPAxVmMRA
        curl -k -v -d "token=${token}" -H "Authorization: Basic U01OYVM0Tkg1UlJKSFJONDVoSzcxWElVbXdNYTo4ajRqTHFBUDZDNjNSTkFTVTdMZDEyeVUxbHNh" -H "Content-Type: application/x-www-form-urlencoded" https://localhost:8243/revoke 
        ```
       
        ```text tab="Sample Response"
        < HTTP/1.1 200 
        < X-Frame-Options: DENY
        < RevokedRefreshToken: eb30e8cd-518e-3284-b3b5-bb2abd1a3385
        < Cache-Control: no-store
        < X-Content-Type-Options: nosniff
        < AuthorizedUser: admin@carbon.super
        < Pragma: no-cache
        < RevokedAccessToken: eyJ4NXQiOiJNell4TW1Ga09HWXdNV0kwWldObU5EY3hOR1l3WW1NNFpUQTNNV0kyTkRBelpHUXpOR00wWkdSbE5qSmtPREZrWkRSaU9URmtNV0ZoTXpVMlpHVmxOZyIsImtpZCI6Ik16WXhNbUZrT0dZd01XSTBaV05tTkRjeE5HWXdZbU00WlRBM01XSTJOREF6WkdRek5HTTBaR1JsTmpKa09ERmtaRFJpT1RGa01XRmhNelUyWkdWbE5nX1JTMjU2IiwiYWxnIjoiUlMyNTYifQ.eyJzdWIiOiJhZG1pbkBjYXJib24uc3VwZXIiLCJhdWQiOiJLOHdqaGc5NWY4R1FEOWhzMF9jWnpWSHF1ek1hIiwibmJmIjoxNjA4NzA1MzA5LCJhenAiOiJLOHdqaGc5NWY4R1FEOWhzMF9jWnpWSHF1ek1hIiwic2NvcGUiOiJhbV9hcHBsaWNhdGlvbl9zY29wZSBkZWZhdWx0IiwiaXNzIjoiaHR0cHM6XC9cL2xvY2FsaG9zdDo5NDQzXC9vYXV0aDJcL3Rva2VuIiwiZXhwIjoxNjA4NzA4OTA5LCJpYXQiOjE2MDg3MDUzMDksImp0aSI6ImJhMjdkMmQyLTBlMzAtNGZhMC1hY2VjLTJmOWYxYWQ4ODkwMCJ9.xdQvckDYVVkPsh_fXyMBE9DoZq0hXloIx5Pb1hQ-026U6P4QDjr66maPpeeprpLb_X0T_QwvD_aKx7YXnTWXONrPBxeZAeeuO1jo8mqtllwLCMvXKTlTZIQRzccNZaV7i_seiBGaiiPucKXI87bq01Fq_oNPWi8OP67UfxR97qNMNYgt1IrojdxvEBM8r5LVFbvKCy_e2JRumSmi1edBgQnaTnevFc5cWh1DHkOQWsuHhXvDtfgtsKV28Uv5B6rM2ttOgM3ErS2nFHutorbv_1BW6w1l6hVLouwqrjzKRx7CdKf1UI_HMOimR3Ghv6_OFsS0ONoeIIy47mPAxVmMRA
        < X-XSS-Protection: 1; mode=block
        < Content-Type: text/html
        < Date: Wed, 23 Dec 2020 06:35:32 GMT
        < Transfer-Encoding: chunked
        < 
        * Connection #0 to host localhost left intact
        * Closing connection 0
        ```

        The following response can be seen via the WSO2 API Microgateway console if you have [enabled debug logs]({{base_path}}/troubleshooting/adding-debug-logs/#how-to-enable-debug-log).

        ``` tab="Sample Response"
        2019-06-14 17:20:45,636 DEBUG [wso2/gateway] - [TokenRevocationJMS] [-] token revoked jms Message Received
        2019-06-14 17:20:45,638 DEBUG [wso2/gateway] - [TokenRevocationJMS] [-] Successfully added to revoked token map
        ```       

    2. Use the revoked token to send a request to WSO2 API Microgateway.

        ```sh tab="Format"
        curl -k -i -H "Authorization: Bearer <token>" https://localhost:9095/petstore/v1/pet/findByStatus?status=available
        ```

        ```sh tab="Example"
        curl -k -i -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5UQXhabU14TkRNeVpEZzNNVFUxWkdNME16RXpPREpoWldJNE5ETmxaRFUxT0dGa05qRmlNUT09In0=.eyJhdWQiOiJodHRwOlwvXC9vcmcud3NvMi5hcGltZ3RcL2dhdGV3YXkiLCJzdWIiOiJhZG1pbiIsImFwcGxpY2F0aW9uIjp7Im93bmVyIjoiYWRtaW4iLCJ0aWVyIjoiVW5saW1pdGVkIiwibmFtZSI6Ikp3dFRlc3QiLCJpZCI6Mn0sInNjb3BlIjoiYW1fYXBwbGljYXRpb25fc2NvcGUgZGVmYXVsdCIsImlzcyI6Imh0dHBzOlwvXC9sb2NhbGhvc3Q6OTQ0M1wvb2F1dGgyXC90b2tlbiIsImtleXR5cGUiOiJQUk9EVUNUSU9OIiwic3Vic2NyaWJlZEFQSXMiOltdLCJjb25zdW1lcktleSI6IkpGTjJiRkJyVzdJR2NBaVoydWVkUzM2UjdBY2EiLCJleHAiOjE1NjA1MTY1ODUsImlhdCI6MTU2MDUxMjk4NSwianRpIjoiM2FkNTY3MmEtYWZjYS00Mzg3LTllYzUtZmNmMjg3ODVmODAzIn0=.NVez5rLf2H05AeuAnelDSiDSqj0VwD8A-KtOKr3GxxnelpTM14iyk_k9uyGdsHQ50t9uemowwTCTR2FRo6aOVg3o-RBHzQx2BQEa0VH6JNN83KS0ySkIxjTbNVyCHbFMgFpK0xnhoJyZwnGYYbozwHv2MTJXKdMBose-PclIAgoqNFWDJfYD1YWkdKzeH7QSYnVl6cJWo562PER9a141ydL1jh0snz8QEGKA25PmuvkZ33ydnXSlV1PIBNiHSWL-gTlCmapcPpRqJ8gG8Ld_BGg5QHvqx8YQRTT9p_AHOrhXm-i02IKxYT0zBs6f6y9YDo3F6OeWdmDzItJu14xeqA==" https://localhost:9095/petstore/v1/pet/findByStatus?status=available
        ```

        ```text tab="Sample Response"
        < HTTP/2 401 
        < www-authenticate: OAuth2 realm="WSO2 API Microgateway", error="invalid token" , error_description="The access token expired"
        < content-type: application/json
        < server: ballerina
        < date: Wed, 23 Dec 2020 12:05:38 +0530

        {"fault":{"code":900901, "message":"Invalid Credentials", "description":"Invalid Credentials. Make sure you have given the correct access token"}}
        ```
       
        The following response can be seen via the WSO2 API Microgateway console if you have [enabled debug logs]({{base_path}}/troubleshooting/adding-debug-logs/#how-to-enable-debug-log).

        ``` tab="Sample Response"
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

### Configure third party Security Token Service (STS) for token revocation

Let's use the following sample scenario.

   - Both Real-time Notifications and Persistent Notifications are enabled.
   - Default etcd based persistent notifier implementation will be used.

!!! info
    **Before you begin** make sure to configure your Security Token Service (STS)<sup>*</sup> and start it. In addition, when working with real-time notifications, you need to define the JMS map message payload in the following format.
    
    ```json
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
    
    <sup>*</sup>Security Token Service (STS) - The WSO2 API Manager is the STS if you are using a JWT token generated from the WSO2 API Manager. Then follow [Configure WSO2 API Manager as the secure token service (STS) for token revocation](#configure-wso2-api-manager-as-the-secure-token-service-sts-for-token-revocation)

1.  If you select etcd/other service as the persistent storage, configure and start the persistent storage service.

    <details>
      <summary>Click here for instructions to configure the etcd server.</summary>

      1.  Download the [etcd distribution](https://github.com/etcd-io/etcd/releases) based on your OS and unzip it.
          In this example, let's work with [etcd v3.3.13](https://github.com/etcd-io/etcd/releases/tag/v3.3.13) .

      2.  Navigate to the unzipped etcd distribution and start the etcd server.

          ```sh
          cd <etcd_HOME>
          ./etcd
          ```

          The following is a sample message that appear.

          ```text
          2019-05-23 19:09:18.921356 I | embed: ready to serve client requests
          ```

        **Note:**  
        If you are working in a production environment, start the etcd server with HTTPS enabled.
        ```sh
        ./etcd --name infra0 --data-dir infra0 --cert-file=<PATH_TO_CERT>/server.crt --key-file=<PATH_TO_CERT>/server.key --advertise-client-urls=https://localhost:2379 --listen-client-urls=https://localhost:2379
        ```

      3. Setup the required authentication with the service as follows:

          1. Create a user.

              ```sh tab="Format"
              ./etcdctl user add <username>
              ```
              
              ```sh tab="Example"
              ./etcdctl user add root
              ```

              ```text tab="Response"
              New password:
              User <username> created
              ```

          2. Create a user role.

              ```sh tab="Format"
              ./etcdctl role add <role_name>
              ```

              ```sh tab="Example"
              ./etcdctl role add root
              ```

              ```text tab="Response"
              Role <role_name> created
              ```

          3.  Assign the user to the user role.

              ```sh tab="Format"
              ./etcdctl user grant <username> --roles <role_name>
              ```
              
              ```sh tab="Example"
              ./etcdctl user grant root --roles root
              ```

              ```text tab="Response"
              User <username> updated
              ```

          4. Enable user authentication.

              ```sh tab="Command"
              ./etcdctl auth enable
              ```

              ```text tab="Response"
              Authentication Enabled
              ```

      4.  Optionally create a directory in the etcd server to store the revoked JWT IDs (jti). This directory can have any name (e.g., jti, jwt) however you have to add this directory name to the `endpointURL` property in persistent storage configuration.

          ```sh tab="Format"
          ./etcdctl -u <username> mkdir <directory_name>
          ```
              
          ```sh tab="Example"
          ./etcdctl -u root mkdir jti
          ```
      
      5.  Store the key value pair in the etcd server.

          ```sh tab="Format"
          ./etcdctl -u <username> set <jti> <ttl>
          ```

          ```sh tab="Example"
          ./etcdctl -u root set "3ad5672a-afca-4387-9ec5-fcf28785f803" 3600
          ```

          - `<jti>` - Enter the JWT ID (jti) that corresponds to the JWT token.
          - `<ttl>` - Enter the time-to-live (TTL) respective to the revoke token in milliseconds.
          - `<username>` - Username of the user created in previous steps.

    !!! info
        If you want to check whether you have stored your key-value pair correctly, run the following command.

        ```sh tab="Format"
        ./etcdctl  -u <username> get <jti>
        ```
        
        ```sh tab="Example"
        ./etcdctl  -u root get 3ad5672a-afca-4387-9ec5-fcf28785f803
        ```
    </details>

2.  Establish security between the persistent storage (etcd server), WSO2 API Microgateway, and the STS.

    !!! note
        This is only required if you are working in a production environment.

    1.  Create a self-signed certificate for the persistent storage.
    2.  Add the self-signed certificate to the Ballerina Trust Store ( `ballerinaTruststore.p12` ) in the WSO2 API Microgateway project and to the Client Trust Store in the STS (e.g., add to `client-truststore.jks` in WSO2 API Manager).

3.  Configure and start a JMS broker to host the token revocation topic.

    <details>
    <summary>Click here for instructions to configure the Apache ActiveMQ JMS Broker as an example.</summary>
    
    1.  Download [Apache ActiveMQ 5.15.8](https://activemq.apache.org/activemq-5158-release.html) and extract it in the filesystem.
    
    2.  Start the server.
    
    ```sh 
    cd <ActiveMQ-home>/bin
    ./activemq start            
    ```
    
    3.  Go to the ActiveMQ admin console (http://localhost:8161/admin is the default URL for ActiveMQ admin console) using your browser. 
    
    4.  Create a topic using the console. We use admin as the username and password for authentication in this example.
    
    5.  Create a topic with the name `tokenRevocation` using the console.
    
    6.  Since we create an ActiveMQ specific broker, relavant jars needs to be copied to `<MGW_project>/lib`. 
        Therefore copy the following files from the ActiveMQ distribution in `<ActiveMQ-home>/lib` to `<MGW_project>/lib` directory. 
        - activemq-client-5.15.9.jar
        - geronimo-j2ee-management_1.1_spec-1.0.1.jar
        
        Then build the project again.
        
    7.  Start the gateway.
        The following response can be seen via the WSO2 API Microgateway console if you have [enabled debug logs]({{base_path}}/troubleshooting/adding-debug-logs/#how-to-enable-debug-log).
        
        ```
        INFO [wso2/gateway] - [TokenRevocationJMS] [-] subscriber service for token revocation is started
        ```
    
    </details>

4. Navigate to the `<MGW_HOME>/conf/micro-gw.conf` file and add `tokenRevocationConfig` configuration similar to the following.

    ``` java tab="Real-time and Persistent Notifications"
    [tokenRevocationConfig]
      [tokenRevocationConfig.realtime]
        enableRealtimeMessageRetrieval = true
        jmsConnectionTopic = "tokenRevocation"
        jmsConnectioninitialContextFactory = "org.apache.activemq.jndi.ActiveMQInitialContextFactory"
        jmsConnectionProviderUrl= "tcp://localhost:61616"
        jmsConnectionUsername = "admin"
        jmsConnectionPassword = "admin"
      [tokenRevocationConfig.persistent]
        enablePersistentStorageRetrieval = true
        type = "etcd"
        endpointURL = "<etcd-server-access-URL>/v3/keys/jti/"
        username = "root"
        password = "root"
    ```

    ``` java tab="Only Real-time Notifications"
    [tokenRevocationConfig]
      [tokenRevocationConfig.realtime]
        enableRealtimeMessageRetrieval = true
        jmsConnectionTopic = "tokenRevocation"
        jmsConnectioninitialContextFactory = "org.apache.activemq.jndi.ActiveMQInitialContextFactory"
        jmsConnectionProviderUrl= "tcp://localhost:61616"
        jmsConnectionUsername = "admin"
        jmsConnectionPassword = "admin"
      [tokenRevocationConfig.persistent]
        enablePersistentStorageRetrieval = false
        type = "etcd"
        endpointURL = "<etcd-server-access-URL>/v3/keys/jti/"
        username = "root"
        password = "root"
    ```

    ``` java tab="Only Persistent Notifications"
    [tokenRevocationConfig]
      [tokenRevocationConfig.realtime]
        enableRealtimeMessageRetrieval = false
        jmsConnectionTopic = "tokenRevocation"
        jmsConnectioninitialContextFactory = "org.apache.activemq.jndi.ActiveMQInitialContextFactory"
        jmsConnectionProviderUrl= "tcp://localhost:61616"
        jmsConnectionUsername = "admin"
        jmsConnectionPassword = "admin"
      [tokenRevocationConfig.persistent]
        enablePersistentStorageRetrieval = true
        type = "etcd"
        endpointURL = "<etcd-server-access-URL>/v3/keys/jti/"
        username = "root"
        password = "root"
    ```

    !!! note
        When working with persistent notifications, if you introduced new configurations in your custom implementation, then you have to provide the corresponding values for those properties in the `<MGW_HOME>/conf/micro-gw.conf` file under the `[tokenRevocationConfig.persistent]` section.

5. Generate a JWT token.

    - **Use any third party secure token service (STS)**
        You need to add the public certificate of the token service that you used to sign the JWT token to the trust store of the WSO2 API Microgateway. Follow the documentation on [Configuring JWT issuers]({{base_path}}/how-tos/security/api-authentication/secure-apis-using-oauth2.0-access-tokens/secure-apis-using-jwt-self-contained-jwt/#configure-multiple-jwt-issuers) for more details.

6. Test the synchronization process of token revocation.

    1.  Send the revocation message using the JMS topic.
        
        <details>
            <summary>Click here for instructions to use ActiveMQ JMS Broker as an example.</summary>
            <p>
            For this example, we use ActiveMQ REST API to push a message to the tokenRevocation topic as follows.
            </p>
            
            token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5UZG1aak00WkRrM05qWTBZemM1TW1abU9EZ3dNVEUzTVdZd05ERTVNV1JsWkRnNE56YzRaQT09In0.eyJhdWQiOiJodHRwOlwvXC9vcmcud3NvMi5hcGltZ3RcL2dhdGV3YXkiLCJzdWIiOiJhZG1pbkBjYXJib24uc3VwZXIiLCJhcHBsaWNhdGlvbiI6eyJvd25lciI6ImFkbWluIiwidGllclF1b3RhVHlwZSI6InJlcXVlc3RDb3VudCIsInRpZXIiOiIxMFBlck1pbiIsIm5hbWUiOiJqd3QiLCJpZCI6MzMsInV1aWQiOm51bGx9LCJzY29wZSI6ImFtX2FwcGxpY2F0aW9uX3Njb3BlIGRlZmF1bHQiLCJpc3MiOiJodHRwczpcL1wvbG9jYWxob3N0Ojk0NDNcL29hdXRoMlwvdG9rZW4iLCJ0aWVySW5mbyI6eyJVbmxpbWl0ZWQiOnsidGllclF1b3RhVHlwZSI6InJlcXVlc3RDb3VudCIsInN0b3BPblF1b3RhUmVhY2giOnRydWUsInNwaWtlQXJyZXN0TGltaXQiOjAsInNwaWtlQXJyZXN0VW5pdCI6bnVsbH19LCJrZXl0eXBlIjoiUFJPRFVDVElPTiIsInN1YnNjcmliZWRBUElzIjpbeyJzdWJzY3JpYmVyVGVuYW50RG9tYWluIjoiY2FyYm9uLnN1cGVyIiwibmFtZSI6IlBpenphU2hhY2tBUEkiLCJjb250ZXh0IjoiXC9waXp6YXNoYWNrXC8xLjAuMCIsInB1Ymxpc2hlciI6ImFkbWluIiwidmVyc2lvbiI6IjEuMC4wIiwic3Vic2NyaXB0aW9uVGllciI6IlVubGltaXRlZCJ9XSwiY29uc3VtZXJLZXkiOiJheFduaVZHZWpQaFRCVGR2OFNidjVjcmdRaE1hIiwiZXhwIjozNzM2MDY2NTY3LCJpYXQiOjE1ODg1ODI5MjAsImp0aSI6ImZjZGIwY2M1LTRjNWUtNDZkOC04OTliLTMxZDJhNmMyZGUxZSJ9.KjNXGc1xNx_zuxg0mW_i8oGE517Y6ewJEWL9WBRXaCgEEYFYyLl-44bd18093QrYB3CkStKuw6_GTAvP_J6HEu9rO44vXhPNHosx_fB0t4n8OO9Gimp882syMiwy9mbpz4NEdMnrdTj-4l-7cCR4YrVcvdd_Z2zlQ9xLx_tq8gTLecnchE7Mb9dCQHgePjzh9a4OTpGparkyi-XOJ12ywyYhjwQK1C2uzyqeE0XpQzFzMZDtEktNmsP-AAEne27DVcvepJsUgDihhWQzsXBmnYV6zPHKG3pXlFCVhnVPIiC6RIA94tMle2_WjqFNZNQx0HqJyIH_vb2WeSO6DeUMvA
            curl -X POST -d 'revokedToken=${token}' "http://admin:admin@localhost:8161/api/message/tokenRevocation" -v
     
        </details>
        
        The following response can be seen via the WSO2 API Microgateway console after revocation data successfully retrieved to the Microgateway if you have [enabled debug logs]({{base_path}}/troubleshooting/adding-debug-logs/#how-to-enable-debug-log).
        
        ``` tab="Sample Response"
        2019-06-14 17:20:45,636 DEBUG [wso2/gateway] - [TokenRevocationJMS] [-] token revoked jms Message Received
        2019-06-14 17:20:45,638 DEBUG [wso2/gateway] - [TokenRevocationJMS] [-] Successfully added to revoked token map
        ```

    2.  Use the revoked JWT token to send a request to WSO2 API Microgateway.

        ```sh tab="Format"
        curl -k -i -H "Authorization: Bearer <JWT-token>" https://localhost:9095/petstore/v1/pet/findByStatus?status=available
        ```

        ```sh tab="Example"
        curl -k -i -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5UQXhabU14TkRNeVpEZzNNVFUxWkdNME16RXpPREpoWldJNE5ETmxaRFUxT0dGa05qRmlNUT09In0=.eyJhdWQiOiJodHRwOlwvXC9vcmcud3NvMi5hcGltZ3RcL2dhdGV3YXkiLCJzdWIiOiJhZG1pbiIsImFwcGxpY2F0aW9uIjp7Im93bmVyIjoiYWRtaW4iLCJ0aWVyIjoiVW5saW1pdGVkIiwibmFtZSI6Ikp3dFRlc3QiLCJpZCI6Mn0sInNjb3BlIjoiYW1fYXBwbGljYXRpb25fc2NvcGUgZGVmYXVsdCIsImlzcyI6Imh0dHBzOlwvXC9sb2NhbGhvc3Q6OTQ0M1wvb2F1dGgyXC90b2tlbiIsImtleXR5cGUiOiJQUk9EVUNUSU9OIiwic3Vic2NyaWJlZEFQSXMiOltdLCJjb25zdW1lcktleSI6IkpGTjJiRkJyVzdJR2NBaVoydWVkUzM2UjdBY2EiLCJleHAiOjE1NjA1MTY1ODUsImlhdCI6MTU2MDUxMjk4NSwianRpIjoiM2FkNTY3MmEtYWZjYS00Mzg3LTllYzUtZmNmMjg3ODVmODAzIn0=.NVez5rLf2H05AeuAnelDSiDSqj0VwD8A-KtOKr3GxxnelpTM14iyk_k9uyGdsHQ50t9uemowwTCTR2FRo6aOVg3o-RBHzQx2BQEa0VH6JNN83KS0ySkIxjTbNVyCHbFMgFpK0xnhoJyZwnGYYbozwHv2MTJXKdMBose-PclIAgoqNFWDJfYD1YWkdKzeH7QSYnVl6cJWo562PER9a141ydL1jh0snz8QEGKA25PmuvkZ33ydnXSlV1PIBNiHSWL-gTlCmapcPpRqJ8gG8Ld_BGg5QHvqx8YQRTT9p_AHOrhXm-i02IKxYT0zBs6f6y9YDo3F6OeWdmDzItJu14xeqA==" https://localhost:9095/petstore/v1/pet/findByStatus?status=available
        ```

        ```text tab="Sample Response"
        HTTP/1.1 401 Unauthorized
        content-type: application/json
        content-length: 146
        server: ballerina/0.990.5
        date: Fri, 14 Jun 2019 17:25:27 +0530

        {"fault":{"code":900901, "message":"Invalid Credentials", "description":"Invalid Credentials. Make sure you have given the correct access token"}}
        ```
       
        The following response can be seen via the WSO2 API Microgateway console if you have [enabled debug logs]({{base_path}}/troubleshooting/adding-debug-logs/#how-to-enable-debug-log).

        ``` tab="Sample Response"
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
       
## Configurations

#### Realtime configurations

<table>
  <thead>
  <tr>
  <th style="width: 40%">Property</th>
  <th style="width: 60%">Description</th>
  </tr>
  </thead>
  <tbody>
  <tr>
  <td>
  `enableRealtimeMessageRetrieval`
  </td>
  <td>
  This property is disabled by default. Set this property to `true` if you want to enable Real-time Notifications.
  </td>
  </tr>
  <tr>
  <td>
  `jmsConnectionTopic`
  </td>
  <td>
  This is the name of the topic in the JMS Message Broker that WSO2 API Microgateway listens to in order to identify messages related to revoked tokens.
  </td>
  </tr>
  <tr>
  <td>
  `jmsConnectioninitialContextFactory`

  `jmsConnectionProviderUrl`

  `jmsConnectionUsername`

  `jmsConnectionPassword`
  </td>
  <td>
  Similar to `jmsConnectionTopic` these are also JMS related configuration. As you can't use a custom implementation when using Real-time Notifications, you will not need to change these configurations.
  </td>
  </tr>
  </tbody>
</table>


#### Persistent configurations

<table>
  <thead>
  <tr>
  <th style="width: 40%">Property</th>
  <th style="width: 60%">Description</th>
  </tr>
  </thead>
  <tbody>
  <tr>
  <td>
  `enablePersistentStorageRetrieval`
  </td>
  <td>Set this property to `true` if you want enable Persistent Notifications.</td>
  </tr>
  <tr>
  <td>
  `type`
  </td>
  <td>
  This property is set to `"default"` in the default configuration, so that you can use WSO2 API Manager eventhub as the default persistent storage. If you are using any other persistent storage, other than `default`, you need to define your custom implementation in the `<MGW_PROJECT>/extensions/token_revocation_extension.bal` file. Thereafter, you need to name set a preferred type name in this property.
  </td>
  </tr>
  <tr>
  <td>
  `endpointURL`
  </td>
  <td>
  The default value is `https://localhost:9443/internal/data/v1`. However, you can use etcd version 3 (`<etcd-server-access-URL>/v3/keys/jti/`) or even another persistent storage of your choice.
  </td>
  </tr>
  <tr>
  <td>
  `username`
  </td>
  <td>
  Enter the username of your persistent storage server (e.g., etcd server). The default value for the etcd server is "root".
  </td>
  </tr>
  <tr>
  <td>
  `password`
  </td>
  <td>
  Enter the password of your persistent storage server (e.g., etcd server). The default value for the etcd server is "root".
  </td>
  </tr>
  </tbody>
</table>
     