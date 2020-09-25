# Project Directory

When you initialize a WSO2 API Microgateway project, it creates a directory structure for the project in the location where you ran the command. An empty `api_definitions` folder is created inside the project directory. You, as an API developer, can add multiple open API definitions inside the `api_definitions` folder. You can modify the Open API definition using open API vendor specific extensions for the purpose of specifying the backend endpoint details, request and response interceptors, throttle policies, CORS configuration and more.

After the project is initialized, you will notice that a directory based on the name of the MGW project (e.g., petstore) has been created within the directory where you executed the `init` command. The folder structure is similar to the following.

``` text
[project_name]
├── api_definitions
├── conf
│   └── deployment-config.toml
├── extensions
│   ├── extension_filter.bal
│   ├── startup_extension.bal
│   └── token_revocation_extension.bal
├── gen
|   └── api_definitions
├── grpc_definitions
├── interceptors
├── lib
└── policies.yaml
```

The following table explains the usage of each folder.

| Folder                                         | Description                                                                                                                                                                                                                                                                                                                                                                                                               |
|------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| api_definitions                                | Stores Open API definitions.                                                                                                                                                                                                                                                                                                                                                                                              |
| grpc_definitions                               | Stores gRPC definitions.                                                                                                                                                                                                                                                                                                                                                                                              |
| conf                                           | Includes the `deployment-config.toml ` file. This file includes details of the Docker image or the Kubernetes artifacts that need to be created including the Microgateway project. The Docker image or the Kubernetes artifacts are generated at build time.                                                                                                                                     |
| extensions                                     | Includes the following extensions. <li> `extension_filter.bal` - This sets meaningful error codes to the response. </li> <li>`startup_extension.bal` </li> <li>`token_revocation_extension.bal` - If a custom token revocation endpoint other than WSO2 API Manager is used, you can use this extension to notify WSO2 API Microgateway with regard to the revoked tokens. When the token is added to the revoked token map via this extension, WSO2 API Microgateway is able to read the revoked token map and restrict access to the revoked token.</li>  |
| gen/api_definitions                            | Is it used to store all the OpenAPI definitions of the APIs that you import from WSO2 API Manager.                                                                                                                                                                                                                                                                                                                        |
| interceptors                                   | Includes any request interceptors or response interceptors that you use for the APIs in the Microgateway project. An interceptor is able to perform mediation and transformation on a request or response.                                                                                                                                                                                                                |
| policies.yaml                                  | This specifies the predefined policies used for the project.                                                                                                                                                                                                                                                                                                                                                              |
| target                                         | Includes the runtime artifacts from the build command.                                                                                                                                                                                                                                                                                                                                                                    |
| target/gen                                     | It is used to store all the generated Ballerina sources.                                                                                                                                                                                                                                                                                                                                                                  |


