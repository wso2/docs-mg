# Circuit Breaker
In case of a failure attempt when connecting with back end , circuit breaker config specifies 
how to respond to subsequent requests to same back end. Based on the config, the circuit 
will be remain close allowing back end to be queried, or circuit will be open where gateway 
will not contact backend and respond from gateway itself.
This kind of mechanism is required to prevent overloading backend services during a high load.

``` java
  x-wso2-production-endpoints:
    urls:
      - https://localhost:2380/v3
    advanceEndpointConfig:
      circuitBreaker:
        rollingWindow:
          requestVolumeThreshold: 1
          timeWindowInMillis: 60000
          bucketSizeInMillis: 2000
        failureThreshold: 0.2
        resetTimeInMillis: 5000
        statusCodes:
          - 500
```

<table>
    <tbody>
        <tr class="odd">
            <td>
                <div class="content-wrapper">
                    <ul><li>
                    <p><strong>rollingWindow:</strong> Circuit breaker looks for errors across a rolling window time</p>
                        <ul><li><p><strong>requestVolumeThreshold:</strong> Minimum number of requests in a `rollingWindow` that will trip the circuit.</p>
                        </li><li><p><strong>timeWindowInMillis:</strong> Time period in milliseconds for which the failure threshold is calculated.</p>
                        </li><li><p><strong>bucketSizeInMillis:</strong> The granularity at which the time window slides. This is measured in milliseconds..</p>
                        </li></ul>
                    </li><li>
                    <p><strong>failureThreshold:</strong> The threshold for request failures. When this threshold exceeds, the circuit trips. The threshold should be a value between 0 and 1.</p>
                    </li><li>
                    <p><strong>resetTimeInMillis:</strong> The time period(in milliseconds) to wait before attempting to make another request to the upstream service</p>
                    </li><li>
                    <p><strong>statusCodes:</strong> Array of HTTP response status codes which are considered as failures. If these codes were returned from backend, retry will be attempted.</p>
                    </li></ul>
                </div>
            </td>
        </tr>
    </tbody>
</table> 
