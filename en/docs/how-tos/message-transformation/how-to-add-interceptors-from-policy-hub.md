# How to add interceptors from Policy Hub

In this guide we will see how we can engage a shared interceptor using [policy hub]({{base_path}}/concepts/the-policy-hub/), in three simple steps. First check our [quick start guide]({{base_path}}/getting-started/quick-start-guide/quick-start-guide-binary/) to understand the basic commands of Microgateway.

## Initialize and setup a Microgateway Project.

Run below command to initialize a new microgateway project with an openapi definition.

**Init Project**

``` bash
micro-gw init petstore -a https://raw.githubusercontent.com/wso2/product-microgateway/v3.0.4/samples/petstore_basic.yaml
```

## Include the extensions in the OpenAPI definitions using the policy sharing syntax

Now we have a project initilized for petstore API. Next step is to add required extensions to engage the interceptors from Policy hub. For this we can use `x-wso2-request-interceptor` and `x-wso2-response-interceptor` extensions. To see complete usage details of these extensions, check [adding interceptors]({{base_path}}/how-tos/message-transformation/how-to-add-interceptors-from-policy-hub/) .

1.  Open the `petstore_basic.yaml` inside `petstore/api_definitions` directory.
2.  Before `x-wso2-basePath: /petstore/v1` extension, add `x-wso2-request-interceptor: prasadhi/Interceptors/0.1.0:validateRequest`

    !!! info
        Interceptor reference should be provided in following format.`<ballerina_org>/<ballerina_module>/<ballerina_module_version>:<interceptor_function>` .
        If version is not provided, latest version of the specified module will be engaged.

## Build and run Microgateway Project

Lets build and run the project to see if new interceptors are working.

1.  Build the petstore project.

    ``` bash
    micro-gw build petstore
    ```

2.  Start the gateway.

    ``` bash
    gateway <project_path>/target/petstore.jar
    ```

3.  Send a request to petstore API. This request should fail since we don't mention the required header X-API-Key which is validated inside the prasadhi/Interceptors:validateRequest interceptor.

    ``` bash
    curl -k -H "Authorization: Bearer $TOKEN" -X GET https://localhost:9095/petstore/v1/pet/1
    ```

4.  Now send the correct request to see if it gives the correct response

    ``` bash
    curl -k -H "Authorization: Bearer $TOKEN" -H "X-API-Key: apikey-value" -X GET https://localhost:9095/petstore/v1/pet/1
    ```

Thats how you could configure Microgateway to use shared interceptors through Policy Hub. To see more detailed information on this feature checkout the feature documentation at [the policy hub]({{base_path}}/concepts/the-policy-hub/) .


