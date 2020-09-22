# Importing a Single API from WSO2 API Manager

Follow the instructions below to import a single API from WSO2 API Manager and create a Microgateway distribution for it.

### Step 1 - Follow the installation prerequisites

Make sure to carry out all the [installation prerequisites]({{base_path}}/install-and-setup/install-on-vm/) .

### Step 2 - Create a Microgateway project

Let's initialize an API Microgateway project named marketing\_project. Navigate to a preferred folder where you want to create the Microgateway project. Thereafter, run the following command.

``` text tab="Format"
micro-gw init <project-name>
```

``` text tab="Example"
micro-gw init marketing_project
```

``` text tab="Response"
Project 'marketing_project' is initialized successfully.
```

### Step 3 - Build the Microgateway project

1.  Import the API, which is published in WSO2 API Manager.

    ``` text tab="Format"
    micro-gw import -a <API_name> -v <version> <project_name> 
    ```

    ``` text tab="Example"
    micro-gw import -a PizzaShackAPI -v 1.0.0 marketing_project
    ```

    ``` text tab="Response"
    WSO2 API Microgateway Toolkit prompts for the following.
    
    Enter Username: 
    admin
    Enter Password for admin: 
    Enter APIM base URL [https://localhost:9443]: 
    You are using REST version - v1.2 and dynamic client registration version - v0.17 of API Manager. (If you want to change this, go to <MGW-TK_HOME>/conf/toolkit-config.toml)
    Enter Trust store location: [lib/platform/bre/security/ballerinaTruststore.p12]
    Enter Trust store password: [ use default? ]
    ID for API with name PizzaShackAPI : api_30e623704c5c5479b7c0d9ab78e965df02c1610401e37cbd557e6353e3191c76

    -  Username and Password - Enter your user credentials. The username and password should correspond to a valid user in WSO2 API Manager
    -  APIM base URL - This is the WSO2 API Manager URL. If you want to use the default value, click enter.
    -  Trust store location and password of WSO2 API Microgateway Toolkit - If you want to use the default value, click enter.
    ```

    After the API is imported, you can find the auto-generated Swagger definition in the `<MGW-project>/gen/api_definitions/<Swagger-file-name>.json` directory of the project (e.g., `/marketing_project/gen/api_definitions/api_30e623704c5c5479b7c0d9ab78e965df02c1610401e37cbd557e6353e3191c76.json` )

    !!! info
        The Microgateway does not support APIs with synapse mediation extension.
        If the API in the API manager has synapse mediations as custom mediations, then those mediations will not be supported by the microgateway. The logic in custom mediation will have to be reimplemented using the [microgateway message transformation feature.](/how-tos/message-transformation/message-transformation-overview/)

2.  Build the WSO2 API Microgateway project (e.g.,marketing\_project).
    Navigate to the `<MGW_HOME>/bin` directory and run the following command.

    ``` text tab="Format"
    micro-gw build <project-name>
    ```

    ``` text tab="Example"
    micro-gw build marketing_project
    ```

    ``` text tab="Response"
    Compiling source
        wso2/marketing_project:3.2.0

    Creating balos
        target/balo/marketing_project-2020r1-java8-3.2.0.balo

    Running tests
        wso2/marketing_project:3.2.0
        No tests found


    Generating executables
        target/bin/marketing_project.jar

    BUILD SUCCESSFUL
    Target: /Users/Downloads/TestProj/marketing_project/target/marketing_project.jar
    ```

    This creates a jar file ( `/marketing_project/target/marketing_project.jar` ) that you can use to expose the API via WSO2 API Microgateway runtime.

### Step 4 - Run the Microgateway

Start WSO2 API Microgateway as follows:

 ``` text tab="Format"
 gateway <path-to-MGW-jar-file>
 ```

 ``` text tab="Example"
 gateway /Users/kim/Downloads/TestProj/marketing_project/target/marketing_project.jar 
 ```

WSO2 API Microgateway starts for the API.

 ``` text
 ballerina: HTTP access log enabled
 [ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9096
 [ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9095
 [ballerina/http] started HTTP/WS endpoint 0.0.0.0:9090
 2019-05-30 18:09:32,540 INFO  [wso2/gateway] - HTTPS listener is active on port 9095 
 2019-05-30 18:09:32,541 INFO  [wso2/gateway] - HTTP listener is active on port 9090 
 ```
