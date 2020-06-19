# Adding Throttling Policies

A rate limiting policy can be specified for a resource or for an API using the `x-wso2-throttling-tier` OpenAPI extension. You need to define the policy (or policies) in the `policies.yaml` file, which is in the `<MGW-project>` directory. A set of policies are available by default. However, you can add custom policies to the file and refer to them by name via the OpenAPI definition. If you simultaneously define a throttling policy at the API level **and** at the resource level, the resource level policy will be applied as the resource level policy will override the API level policy.

The following sub sections explain as to how you can work with OpenAPI extensions to add custom API level and/or resource level throttling policies.

### Defining a throttling policy

You need to define the throttling policy (or policies) in the `policies.yaml` file, which is in the `<MGW-project>` directory. The following is a sample on how you can define a throttling policy named `10kPerMin` in the `policies.yaml` file. For more information a sample `policy.yaml` can be found [here](https://github.com/wso2/product-microgateway/blob/master/samples/policies_sample.yaml) .

**Defining a throttling policy**

``` java
- 10kPerMin:
     count: 10000
     unitTime: 1
     timeUnit: min
```

### Adding an API level throttling policy

**API level throttling policy**

``` java
x-wso2-basePath: /petstore/v1
x-wso2-throttling-tier: 10kPerMin
x-wso2-production-endpoints:
   urls:
     - https://petstore.swagger.io/v2
```

### Adding a resource level throttling policy

**Resource level throttling policy**

``` java
paths:
  "/pet/findByStatus":
    get:
      tags:
      - pet
      summary: Finds Pets by status
      description: Multiple status values can be provided with comma separated strings
      operationId: findPetsByStatus
      x-wso2-throttling-tier: 10kPerMin
```
