# Add a gRPC API

### What is gRPC

gRPC is a high-performance RPC framework implemented on top of HTTP/2. And also it is a CNCF (Cloud Native Computation Foundation) incubating project. You can find more details [here](https://grpc.io/).

### Why you need a microgateway for gRPC

One of the most important features in gRPC is that you can generate your client and server stubs in so many different programming languages. But if the gRPC service developer needs some advanced features like authentication, authorization, and rate-limiting, it is required to put a considerable effort. By using the wso2 microgateway, the service developer can focus on the actual service implementation rather than worrying about those features. The following features are provided by the wso2 microgateway.

1.  Authentication
    -   The supported authentication schemes are JWT, Basic Auth and API Key.
2.  Authorization
    -   Users can add scopes to the methods inside the service definition so that the gRPC clients only access those specific methods only if they are authorized.
3.  Rate Limiting
    -   Rate limiting can be applied based on RPC method invocations per-method basis or per-service basis.
4.  Single Source of Truth
    -   In gRPC, the .proto file containing the service definition acts as the single source of truth. Hence the configurations related to microgateway including endpoint configurations, authentication schemes, etc. need to be provided inside the gRPC service definition.

Please refer to the [Microgateway Extensions](#microgateway-extensions) for more information.

### Microgateway Extensions

To generate the microgateway for gRPC services, there is a set of extensions introduced which need to be added to the existing service .proto files inside the microgateway project. Those extensions are defined in a separate .proto file called "wso2\_options.proto" which is defined by the microgateway.

``` text tab="wso2_options.proto"
syntax = "proto3";
package wso2;

option java_package = "org.wso2.apimgt.gateway.cli.protobuf";
option java_outer_classname = "ExtensionHolder";

import "google/protobuf/descriptor.proto";

//service level extensions
extend google.protobuf.ServiceOptions {
Endpoints production_endpoints = 50001;
Endpoints sandbox_endpoints = 50002;
repeated Security security = 50003;
string throttling_tier = 50004;
}

//method level extensions
extend google.protobuf.MethodOptions {
string method_throttling_tier = 50006;
string method_scopes = 50007;
}

//to define endpoints
message Endpoints {
repeated string url = 1;
EndpointType type = 2;
}

enum EndpointType {
DEFAULT = 0;
}

enum Security {
NONE = 0;
BASIC = 1;
OAUTH2 = 2;
JWT = 3;
APIKEY = 4;
}
```

#### Service Extensions
<table>
<thead>
<tr class="header">
<th>Extension</th>
<th>Description</th>
<th>Sample</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>wso2.production_endpoints</td>
<td>This field holds the backend grpc server endpoint for the production environment. <strong>Required</strong> if any sandbox_endpoints are not included.</td>
<td>option (wso2.production_endpoints) = {<br />
url : &quot;&lt;backend-server-url&gt;&quot;;<br />
};</td>
</tr>
<tr class="even">
<td>wso2.sandbox_endpoints</td>
<td>This field holds the backend grpc server endpoint for the sandbox environment. <strong>Required</strong> if any production_endpoints are not included.</td>
<td>option (wso2.sandbox_endpoints) = {<br />
url : &quot;&lt;backend-server-url&gt;&quot;;<br />
};</td>
</tr>
<tr class="odd">
<td>wso2.security</td>
<td>This field defines the security scheme used to authenticate the gRPC client. If this field is not declared inside the service .proto file, no authentication will happen from the microgateway as the default value is <strong>NONE</strong> . The user can pick one or many of the following values for this extension; “ <strong>NONE</strong> ”, “ <strong>BASIC</strong> ”, “ <strong>OAUTH2</strong> ”, “ <strong>JWT</strong> ” and “ <strong>APIKEY</strong> ”.</td>
<td>option (wso2.security) = BASIC;</td>
</tr>
<tr class="even">
<td>wso2.throttling_tier</td>
<td>This field defines the throttling tier for the entire service. If the throttling tier exceeds, gRPC client will receive a response with the grpc-status &quot;8&quot; which indicated that there has been &quot; <em>too many function calls</em> &quot;. It is required to make sure that the throttling policy value should be inside the <strong>policies.yaml</strong> under <em>resourcePolicies</em> .(&lt;microgateway-project&gt;/policies.yaml).</td>
<td>option (wso2.throttling_tier) = 20kPerMin;</td>
</tr>
</tbody>
</table>

#### Method Extensions

| Extension                     | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Sample                                      |
|-------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------|
| wso2.method\_throttling\_tier | This field defines the throttling tier for the method. If there is a service level throttling tier, it will be overridden by this. If the throttling tier exceeds, gRPC client will receive a response with the grpc-status "8" which indicated that there has been " *too many functions calls* ". It is required to make sure that the throttling policy value should be inside the **policies.yaml** under *resourcePolicies* .(&lt;microgateway-project&gt;/policies.yaml). | option (wso2.throttling\_tier) = 10kPerMin; |
| wso2.scopes                   | This field accepts a string that denotes the scope. If there are multiple scopes, it should be provided as a comma separated string value. This extension can be used only if the selected security scheme is OAUTH2 or JWT.                                                                                                                                                                                                                                                    | option (wso2.scopes) = "scope1, scope2"     |

### How to setup gRPC microgateway

1.  Implement a gRPC server and a client.
    First, you need to design your service definition in a .proto file/files and implement the gRPC server and the client based on that. There are many examples you can find online which implemented in different programming languages. For this guide, the following gRPC service definition will be used. You can find the java gRPC client and java gRPC service implementation for the following .proto [here](https://github.com/VirajSalaka/HelloworldGrpcImpl) .

    ``` text
    syntax = "proto3";
    package org.virajsalaka.grpc;

    service HelloWorld {
    rpc hello (HelloRequest) returns (HelloReply);
    }

    message HelloRequest {
    string input = 1;
    }

    message HelloReply {
    string output = 1;
    }
    ```
2.  Make sure to follow [install and set up]({{base_path}}/install-and-setup/install-on-vm/) guide.
3.  Create an API Microgateway project (e.g., grpc-helloworld-project).
    Navigate to a preferred folder where you want to create the Microgateway project, and then run the following command.

    ``` java tab="Format"
    micro-gw init <project-name>
    ```

    ``` java tab="Example"
    micro-gw init grpc-helloworld-project
    ```

4.  Navigate to the directory &lt;project-location&gt;/grpc\_definitions. Create a sub-directory inside. And then copy the .proto file/files of your project to that sub-directory preserving the directory structure. (In the protocol buffers context, the compiler should be able to compile the service .proto files without providing --proto-path parameter.) In our case, it is only required to copy the above mentioned .proto file. Then, the directory structure will appear as follows.

    ``` text
    grpc-helloworld-project
    ├── api_definitions
    ├── conf
    ├── extensions
    ├── grpc_definitions
    │   └── HelloWorld
    │       └── wso2_hello_world.proto
    ├── interceptors
    ├── lib
    └── policies.yaml
    ```

 5.  It is mandatory to customize the gRPC service definition .proto file/files with the predefined set of extensions. First, you need to add the specific import statement to allow wso2 microgateway specific extensions. To keep the example simple, only the production endpoint field and the security scheme is added. To add more functionalities like authorization and rate-limiting, please refer to the above section on extensions. (Please note that you do not need to copy the wso2\_options.proto file to the grpc\_definitions directory as it is internally handled by the toolkit).

    ``` java
    syntax = "proto3";
    package org.virajsalaka.grpc;

    // To import the optional extensions defined for wso2 microgateway
    import "wso2_options.proto";

    service HelloWorld {
    rpc hello (HelloRequest) returns (HelloReply);

    //production endpoint of the backend grpc server
    option (wso2.production_endpoints) = {
    url : "http://localhost:50051"; 
    };

    //Security scheme is set as API-Key
    option (wso2.security) = APIKEY;
    }

    message HelloRequest {
    string input = 1;
    }

    message HelloReply {
    string output = 1;
    }
    ```

6.  Build the microgateway project.

    ``` java tab="Format"
    micro-gw build <project-name>
    ```

    ``` java tab="Example"
    micro-gw build grpc-helloworld-project
    ```

7.  Run the microgateway project. (Microgateway Runtime is used here. Please refer [installation prerequisites]({{base_path}}/install-and-setup/install-on-vm/#microgateway-runtime) for more information).

    ``` java tab="Format"
    gateway <path to generated microgateway artifact>
    ```

    ``` java tab="Example"
    gateway /home/wso2/grpc-helloworld-project/target/grpc-helloworld-project.jar
    ```

8.  For the provided sample implementation, you need to obtain an API-KEY token.

    ``` text
    curl -X get "https://localhost:9095/apikey" -H "Authorization:Basic YWRtaW46YWRtaW4=" -k
    ```

9.  Finally, you can start your gRPC server and gRPC client with the acquired token.

    To start the gRPC server in the provided [sample implementation](https://github.com/VirajSalaka/HelloworldGrpcImpl) ,

    ``` text tab="gRPC server Invocation"
    java -jar serverImpl/target/serverImpl-1.0-SNAPSHOT.jar <port>
    ```

    ``` text tab="gRPC client Invocation"
    java -jar clientImpl/target/clientImpl-1.0-SNAPSHOT.jar <your input> <api_key_token> <port>
    ```


