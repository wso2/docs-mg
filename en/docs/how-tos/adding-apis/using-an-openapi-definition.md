# Using an OpenAPI Definition

Let's use a developer-first approach and host your API, which uses an OpenAPI definition, on WSO2 API Microgateway using the WSO2 Microgateway runtime.

-   [Step 1 - Generate an executable using WSO2 API Microgateway Toolkit](#UsinganOpenAPIDefinition-Step1-GenerateanexecutableusingWSO2APIMicrogatewayToolkit)
    -   [Step 1.1 - Initialize a project](#UsinganOpenAPIDefinition-Step1.1-Initializeaproject)
    -   [Step 1.2 - Build the project](#UsinganOpenAPIDefinition-Step1.2-Buildtheproject)
-   [Step 2 - Run the API Microgateway](#UsinganOpenAPIDefinition-Step2-RuntheAPIMicrogateway)

### Step 1 - Generate an executable using WSO2 API Microgateway Toolkit

##### Step 1.1 - Initialize a project

1.  Navigate to a preferred workspace folder using the command line to set the location that is used to store the Microgateway project.
2.  Create a project named "petstore" by running the following command. This will create the folder structure for the artifacts to be included.

    ``` java
        micro-gw init petstore
    ```

        The project is now initialized. A directory with the name "petstore" has been created within the directory where you executed the init command.

        !!! info
        More Information
        -   For more information on the MGW project directory that gets created, see [Project Directory](_Project_Directory_) .

##### Step 1.2 - Build the project

1.  Add the OpenAPI definition to the project.
    Navigate to the `           /petstore/api_definitions          ` directory. Add the API definition(s) to th `           is          ` directory. Let's use the [Petstore sample open API definition](https://github.com/wso2/product-microgateway/blob/master/samples/petstore_basic.yaml) .

    !!! info
        If you open the Open API Specification (OAS) document of the Petstore API using a text editor, you will notice the resource (path) definitions of the API following the standard Open API Specification. You will also see the target server (back-end) URL of the API under the "x-wso2-production-endpoints" OAS vendor extension. WSO2 Microgateway uses this interface definition and the target server URL to generate a gateway proxy for the Petstore API.

2.  Use your command line tool to navigate to where the project directory ("petstore") was created and execute the following command to build the project
    An executable file ( `           /petstore/target/petstore.balx          ` ) is created to expose the API via WSO2 API Microgateway.

    ``` java
        micro-gw build petstore
    ```

        ### Step 2 - Run the API Microgateway

        The executable file ( `         .balx        ` ), which includes the API artifacts of the project, is used as input to the WSO2 API Microgateway runtime component for the purpose of exposing the APIs. Follow the steps below to expose the APIs via WSO2 API Microgateway.

        1.  Execute the following command to start WSO2 API Microgateway.

        -   [**Format**](#format-store-key-value)
        -   [**Example**](#example-store-key-value)
        -   [**Response**](#response-store-key-value)

        **Format - Linux/Mac OS**

    ``` java
        gateway <path-to-MGW-executable-file>
    ```

        **Format - Windows OS**

    ``` java
        .\gateway.bat <path-to-MGW-executable-file>
    ```

        **Example**

    ``` java
        gateway /Users/kim/Downloads/TestProj/petstore-project/target/petstore-project.jar
    ```

        When WSO2 Microgateway starts successfully, the following log is printed on the console.

    ``` java
        ballerina: HTTP access log enabled
        [ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9096
        [ballerina/http] started HTTPS/WSS endpoint 0.0.0.0:9095
        [ballerina/http] started HTTP/WS endpoint 0.0.0.0:9090
        2019-05-30 18:09:32,540 INFO  [wso2/gateway] - HTTPS listener is active on port 9095 
        2019-05-30 18:09:32,541 INFO  [wso2/gateway] - HTTP listener is active on port 9090 
    ```


