# Distributed Throttling

WSO2 API Microgateway has an in-memory mechanism by default to handle throttling(node-level throttling).  In a distributed Microgateway deployement, throttling becomes a challenge with the current implementation. This is because, throttling decisions are taken by evaluating the local counter maintained within each node. Hence, scaling the microgateway will multiply the allowed limits. I.e. for e.g if the throttling limit is set to 10, if we have 3 gateways in a cluster, it will allow 30 requests to pass to the backend, before all three gateways throttle out requests. This will put an unexpected load on the backend. In order to address this, the API Microgateway supports distributed throttling where it is able to work with a central traffic management solution.

The API Microgateway upon recieving a request, checks against the local counter and if throttling limit  has not exceeded it publishes the events via a stream to a central traffic management solution. This is done over HTTP. The  central traffic management solution then  executes throttle policies against the events streams. When a particular request is throttled, the  central traffic management solution sends the details of the throttled out event to a JMS topic. Each API Microgateway node is subscribed to this JMS topic, and updates the local counter when the JMS topic is updated. Hence the API Microgateway nodes gets notified of the throttle decisions through JMS messages.

![distributed throttling](/assets/img/how-tos/distributed-throttling.png)

### Enabling distributed throttling

1.  Lets create a microgateway project

    Create a project using the command given below,
  
    ``` java tab="Format"
    micro-gw init <project_name>
    ```

    ``` java tab="Example"
    micro-gw init petstore
    ```

    ``` java tab="Response"
    Project 'petstore' is initialized successfully.
    ```

2.  Add the API definition(s) to `petstore/api_definitions` directory. A sample open API definition can be found [here](https://github.com/wso2/product-microgateway/blob/master/samples/petstore_basic.yaml) .

3.  Build the microgateway distribution for the project using the following command

    ``` java tab="Format"
    micro-gw build <project_name>
    ```

    ``` java tab="Example"
    micro-gw build petstore
    ```

    ``` java tab="Response"
    BUILD SUCCESSFUL
    Target: /Users/praminda/Documents/workspace/mgw/320/rc2/petstore/target/petstore.jar
    ```

    Once the above command is executed, An executable file (`petstore/target/petstore.jar`) is created to expose the API via WSO2 API Microgateway

4.  Open `<MGW_HOME>/conf/micro-gw.conf` file.
5.  Enable the `enabledGlobalTMEventPublishing` property found inside the `throttlingConfig` tag. This will allow the API Microgateway to connect with the central traffic manager.

    ``` toml
    [throttlingConfig]
      enabledGlobalTMEventPublishing=true
    ```

6.  In the "micro-gw.conf" file under \[throttlingConfig\]  configure the following.This is to configure the  traffic manager and the message broker in the configuration file as follows

    <table>
      <thead>
        <tr>
          <th style="width: 30%">Configuration</th>
          <th style="width: 30%">Sample Value</th>
          <th style="width: 40%">Description</th>
        </tr>
      </thead>
      <tbody>
        <tr>
        <td>`jmsConnectionProviderUrl`</td>
        <td>amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'</td>
        <td>The message broker connection URL. For e.g. a [WSO2 API instance can be used as the Traffic Manager](https://docs.wso2.com/display/AM260/Product+Profiles) . In such an instance, the URL will point to the message broker inside the API Traffic Manager instance.
        </td>
        </tr>
        <tr>
        <td>`jmsConnectionUsername`</td>
        <td>""</td>
        <td>The username used to establish the message broker connection</td>
        </tr>
        <tr>
        <td>`jmsConnectionPassword`</td>
        <td>""</td>
        <td>The password used to establish the message broker connection</td>
        </tr>
        <tr>
        <td>`throttleEndpointUrl`</td>
        <td>https://localhost:9443/endpoints</td>
        <td>
        The central traffic management solution URL. For e.g. a [WSO2 API instance can be used as the Traffic Manager](https://docs.wso2.com/display/AM260/Product+Profiles) . In such an instance, the URL points to the API Traffic Manager instance.
        </td>
        </tr>
        <tr>
        <td>`throttleEndpointbase64Header`</td>
        <td>admin:admin</td>
        <td>
        The username and password (in the format username:password) used to create the connection to the central traffic manager.
        </td>
        </tr>
      </tbody>
    </table>

7.  Execute the following command to start WSO2 API Microgateway with new configurations.

    ```java tab="Format"
    gateway <path-to-executable-file>
    ```

    ```java tab="Example"
    gateway /Users/kim/petstore/target/petstore.jar
    ```

### Conditional throttling

There can be situations where a certain APIs require more granular level of throttling. Assume you want to provide limited access to a certain IP range or a type of client applications (identified by User-Agent header). For these scenarios, a simple throttle policy with API/resource level limits is not sufficient. To address complex throttling requirements as above, microgateway is capable of throttling request based on several conditions. Following types of conditions are supported.

1.  Specific IP or IP range conditions.     
    This condition can be used to provide specific limits to a certain IP address or a range of IP addresses.

1.  Header conditions.      
    This condition can be used to set specific limits to a certain header value.

1.  Query parameter conditions.     
    Same as the header conditions, this allows applying a specific limit to a certain query parameter value.

1.  JWT claim conditions.       
    This type of conditions will evaluate the [backend jwt](/how-tos/passing-enduser-attributes-to-the-backend-using-jwt) and check if it has a specific claim value in it to set the throttle limit.

#### Configure and enable conditional throttling

1.  Open `micro-gw.conf` file in `<MGW_HOME>/conf` directory.
1.  Add/Enable below configurations to enable each of the required condition type.

    ```toml
    [throttlingConfig]
      enabledGlobalTMEventPublishing = true
      enableHeaderConditions = true
      enableQueryParamConditions = true
      enableJwtClaimConditions = true
    ```    

1.  Define the Advance Throttle Policy containing the required conditions in WSO2 API Manager. To do this follow [Adding a new advanced throttling policy](https://apim.docs.wso2.com/en/latest/learn/rate-limiting/adding-new-throttling-policies/#adding-a-new-advanced-throttling-policy)
1.  Conditional throttle policies are an advance set of API and Resource level policies. Thefore you need to define the required policy to apply in the OpenAPI definition. To do that, define `x-wso2-throttling-tier` extension with the Advance Throttle Policy name you defined in API Manager in above step. This extension can be defined in both API and Resource levels.

    ```yaml tab="API level sample"
    x-wso2-basePath: /petstore/v1
    x-wso2-throttling-tier: IPPolicy
    x-wso2-production-endpoints:
      urls:
        - https://petstore.swagger.io/v2
    ```

    ```yaml tab="Resource level sample"
    paths:
      "/pet/findByStatus":
          get:
            summary: Finds Pets by status
            x-wso2-throttling-tier: 10kPerMin
    ```
