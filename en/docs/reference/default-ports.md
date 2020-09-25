# Default Ports

The following are the default product ports used when working with WSO2 API Microgateway.

<table>
<thead>
<tr class="header">
<th>Port</th>
<th>Default value</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td><p>httpPort</p></td>
<td><p>9090</p></td>
<td>HTTP port that is used to make APIs available to the outside.</td>
</tr>
<tr>
<td>httpsPort</td>
<td>9095</td>
<td>HTTPS port that is used to make APIs available to the outside.<br />
And also this port exposes the following endpoints of the Key Manager, which is a proxy to the Key Manager via the gateway.
<ul>
<li><code>               /token              </code></li>
<li><code>               /authorize              </code></li>
<li><code>               /revoke              </code></li>
<li><code>               /userinfo              </code></li>
<li><code>               /apikey              </code></li>
</ul></td>
</tr>
</table>

## Health Check Endpoints

Microgateway exposes the health check endpoints for the above default ports. It sends a response about the availability of the services.

**Health check service call**

``` text tab="Example"
curl -k "https://localhost:9095/health"
curl -k "http://localhost:9090/health"
```

``` text tab="Response"
{status:healthy}
```
