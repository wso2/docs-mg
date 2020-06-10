# Overview

-   [Introduction](#Overview-Introduction)
-   [How the API Microgateway works](#Overview-HowtheAPIMicrogatewayworks)
-   [API Microgateway vs API Gateway](#Overview-APIMicrogatewayvsAPIGateway)
    -   [Design and deployment comparison](#Overview-Designanddeploymentcomparison)
    -   [Security Comparison](#Overview-SecurityComparison)
    -   [Feature Comparison](#Overview-FeatureComparison)

### Introduction

The WSO2 API Microgateway is a lightweight message processor for APIs. The API Microgateway is used for message security, transport security, routing, and other common API Management related quality of services. It can process incoming and outgoing messages while collecting information required for usage metering and throttling capabilities. The API Microgateway natively supports scaling in highly decentralized environments including microservice architecture. An immutable, ephemeral Microgateway fits well with the microservice architecture. The API Microgateway is also capable of operating in lockdown environments such as IoT devices since connectivity from the API Microgateway to the API Management system is not mandatory.

Setting up API Microgateways close to your backend servers and on-premise deployments not only reduces network latency but also adds additional security as you can run it in a fully controlled environment with a selected set of APIs. You can run the API Microgateway in a regional data center, in the same server instance as your service implementation, or even in your local workstation.

In summary, the API Microgateway is a simplified lightweight version of the API Gateway. However, the API Microgateway only supports a subset of the capabilities of the API Gateway.

### How the API Microgateway works

![API Microgateway overview]({{base_path}}/assets/img/api-mg-overview.png)

WSO2 API Microgateway comes with a toolkit that is responsible for API Microgateway related development tasks. This command line tool helps devlopers to create  API Microgateway projects, which are used later to build the executable for API Microgateway runtime component.

After setting up the API Mircogateway CLI, the user can initiate the API Microgateway project and can add any number of open APIs definitions (with WSO2 specific custom open API extensions) to the project, which are exposed via the runtime after the API Microgateway distribution is built using the CLI tool.

When you issue a build command, the project build is triggered and an executable is created with API definitions. You can directly run this executable on the Cloud, on-premise, or on a local workstation as required using the API Microgateway runtime component.

For details on how analytics works with API Microgateway and how to configure Analytics, see [Analytics for API Microgateway]({{base_path}}/how-tos/analytics-for-microgateway/) .

### API Microgateway vs API Gateway

API Microgateway and the "traditional" API Gateway can be compared in different aspects as below.  The decision to select either of the gateways is highly dependent on the architecture, design, and deployment. Both gateways mostly have similar features and functionalities, but works differently in order to cater for the purpose it was designed to serve.

###### Design and deployment comparison

<table>
<tbody>
<tr class="odd">
<td>Design/ Deployment</td>
<td><p>API Microgateway</p></td>
<td><p>API Gateway</p></td>
</tr>
<tr class="even">
<td><p>Architecture</p></td>
<td><p>Designed for microservices</p></td>
<td><p>Designed for monolith</p></td>
</tr>
<tr class="odd">
<td><p>Horizontal Scaling<br />
</p></td>
<td><p>Scales independently as the runtime does not have a direct dependency on other components. Security and throttling validations are done within the Microgateway.<br />
</p></td>
<td><p>Scaling can be done with other components. For example, the traffic manager (one node per dedicated cluster of gateway nodes ) and key manager can be scaled along with the gateway.<br />
</p></td>
</tr>
<tr class="even">
<td><p>Deployment distribution</p></td>
<td><p>Decentralized</p></td>
<td><p>Centralized<br />
</p></td>
</tr>
<tr class="odd">
<td><p>Runtime footprint</p></td>
<td><p>Lightweight and can run on computers with low performance.</p></td>
<td><p>Designed to run on high performing computers with high load (memory and high CPUs )</p></td>
</tr>
<tr class="even">
<td><p>Isolated lockdown environments</p></td>
<td><p>Designed to work in a network isolated environment</p></td>
<td><p>Limited functionalities(affect on throttling/ analytics)</p></td>
</tr>
<tr class="odd">
<td><p>Cloud ready</p></td>
<td><p>Yes</p></td>
<td><p>Yes</p></td>
</tr>
<tr class="even">
<td><p>Automated API CI/CD flows</p></td>
<td><p>Supported with CLI tools</p></td>
<td><p>Supported with CLI tools</p></td>
</tr>
<tr class="odd">
<td><p>Update APIs</p></td>
<td><p>Immutable gateways. Rebuild the gateway upon updating the API</p></td>
<td><p>Mutable gateway, updating APIs supported</p></td>
</tr>
</tbody>
</table>

###### Security Comparison

API Gateway and API Microgateway both support different security mechanisms.

|                              |                                                                       |                                                                       |
|------------------------------|-----------------------------------------------------------------------|-----------------------------------------------------------------------|
| Security Mechanism           | API Microgateway                                                      | API Gateway                                                           |
| Oauth2 opaque tokens         | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick} | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick} |
| Oauth2 self-contained tokens | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick} | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick} |
| Mutual SSL                   | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick} | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick} |
| Basic Auth                   | Config based supported, can not plug user stores                      | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick} |
| API Keys                     | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick} | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick} |

###### 
Feature Comparison

|                                                    |                                                                         |                                                                         |
|----------------------------------------------------|-------------------------------------------------------------------------|-------------------------------------------------------------------------|
| Feature                                            | API Microgateway                                                        | API Gateway                                                             |
| SOAP backends                                      | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   |
| REST APIs                                          | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   |
| JMS backends                                       | ![(error)]({{base_path}}/assets/img/getting-started/error.svg){.emoticon .emoticon-cross} | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   |
| GraphQL APIs                                       | ![(error)]({{base_path}}/assets/img/getting-started/error.svg){.emoticon .emoticon-cross} | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   |
| GRPC Services                                      | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   | ![(error)]({{base_path}}/assets/img/getting-started/error.svg){.emoticon .emoticon-cross} |
| Web socket APIs                                    | ![(error)]({{base_path}}/assets/img/getting-started/error.svg){.emoticon .emoticon-cross} | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   |
| Custom mediation/transformation                    | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   |
| Advance throttling(bandwidth, headers, IP address) | ![(error)]({{base_path}}/assets/img/getting-started/error.svg){.emoticon .emoticon-cross} | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   |
| Schema validation                                  | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   |
| JWT revocation                                     | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   |
| Per resource Endpoints                             | ![(tick)]({{base_path}}/assets/img/getting-started/check.svg){.emoticon .emoticon-tick}   | ![(error)]({{base_path}}/assets/img/getting-started/error.svg){.emoticon .emoticon-cross} |

!!! info
    What's Next?
    [Microgateway Quick Start Guide]({{base_path}}/getting-started/quick-start-guide/quick-start-guide-overview/)


