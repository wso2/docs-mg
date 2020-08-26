# Endpoint Retry and Timeouts

When microgateway connect with upstream backend services, it might require to handle the scenarios where backend
might be unavailable due to load or backend is taking lot of time to respond.

## Retry Config
There can be certain services that are extremely important, gateway as a proxy should retry to connect to the service in case of an failure happens during the first time.
Retry config specifies how gateway retries in the event of an error when connecting with the backend. The retry parameters can be specified in the `x-wso2-production-endpoint` or `x-wso2-sandbox-endpoint` open API extension

``` java
  x-wso2-production-endpoints:
    urls:
      - https://localhost:2380/v3
    advanceEndpointConfig:
      retryConfig:
        count: 1
        intervalInMillis: 1000
        backOffFactor: 1.2
        statusCodes:
          - 504
```

The following are the Endpoint Retry Configurations that you can configure for both the production and sandbox endpoints.
<table>
    <tbody>
        <tr class="odd">
            <td>
                <div class="content-wrapper">
                    <ul><li>
                    <p><strong>count:</strong> Number of retry attempts before giving up</p>
                    </li><li>
                    <p><strong>intervalInMillis:</strong> Retry interval in milliseconds.</p>
                    </li><li><p><strong>backOffFactor:</strong> Multiplier, which increases the retry interval exponentially.</p>
                    </li><li>
                    <p><strong>maxWaitIntervalInMillis:</strong> Maximum time of the retry interval in milliseconds. If the interval value  derived from `backOffFactor` is
                     greater then the max value, the `maxWaitIntervalInMillis` value will be selected as retry interval.</p>
                    </li><li>
                    <p><strong>statusCodes:</strong> HTTP response status codes which are considered as failures. If these codes were returned from backend retry will be attempted.</p>
                    </li></ul>
                </div>
            </td>
        </tr>
    </tbody>
</table> 

## Timeout Config
Certain services might take different amount of time to respond based on the load it is currently handling.
The Timeout config is used to gracefully handle network timeouts, which occur when used to connect upstream backend services via microgateway.
When `timeoutInMillis` config is specified microgateway will wait until specified time period before closing the connection with the backend and sending the error response to the client.
The timeout parameter can also be specified in the `x-wso2-production-endpoint` or `x-wso2-sandbox-endpoint` open API extension.

``` java
  x-wso2-production-endpoints:
    urls:
      - https://localhost:2380/v3
    advanceEndpointConfig:
      timeoutInMillis: 7000
```