# Quick Start Guide - Binary

Let's host your first API on WSO2 API Microgateway using the WSO2 Microgateway runtime.

### Before you begin...

Make sure to install and set up all the [installation prerequisites]({{base_path}}/install-and-setup/install-on-vm/).

### Objectives 

- Generate an executable using WSO2 API Microgateway Toolkit
- Run the API Microgateway
- Invoke the sample API


### Step 1 - Generate an executable using WSO2 API Microgateway Toolkit

#### Step 1.1 - Initialize a project

1.  Navigate to a preferred workspace folder using the command line. This is the location that is used to store the Microgateway project.
2.  Run the following command to create a project named "petstore" . This will create the folder structure for the artifacts to be included.  Use the `-a` option to include the API definition to the project as follows.

      Let's use the [Petstore sample open API definition](https://petstore.swagger.io/v2/swagger.json)
        
      ``` bash tab="Format"
      micro-gw init petstore -a <api definition path>
      ```
      
      ``` bash tab="Example"
      micro-gw init petstore -a https://raw.githubusercontent.com/wso2/product-microgateway/master/samples/petstore_v3.yaml
      ```

3.  The project is now initialized. A directory by the name "petstore" has been created within the directory where you executed the init command.

    !!! note
         The folder structure is similar to the following.
            ``` text
            petstore
            ├── api_definitions
                └──swagger.json
            ├── conf
            │   └── deployment-config.toml
            ├── extensions
            │   ├── extension_filter.bal
            │   ├── startup_extension.bal
            │   └── token_revocation_extension.bal
            ├── grpc_definitions
            ├── interceptors
            ├── lib
            ├── policies.yaml
            ```

    !!! info
        - For more information on the MGW project directory that gets created, see [Project Directory]({{base_path}}/reference/project-directory/) .
        - Check out the [troubleshooting]({{base_path}}/troubleshooting/troubleshooting/) guide if you run into an issue.

#### Step 1.2 - Build the project

1.  Use your command-line tool to navigate to where the project directory ("petstore") was created.  
    Execute the following command to build the project.
    A jar file ( `petstore/target/petstore.jar` ) is created to expose the API via WSO2 API  
    Microgateway.

      ``` bash
      micro-gw build petstore
      ```

    !!! info
        Here are the [FAQs]({{base_path}}/faqs/) .

### Step 2 - Run the API Microgateway

The executable jar file, which includes the API artifacts of the project, is used as input to the WSO2 API Microgateway runtime component in order to expose the APIs.

Follow the steps below to expose the APIs via WSO2 API Microgateway.

1.  Navigate to the `<MGW_HOME>/bin` directory. `<MGW_HOME>` is the installation directory of the WSO2 API Microgateway runtime

    ``` bash
    cd <MGW_HOME>/bin
    ```

2.  Execute the following command to start WSO2 API Microgateway.

    ```bash tab="Format"
    gateway <path-to-MGW-jar-file>
    ```

    ``` bash tab="Example"
    gateway /Users/kim/petstore-project/target/petstore-.jar
    ```

    ``` bash tab="Response"
    [ballerina/http] started HTTPS/WSS listener 0.0.0.0:9096
    [ballerina/http] started HTTP/WS listener 0.0.0.0:9090
    2020-08-14 12:32:17,211 INFO  [wso2/gateway/src/gateway/utils] - [APIGatewayListener] [-] HTTP listener is active on port 9090
    2020-08-14 12:32:17,213 INFO  [wso2/gateway/src/gateway/utils] - [APIGatewayListener] [-] HTTPS listener is active on port 9095
    [ballerina/http] started HTTPS/WSS listener 0.0.0.0:9095
    ```

### Step 3 - Invoke the sample API
#### Step 3.1 - Obtain a token
 --8<--
 ./includes/obtain-api-key.md
 
 --8<--
!!! info
        - You can obtain a JWT token from any third-party secure token service or via the WSO2 API Manager.
        - You can obtain an API Key easily from WSO2 API Microgateway. Follow the documentation to [Obtain an API Key]({{base_path}}/how-tos/security/api-key-security-token-service/).
        - Alternatively, you can also use an opaque token to invoke the API. 
     For more information, see the FAQs on [Working with Tokens]({{base_path}}/faqs/#working-with-tokens) .

#### Step 3.2 - Invoke the API

 --8<--
 ./includes/invoke-api-with-api-key.md
 
 --8<--
!!! note
    You were able to invoke the API resource `pet/findByStatus` using an API Key in `api_key` header because the resource is secured with API Key in API definition as follows. For more information, please refer to the documentation on [API Key Authentication]({{base_path}}/how-tos/security/api-authentication/api-key-authentication/) .
    ```yml
    "paths": {
      "/pet/findByStatus": {
        "get": {
          "security": [
            {
              "api_key": []
            }
          ]
        }
      }
    },
    "securityDefinitions": {
      "api_key": {
        "type": "apiKey",
        "name": "api_key",
        "in": "header"
      }
    }
    ```
