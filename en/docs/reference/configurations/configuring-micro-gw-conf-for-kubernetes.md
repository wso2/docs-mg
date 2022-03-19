# Configuring micro-gw.conf for Kubernetes

You can use environment variables when running Kubernetes to override the configurations in the `<MGW-RUNTIME-HOME>/conf/micro-gw.conf` file.

- You need to create the variable name by appending the `micro-gw.conf` configuration hierarchy by using an `_` underscore to define each level of the hierarchy.

## Example 1 - Defining the Listener HTTPS port with secret references

!!! note
    It is mandatory to define secret references when working with sensitive data such as user credentials.

Let’s assume that you want to fetch the configuration of `httpPort`, which is under `[listenerConfig]`.

```
[listenerConfig]
 ...
  httpPort = 9090
  ...
```

1. Create a Kubernetes secret for the configuration.

     1. Create a file (`secret.yaml`) with the following content. 
         
         The data attributes need to be base64 encoded. Therefore, in this case the encoded value for 8000 has been added in the following code snippet.

         ```
         apiVersion: v1
         kind: Secret
         metadata:
           name: test-secret
         data:
           port: ODAwMA==
         ```

     2. Create the secret.
   
         ``` tab="Format"
         kubectl apply -f <file-path>/<secret-file-name.yaml>
         ```

         ``` tab="Example"
         kubectl apply -f file-path/secret.yaml
         ```

2. Declare the secret as an environment variable in the `deployment-config.toml` file.

    ``` tab="Format"
      [kubernetes]
          [kubernetes.kubernetesDeployment]
              enable = true
              ...
              env = '{ "<environment-variable>": {secretKeyRef: {key: "<configuration>",name: "<secret-name>"}} }'
    ```

    ``` tab="Example"
      [kubernetes]
          [kubernetes.kubernetesDeployment]
              enable = true
              ...
              env = '{ "listenerConfig_httpPort": {secretKeyRef: {key: "port",name: "test-secret"}} }'
    ```

     - As the `httpPort` configuration is under the `listenerConfig`, the variable name can be obtained by appending both with an underscore`_`.
     - You need to use the same key (e.g., `port`) and the name (e.g., `test-secret`) of the secret that you created.

3. Generate the Kubernetes artifacts by building the project.

     ```
     micro-gw build k8s_project — deployment-config deployment.toml
     ```

     The following is the generated Kubernetes artifact (`k8s_project.yaml`) content with the environment variable.

    ```
      ...
      spec:
            containers:
            - env:
              - name: "listenerConfig_httpPort"
                valueFrom:
                  secretKeyRef:
                    key: "port"
                    name: "test-secret"
              - name: "CONFIG_FILE"
                value: "/home/ballerina/conf/ballerina.conf"
      ...
    ```

4. Start the Kubernetes cluster.

## Example 2 - Defining the Listener HTTPS port without secret references

Let’s assume that you want to fetch the configuration of `httpPort`, which is under `[listenerConfig]`.

```
[listenerConfig]
 ...
  httpPort = 9090
  ...
```

1. Declare the secret as an environment variable in the `deployment-config.toml` file.

    ``` tab="Format"
      [kubernetes]
          [kubernetes.kubernetesDeployment]
              enable = true
              ...
              env = '{ "<environment-variable-name>": "<environment-variable-value>" }'
    ```

    ``` tab="Example"
      [kubernetes]
          [kubernetes.kubernetesDeployment]
              enable = true
              ...
              env = '{ "listenerConfig_httpPort": 9090 }'
    ```

    !!! tip
        You can define multiple environment variables in the `deployment-config.toml` file as follows:

        ```
        env = '{ "<environment-variable-name1>": "<environment-variable-value1>", "<environment-variable-name2>": "<environment-variable-value2>" }'
        ```

     - As the `httpPort` configuration is under the `listenerConfig`, the variable name can be obtained by appending both with an underscore`_`.
     
2. Generate the Kubernetes artifacts by building the project.

     ```
     micro-gw build k8s_project — deployment-config deployment.toml
     ```

     The following is the generated Kubernetes artifact (`k8s_project.yaml`) content with the environment variable.

    ```
      ...
      spec:
            containers:
            - env:
              - name: "listenerConfig_httpPort"
                value: 9090
              - name: "CONFIG_FILE"
                value: "/home/ballerina/conf/ballerina.conf"
      ...
    ```

3. Start the Kubernetes cluster.

