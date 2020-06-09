# Troubleshooting

When errors/exceptions occur in the system, the API Microgateway throws error responses to the client by default. The following sections explain about the different ways of troubleshooting common problems that might occur while you use the Microgateway.

### Common exceptions and solutions

The table below shows the common exceptions that might occur when you are trying to use the API Microgateway, and how to fix them. These exceptions can happen due to misconfigurations.

#### Runtime errors

<table>
<thead>
<tr class="header">
<th>Error log</th>
<th>Possible cause</th>
<th>Resolution</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><code>                  micro-gw: Error occurred while trying to connect with server. Is the server running at                                     https://localhost:9443?                                   </code></td>
<td><p>The API Manager node (Publisher) is down when running the setup command in the Microgateway.</p></td>
<td>Verify the connectivity between the Microgateway and the API manager node.</td>
</tr>
<tr class="even">
<td><code>                  Micro-gw: ERROR [src:0.0.0] - Error in client response : {message:&quot;Connection refused: localhost/127.0.0.1:8080&quot;, cause:null}                 </code></td>
<td>Connection to the backend is refused.</td>
<td>Check the connection to the backend.</td>
</tr>
<tr class="odd">
<td><code>                  error [docker plugin]: Unable to build docker image: {&quot;message&quot;:&quot;invalid reference format: repository name must be lowercase&quot;}                 </code></td>
<td><p>The name of the docker images should be in lower case.</p>
<p>When building the project with docker annotations, the docker image name is retrieved from the API name and version. If the API name contains a capital letter, then the docker image n ame would reflect that, resuling in this error.</p>
<p><br />
</p></td>
<td><div class="content-wrapper">
<p><br />
</p>
<div>
When building the API microgateway project, provide a simple letter name for the docker image in the <code>                    deployment.toml                   </code> file as shown below.
</div>
<div>
<p><br />
</p>
<div class="code panel pdl" style="border-width: 1px;">
<div class="codeContent panelContent pdl">
<pre class="java" data-syntaxhighlighter-params="brush: java; gutter: false; theme: Confluence" data-theme="Confluence" style="brush: java; gutter: false; theme: Confluence"><code>[docker]
  [docker.dockerConfig]
    enable = true
    name = &quot;hello_world&quot;</code></pre>
</div>
</div>
<p><br />
</p>
</div>
<p><br />
</p>
</div></td>
</tr>
<tr class="even">
<td><p><code>                   ERROR [wso2/gateway:0.0.0] - Error occurred while reading the key validation response : {message:&quot;Connection refused: localhost/127.0.0.1:9443&quot;, cause:null}                  </code><br />
<code>                   ERROR [wso2/gateway:0.0.0] - Error occurred while converting the authorized value from the key validation response to a                  </code><br />
<code>                   string value : {message:&quot;'null' cannot be cast to 'string'&quot;, cause:null}                  </code></p>
<p><code>                   ERROR [wso2/gateway:0.0.0] - Error occurred while getting key validation information for the access token : {message:&quot;call failed&quot;, cause:{message:&quot;call failed&quot;, cause:{message:&quot;'null' cannot be cast to 'string'&quot;, cause:null}, causes:[{message:&quot;'null' cannot be cast to 'string'&quot;, cause:null}]}, causes:[{message:&quot;call failed&quot;, cause:{message:&quot;'null' cannot be cast to 'string'&quot;, cause:null}, causes:[{message:&quot;'null' cannot be cast to 'string'&quot;, cause:null}]}]}                  </code></p></td>
<td>The Microgateway could not connect to the Key Manager for OAuth2 key validation.</td>
<td>Check the connection between the Microgateway and the Key Manager.</td>
</tr>
<tr class="odd">
<td><code>                  ERROR [wso2/gateway:0.0.0] - Error occurred while reading the key validation response : {message:&quot;General SSLEngine problem/192.168.8.101:9443&quot;, cause:null}                 </code><br />
<code>                  ERROR [wso2/gateway:0.0.0] - Error occurred while converting the authorized value from the key validation response to a                 </code><br />
<code>                  string value : {message:&quot;'null' cannot be cast to 'string'&quot;, cause:null}                 </code></td>
<td>SSL hostname verification has failed in the key validation call.</td>
<td><div>
The <code>                   localhost                  </code> hostname is supported by default.
</div>
<div>
You need to add the public certificate of the Key Manager to the Microgateway truststore. Also, make sure that you change the <code>                   certificateAlias                  </code> accordingly.
</div></td>
</tr>
<tr class="even">
<td><code>                  ERROR [ballerina/http] - Error while validating JWT token : {message:&quot;Invalid signature&quot;, cause:null}                 </code></td>
<td>JWT signature verification has failed.</td>
<td><div>
Verify the following:
</div>
<ul>
<li>JWT signer’s public cert should be available in the Microgateway’s truststore.</li>
<li>The correct Certificate Alias should be given in the <code>                    &lt;MGW_HOME&gt;/conf/micro-gw.conf                   </code> file.</li>
</ul></td>
</tr>
</tbody>
</table>

#### 
Toolkit errors

<table>
<thead>
<tr class="header">
<th>Error log</th>
<th>Possible cause</th>
<th>Resolution</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><code>                  micro-gw: Error in client response : {message:&quot;Network is unreachable: www.mocky.io/54.194.152.6:80&quot;, cause:null}                 </code></td>
<td><p>A connection to the backend could not be established because the network is unavailable.</p></td>
<td>Verify the network stability.</td>
</tr>
<tr class="even">
<td><code>                  ERROR {org.wso2.apimgt.gateway.cli.cmd.Main} - Internal error occurred while executing command.                 </code><br />
<code>                  com.github.jknack.handlebars.HandlebarsException: /home/kim/Downloads/wso2am-micro-gw-toolkit-x.x.x/resources/templates/service.mustache:36:87: java.lang.IllegalStateException: Can't resolve: 'doc'                 </code></td>
<td>This is due to an issue in the Service template.</td>
<td><ul>
<li>Open the <code>                    &lt;MGW_HOME&gt;/resources/templates                   </code> file.</li>
<li>Remove the <code>                    @swagger:ServiceInfo                   </code> annotation-related <a href="https://github.com/wso2/product-microgateway/blob/v2.5.0-rc2/components/micro-gateway-cli/src/main/resources/templates/service.mustache#L28-L42">configuration</a> in the service.mustache template.</li>
<li>Re-run the setup command.</li>
</ul></td>
</tr>
<tr class="odd">
<td><div class="content-wrapper">
<p><code>                                                            micro-gw: Project name `petstore` already exist.                   </code></p>
</div></td>
<td>You get this error when you re-run the <code>                  micro-gw init &lt;project-name&gt;                 </code> command while you already have a project with the same name (e.g., <code>                  petstore                 </code> ) in the current working directory.</td>
<td><div class="content-wrapper">
<p>Carry out one of the following actions to overcome this error.</p>
<ul>
<li><p><strong>Override the current project</strong><br />
If you need to override the current Microgateway project, run the <code>                      init                     </code> command with the <code>                      -f (--force)                     </code> option. For example, use the following command if you are overriding the petstore project.</p>
<div class="code panel pdl" style="border-width: 1px;">
<div class="codeContent panelContent pdl">
<pre class="java" data-syntaxhighlighter-params="brush: java; gutter: false; theme: Confluence" data-theme="Confluence" style="brush: java; gutter: false; theme: Confluence"><code>micro-gw init &lt;project-name&gt; -f</code></pre>
</div>
</div></li>
<li><p><strong>Create a new project</strong></p>
<p>If you do not need to override the current Microgateway project, run the <code>                      init                     </code> command with another project name from another working directory.</p>
<div class="code panel pdl" style="border-width: 1px;">
<div class="codeContent panelContent pdl">
<pre class="java" data-syntaxhighlighter-params="brush: java; gutter: false; theme: Confluence" data-theme="Confluence" style="brush: java; gutter: false; theme: Confluence"><code>micro-gw init &lt;project-name&gt;</code></pre>
</div>
</div></li>
</ul>
</div></td>
</tr>
</tbody>
</table>

------------------------------------------------------------------------

### Runtime error responses

| Error code                                  | Error response                                               | Possible reasons                                                                   |
|---------------------------------------------|--------------------------------------------------------------|------------------------------------------------------------------------------------|
| `                  101503                 ` | ``` java                                                     
    {                                                             
    "fault": {                                                
    "code": "101503",                                     
    "message": "Runtime Error",                           
    "description": "Error connecting to the back end"     
    }                                                         
    }                                                             
  ```                                                           | -   The network is not reachable by the backend.                                   
      -   The connection from the backend has been refused.                               |
| `                  101504                 ` | ``` java                                                     
    {                                                             
    "fault": {                                                
    "code": "101504",                                     
    "message": "Runtime Error",                           
    "description": "Connection timed out"                 
    }                                                         
    }                                                             
  ```                                                           | -   The connection has timed out.                                                  
      -   The connection has timed out from the Microgateway end.                         
      -   Response is getting delayed and hence timeout occurs in the Microgateway side.  |
| 900900                                      | ``` java                                                     
    {                                                             
    "fault": {                                                
    "code": 900900,                                       
    "message": "Unclassified Authentication Failure",     
    "description": "Unclassified Authentication Failure"  
    }                                                         
    }                                                             
  ```                                                           | -   The Key Manager is down.                                                       |

      ------------------------------------------------------------------------

      ### Runtime error codes

      Given below are some WSO2 API Microgateway specific error codes and their meanings.

      | Error code                                  | Possible reason                                                                                                                                                                                                    |
      |---------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
      | `                  900901                 ` | The production/sandbox key offered by the requested endpoint is not specified.                                                                                                                                     |
      | `                  900900                 ` | API-M authentication related error.                                                                                                                                                                                |
      | `                  900901                 ` | Invalid access token.                                                                                                                                                                                              |
      | `                  900902                 ` | Missing credentials.                                                                                                                                                                                               |
      | `                  900903                 ` | Access token expired.                                                                                                                                                                                              |
      | `                  900904                 ` | Access token inactive.                                                                                                                                                                                             |
      | `                  900905                 ` | Incorrect access token type provided.                                                                                                                                                                              |
      | `                  900906                 ` | No matching resource found in the API for the given request.                                                                                                                                                       |
      | `                  900907                 ` | The requested API is temporarily blocked.                                                                                                                                                                          |
      | `                  900908                 ` | Resource forbidden.                                                                                                                                                                                                |
      | `                  900909                 ` | The subscription to the API is inactive.                                                                                                                                                                           |
      | `                  900910                 ` | The access token does not allow you to access the requested resource.                                                                                                                                              |
      | `                  900803                 ` | Application level throttled out.                                                                                                                                                                                   |
      | `                  900804                 ` | Subscription level throttled out.                                                                                                                                                                                  |
      | `                  900808                 ` | An internal error occurred in the Microgateway.                                                                                                                                                                    |
      | `                  900809                 ` | An internal error occurred in the Microgateway, since a subscription or application throttle policy is not deployed. This might be due to adding a throttle policy to API-M and not regenerating the Microgateway. |

      ### Adding Debug Logs

      Micro gateway uses two types of logs to track realtime internal and external activities. Separate log files are created for each of those log types in the &lt;MGW\_RUNTIME\_HOME&gt; `             /repository/logs            ` directory. Following illustrates the log types supported by the MGW and how those logs can be configured.

      ##### How to enable debug log

      If we want to log any information that helps us identify what went wrong we can get information by enable DEBUG level. Ther are two ways to enable debug log.

      Method 1. We can set in the request command.

      **Response caching**

``` java
    sh gateway <path-to-MGW-executable-jar-file> --b7a.log.level=DEBUG
```

    Method 2. We can set in the micro-gw.conf file which is located in the `             <MGW-RUNTIME-HOME>/conf            ` directory.

    **Response caching**

``` java
    [b7a.log]
      level = "DEBUG"
```

    ##### How to enable HTTP trace log

    If we want to monitor the HTTP message flow through API Gateway and track the request headers, request payloads, response headers, response payloads etc of incoming and outgoing http traffic we can get information by enable HTTP trace log .

    Method 1. We can set in the request command.

``` java
    sh gateway <path-to-MGW-executable-jar-file> --b7a.http.tracelog.console=true
```

    Method 2. We can set in the micro-gw.conf file which is located in the `             <MGW-RUNTIME-HOME>/conf            ` directory.

``` java
    [b7a.http]
    [b7a.http.tracelog]
    console = true
```

    -   [Common exceptions and solutions](#Troubleshooting-Commonexceptionsandsolutions)
    -   [Runtime errors](#Troubleshooting-Runtimeerrors)
    -   [Toolkit errors](#Troubleshooting-Toolkiterrors)
    -   [Runtime error responses](#Troubleshooting-Runtimeerrorresponses)
    -   [Runtime error codes](#Troubleshooting-Runtimeerrorcodes)
    -   [Adding Debug Logs](#Troubleshooting-AddingDebugLogs)
    -   [How to enable debug log](#Troubleshooting-Howtoenabledebuglog)
    -   [How to enable HTTP trace log](#Troubleshooting-HowtoenableHTTPtracelog)


