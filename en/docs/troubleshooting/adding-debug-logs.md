# Adding Debug Logs

Microgateway uses two types of logs to track real-time internal and external activities. Separate log files are created for each of those log types in the &lt;MGW\_RUNTIME\_HOME&gt; `         /repository/logs        ` directory. The log types supported by the MGW and how those logs can be configured is described below.

##### How to enable debug log

Enabling DEBUG level logs will help troubleshoot an issue. There are two ways to enable the DEBUG logs.

Method 1. Set as a command-line option

**Response caching**

``` java
    sh gateway <path-to-MGW-executable-jar-file> --b7a.log.level=DEBUG
```

    Method 2. Configure in the micro-gw.conf file which is located in the `         <MGW-RUNTIME-HOME>/conf        ` directory.

    **Response caching**

``` java
    [b7a.log]
      level = "DEBUG"
```

    ##### How to enable HTTP trace log

    HTTP trace logs are used to monitor the HTTP message flows through the API Gateway. It can track request headers , request payloads, response headers, response payloads, etc. of incoming and outgoing HTTP traffic of the API Gateway. There are two ways to enable the HTTP trace logs.

    Method 1. Set as a command-line option.

``` java
    sh gateway <path-to-MGW-executable-jar-file> --b7a.http.tracelog.console=true
```

    Method 2. Configure in the micro-gw.conf file which is located in the `         <MGW-RUNTIME-HOME>/conf        ` directory.

``` java
    [b7a.http]
    [b7a.http.tracelog]
    console = true
```
