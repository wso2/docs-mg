# Install on Docker

WSO2 API Microgateway has a runtime Docker image in which you can provide executable runtime artifacts as a mount. Let's follow the instructions below to see how we can create Docker image by including the runtime executable artifacts, so that you do not have to mount the artifacts from outside. This approach uses the `         deployment-config.toml        ` file as an input to the WSO2 API Microgateway Toolkit, which generates the Docker images from the WSO2 API Microgateway base image by including all the API runtime artifacts as well.

Let's deploy WSO2 API Microgateway in Docker.

-   [Step 1 - Follow the installation prerequisites](#InstallonDocker-Step1-Followtheinstallationprerequisites)
-   [Step 2 - Create a microgateway project](#InstallonDocker-Step2-Createamicrogatewayproject)
-   [Step 3 - Build the microgateway project](#InstallonDocker-Step3-Buildthemicrogatewayproject)
-   [Step 4 - Run the Docker container](#InstallonDocker-Step4-RuntheDockercontainer)

### Step 1 - Follow the installation prerequisites

Make sure to install and set up [Docker](https://www.docker.com) and the [installation prerequisites for the Microgateway Toolkit](Install-on-VM_141247030.html#InstallonVM-MicrogatewayToolkit) .

### Step 2 - Create a microgateway project

1.  Navigate to a preferred workspace folder using the command line to set the location that is used to store the Microgateway project.
2.  Create a project.
    Let's create a project named "petstore" by running the following command. This will create the folder structure for the artifacts to be included.

    **Format**

    ``` java
        micro-gw init <project_name> 
    ```

        **Example**

    ``` java
        micro-gw init petstore  
        Project 'petstore' is initialized successfully.
    ```

        ### Step 3 - Build the microgateway project

        1.  Add the API to the project.

        Navigate to the `           /petstore/api_definitions          ` directory and add the OpenAPI definition(s) to this  directory. Let's use the [Petstore sample OpenAPI definition](https://github.com/wso2/product-microgateway/blob/master/samples/petstore_basic.yaml) in this scenario.

        2.  Create the input for WSO2 API Microgateway Toolkit.
        Create a file named `           deployment.toml          ` in a preferred location. This TOML file should contain the relevant deployment configurations (e.g., Docker image name, registry, tag, etc.) as shown below. For more information on each of the above parameters, see [deployment.toml for Docker](_deployment.toml_for_Docker_) . Note that you will have to use this TOML file as the input, by way of passing the file path, when creating the Microgateway project.

    ``` java
        [docker]
        [docker.dockerConfig]
        enable = true
        name = "petstore"
        registry = 'docker.wso2.com'
        tag = 'v1'
        #buildImage = ''
        #dockerHost = ''
        #dockerCertPath = ''
        baseImage = 'wso2/wso2micro-gw:latest'
        #enableDebug = ''
        #debugPort = ''
        #push = ''
        [docker.dockerCopyFiles]
        enable = true
        [[docker.dockerCopyFiles.files]]
        source = '<MICROGW_TOOLKIT_HOME>/resources/conf/micro-gw.conf'
        target = '/home/ballerina/conf/micro-gw.conf'
        isBallerinaConf = true
    ```

        !!! info
        Specify docker credentials
        If the docker image push is enabled to push the image to a docker registry , specifying of the username and password should be done using environment variables
        export DOCKER\_USERNAME=&lt;USER&gt;
        export DOCKER\_PASSWORD  =&lt;PASSWORD&gt;

3.  Build the microgateway project.
    Use your command line tool to navigate to where the project directory ("petstore") was created and execute the following command to build the project.

    **Format**

    ``` java
        micro-gw build <project_name> --deployment-config <path-to-the-deployment.toml>
    ```

        **Example**

    ``` java
        micro-gw build petstore --deployment-config /Users/kim/Downloads/deployment.toml
    ```

        The Docker image is created in your local registry and you can use it to spawn a WSO2 API Microgateway Docker container.

        ### Step 4 - Run the Docker container

        Run the Docker container using the following command.

``` java
    docker run -d -p 9090:9090 -p 9095:9095 docker.wso2.com/<docker_name>:<docker_tag>
```

    !!! note
    When following this Docker deployment guide, you will end up by creating a Docker image with APIs built into it; thereby, the `         .balx        ` file will already be inside the Docker image. Therefore, unlike in the Quick Start Guide for Docker you do not have to specify the project name when running the Docker container and mount the `         .balx        ` file in to the Docker image.
!!! info
    If you are working in a Linux environment, you can **either use** the above command or the following command to start your Docker container. In the following command the Docker container uses the host network driver for your container.
``` java
    docker run --network host -d docker.wso2.com/<docker_name>:<docker_tag>
```
    For more information on working with Docker in different environments, see the relevant Docker documentation: [Docker for Mac](https://docs.docker.com/docker-for-mac/) , [Docker for Windows](https://docs.docker.com/docker-for-windows/) .

Step 5 - Invoke the sample API

##### Step 5.1 - Obtain token

After the APIs are exposed via WSO2 API Microgateway, you can invoke an API with a valid JWT token or an opaque access token.
In order to use JWT tokens, WSO2 API Microgateway should be presented with a JWT signed by a trusted OAuth2 service.
Let's use the following **sample JWT token** that never expires, which was generated using WSO2 API Manager for testing purposes.

**JWT Token**

``` java
    eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5UQXhabU14TkRNeVpEZzNNVFUxWkdNME16RXpPREpoWldJNE5ETmxaRFUxT0dGa05qRmlNUSJ9.eyJhdWQiOiJodHRwOlwvXC9vcmcud3NvMi5hcGltZ3RcL2dhdGV3YXkiLCJzdWIiOiJhZG1pbiIsImFwcGxpY2F0aW9uIjp7ImlkIjoyLCJuYW1lIjoiSldUX0FQUCIsInRpZXIiOiJVbmxpbWl0ZWQiLCJvd25lciI6ImFkbWluIn0sInNjb3BlIjoiYW1fYXBwbGljYXRpb25fc2NvcGUgZGVmYXVsdCIsImlzcyI6Imh0dHBzOlwvXC9sb2NhbGhvc3Q6OTQ0M1wvb2F1dGgyXC90b2tlbiIsImtleXR5cGUiOiJQUk9EVUNUSU9OIiwic3Vic2NyaWJlZEFQSXMiOltdLCJjb25zdW1lcktleSI6Ilg5TGJ1bm9oODNLcDhLUFAxbFNfcXF5QnRjY2EiLCJleHAiOjM3MDMzOTIzNTMsImlhdCI6MTU1NTkwODcwNjk2MSwianRpIjoiMjI0MTMxYzQtM2Q2MS00MjZkLTgyNzktOWYyYzg5MWI4MmEzIn0=.b_0E0ohoWpmX5C-M1fSYTkT9X4FN--_n7-bEdhC3YoEEk6v8So6gVsTe3gxC0VjdkwVyNPSFX6FFvJavsUvzTkq528mserS3ch-TFLYiquuzeaKAPrnsFMh0Hop6CFMOOiYGInWKSKPgI-VOBtKb1pJLEa3HvIxT-69X9CyAkwajJVssmo0rvn95IJLoiNiqzH8r7PRRgV_iu305WAT3cymtejVWH9dhaXqENwu879EVNFF9udMRlG4l57qa2AaeyrEguAyVtibAsO0Hd-DFy5MW14S6XSkZsis8aHHYBlcBhpy2RqcP51xRog12zOb-WcROy6uvhuCsv-hje_41WQ==
```

    !!! info
    More information
    -   You can obtain a JWT token from any third-party secure token service or via the WSO2 API Manager.
    -   Alternatively, you can also use an OAuth2 token to invoke the API.
    For more information, see the FAQs on [Working with Tokens](FAQs_141247125.html#FAQs-WorkingwithTokens) .

##### Step 5.2 - Invoke the API

Invoke the API using the JWT token using the following command.

1.  Retrieve the Docker container IP address.

    ``` java
        docker ps - This provides the container id
        docker inspect <Container_ID> | grep "IPAddress"
    ```

        2.  Execute the command below to set a self-contained OAuth2.0 access token in the JWT format as a variable on your terminal session.

        **Format**

    ``` java
        TOKEN=<JWT-token>
    ```

        **Example**

    ``` java
        TOKEN=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5UQXhabU14TkRNeVpEZzNNVFUxWkdNME16RXpPREpoWldJNE5ETmxaRFUxT0dGa05qRmlNUSJ9.eyJhdWQiOiJodHRwOlwvXC9vcmcud3NvMi5hcGltZ3RcL2dhdGV3YXkiLCJzdWIiOiJhZG1pbiIsImFwcGxpY2F0aW9uIjp7ImlkIjoyLCJuYW1lIjoiSldUX0FQUCIsInRpZXIiOiJVbmxpbWl0ZWQiLCJvd25lciI6ImFkbWluIn0sInNjb3BlIjoiYW1fYXBwbGljYXRpb25fc2NvcGUgZGVmYXVsdCIsImlzcyI6Imh0dHBzOlwvXC9sb2NhbGhvc3Q6OTQ0M1wvb2F1dGgyXC90b2tlbiIsImtleXR5cGUiOiJQUk9EVUNUSU9OIiwic3Vic2NyaWJlZEFQSXMiOltdLCJjb25zdW1lcktleSI6Ilg5TGJ1bm9oODNLcDhLUFAxbFNfcXF5QnRjY2EiLCJleHAiOjM3MDMzOTIzNTMsImlhdCI6MTU1NTkwODcwNjk2MSwianRpIjoiMjI0MTMxYzQtM2Q2MS00MjZkLTgyNzktOWYyYzg5MWI4MmEzIn0=.b_0E0ohoWpmX5C-M1fSYTkT9X4FN--_n7-bEdhC3YoEEk6v8So6gVsTe3gxC0VjdkwVyNPSFX6FFvJavsUvzTkq528mserS3ch-TFLYiquuzeaKAPrnsFMh0Hop6CFMOOiYGInWKSKPgI-VOBtKb1pJLEa3HvIxT-69X9CyAkwajJVssmo0rvn95IJLoiNiqzH8r7PRRgV_iu305WAT3cymtejVWH9dhaXqENwu879EVNFF9udMRlG4l57qa2AaeyrEguAyVtibAsO0Hd-DFy5MW14S6XSkZsis8aHHYBlcBhpy2RqcP51xRog12zOb-WcROy6uvhuCsv-hje_41WQ==
    ```

        !!! note
        When you are deploying the Microgateway in production, make sure to change its default certificates.

3.  You can now invoke the API running on the Microgateway using the following cURL command.

    **Format**

    ``` java
        curl -X GET "<container_IP>:<MGW-runtime-port>/<API-context>/<API-resource>" -H "accept: application/xml" -H "Authorization:Bearer <JWT_TOKEN>" -k
    ```

        **Examples**

    ``` java
        curl -X GET "https://localhost:9095/petstore/v1/pet/findByStatus?status=available" -H "accept: application/xml" -H "Authorization:Bearer $TOKEN" -k

        curl -X GET "https://localhost:9095/petstore/v1/pet/1" -H "accept: application/xml" -H "Authorization:Bearer $TOKEN" -k
    ```


