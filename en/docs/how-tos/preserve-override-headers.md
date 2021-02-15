# Preserve/Override Headers

By default, some headers of the request and the response will be overridden in the API Microgateway. 
If you have a requirement to preserve/override the request headers to be sent to the back-end or to preserve/override response headers to be received from the back-end.

- [Preserve Request and Response Headers](#preserve-request-and-response-headers)
- [Override Request and Response Headers](#override-request-and-response-headers)

!!! note
    This feature is available from WUM timestamp **1612370612556** (03/02/2021) of API Microgateway Toolkit 3.2.0.

### Preserve Request and Response Headers

You have the capability to preserve the headers by adding the following configuration to the `<MICRO-GW-RUNTIME_HOME>/conf/micro-gw.conf` file. 
In order to preserve the header, you have to set the `preserveHeader` configuration to `true`.
    
```toml
[server]
  [[server.headerConf]]
    headerName = "User-Agent"
    preserveHeader = true
```

### Override Request and Response Headers

You have the capability to override the headers by adding the following configuration to the `<MICRO-GW-RUNTIME_HOME>/conf/micro-gw.conf` file. 
In order to override the header, you have to set the `preserveHeader` configuration to `false` and add the desired header to `overrideValue`.

```toml
[server]
  [[server.headerConf]]
    headerName = "User-Agent"
    preserveHeader = false
    overrideValue = "ballerina"
```
  <table>
    <thead>
    <tr>
    <th style="width: 30%">Configuration</th>
    <th style="width: 35%">Description</th>
    <th style="width: 25%">Default</th>
    </tr>
    </thead>
    <tbody>
    <tr>
    <td>`headerName`</td>
    <td>Name of the header to be preserved/overridden.</td>
    <td>-</td>
    </tr>
    <tr>
    <td>`preserveHeader`</td>
    <td>Preserve/override the header (true to preserve the header/false to override the header).</td>
    <td>`true`</td>
    </tr>
    <tr>
    <td>`overrideValue`</td>
    <td>The value to override the header (this value will not be considered if the `preserveHeader` value is true).</td>
    <td>`ballerina`</td>
    </tr>
    </tbody>
  </table>

!!! note
    **User-Agent** header in the request and the **server** header in the response can be considered as two special cases. **User-Agent** header in the request will not be preserved and **server** header in the response will be preserved by 
    default. 
    
    You can preserve the **User-Agent** header in the request by adding a configuration and setting `preserveHeader` 
    to true. If you have a requirement to override the **server** header in the response, you can set `preserveHeader` to 
    false and add the desired string to `overrideValue` configuration.
