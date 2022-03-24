# Configuring micro-gw.conf for Docker

You can use environment variables when running Docker to override the configurations in the `<MGW-RUNTIME-HOME>/conf/micro-gw.conf` file.

- The `-e` flag in the docker run command is used to create environment variables. 
- You need to create the variable name by appending the `micro-gw.conf` configuration hierarchy by using an `_` underscore to define each level of the hierarchy.

## Example 1 - Defining the Listener HTTPS port

Let’s assume that you are going to override the assigned value for `httpsPort` under the `[listenerConfig]` configuration in the `micro-gw.conf` file. The following code snippet shows part of the configuration in the `micro-gw.conf` file.

```
[listenerConfig]
  host = "0.0.0.0"
  httpPort = 9090
  httpsPort = 9095
  ...
  ...
...
...
```

As you can see in the above sample, the `httpsPort` is set as `9095`. In order to change value of the `httpsPort` value you do not need to make any changes to the `mico-gw.conf` file. Instead, when you run the Docker command, you need to append the environment variable in the `run` command as shown below.

``` tab="Format"
docker run -e <environment-variable>='<value-for-environment-variable>' -d <api>:<api-version>
```

``` tab="Example"
docker run -e listenerConfig_httpsPort='9091' -d petstore:v1
```

- As the `httpsPort` configuration is under `listenerConfig`, the environment variable name is `listenerConfig_httpsPort` by appending both configuration hierarchy levels with `_`.

## Example 2 - Disabling Key Manager security

Let’s assume that you are going to fetch an environment variable for `enabled` under the `[keymanager.security.basic]` configuration in the `micro-gw.conf` file. The following code snippet shows part of the configuration in the `micro-gw.conf` file.

```
[keyManager]
  serverUrl = "https://localhost:9443"
  tokenContext = "oauth2"
  [keymanager.security.basic]
    enabled = true
    username = "admin"
    password = "admin"
...
...
```

As you can see in the above sample, the `enabled` is set as `true`. In order to change the value of the `enabled` value you do not need to make any changes to the `mico-gw.conf` file. Instead, when you run the Docker command, you need to append the environment variable in the `run` command as shown below.

``` tab="Format"
docker run -e <environment-variable>='<value-for-environment-variable>' -d <api>:<api-version>
```

``` tab="Example"
docker run -e keymanager_security_basic_enabled='false' -d petstore:v1
```

!!! tip
    You can define multiple environment variables when you run Docker as follows:

    ``` tab="Format"
    docker run -e <environment-variable-1>='<value-for-environment-variable-1>' -e <environment-variable-2>='<value-for-environment-variable-2>' -d <api>:<api-version>
    ```

- As the `enabled` configuration is under the `keymanager_security_basic` configuration, the environment variable name is `keymanager_security_basic_enabled`.

