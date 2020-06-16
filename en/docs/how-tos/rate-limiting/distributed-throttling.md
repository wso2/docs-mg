# Distributed Throttling

WSO2 API Microgateway has an in-memory mechanism by default to handle throttling(node-level throttling).  In a distributed Microgateway deployement, throttling becomes a challenge with the current implementation. This is because, throttling decisions are taken by evaluating the local counter maintained within each node. Hence, scaling the microgateway will multiply the allowed limits. I.e. for e.g if the throttling limit is set to 10, if we have 3 gateways in a cluster, it will allow 30 requests to pass to the backend, before all three gateways throttle out requests. This will put an unexpected load on the backend. In order to address this, the API Microgateway supports distributed throttling where it is able to work with a central traffic management solution.

The API Microgateway upon recieving a request, checks against the local counter and if throttling limit  has not exceeded it publishes the events via a stream to a central traffic management solution. This is done over HTTP. The  central traffic management solution then  executes throttle policies against the events streams. When a particular request is throttled, the  central traffic management solution sends the details of the throttled out event to a JMS topic. Each API Microgateway node is subscribed to this JMS topic, and updates the local counter when the JMS topic is updated.  Hence the API Microgateway nodes gets notified of the throttle decisions through JMS messages.

![distributed throttling]({{base_path}}/assets/img/how-tos/distributed-throttling.png)

### Enabling distributed throttling

1.  Lets create a microgateway project/

    1.  Navigate to a preferred workspace folder using the command line. (This location is used to run the Microgateway commands and to generate Microgateway artifacts.)
    2.  Create a project using the command given below,

        ``` java tab="Format
        micro-gw init <project_name> 
        ```

        ``` java tab="Example"
        micro-gw init petstore  
        Project 'petstore' is initialized successfully.
        ```

2.  Now lets add the API(open API definition) to the project. Navigate to the `           /petstore/api_definitions          ` directory. Add the API definition(s) to th `           is          ` directory. A sample open API definition can be found [here](https://github.com/wso2/product-microgateway/blob/master/samples/petstore_basic.yaml) .

3.  Build the microgateway distribution for the project using the following command:

    ``` java tab="Format"
    micro-gw build <project_name>
    ```

    ``` java tab="Example"
    $ micro-gw build petstore
      Build successful  for the project - petstore
    ```

        Once the above command is executed, An executable file ( `/petstore/target/petstore.jar` ) is created to expose the API via WSO2 API Microgateway

4.  Navigate to the `<MGW-RUNTIME-HOME>/conf/` directory and open the “micro-gw.conf" file.
5.  Enable the `enabledGlobalTMEventPublishing` property found inside the `throttlingConfig` tag. This will allow the API Microgateway to connect with the central traffic manager.

    ``` java
    [throttlingConfig]
    enabledGlobalTMEventPublishing=true
    ```

6.  In the "micro-gw.conf" file under \[throttlingConfig\]  configure the following.This is to configure the  traffic manager and the message broker in the configuration file as follows

        | Configuration                | Sample Value                                                       | Description                                                                                                                                                                                                                                                         |
        |------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
        | jmsConnectionProviderUrl     | amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672' | The message broker connection URL. For e.g. a [WSO2 API instance can be used as the Traffic Manager](https://docs.wso2.com/display/AM260/Product+Profiles) . In such an instance, the URL will point to the message broker inside the API Traffic Manager instance. |
        | jmsConnectionUsername        | ""                                                                 | The user name used to establish  the message broker connection                                                                                                                                                                                                      |
        | jmsConnectionPassword        | ""                                                                 | The password used to establish  the message broker connection                                                                                                                                                                                                       |
        | throttleEndpointUrl          | https://localhost:9443/endpoints                                   | The central traffic management solution URL. For e.g. a [WSO2 API instance can be used as the Traffic Manager](https://docs.wso2.com/display/AM260/Product+Profiles) . In such an instance, the URL points to the API Traffic Manager instance.                     |
        | throttleEndpointbase64Header | admin:admin                                                        | The username and password (in the format username:password) used to create the connection to the central traffic manager.                                                                                                                                           |

7.  Execute the following command to start WSO2 API Microgateway with new configurations.

    ``` java tab="Format"
    gateway <path-to-executable-file>
    ```

    ``` java tab="Example"
    gateway /Users/kim/petstore/target/petstore.jar
    ```


