# Distributed Throttling

WSO2 API Microgateway has an in-memory mechanism by default, to handle throttling
([node-level throttling](/how-tos/rate-limiting/adding-throttling-policies)).  
In a deployment with multiple microgateways, throttling becomes a challenge with node local throttling as the throttling 
decision is made based on the local counter within each node. If we proceed with the node local throttling in such 
environment, the API user would be allowed to send multiples of the throttling limit.I.e. if the throttling limit is set to 10, 
if we have 3 gateways in a cluster, it will allow 30 requests to pass to the backend before all three gateways 
throttle out requests. This will put an unexpected load on the backend. To address this requirement, the API Microgateway 
supports distributed throttling where it is able to work with a central traffic management solution. In this case, 
multiple microgateways can connect with WSO2 API Manager 
([WSO2 Traffic Manager](https://apim.docs.wso2.com/en/3.2.0/install-and-setup/setup/distributed-deployment/product-profiles/)) 
and perform rate-limiting precisely.

!!! note
    If you start the WSO2 API Manager without providing any profile, it runs as All in One Node (All the profiles 
    are activated). For testing purposes, you can simply start the API Manager following the 
    [quick start guide](https://apim.docs.wso2.com/en/3.2.0/getting-started/quick-start-guide/) and test.

<!---TODO:@VirajSalaka Add concept page and mention it here--->
<!---TODO:@VirajSalaka Update image (old) and add to concept page--->

### Enabling distributed throttling

1.  Let's create a microgateway project.

    1. Navigate to a preferred workspace folder using the command line. (This location is used to run the Microgateway commands and to generate Microgateway artifacts.)
    2. Create a project using the command given below,
  
           ``` java tab="Format"
           micro-gw init <project_name> 
           ```

           ``` java tab="Example"
           micro-gw init petstore  
           ```

2.  Now let's add the API(open API definition) to the project. Navigate to the `/petstore/api_definitions` directory. 
    Add the API definition(s) to this directory. A sample open API definition can be found 
    [here](https://github.com/wso2/product-microgateway/blob/master/samples/petstore_basic.yaml). To provide an API 
    Level throttling tier, add the following extension to the openAPI at the root level.
    
    ```text tab="Sample"
    x-wso2-throttling-tier : "5PerMin"    
    ```
    
3.  The policies added in the OpenAPI definition should be deployed in the Traffic Manager. (If you are adding the
    tier "5PerMin" in openAPI definition, you need to create and deploy it in the Traffic Manager).
    The relevant documentation can be found [here](https://apim.docs.wso2.com/en/3.2.0/learn/rate-limiting/adding-new-throttling-policies/#adding-a-new-advanced-throttling-policy).

4.  Build the microgateway distribution for the project using the following command:

    ``` java tab="Format"
    micro-gw build <project_name>
    ```

    ``` java tab="Example"
    micro-gw build petstore
    ```

    Once the above command is executed, An executable file ( `/petstore/target/petstore.jar` ) is created to expose the API via WSO2 API Microgateway

5.  Navigate to the `<MGW-RUNTIME-HOME>/conf/` directory and open the “micro-gw.conf" file.

6.  Enable the `enabledGlobalTMEventPublishing` property found inside the `throttlingConfig` tag. This will allow the API Microgateway to connect with the central traffic manager.

    ``` toml
    [throttlingConfig]
    enabledGlobalTMEventPublishing=true
    ```

7.  In the "micro-gw.conf" file under \[throttlingConfig\]  configure the following. The purpose here is to configure 
    the message broker.

    <table>
        <thead>
            <tr class="header">
                <th>Property</th>
                <th>Default value</th>
                <th>Description</th>
            </tr>
        </thead>
        <tbody>
            <tr class="odd">
                <td><code>jmsConnectionProviderUrl</code></td>
                <td><code>"amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"</code></td>
                <td>The message broker connection URL of WSO2 API/Traffic Manager</td>
            </tr>
            <tr class="even">
                <td><code>jmsConnectionUsername</code></td>
                <td><code>""</code></td>
                <td>The user name used to establish  the message broker connection</td>
            </tr>
            <tr class="odd">
                <td><code>jmsConnectionPassword</code></td>
                <td><code>""</code></td>
                <td>The password used to establish  the message broker connection</td>
            </tr>
        </tbody>
    </table>
    
    The message broker connection URL. For e.g. a [WSO2 API instance can be used as the Traffic Manager](https://apim.docs.wso2.com/en/latest/install-and-setup/setup/distributed-deployment/product-profiles/). 
    In such an instance, the URL will point to the message broker inside the API Traffic Manager instance.
    In the "micro-gw.conf" file under \[throttlingConfig.binary\], we should list down all the configurations related to
    event publishing.                   
        
    <table>
        <thead>
        <tr class="header">
            <th>Property</th>
            <th>Default value</th>
            <th>Description</th>
            </tr>
        </thead>
        <tbody>
            <tr class="odd">
                <td><code>enabled</code></td>
                <td><code>true</code></td>
                <td>Enable the binary event publisher. If it is `false`, http event publisher will be enabled</td>
            </tr>
            <tr class="even">
                <td><code>username</code></td>
                <td><code>"admin"</code></td>
                <td>The user name used for authentication prior to publishing events via binary publisher</td>
            </tr>
            <tr class="odd">
                <td><code>password</code></td>
                <td><code>"admin"</code></td>
                <td>The password used for authentication prior to publishing events via binary publisher</td>
            </tr>
        </tbody>
    </table>
    
    The binary receiver URL(s) needs to be added as an Array using the key \[\[throttlingConfig.binary.URLGroup\]\]. 
    If multiple receivers are provided, the microgateway will publish events to all of them in parallel.
     
    <table>
        <thead>
        <tr class="header">
            <th>Property</th>
            <th>Default value</th>
            <th>Description</th>
            </tr>
        </thead>
        <tbody>
            <tr class="odd">
                <td><code>receiverURL</code></td>
                <td><code>"tcp://localhost:9611"</code></td>
                <td>The URL to which the thorttle events are sent.</td>
            </tr>
            <tr class="even">
                <td><code>authURL</code></td>
                <td><code>"ssl://localhost:9711"</code></td>
                <td>The URL to which the credentials required for authentication, are sent.</td>
            </tr>
        </tbody>
    </table>

8.  If you are using API Manager 3.2.0 or later version, you need to configure the API Manager Eventhub. This step is 
    not required if you need to do only the resource level throttling or API level throttling.
    <!---TODO:@VirajSalaka Add the Event hub configuration guide URL--->
    
9.  Finally the added configurations in `microgw.conf` would look like this. 
    
    ``` toml tab="Sample Configuration"
    [throttlingConfig]
        enabledGlobalTMEventPublishing=true
        jmsConnectionProviderUrl = "amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"
        
        [throttlingConfig.binary]
            enabled = true
            username = "admin"
            password = "admin"
            [[throttlingConfig.binary.URLGroup]]
                receiverURL = "tcp://localhost:9611"
                authURL = "ssl://localhost:9711"
    
    [apim.eventHub]
      enable = true
      serviceUrl = "https://localhost:9443"
      internalDataContext="/internal/data/v1/"
      username="admin"
      password="admin"
      eventListeningEndpoints = "amqp://admin:admin@carbon/carbon?brokerlist='tcp://localhost:5672'"
    ```  
                                                                                                                                     
10.  Execute the following command to start WSO2 API Microgateway with new configurations.

    ``` java tab="Format"
    gateway <path-to-executable-file>
    ```

    ``` java tab="Example"
    gateway /Users/kim/petstore/target/petstore.jar
    ```
