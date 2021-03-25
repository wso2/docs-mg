# Health Check Endpoints

Microgateway exposes the health check endpoints for the above default ports. It sends a response about the availability of the services.

**Health check service call**

``` text tab="Example"
curl -k "https://localhost:9095/health"
curl -k "http://localhost:9090/health"
```

``` text tab="Response"
{status:healthy}
```