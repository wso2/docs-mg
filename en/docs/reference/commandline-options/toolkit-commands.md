# Toolkit commands

## init
It initiates a toolkit project with the basic structure. You will get a project structure similar to the following.

``` java tab="Example"
micro-gw init petstore
```

```
petstore
├── api_definitions
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
**Optional options**

1) `-a` / `--api-definition`  
Provide a file path or URL of an API definition file.

```java tab="Example"
micro-gw init petstore -a https://github.com/wso2/product-microgateway/blob/master/samples/petstore_basic.yaml
```

*more options:*

- `--headers` - Request properties to download the file. Provide separated with Commas(,).
- `--values` - Corresponding values for request properties to download the file. Provide separated with Commas(,).
- `-k` / `--insecure` - for downloads from untrusted clients.


2) `-f` / `--force`     
    By default, when the microgateway CLI does not allow you to run "micro-gw init" upon an existing project. But, if you want to overwrite the existing project, use this option.

3) `-d` / `--deployment-config`     
    To override the default deployment configuration file for docker/kubernetes environments. The default is `<project_home>`/conf/deployment-config.toml

4) `-h` / `--help`      
    help command for the toolkit. Displays available options.

## import
This command is used to import a single or multiple APIs from WSO2 API Manager. During the import phase, the Microgateway CLI will communicate with the API Manager REST APIs and retrieve the details of the resources (APIs, policies ..) which are required to generate the microgateway project artifacts. 

There are two main ways to execute the Import Command.

1) import a single API.
Specify the name and the version of the API using the below format.

```java tab="Format"
micro-gw import <project> -a <api-name> -v <version> [args..]
```

```java tab="Example"
micro-gw import pizzashack-project -a PizzaShackAPI -v 1.0.0
```


2) Setting up a project for a group of APIs using a label.
Specify the label which represents the group of APIs using the below format.

```java tab="Format"
micro-gw import <project> -l <label-name> [args..]
```

```java tab="Example"
micro-gw import accounts-project -l accounts
```

**Optional options**

1. `-l` / `--label`     
Name of the label which indicates a group of APIs.

2. `-u` / `--username`  
The username of the user.

3. `-p` / `--password`  
The password of the user.

4. `-s` / `--server-url`    
API-M base URL assuming all the portals are running in the same node. By default, the base URL is https://localhost:9443/.

5. `-t` / `--truststore`    
Path to the trustStore file. By default the `<MGW-TK_HOME>`/lib/platform/bre/security/ballerinaTruststore.p12 will be used.

6. `-w` / `--truststore-pass`   
Password for the given trustStore. The default trustStore password will be used if the password is not specified.

7. `-c` / `--config`    
Path to the microgateway configuration file. The `<MGW-TK_HOME>`/conf/toolkit-config.toml configuration file will be used if not specified.

8. `-a` / `--api-name`  
Name of the API.

9. `-v` / `--version`   
The version of the API.

10. `-k` / `--insecure`     
Allow insecure server connections when using SSL.

## build
This will create executables for your project. After adding API definitions, configuring toolkit-config.toml and deployment-config.toml and other interceptors and extensions, run the below command.

Please refer to [the Configurations documentation]({{base_path}}/reference/configurations/configurations-overview/) for more information on configuring the project.

``` java tab="Example"
micro-gw build petstore
```

You will see something similar to the following if it is successful.

``` yml
BUILD SUCCESSFUL
Target: project/petstore/target/petstore.jar
```

**Optional options**

1.  `-d` / `--deployment-config`    
To override the default deployment configuration file for docker/kubernetes environments. The default is `<project_home>`/conf/deployment-config.toml.

2. `--docker`   
Prompt for docker image and docker base image for docker configurations.

3. `--docker-image`     
Provide a docker image name and tag.

4. `--docker-base-image`
Provide the docker base image.

4. `--help` / `-h`  
help command for the toolkit. Displays available options.

``` java tab="Example"
micro-gw build petstore --docker-image petstore:v1 --docker-base-image wso2/micro-gw:latest
```

## reset
The reset command is used to clear all the previous user configurations, such as username, clientId, etc, and to reset user-related configuration to default. This will enable us to provide different user credentials to be used when importing APIs from WSO2 API Manager. When running the import command for the first time, those configurations are stored internally within the microgateway CLI tool and will be reused for subsequent import commands. To rerun the import command with different user credentials, the reset command needs to be run beforehand.

```
micro-gw reset
```
**Optional options**

1. `-c` / `--config`    
To reset to an external config file.

``` java tab="Example"
micro-gw reset -c /home/user/micro-gw/external-config.toml
```

## version
It provides the micro-gw toolkit version.

```
micro-gw version
```
