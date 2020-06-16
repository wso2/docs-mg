# Importing a Group of APIs from WSO2 API Manager

!!! note
    This is only applicable if you wish to import your APIs from WSO2 API Manager.

It is possible to create a Microgateway distribution for a group of APIs that exist in WSO2 API Manager (WSO2 API-M), by simply importing the group of APIs based on a preferred Microgateway label, which has been assigned to the group of APIs in WSO2 API Manager. Similarly, you can also use a Microgateway label to import a single published API from WSO2 API Manager as well if required.

Follow the instructions below to create a Microgateway distribution for a group of APIs that you have imported from WSO2 API Manager.

### Step 1 - Follow the installation prerequisites

Make sure to carry out all the [installation prerequisites({{base_path}}/install-and-setup/install-on-vm/).

### Step 2 - Assign an API to an API Microgateway

Create and attach a Microgateway to an API. For more information, see one of the following links based on the WSO2 API Manager version that you are using.

-   Follow the instructions in the [Grouping APIs with Microgateway Labels](https://apim.docs.wso2.com/en/latest/Learn/APIMicrogateway/grouping-apis-with-labels/) page in the **WSO2 API Manager 3.0.0** documentation.
-   Follow the instructions under the [Creating and attaching a Microgateway to an API](https://docs.wso2.com/display/AM260/Grouping+APIs+with+Labels#GroupingAPIswithLabels-CreatingandattachingaMicrogatewaytoanAPI) section in the **WSO2 API Manager 2.6.0** documentation.

### Step 3 - Generate a Microgateway project

Follow the instructions below to generate a Microgateway project for a group of APIs that correspond to a specific Microgateway label.

1.  Initialize an API Microgateway project (e.g., marketing\_project).
    Navigate to a preferred folder where you want to create the Microgateway project. Thereafter, run the following command.

    ``` java tab="Format"
        micro-gw init <project-name>
    ```

    ``` java tab="Example"
        micro-gw init marketing_project
    ```

    ``` java tab="Response"
        Project 'marketing_project' is initialized successfully.
    ```

2.  Import the APIs, which are published in WSO2 API Manager, that correspond to a specific Microgateway label.

    ``` java tab="Format"
    micro-gw import -l <label> <project-name> 
    ```

    ``` java tab="Example"
    micro-gw import -l MARKETING_STORE marketing_project
    ```
    
    ``` tab="Response"
    WSO2 API Microgateway Toolkit prompts for the following.
   
    Enter Username: 
    admin
    Enter Password for admin: 
    Enter APIM base URL [https://localhost:9443]: 
    You are using REST version - v0.14 of API Manager. (If you want to change this, go to <MICROGW_HOME>/conf/toolkit-config.toml)
    Enter Trust store location: [lib/platform/bre/security/ballerinaTruststore.p12]
    Enter Trust store password: [ use default? ]
    ID for API 30e623704c5c5479b7c0d9ab78e965df02c1610401e37cbd557e6353e3191c76
    
        
    - Username and Password - Enter your user credentials. The username and password should correspond to a valid user in WSO2 API Manager
    - APIM base URL - This is the WSO2 API Manager URL. If you want to use the default value, click enter.
    - Trust store location and password of WSO2 API Microgateway Toolkit - If you want to use the default value, click enter.
    ```
    
    After the APIs are imported, you can find the auto-generated OpenAPI definitions in the `<MGW-project>/gen/<OpenAPI_definition-file-name>.JSON ` directory of the project (e.g., `           /marketing_project/gen/30e623704c5c5479b7c0d9ab78e965df02c1610401e37cbd557e6353e3191c76swagger.json` )

### Step 4 - Build the Microgateway

Build the WSO2 API Microgateway project (e.g.,marketing\_project).

1.  Navigate to the `           <MGW_HOME>/bin          ` directory and run the following command.

    ``` java tab="Format"
    micro-gw build <project-name>
    ```

    ``` java tab="Example"
    micro-gw build marketing_project
    ```

    ``` java tab="Response"
    Compiling source
    src:0.0.0

    Running tests
    src:0.0.0
    No tests found

    Generating executable
    ./target//Users/kim/Downloads/TestProj/marketing_project/target/marketing_project.balx
    ```

    This creates an executable file ( `/marketing_project/target/marketing_project.balx` ) that you can use to expose the group of APIs via WSO2 API Microgateway.

### Step 5 - Run the Microgateway

Start WSO2 API Microgateway.

``` java tab="Format - Linux/Mac OS"
gateway <path-to-MGW-executable-file>
```

``` java tab="Format - Windows OS"
.\gateway.bat <path-to-MGW-executable-file>
```

``` java tab="Example - Linux/Mac OS"
gateway /Users/kim/Downloads/TestProj/marketing_project/target/marketing_project.balx
```

WSO2 API Microgateway starts for the APIs, which are grouped with the `MARKETING_STORE` label.

``` java
ballerina: HTTP access log enabled
[ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9096
[ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9095
[ballerina/http] started HTTP/WS endpoint 0.0.0.0:9090
2019-05-30 18:09:32,540 INFO  [wso2/gateway] - HTTPS listener is active on port 9095 
2019-05-30 18:09:32,541 INFO  [wso2/gateway] - HTTP listener is active on port 9090 
```
