# Connect to an Endpoint via a Proxy

There can be certain scenarios where microgateway has to connect to the backend service via a proxy server. This can due to the reason that microgateway sits behind a firewall
and the egress (outgoing) traffic from microgateway is only allowed a via proxy server.
For those cases microgateway should be able to configure to connect to the backend service via the proxy server.

##Configure the proxy server
The common set of configurations that are used when connecting with upstream backend services can be found under the section
`httpClients` of the `<MICROGW_HOME>/conf/micro-gw.conf` file. `[httpClients.proxy]` section describes the proxy related configurations. The following config section should be added to 
`micro-gw.conf` file in order to configure the proxy server. 

```toml
[httpClients]
  [httpClients.proxy]
    enabled = true
    host = "localhost"
    port = 3128
    username = ""
    password = ""
    enableInternalServices = true
```

The configurations are described in the table below.

<table>
<thead>
<tr class="header">
<th>Property</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>enabled</td>
<td>Enables or disable via connecting a proxy server to the upstream endpoints.</td>
</td>
</tr>
<tr class="even">
<td>host</td>
<td>Hostname or IP address of the proxy server.</td>
</tr>
<tr class="odd">
<td>port</td>
<td>Port of the proxy server</td>
</tr>
<tr class="even">
<td>username</td>
<td>Username of a valid user if the proxy server is secured using basic authentication.</td>
</tr>
<tr class="odd">
<td>password</td>
<td>Passwrod of the corresponding user.</td>
</tr>
<tr class="even">
<td>enableInternalServices</td>
<td>Whether to enable the proxy server when communicating with analytics server, key manager, traffic manager and etc.

</td>
</tr>
</tbody>
</table>

!!! note
    <p> `enableInternalServices` config is applicable for following scenarios.</p> 
    
    1. The client used to communicate with key manager for token validation
    1. The client used to publish throttle events to the traffic manager component of WSO2 API Manager
    1. The client used to communicate with etcd server for service discovery
    1. The client used to communicate with WSO2 Analytics server to upload analytics event written files
    1. The client used to communicate with etcd server to get updates regarding revoked jwt tokens