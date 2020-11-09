<h1>FAQs</h1>

[TOC]

### Using WSO2 Gateways

##### When should I use WSO2 API Microgateway instead of WSO2 API Gateway?

-   When you need to run a gateway in a lockdown environment or in an offline mode with no connection to the API Management system.

-   When you need to run a specific subset of APIs in a controlled environment (e.g., in private jet gateway mode), when you are faced with unusual traffic patterns on an API and need to scale it.

-   When you need to independently scale only a specific subset of APIs based on traffic patterns.

-   When you are either using an internal or on-premise API Gateway deployment, where the service instances and delivery channels (consumers) reside on the same network. In this situation, you can easily reduce your latency by having your Microgateway in close proximity to your backend server.
-   When you are using a sidecar deployment pattern and you need to run a Gateway in sidecar mode together with an application server within a single runtime or within the same pod in Kubernetes (k8s).

##### When should I use WSO2 API Gateway instead of WSO2 API Microgateway?

-   When you need to run an API Gateway using a centralized approach so that the gateway can handle requests for multiple different APIs and backend servers.

### Prerequisites

##### How can I run the toolkit command from any directory location?

You need to append the full path of the `/bin` folder of the extracted WSO2 API Microgateway toolkit (WSO2 MGW-TK) distribution to the PATH environment variable as follows to be able to run the WSO2 API Microgateway toolkit command from any directory location. For more information, see [Install on VM]({{base_path}}/install-and-setup/install-on-vm/).

##### How can I run the Microgateway distribution command from any directory location?

You need to append the full path of the `/bin` folder of the extracted WSO2 API Microgateway distribution to the PATH environment variable as follows to be able to run the WSO2 API Microgateway distribution command from any directory location. For more information, see [Install on VM](_Install_on_VM_).

### Compatibility

##### What versions of WSO2 API-M are compatible with WSO2 API Microgateway 3.2.0?

-   [WSO2 API Manager 3.2.0](https://apim.docs.wso2.com/en/3.2.0/)
-   [WSO2 API Manager 3.1.0](https://apim.docs.wso2.com/en/3.1.0/)
-   [WSO2 API Manager 3.0.0](https://apim.docs.wso2.com/en/3.0.0/)
-   [WSO2 API Manager 2.6.0](https://docs.wso2.com/display/AM260/WSO2+API+Manager+Documentation)
-   [WSO2 API Manager 2.5.0](https://docs.wso2.com/display/AM250/WSO2+API+Manager+Documentation)

### MGW commands

##### Can I run the Microgateway commands without appending the paths?

Yes.  If you have not appended the paths, you have to run the MGW commands in the following manner.
Instead of using the ` micro-gw` command, navigate to the `<MGWTK_HOME>/bin` directory and run the following command.

``` text
./micro-gw
```

Always navigate to the `<MGW_HOME>/bin` directory and run the following command.

``` text
./gateway
```

##### How can I reset the Microgateway CLI tool?

When you run the Microgateway CLI tool for the first time, it preserves all the information (i.e., your username, API-M base URL, TrustStore location and the TrustStore password) with the exception of your password, for later use. If you want to reset the Microgateway CLI tool in order to clear all the data that has been preserved by the Microgateway CLI tool, you can use the `reset` argument as follows. Note that you can invoke the Microgateway CLI tool reset command from any directory in the system.

``` text
micro-gw reset
```

##### How can I run the Microgateway?

You can use either one of the following command formats to start WSO2 API Microgateway based on your OS.

``` text tab="Format"
gateway <path-to-MGW-jar-file>
```

``` text tab="Example"
gateway /Users/kim/Downloads/TestProj/petstore-project/target/petstore-project.jar
```

When WSO2 Microgateway starts successfully, the following log is printed on the console.

``` text
ballerina: HTTP access log enabled
[ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9096
[ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9095
[ballerina/http] started HTTP/WS endpoint 0.0.0.0:9090
2019-05-30 18:09:32,540 INFO  [wso2/gateway] - HTTPS listener is active on port 9095 
2019-05-30 18:09:32,541 INFO  [wso2/gateway] - HTTP listener is active on port 9090 
```

### Building a Microgateway project

##### What if I change the APIs or policies after I have built and deployed the project on WSO2 API Microgateway?

The gateway runtime is immutable. Therefore, if you change the APIs or policies after you have built and deployed the project on WSO2 API Microgateway, you need to rebuild the Microgateway project to capture the changes.

### Deployment

##### How can I deploy a Docker image on a Kubernetes environment?

You can use one of the following options for this purpose.

-   Push the Docker image to the DockerHub or to a private Docker registry.

!!! note
    If you use this approach and if you decide to change your Docker image name, you need to change the image name in the `<MGW-project>/conf/deployment-config.yaml` file as well.

-   SCP the image to the Kubernetes nodes

### Testing the Microgateway

##### How can I test the Microgateway?

You can invoke the API through the Microgateway using the following cURL command in order to test the WSO2 API Microgateway. By default, the Microgateway serves the HTTPS/WSS request via the 9095 port.

``` text tab="Format"
curl -v -k https://<hostname-of-server-running-the-Microgateway>:9095/<API-name>/<API-version>/<API-resource-path> -H "Authorization: Bearer <base64-encoded-client-id-and-secret>"
```

``` text tab="Example"
curl -v -k https://mg.knnect.com:9095/wolaa/1.1.1/getmeall -H "Authorization: Bearer 104cd62f-a8ab-3089-bc12-f7cc36e73e77"
```

### Working with Tokens

##### What is a signed JWT token?

A signed JWT token is a self-contained access token that can be validated by the Microgateway itself without connecting to any external party Key Manager. In contrast, a typical OAuth2 token needs to be validated by connecting to the Key Manager. This is one of the key advantages of the Microgateway if you need to run it in offline mode.

##### What are the ways in which I can obtain a JWT Token?

A JWT token can be obtained via one of the following methods:

-   From any third party secure token service.
The public certificate of the token service, which is used to sign the token, should be added to the trust store of WSO2 API Microgateway. The JWT should have the claims **iss, sub** in order for the API Microgateway to validate the JWT.

-   From WSO2 API Manager
For more information, see [Generating a JWT token from the API Developer Portal]({{apim_path}}/learn/api-security/oauth2/access-token-types/jwt-tokens/#using-jwt-access-tokens).

##### Can I use an OAuth2 token instead of a JWT token?

Yes, you can use an OAuth2 token instead of a JWT token. WSO2 API Microgateway can be configured to connect to a key manager (external or to the WSO2 API Manager).
For more information please refer documentation on [configuring key manager when using an OAuth2token]({{base_path}}/how-tos/security/api-authentication/configuring-an-external-key-manager/).

!!! note
    For the WSO2 API Manager, You can use an OAuth2 token instead of a JWT only if the API that you are working with is also available Publisher as well. You need a valid subscription for the API from the WSO2 API Manager Developer Portal as well.

##### How can I use an OAuth2 token to invoke an API?

Invoking the API using an OAuth2 token is similar to a usual API invocation, which uses the standard WSO2 API Manager Gateway by generating an access token from the API Developer Portal.

``` text tab="Format"
curl -k -i -H "Authorization: Bearer <OAUTH_token>" <API_URL>
```

``` text tab="Example"
curl -k -i -H "Authorization: Bearer 20ac019e-16a7-3ba5-8940-7d42c7e56326" https://localhost:9095/petstore/v1/pet/1
```