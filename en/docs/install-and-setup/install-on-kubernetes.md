# Install on Kubernetes

**K8s API Operator**

[The K8s API Operator](https://apim.docs.wso2.com/en/latest/learn/kubernetes-operators/k8s-api-operator/k8s-api-operator/) is the recommended approach to deploy microgateway in kubernetes. API operator makes an API first class citizen in the kuberneters eco system and allows to deploy the microgateways in k8s cluster with a single command using open API definition.

Following content explains configuration based deployment of microgateway in k8s.

Follow the instructions below to deploy WSO2 API Microgateway in Kubernetes.

- [Step 1 - Follow the installation prerequisites](#InstallonKubernetes-Step1-Followtheinstallationprerequisites)
- [Step 2 - Create a Microgateway project](#InstallonKubernetes-Step2-CreateaMicrogatewayproject)
- [Step 3 - Build the Microgateway project](#InstallonKubernetes-Step3-BuildtheMicrogatewayproject)
- [Step 4 - Deploy the Docker image in a Kubernetes environment](#InstallonKubernetes-Step4-DeploytheDockerimageinaKubernetesenvironment)
- [Step 5 - Start the Kubernetes cluster](#InstallonKubernetes-Step5-StarttheKubernetescluster)
- [Step 6 - Invoke the sample API](#InstallonKubernetes-Step6-InvokethesampleAPI)
    - [Step 6.1 - Obtain token](#InstallonKubernetes-Step6.1-Obtaintoken)
    - [Step 6.2 - Invoke the API](#InstallonKubernetes-Step6.2-InvoketheAPI)

### Step 1 - Follow the installation prerequisites

Make sure to do the following:

1.  Carry  out the [installation prerequisites for WSO2 API Microgateway Toolkit]({{base_path}}/install-and-setup/install-on-vm/#microgateway-toolkit) .
2.  [Install and setup kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) in your client machine.
3.  Setup a Kubernetes cluster.
4.  Make sure that kubectl points to your Kubernetes cluster.

### Step 2 - Create a Microgateway project

1.  Navigate to a preferred workspace folder using the command line to set the location that is used to store the Microgateway project.
2.  Create a project.
    Let's create a project named `k8s_project` by running the following command. This will create the folder structure for the artifacts to be included.

    ``` java tab="Format"
    micro-gw init <project_name>
    ```

    ``` java tab="Example"
    micro-gw init k8s_project
    ```

    ``` java tab="Response"
    Project 'k8s_project' is initialized successfully.

    (Use "micro-gw build k8s-project" after copying the api definitions)
    (Use "micro-gw import k8s-project [-l]|[-a -v]" to import APIs from WSO2 API Manager)
    ```

### Step 3 - Build the Microgateway project

1.  Add the API to the project.

    Navigate to the `/petstore/api_definitions` directory and add the OpenAPI definition(s) to this  directory. Let's use the [Petstore sample OpenAPI definition](https://github.com/wso2/product-microgateway/blob/master/samples/petstore_basic.yaml) in this scenario.

    !!! info
        The latter mentioned instructions uses the developer first approach. However, if you wish to work with APIs that you have published in WSO2 API Manager, you can either [import a single API](https://mg.docs.wso2.com/en/latest/how-tos/adding-apis/importing-a-single-api-from-wso2-api-manager/#importing-a-single-api-from-wso2-api-manager) or [you can import the APIs as a group](https://mg.docs.wso2.com/en/latest/how-tos/adding-apis/importing-a-group-of-apis-from-wso2-api-manager/#importing-a-group-of-apis-from-wso2-api-manager) .

2.  Create the input for WSO2 API Microgateway Toolkit.
    Create a `deployment.toml` file that you will use as the input when creating the microgateway project. This TOML file should contain the relevant deployment configurations as shown below. For more information on each of the above parameters, see [deployment.toml for Kubernetes]({{base_path}}/reference/configurations/deployment.toml-for-kubernetes/) .

    ``` yml tab="Example"
    [kubernetes]
        [kubernetes.kubernetesDeployment]
            enable = true
            name = 'k8s'
            tag = 'v1'
            #labels = ''
            replicas = '1'
            #enableLiveness = ''
            #initialDelaySeconds = ''
            #periodSeconds = ''
            #livenessPort = ''
            #imagePullPolicy = 'Always'
            #imagePullSecrets = ['secret1']
            #image = ''
            #env = ''
            buildImage = true
            #cmd = 'CMD gateway ${APP} --b7a.config.file=conf/micro-gw.conf --b7a.log.level=DEBUG'
            #copyFiles = ''
            #dockerHost = ''
            #dockerCertPath = ''
            #push = 'false'
            #registry = 'index.docker.io/<DOCKER_USERNAME>'
            #username = ''
            #password = ''
            baseImage = 'wso2/wso2micro-gw:3.2.0'
        [kubernetes.kubernetesDeployment.livenessProbe]
            enable = true
            initialDelaySeconds = '20'
            periodSeconds = '20'
        [kubernetes.kubernetesDeployment.readinessProbe]
            enable = true
            initialDelaySeconds = '30'
            periodSeconds = '30'
        [kubernetes.kubernetesServiceHttps]
            enable = true
            name = 'httpsService'
            #labels = '{"": ""}'
            serviceType = 'NodePort'
            #port = ''
        [kubernetes.kubernetesServiceHttp]
            enable = true
            name = 'httpService'
            #labels = '{"": ""}'
            serviceType = 'NodePort'
            #port = ''
        [kubernetes.kubernetesConfigMap]
            enable = true
            ballerinaConf = '<MICROGW_TOOLKIT_HOME>/resources/conf/micro-gw.conf'
    ```

    !!! note
        Make sure to specify the absolute path for the `ballerinaConf` , by replacing the `<MICROGW_TOOLKIT_HOME>` placeholder with the full path.
        Example:
        `/home/users/wso2am-micro-gw-toolkit-3.x.x/resources/conf/micro-gw.conf`
    
    -   Furthermore, you can add additional configurations using the env variable if needed. Following is an example of how to configure the timezone of the docker image.
    
    ``` java tab="Format"
    [kubernetes]
        [kubernetes.kubernetesDeployment]
        .........
        env = 'env = '{ "<env_key>":"<env_value>" }'
    ```

    ``` java tab="Example"
    [kubernetes]
        [kubernetes.kubernetesDeployment]
        .........
        env = '{ "TZ":"Australia/Brisbane" }'
    ```


3.  Build the microgateway project.

    Use your command line tool to navigate to where the project directory ( `k8s_project` ) was created and execute the following command to build the project.

    ``` java tab="Format"
    micro-gw build <project_name> --deployment-config deployment.toml
    ```

    ``` java tab="Example"
    micro-gw build k8s_project --deployment-config deployment.toml
    ```

    This generates the following Kubernetes resources.
    
    `├── k8s_project`
    
    `│    └── api_definitions`
    
    `│    └── conf`

    `│    └── extensions`

    `│    └── grpc_definitions`

    `│    └── policies.yaml`

    `│    └── target`

    `│    │     └── gen`

    `│    │     │    └── gRPCSrc`

    `│    │     │    └── src` 

    `│    │     │    └── target`

    `│    │     │    │    └── balo`

    `│    │     │    │    └── bin`   

    `│    │     │    │    └── caches`

    `│    │     │    │    └── docker`

    `│    │     │    │    │     └── k8s_project`

    `│    │     │    │    │     │    └── Dockerfile`

    `│    │     │    │    └── kubernetes`

    `│    │     │    │    │     └── k8s_project`

    `│    │     │    │    │     │      └── k8s`

    `│    │     │    │    │     │      │    └── Chart.yaml`

    `│    │     │    │    │     │      │    └── templates`

    `│    │     │    │    │     │      │    │      └── k8s_project.yaml`

    `│    │     │    │    │     │      └── k8s_project.yaml`

    `│    └── k8s_project.jar`

    The Docker image to be deployed in Kubernetes is created in your local registry. You can find the image `k8s_project:latest` when you execute the Docker images command.

### Step 4 - Deploy the Docker image in a Kubernetes environment

Let's SCP the image to the Kubernetes nodes to deploy the Docker image in a K8s environment.

!!! info
    For more options on deploying the Docker image in a Kubernetes environment, see the [Deployment related FAQs]({{base_path}}/faqs/#deployment).

1.  Save the Docker image to a `tar` file.

    ``` java tab="Format"
    docker save <MGW-project-name>:latest > <Docker-image-name>.tar
    ```

    ``` java tab="Example"
    docker save k8s_project:latest > image.tar
    ```

2.  SCP the image to the Kubernetes nodes.

    ``` java tab="Format"
    scp -i <identity-file> image.tar username@<K8s_NODE_IP>:
    ```

    -   `<identity-file>` - This refers to the public key of the Kubernetes node. For example you can get a google\_compute\_engine.pub for GCE. You have to `               scp              ` the Docker image for each and every Kubernetes node.

    !!! note
        When using minikube, the username is `docker` and you can find the IP address by using the `minikube ip` command.

3.  Load the Docker image in the Kubernetes nodes.
    You need to execute the following command in the Kubernetes nodes.

    ``` java tab="Format"
    docker load < [Docker-image-name].tar
    ```

    ``` java tab="Example"
    docker load < image.tar
    ```

### Step 5 - Start the Kubernetes cluster

Deploy WSO2 API Microgateway in Kubernetes by deploying the Kubernetes resources (artifacts) and starting WSO2 API Microgateway.

``` java tab="Format"
kubectl create -f <MGW-project-name>/target/gen/target/kubernetes/k8s-project
```

``` java tab="Example"
kubectl create -f k8s-project/target/gen/target/kubernetes/k8s-project
```

### Step 6 - Invoke the sample API

##### Step 6.1 - Obtain token

After the APIs are exposed via WSO2 API Microgateway, which you deployed in a Kubernetes cluster, you can invoke an API with a valid JWT token or an opaque access token.
In order to use JWT tokens, WSO2 API Microgateway should be presented with a JWT signed by a trusted OAuth2 service.
Let's use the following **sample JWT token** that never expires, which was generated using WSO2 API Manager for testing purposes.

``` java tab="Sample"
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5UQXhabU14TkRNeVpEZzNNVFUxWkdNME16RXpPREpoWldJNE5ETmxaRFUxT0dGa05qRmlNUSJ9.eyJhdWQiOiJodHRwOlwvXC9vcmcud3NvMi5hcGltZ3RcL2dhdGV3YXkiLCJzdWIiOiJhZG1pbiIsImFwcGxpY2F0aW9uIjp7ImlkIjoyLCJuYW1lIjoiSldUX0FQUCIsInRpZXIiOiJVbmxpbWl0ZWQiLCJvd25lciI6ImFkbWluIn0sInNjb3BlIjoiYW1fYXBwbGljYXRpb25fc2NvcGUgZGVmYXVsdCIsImlzcyI6Imh0dHBzOlwvXC9sb2NhbGhvc3Q6OTQ0M1wvb2F1dGgyXC90b2tlbiIsImtleXR5cGUiOiJQUk9EVUNUSU9OIiwic3Vic2NyaWJlZEFQSXMiOltdLCJjb25zdW1lcktleSI6Ilg5TGJ1bm9oODNLcDhLUFAxbFNfcXF5QnRjY2EiLCJleHAiOjM3MDMzOTIzNTMsImlhdCI6MTU1NTkwODcwNjk2MSwianRpIjoiMjI0MTMxYzQtM2Q2MS00MjZkLTgyNzktOWYyYzg5MWI4MmEzIn0=.b_0E0ohoWpmX5C-M1fSYTkT9X4FN--_n7-bEdhC3YoEEk6v8So6gVsTe3gxC0VjdkwVyNPSFX6FFvJavsUvzTkq528mserS3ch-TFLYiquuzeaKAPrnsFMh0Hop6CFMOOiYGInWKSKPgI-VOBtKb1pJLEa3HvIxT-69X9CyAkwajJVssmo0rvn95IJLoiNiqzH8r7PRRgV_iu305WAT3cymtejVWH9dhaXqENwu879EVNFF9udMRlG4l57qa2AaeyrEguAyVtibAsO0Hd-DFy5MW14S6XSkZsis8aHHYBlcBhpy2RqcP51xRog12zOb-WcROy6uvhuCsv-hje_41WQ==
```

!!! info
    More information
    - You can obtain a JWT token from any third-party secure token service or via the WSO2 API Manager.
    - Alternatively, you can also use an OAuth2 token to invoke the API.
    For more information, see the FAQs on [Working with Tokens]({{base_path}}/faqs/#working-with-tokens) .

##### Step 6.2 - Invoke the API

Invoke the API using the JWT token using the following command.

1.  Execute the command below to set a self-contained OAuth2.0 access token in the JWT format as a variable on your terminal session.

    ``` java tab="Format"
    TOKEN=<JWT-token>
    ```

    ``` java tab="Example"
    TOKEN=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5UQXhabU14TkRNeVpEZzNNVFUxWkdNME16RXpPREpoWldJNE5ETmxaRFUxT0dGa05qRmlNUSJ9.eyJhdWQiOiJodHRwOlwvXC9vcmcud3NvMi5hcGltZ3RcL2dhdGV3YXkiLCJzdWIiOiJhZG1pbiIsImFwcGxpY2F0aW9uIjp7ImlkIjoyLCJuYW1lIjoiSldUX0FQUCIsInRpZXIiOiJVbmxpbWl0ZWQiLCJvd25lciI6ImFkbWluIn0sInNjb3BlIjoiYW1fYXBwbGljYXRpb25fc2NvcGUgZGVmYXVsdCIsImlzcyI6Imh0dHBzOlwvXC9sb2NhbGhvc3Q6OTQ0M1wvb2F1dGgyXC90b2tlbiIsImtleXR5cGUiOiJQUk9EVUNUSU9OIiwic3Vic2NyaWJlZEFQSXMiOltdLCJjb25zdW1lcktleSI6Ilg5TGJ1bm9oODNLcDhLUFAxbFNfcXF5QnRjY2EiLCJleHAiOjM3MDMzOTIzNTMsImlhdCI6MTU1NTkwODcwNjk2MSwianRpIjoiMjI0MTMxYzQtM2Q2MS00MjZkLTgyNzktOWYyYzg5MWI4MmEzIn0=.b_0E0ohoWpmX5C-M1fSYTkT9X4FN--_n7-bEdhC3YoEEk6v8So6gVsTe3gxC0VjdkwVyNPSFX6FFvJavsUvzTkq528mserS3ch-TFLYiquuzeaKAPrnsFMh0Hop6CFMOOiYGInWKSKPgI-VOBtKb1pJLEa3HvIxT-69X9CyAkwajJVssmo0rvn95IJLoiNiqzH8r7PRRgV_iu305WAT3cymtejVWH9dhaXqENwu879EVNFF9udMRlG4l57qa2AaeyrEguAyVtibAsO0Hd-DFy5MW14S6XSkZsis8aHHYBlcBhpy2RqcP51xRog12zOb-WcROy6uvhuCsv-hje_41WQ==
    ```

    !!! note
        When you are deploying the Microgateway in production, make sure to change its default certificates.

2.  You can now invoke the API running on the Microgateway using the following cURL command.

    ``` java tab="Format"
    curl -X GET "<Any_Kubernetes_Node_IP>:<NodePort>/<API-context>/<API-resource>" -H "accept: application/xml" -H "Authorization:Bearer <JWT_TOKEN>" -k
    ```

    ``` java tab="Example"
    curl -X GET "https://localhost:32616/petstore/v1/pet/findByStatus?status=available" -H "accept: application/xml" -H "Authorization:Bearer $TOKEN" -k
    ```
    
    !!! info
        As this example uses `NodePort` as the service typein Kubernetes, you can access the API using any of the Kubernetes node IP addresses and node ports when using the latter mentioned service type.
        In addition, you can use the `kubectl get svc` command to list down the services that run on Kubernetes in order to get the port. However, to identify an external IP of the Kubernetes cluster you can use the command
        `kubectl get nodes -o yaml | grep -B 1 "type: ExternalIP"`
        If you use minukube as the Kuberneterse cluster, then you can find the external IP by using the `minikube ip` command.

    !!! info
        As this example uses `NodePort` as the service typein Kubernetes, you can access the API using any of the Kubernetes node IP addresses and node ports when using the latter mentioned service type.
        In addition, you can use the `kubectl get svc` command to list down the services that run on Kubernetes in order to get the port. However, to identify an external IP of the Kubernetes cluster you can use the command
        `kubectl get nodes -o yaml | grep -B 1 "type: ExternalIP"`
        If you use minukube as the Kuberneterse cluster, then you can find the external IP by using the `minikube ip` command.

    In the above example, localhost is used as the Kubernetes node IP, because it was based on Kubenetes that was provided by Docker for Mac.
