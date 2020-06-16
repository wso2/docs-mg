# Service Discovery

When you create an API Microgateway, the API Microgateway is capable of routing traffic to the URL of the API that you provide at the time of creating an API. The micro services have dynamic URLs. As the API Microgateway is immutable, the dynamic URL changes will not get reflected on the Microgateway. Therefore, in such a scenario you need to stop the Microgateway and restart it by providing the new URL. However, as the latter mentioned scenario is not practical in a real-world scenario, the API Microgateway is integrated with [etcd](https://github.com/etcd-io/etcd) for the purpose of discovering the target endpoints in order to route API traffic.

!!! info
    What is etcd?
    -   [etcd](https://github.com/etcd-io/etcd) is a distributed key-value store that provides a reliable way to store data across a cluster of machines.
    -   Each entry in the etcd is a key-value pair.
    Example:
    ``` java
    key - worldbank-key
    value - http://api.worldbank.org
    key - phoneverify  
    value - http://ws.cdyne.com/phoneverify/phoneverify.asmx
    ```

API Microgateway uses [etcd](https://github.com/etcd-io/etcd) to store a static key together with the corresponding dynamic value that represents the URL of the target endpoint. If the URL of the endpoint changes due to some reason, you need to update the etcd server by providing the new URL for the relevant key. Therefore, WSO2 API Microgateway should know the etcd key, which WSO2 API Microgateway uses to perform an etcd lookup, in order to obtain the correct URL that is needed to route the traffic.

### Enabling service discovery

Enable service discovery using etcd on WSO2 API Microgateway as follows:

1.  Make sure to install and set up all the [installation prerequisites]({{base_path}}/install-and-setup/install-on-vm/) .
2.  Configure and start the etcd server.

    Click here for instructions.

    1.  Download the [etcd distribution](https://github.com/etcd-io/etcd/releases) based on your OS and unzip it.
        In this example, let's work with [etcd v3.4.5](https://github.com/etcd-io/etcd/releases/tag/v3.4.5) .

        !!! tip
            If you are using a Mac OS, download the Darwin files.

    2.  Navigate to the unzipped etcd distribution and start the etcd server.

        ``` java
        cd <etcd_HOME>
        ./etcd
        ```

        The following is a sample message that appears.

        ``` java
        2019-05-23 19:09:18.921356 I | embed: ready to serve client requests
        ```
    3.  Setup the required authentication with the service as follows:

        1.  Create a user.

            ``` java tab="Format"
            curl -L <etcd-client-URL>/<service>/user/add -X POST -d '{"name": "<username>", "password": "<password>"}'

             - <etcd-client-URL> - You can find your client URL in the terminal that you used to start the etcd server.
             - <service> - The service changes based on your etcd version. `/v3alpha/auth` is the service that corresponds to etcd v3.3.13.
            ``` 

            ``` java tab="Example"
            curl -L http://localhost:2379/v3/auth/user/add -X POST -d '{"name": "root", "password": "pass"}'
            ```

            ``` java tab="Sample Response"
            {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"1","raft_term":"2"}}
            ```

        2.  Create a user role.

            ``` java tab="Format"
            curl -L <etcd-client-URL>/<service>/role/add -X POST -d '{"name": "<user-role>"}'

            - <etcd-client-URL>  - You can find your client URL in the terminal that you used to start the etcd server.
            - <service> - The service changes based on your etcd version. `/v3alpha/auth` is the service that corresponds to etcd v3.3.13.
            ```    

            ``` java tab="Example"
            curl -L http://localhost:2379/v3/auth/role/add -X POST -d '{"name": "root"}'
            ```

            ``` java tab="Sample Response"
            {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"1","raft_term":"2"}}
            ```

        3.  Assign the user to the user role.

            ``` java tab="Format"
            curl -L <etcd-client-URL>/<service>/user/grant -X POST -d '{"user": "<username>", "role": "<user-role>"}'
           
            -  <etcd-client-URL> - You can find your client URL in the terminal that you used to start the etcd server.
            -  <service>  - The service changes based on your etcd version. `/v3alpha/auth` is the service that corresponds to etcd v3.3.13.
            ```

            ``` java tab="Example"
            curl -L http://localhost:2379/v3/auth/user/grant -X POST -d '{"user": "root", "role": "root"}'
            ```

            ``` java tab="Sample Response"
            {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"1","raft_term":"2"}}
            ```

        4.  Enable user authentication.

            ``` java tab="Format"
            curl -L <etcd-client-URL>/<service>/enable -X POST -d '{}'
            - <etcd-client-URL> - You can find your client URL in the terminal that you used to start the etcd server.
            - <service> - The service changes based on your etcd version. `/v3alpha/auth` is the service that corresponds to etcd v3.3.13.
            ```

            ``` java tab="Example"
            curl -L http://localhost:2379/v3/auth/enable -X POST -d '{}'
            ```

            ``` java tab="Sample Response"
            {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"1","raft_term":"2"}}
            ```

        5.  Authenticate the user with etcd and obtain an authentication token.

            ``` java tab="Format"
            curl -L <etcd-client-URL>/<service>/authenticate -X POST -d '{"name": "<username>", "password": "<password>"}'
             - <etcd-client-URL> - You can find your client URL in the terminal that you used to start the etcd server.
             - <service> - The service changes based on your etcd version. `/v3alpha/auth` is the service that corresponds to etcd v3.3.13.
            ```
                      
            ``` java tab="Example"
            curl -L http://localhost:2379/v3/auth/authenticate -X POST -d '{"name": "root", "password": "pass"}'
            ```

            ``` java tab="Sample Response"
             {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"1","raft_term":"2"},"token":"hjdNkYCLWVBgNBbL.9"}
            ```

            !!! info
                Troubleshooting
                ``` java
                {"error":"etcdserver: invalid auth token","code":16}
                ```
                If you get the above error, regenerate your Auth token by repeating this step.

    4.  Store the key-value pair in the etcd server.

        **Format**
        
        ``` java 
        curl -L <etcd-client-URL>/<etcd-cli-version>/kv/put -X POST -d '{"key": "<base64-encoded-key>", "value": "<base64-encoded-value>"}' -H 'Authorization : <auth-token>'

        - <etcd-client-URL> - You can find your client URL in the terminal that you used to start the etcd server.
        - <etcd-cli-version> - The CLI version that corresponds to your etcd version. For example, `/v3alpha` is the CLI version that corresponds to etcd v3.3.13.
        - <base64-encoded-key> - Encode the key using a base64 encoder (e.g., <https://www.base64encode.org/> ) and enter the respective value.
        - <base64-encoded-value> - Encode the endpoint URL using a base64 encoder and enter the respective value.
        - <auth-token> - Enter the Auth token that you obtained in [step c](#auth-token) .
        ```
        
        **Sample**    
        Let's use the following values for this example.

        <table>
        <thead>
        <tr class="header">
        <th><br />
        </th>
        <th>Base64 Decoded Value</th>
        <th>Base64 Encoded Value</th>
        </tr>
        </thead>
        <tbody>
        <tr class="odd">
        <td>Key</td>
        <td>petstore</td>
        <td><code>                      cGV0c3RvcmU=                     </code></td>
        </tr>
        <tr class="even">
        <td>Value</td>
        <td><a href="http://petstore.swagger.io/v2/" class="uri">http://petstore.swagger.io/v2/</a></td>
        <td><code>                      aHR0cDovL3BldHN0b3JlLnN3YWdnZXIuaW8vdjIv                     </code></td>
        </tr>
        </tbody>
        </table>


        ``` java tab="Example"
        curl -L http://localhost:2379/v3/kv/put -X POST -d '{"key": "cGV0c3RvcmU=", "value": "aHR0cDovL3BldHN0b3JlLnN3YWdnZXIuaW8vdjIv"}' -H 'Authorization : UOkLazCNAoHnxiDz.11'
        ```
        
        ``` java tab="Sample Response"
        {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"2","raft_term":"2"}}
        ```
        
        !!! info
            If you want to check whether you have stored your key-value pair correctly, run the following command.

        **Format**
        
        ``` java 
            curl -L <etcd-client-URL>/<etcd-cli-version>/kv/range -X POST -d '{"key": "<base64-encoded-key>"}' -H 'Authorization : <auth-token>'
        
            - <etcd-client-URL> - You can find your client URL in the terminal that you used to start the etcd server.
            - <etcd-cli-version> - The CLI version that corresponds to your etcd version. For example, `/v3alpha` is the CLI version that corresponds to etcd v3.3.13.
            - <base64-encoded-key> - Encode the key using a base64 encoder (e.g., <https://www.base64encode.org/> ) and enter the respective value.
            - <base64-encoded-value> - Encode the endpoint URL using a base64 encoder and enter the respective value. 
            - <auth-token> - Enter the Auth token that you obtained in [step c](#ServiceDiscovery-AuthToken) .
        ```
        **Sample**
        
        Let's use the following value for this example.
        <table>
        <thead>
        <tr class="header">
        <th><br />
        </th>
        <th>Base64 Decoded Value</th>
        <th>Base64 Encoded Value</th>
        </tr>
        </thead>
        <tbody>
        <tr class="odd">
        <td>Key</td>
        <td>petstore</td>
        <td><code>                      cGV0c3RvcmU=                     </code></td>
        </tr>
        </tbody>
        </table>
            
            
        ``` java tab="Example"
        curl -L http://localhost:2379/v3/kv/range -X POST -d '{"key": "cGV0c3RvcmU="}' -H 'Authorization : UOkLazCNAoHnxiDz.11'
        ```
        
        ``` java tab="Sample Response"
        {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"4","raft_term":"2"},"kvs":[{"key":"cGV0c3RvcmU=","create_revision":"4","mod_revision":"4","version":"1","value":"aHR0cDovL3BldHN0b3JlLnN3YWdnZXIuaW8vdjIv"}],"count":"1"}
        ```

3.  Create an API Microgateway project (e.g., petstore-project).
    Navigate to a preferred folder where you want to create the Microgateway project, and then run the following command.


    ``` java tab="Format"
    micro-gw init <project-name>
    ```

    ``` java tab="Example"
    micro-gw init petstore-project
    ```

4.  Add the API definition with the etcd configurations in the `/petstore-project/api_definitions` directory.
    Let's copy the [sample open API definition](https://github.com/wso2/product-microgateway/blob/master/samples/petstore_basic.yaml) , define the etcd configurations, and add the API definition ( `petstore_basic.yaml` ) to the `api_definitions` folder.

    ``` java tab="Format"
    x-wso2-production-endpoints:
    urls:
    - etcd (<etcd_key>,<default_URL>)

    - <etcd_key> - The value of the key that you store in the etcd server for the purpose of mapping the endpoint URL.

    - <default_URL> - This is the URL that WSO2 API Microgateway redirects the requests to in the event it is unable to resolve the etcd key or/and is unable to find the etcd server.
    ```
    
    ``` java tab="Example"
    x-wso2-production-endpoints:
    urls:
      - etcd (petstore,https://www.example.com)
    ```

5.  Build the WSO2 API Microgateway project (e.g., petstore-project).

    ``` java tab="Format"
    micro-gw build <project-name>
    ```

    ``` java tab="Example"
    micro-gw build petstore-project
    ```

    This creates an executable file ( `/petstore-project/target/petstore-project.balx` ) that you can use to expose the API via WSO2 API Microgateway.

6.  Start WSO2 API Microgateway.

    **Format**

    ``` java 
    bash gateway <path-to-MGW-executable-jar-file> --etcdurl=<http-or-https-etcdurl> --etcdusername=<etcd-username> --etcdpassword=<etcd-password> --etcdtimer=<etcd-timer> 
    ```
    - <etcd-url> - Provide the etcd URL. If the etcd URL is secured (https), you need to add the etcd server certificate to the [Ballerina](https://ballerina.io/) truststore.

    - <etcd-timer> - This is the time period for the periodic timer task (in milliseconds). The default time period is 10 seconds. The `<etcd-timer>` parameter specifies the time interval for each periodic query to etcd; therefore, this value should be lower than the TTL of the token. etcd supports the following types of tokens.

    -   **Simple tokens**
        These tokens have a default time-to-live (TTL) of 5 minutes, but the TTL is configurable. In addition, the TTL refreshes each time a request goes to etcd.

    Example:
        If etcd is configured to use simple tokens with a TTL of 5 minutes, the `etcdTimer` parameter value should contain a value less than 5 minutes. If the etcdTimer value is 24000 (24000 ms = 4 minutes). Every 4 minutes the timer task is triggered, the etcd is queried, and it refreshes the TTL of the token.

    -   **JWT tokens**
        This token does not have a TTL.

    **Example**

    ``` java
    bash gateway /Users/kim/Downloads/TestProj/petstore-project/target/petstore-project.jar --etcdurl=http://127.0.0.1:2379 --etcdusername=root --etcdpassword=pass --etcdtimer=10000 
    ```

    ``` java
    ballerina: HTTP access log enabled
    [ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9096
    2019-05-24 01:52:51,352 INFO  [wso2/gateway] - [EtcdUtil] [-] Etcd Authentication Successful 
    2019-05-24 01:52:51,357 INFO  [wso2/gateway] - [EtcdUtil] [-] Etcd periodic timer task started with a periodic time of 10000ms 
    [ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9095
    [ballerina/http] started HTTP/WS endpoint 0.0.0.0:9090
    2019-05-24 01:52:51,418 INFO  [wso2/gateway] - HTTPS listener is active on port 9095 
    2019-05-24 01:52:51,418 INFO  [wso2/gateway] - HTTP listener is active on port 9090
    ```

### Changing the API endpoint URL via etcd

For example, If the production endpoint URL changes and the corresponding etcd key is `etcddemoprod` , update the value related to the `etcddemoprod` key on the etcd server.
If the periodic timer task value is 10 seconds, within 10 seconds the periodic task gets executed and updates WSO2 API Microgateway with the new value. Thereby, in this scenario, if you are in the process of querying an API, WSO2 API Microgateway redirects your request to the new URL of the endpoint within 10 seconds.

Follow the instructions below to update the etcd with your new API endpoint URL:

   1.  If your Auth token has expired, obtain a new Auth token.

       ``` java
       curl -L <etcd-client-URL>/<service>/authenticate -X POST -d '{"name": "<username>", "password": "<password>"}'

       - <etcd-client-URL> - You can find your client URL in the terminal that you used to start the etcd server.
       - <service> - The service changes based on your etcd version. `/v3alpha/auth` is the service that corresponds to etcd v3.3.13.
       ```    

       ``` java
       curl -L http://localhost:2379/v3alpha/auth/authenticate -X POST -d '{"name": "root", "password": "pass"}'
       ```

       ``` java
       {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"1","raft_term":"2"},"token":"EYLniBWxqJANrUEQ.17"}
       ```

   2.  Add the etcd value, which corresponds to the new endpoint URL, in the etcd server.

       ``` java tab="Format"
        curl -L <etcd-client-URL>/<etcd-cli-version>/kv/put -X POST -d '{"key": "<base64-encoded-key>", "value": "<base64-encoded-value>"}' -H 'Authorization : <auth-token>'

       - <etcd-client-URL> - You can find your client URL in the terminal that you used to start the etcd server.
       - <etcd-cli-version> - The CLI version that corresponds to your etcd version. For example, `/v3alpha` is the CLI version that corresponds to etcd v3.3.13.
       - <base64-encoded-key> - Encode the key using a base64 encoder (e.g., <https://www.base64encode.org/> ) and enter the respective value.
       - <base64-encoded-value> - Encode the value, which corresponds to the new endpoint URL, using a base64 encoder and enter the respective value. `               `
       - <auth-token> - Enter the Auth token that you obtained in the previous step.
       ```

    Let's use the following values for this example.

    <table>
    <thead>
    <tr class="header">
    <th><br />
    </th>
    <th>Base64 Decoded Value</th>
    <th>Base64 Encoded Value</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td>Key</td>
    <td>petstore</td>
    <td><code>                  cGV0c3RvcmU=                 </code></td>
    </tr>
    <tr class="even">
    <td>Value</td>
    <td><a href="http://petstore.swagger.io/v2/" class="uri">http://petstore.swagger.io/v2/</a></td>
    <td><code>                  aHR0cDovL3BldHN0b3JlLnN3YWdnZXIuaW8vdjIv                 </code></td>
    </tr>
    </tbody>
    </table>
        

       ``` java tab="Example"
       curl -L http://localhost:2379/v3alpha/kv/put -X POST -d '{"key": "cGV0c3RvcmU=", "value": "aHR0cDovL3BldHN0b3JlLnN3YWdnZXIuaW8vdjIv"}' -H 'Authorization : UOkLazCNAoHnxiDz.11'
       ```

       ``` java tab="Sample Response"
       {"header":{"cluster_id":"14841639068965178418","member_id":"10276657743932975437","revision":"2","raft_term":"2"}}
       ```


