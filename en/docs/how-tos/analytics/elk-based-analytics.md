# ELK Based Analytics

!!! note
    Refer to the [compatibility documentation]({{base_path}}/faqs/#compatibility) to confirm the supported update level for this feature.

![Deployment diagram]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/architecture.png)

### Analytics Data flow

The new On-Premise Analytics solution for WSO2 API Microgateway will publish analytics data into a log file and that file will be used as the source for the analytics solution.

ELK based WSO2 API Microgateway On-Premise Analytics deployment architecture has 4 main components.

1. Filebeats
2. Logstash
3. Elasticsearch
4. Kibana

This section will cover the steps required to configure the WSO2 API-M and then publish it to an external ELK cluster.

!!! note
    The solution is developed on ELK 8.1.0 and will only be supported in Kibana versions 8.0.0 and higher.

### Step 1 - Configuring API Microgateway

#### Step 1.1 - Configuring the micro-gw.conf file.

Open the &lt;MICRO\_GW\_HOME&gt;/conf/micro-gw.conf file and add the following configuration.

``` toml
[analytics.elkAnalytics]
  enable = true
```

#### Step 1.2 - Enabling Logs

!!! note
    The LOG4J configuration file `log4j2.properties` is not available in the API Microgateway runtime by default since the logging is carried out by an in-built configuration factory. In order to configure API Microgateway runtime for ELK analytics, create a `log4j2.properties` configuration file in the location &lt;MICRO\_GW\_HOME&gt;/conf/.

    In order to configure log level for the Java components the following configuration can be added to the `log4j2.properties` file.

    ``` properties
    # This sets the global logging level and specifies the appenders
    status = error
    monitorInterval = 30
    rootLogger.level = error
    rootLogger.appenderRef.stdout.ref = STDOUT

    # settings for the console appender
    appender.console.type = Console
    appender.console.name = STDOUT
    appender.console.layout.type = PatternLayout
    appender.console.layout.pattern = %d %-5p [%c] - [%c{1}] %x %m%n

    # To debug the native implementation
    # logger.classLevelLogger.name = org.wso2.micro.gateway.core
    # logger.classLevelLogger.level = debug

    appenders = console
    ```

Open the &lt;MICRO\_GW\_HOME&gt;/conf/ directory. To enable logging for a reporter, edit the `log4j2.properties` file following the instructions given below.


1. Add  APIM_METRICS_APPENDER to the appenders  list:

    ``` properties
    appenders = APIM_METRICS_APPENDER, .... (list of other available appenders)
    ```

2. Add the following configuration after the appenders:

    ``` properties
    appender.APIM_METRICS_APPENDER.type = RollingFile
    appender.APIM_METRICS_APPENDER.name = APIM_METRICS_APPENDER
    appender.APIM_METRICS_APPENDER.fileName = ${sys:carbon.home}/repository/logs/apim_metrics.log
    appender.APIM_METRICS_APPENDER.filePattern = ${sys:carbon.home}/repository/logs/apim_metrics-%d{MM-dd-yyyy}-%i.log
    appender.APIM_METRICS_APPENDER.layout.type = PatternLayout
    appender.APIM_METRICS_APPENDER.layout.pattern = %d{HH:mm:ss,SSS} [%X{ip}-%X{host}] [%t] %5p %c{1} %m%n
    appender.APIM_METRICS_APPENDER.policies.type = Policies
    appender.APIM_METRICS_APPENDER.policies.time.type = TimeBasedTriggeringPolicy
    appender.APIM_METRICS_APPENDER.policies.time.interval = 1
    appender.APIM_METRICS_APPENDER.policies.time.modulate = true
    appender.APIM_METRICS_APPENDER.policies.size.type = SizeBasedTriggeringPolicy
    appender.APIM_METRICS_APPENDER.policies.size.size=1000MB
    appender.APIM_METRICS_APPENDER.strategy.type = DefaultRolloverStrategy
    appender.APIM_METRICS_APPENDER.strategy.max = 10
    ```

3. Add a reporter to the loggers list:

    ``` properties
    loggers = reporter, ...(list of other available loggers)
    ```

4. Add the following configurations after the loggers:

    ``` properties
    logger.reporter.name = org.wso2.am.analytics.publisher.reporter.elk
    logger.reporter.level = INFO
    logger.reporter.additivity = false
    logger.reporter.appenderRef.APIM_METRICS_APPENDER.ref = APIM_METRICS_APPENDER
    ```

!!! note
    The `apim_metrics.log` file will be rolled each day or when the log size reaches the limit of 1000 MB by default. Furthermore, only 10 revisions will be kept and older revisions will be deleted automatically. You can change these configurations by updating the configurations provided in step 2 above.

!!! note
    Following are the details that are available on analytics log events:

    `apim_event_response` -  This event will be triggered for each successful API invocation. When an API associated backend returns an error response, it will also be logged through this event.

    ```
    apim_event_response:
    {
        "apiCreator": "admin",
        "apiCreatorTenantDomain": "carbon.super",
        "apiId": "43d030dc-427f-4678-98e3-87b7d9882b5f",
        "apiMethod": "GET",
        "apiName": "SampleAPI",
        "apiResourceTemplate": "/*",
        "apiType": "HTTP",
        "apiVersion": "1.0.0",
        "applicationId": "2d6c54b0-7c7d-4b50-83dc-e6ae6f88962e",
        "applicationName": "DefaultApplication",
        "applicationOwner": "admin",
        "backendLatency": 13,
        "correlationId": "79ed20c3-55b1-434a-adf6-eea25e2d09c3",
        "destination": "http://192.168.114.51:8281/services/sampleAPIBackend",
        "eventType": "response",
        "gatewayType": "SYNAPSE",
        "keyType": "SANDBOX",
        "platform": "Mac OS X",
        "properties":{},
        "apiContext":"/api1/2.0.0",
        "userName":"admin@carbon.super"
        "proxyResponseCode": 202,
        "regionId": "default",
        "requestMediationLatency": 54,
        "requestTimestamp": "2022-01-20T03:34:36.451Z",
        "responseCacheHit": false,
        "responseLatency": 73,
        "responseMediationLatency": 6,
        "targetResponseCode": 202,
        "userAgent": "Chrome",
        "userIp": "172.16.2.70"
    }
    ```

    apim_event_faulty - This event will be triggered for each failed and throttled API invocation
    
    ```
    apim_event_faulty:
    {
        "apiCreator": "admin",
        "apiCreatorTenantDomain": "carbon.super",
        "apiId": "43d030dc-427f-4678-98e3-87b7d9882b5f",
        "apiName": "SampleAPI",
        "apiType": "HTTP",
        "apiVersion": "1.0.0",
        "applicationId": "0b5ccc91-30e2-4ee5-9355-d1698075c028",
        "applicationName": "SampleApp3",
        "applicationOwner": "admin",
        "correlationId": "ccf2196f-9db8-429b-aaae-98f4c6edf6d7",
        "errorCode": 900803,
        "errorMessage": "APPLICATION_LEVEL_LIMIT_EXCEEDED",
        "errorType": "THROTTLED",
        "eventType": "fault",
        "gatewayType": "SYNAPSE",
        "keyType": "PRODUCTION",
        "proxyResponseCode": 429,
        "regionId": "default",
        "requestTimestamp": "2022-02-01T04:18:48.023Z",
        "responseCacheHit": false,
        "targetResponseCode": -1
    }
    ```
    

    | **Parameter**                | **Type**  | **Description**                                                      |
    |------------------------------|-----------|----------------------------------------------------------------------|
    | "apiCreator"                 | string    | The creator of the API.                                              |
    | "apiCreatorTenantDomain"     | string    | The tenant domain of the API creator.                                |
    | "apiId"                      | string    | Unique identifier of the API.                                        |
    | "apiMethod"                  | string    | The HTTP method used by the API (e.g., GET, POST).                   |
    | "apiName"                    | string    | The name of the API.                                                 |
    | "apiResourceTemplate"        | string    | The template of the API resource accessed.                           |
    | "apiType"                    | string    | The type of the API (e.g., HTTP, REST).                              |
    | "apiVersion"                 | string    | The version of the API.                                              |
    | "applicationId"              | string    | Unique identifier of the application that makes the API call.        |
    | "applicationName"            | string    | Name of the application that makes the API call.                     |
    | "applicationOwner"           | string    | Owner of the application that makes the API call.                    |
    | "backendLatency"             | long      | The time taken by the backend to process the request.                |
    | "correlationId"              | string    | Unique identifier for tracking API calls.                            |
    | "destination"                | string    | The backend URL to which the API call was redirected.                |
    | "eventType"                  | string    | The type of event.                                                   |
    | "gatewayType"                | string    | The type of the API gateway.                                         |
    | "keyType"                    | string    | Indicates whether the API key used was for SANDBOX or PRODUCTION.    |
    | "platform"                   | string    | Operating system was used to access the API.                         |
    | "properties"                 | object    | Properties of the event.                                             |
    | "apiContext"                 | string    | The context of the API call.                                         |
    | "userName"                   | string    | The username of the individual who made the API call.                |
    | "proxyResponseCode"          | int       | The HTTP response code returned by the API gateway.                  |
    | "regionId"                   | string    | The region identifier for the API call.                              |
    | "requestMediationLatency"    | int       | Time taken for request mediation.                                    |
    | "requestTimestamp"           | long      | Timestamp when the request was made.                                 |
    | "responseCacheHit"           | bool      | Indicates if the response was served from cache.                     |
    | "responseLatency"            | long      | Total time taken to respond to the request.                          |
    | "responseMediationLatency"   | long      | Time taken for response mediation.                                   |
    | "targetResponseCode"         | int       | The HTTP response code received from the backend target.             |
    | "userAgent"                  | string    | The user agent of the client making the API call.                    |
    | "userIp"                     | string    | The IP address of the user making the API call.                      |
    | "errorCode"                  | int       | The error code generated in a fault.                                 |
    | "errorMessage"               | string    | The error message associated with the fault.                         |
    | "errorType"                  | string    | The type of error (e.g., THROTTLED).                                 |

### Step 2 - Configuring ELK

#### Installing Elasticsearch

1. [Install Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/8.1/install-elasticsearch.html) according to your operating system.

2. Make sure Elasticsearch is [up and running](https://www.elastic.co/guide/en/elasticsearch/reference/8.1/configuring-stack-security.html).

!!! info
    As recommended by ELK, a minimum 3 node cluster is required for a production environment.

#### Installing Filebeat

1. [Install Filebeat](https://www.elastic.co/guide/en/beats/filebeat/8.1/filebeat-installation-configuration.html#installation) according to your operating system.

2. Configure **Filebeats** to read the log file in the `repository/logs` folder.

    ``` yaml
    filebeat.inputs:
    -   type: log
        enabled: true
        paths:
            - {MGW_HOME}/logs/apim_metrics.log
        include_lines: ['(apimMetrics):']
    output.logstash:
    # The Logstash hosts
         hosts: ["{LOGSTASH_URL}:5044"]
    ```

#### Installing Logstash

1. [Install Logstash](https://www.elastic.co/guide/en/logstash/8.1/installing-logstash.html) according to your operating system.

    ``` java
    input {
        beats {
            port => 5044
        }
    }

    filter {
        grok {
            match => ["message", "%{GREEDYDATA:UNWANTED}\ apimMetrics:%{GREEDYDATA:apimMetrics}\, %{GREEDYDATA:UNWANTED} \:%{GREEDYDATA:properties}"]
        }
        json {
            source => "properties"
        }
    }
    output {
        if [apimMetrics] == " apim:response" {
            elasticsearch {
                hosts => ["http://{ELK_URL}:9200"]
                index => "apim_event_response"
                user => "elastic"
                password => "Admin1234"
            }
        } else if [apimMetrics] == " apim:faulty" {
            elasticsearch {
                hosts => ["http://{ELK_URL}:9200"]
                index => "apim_event_faulty"
                user => "elastic"
                password => "Admin1234"
            }
        }
    }
    ```

#### Installing Kibana

1. [Install Kibana](https://www.elastic.co/guide/en/kibana/8.1/install.html#_install_kibana_yourself) according to your operating system.

2. [Launch](https://www.elastic.co/guide/en/kibana/8.1/access.html#log-on-to-the-web-application) the Kibana web interface.

3. Log in to the Kibana dashboards.

4. Navigate to Stack Management > index pattern. If you already have index patterns created under the following names, delete them before importing the saved artifacts.
    ```
    apim_event*
    apim_event_faulty
    apim_event_response
    ```

5. Download the artifact file from below.<br />
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Artifacts - [here]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/export.ndjson)
6. Navigate to **Stack Management** > **Saved Object** and click on **Import**. Add the downloaded artifact file as an import object, and import

!!! info
    Follow the recommendations of Elastic in order to optimize the performance of the system.

### Dashboards

#### Analyzing statistics

Once you have set up the Kibana dashboards, you can access the following dashboards.

##### Overview

The Overview page gives you a quick overview of the performance of the system. It can be used as a dashboard to view the current system status.

![Overview chart]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/overview.png)

##### Traffic
The Traffic page shows information related to the traffic that goes through your API management deployments. This includes API usage, application usage, resource usage, etc. You can use this page to investigate the usage of APIs and applications, traffic patterns, etc.

![Traffic chart 1]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/traffic1.png)

![Traffic chart 2]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/traffic2.png)

##### API Analytics

The API Analytics Dashboard provides business insights on APIs enabling users to take proactive actions to improve profitability and the quality of the service provided. This dashboard focuses on API developers who wish to see the analytics related to their APIs. This dashboard includes graphs to represent, overall API usage, top 10 API and Application creators, API usage by version, application, resource path and backend.

![API analytics chart 1]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/api-analytics-1.png)

![API analytics chart 2]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/api-analytics-2.png)

##### User Analytics

User Analytics dashboard provides insights of API Users, most API Users, number of success and fault requests by users for each API and application.

![User analytics chart]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/user-analytics.png)

##### Errors

The Errors page shows information related to erroneous API calls that are received by your system. The errors are categorized based on the error type. You can further drill down using the error subtypes. Use this page as the starting point for debugging any API errors.

![Errors chart]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/errors.png)

##### Latency

The Latency page shows information related to the latency of API calls within the API management deployment. You can view a summary of the slowest APIs and then drill down into the API view for further analysis. Use this page as a starting point to debug API slowness.

![Latency chart 1]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/latency1.png)

![Latency chart 2]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/latency2.png)

##### Cache
The Cache page shows statistics that indicate the efficiency with which response caching is carried out for the requests sent to your APIs.

![Cache chart]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/cache.png)

##### Devices

The Devices page displays information about operating systems and HTTP agents that end-users use to invoke the APIs. You can use this page to get an idea of the distribution of your user base and improve your APIs to match the audience.

![Devices chart]({{base_path}}/assets/img/how-tos/analytics/elk-based-analytics/devices.png)
