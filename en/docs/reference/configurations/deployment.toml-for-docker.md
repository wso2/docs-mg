# deployment.toml for Docker

You need to create a `         deployment-config.toml        ` file and use it as the input to the WSO2 API Microgateway Toolkit in order to create a Docker image by including the runtime executable artifacts, so that you do not have to mount the artifacts from outside. When using this approach, it will generate the Docker images from the WSO2 API Microgateway base image by including all the API runtime artifacts as well.

-   [Sample file](#deployment.tomlforDocker-Samplefile)
-   [Descriptions for the configurations](#deployment.tomlforDocker-Descriptionsfortheconfigurations)
    -   [docker.dockerConfig](#deployment.tomlforDocker-docker.dockerConfig)
    -   [docker.dockerCopyFiles](#deployment.tomlforDocker-docker.dockerCopyFiles)

### Sample file

The following is a sample `         deployment-config.toml        ` file.

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
    baseImage = 'wso2/wso2micro-gw:3.0.1'
    #enableDebug = ''
    #debugPort = ''
    #push = ''
    #username = ''
    #password = ''
    [docker.dockerCopyFiles]
    enable = true
    [[docker.dockerCopyFiles.files]]
    source = '<MICROGW_TOOLKIT_HOME>/resources/conf/micro-gw.conf'
    target = '/home/ballerina/conf/micro-gw.conf'
    isBallerinaConf = true
```

    ### Descriptions for the configurations

    ##### docker.dockerConfig

    <table>
    <thead>
    <tr class="header">
    <th>Parameter</th>
    <th>Description</th>
    <th>Default Value</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td><code>             name            </code></td>
    <td><div class="content-wrapper">
    <p>Name of the Docker image.</p>
    !!! note
    <p>Note that the Docker image name must be specified in lower case.</p>

</div></td>
<td>output <code>             .balx            </code> file name</td>
</tr>
<tr class="even">
<td><code>             registry            </code></td>
<td>Name of the Docker registry.</td>
<td>None</td>
</tr>
<tr class="odd">
<td><code>             tag            </code></td>
<td>The Docker image tag.</td>
<td>latest</td>
</tr>
<tr class="even">
<td><code>             buildImage            </code></td>
<td>Enable or disable the option to build the Docker images.</td>
<td>true</td>
</tr>
<tr class="odd">
<td><code>             dockerHost            </code></td>
<td>Docker host IP and Docker PORT. (e.g., minikube IP and Docker PORT)</td>
<td><a href="unix://docs.wso2.com/var/run/docker.sock">unix:///var/run/docker.sock</a></td>
</tr>
<tr class="even">
<td><code>             dockerCertPath            </code></td>
<td>Docker cert path.</td>
<td>null</td>
</tr>
<tr class="odd">
<td><code>             baseImage            </code></td>
<td>Base image to create the Docker image.</td>
<td>ballerina/ballerina:latest</td>
</tr>
<tr class="even">
<td><code>             enableDebug            </code></td>
<td>Enable or disable debugging.</td>
<td>false</td>
</tr>
<tr class="odd">
<td><code>             debugPort            </code></td>
<td>Remote debug port.</td>
<td>5005</td>
</tr>
<tr class="even">
<td><code>             push            </code></td>
<td>Enable or disable pushing the Docker image to the remote registry.</td>
<td>false</td>
</tr>
<tr class="odd">
<td><code>             username            </code></td>
<td>Username for Docker registry.</td>
<td>None</td>
</tr>
<tr class="even">
<td><code>             password            </code></td>
<td>Password for Docker registry.</td>
<td>None</td>
</tr>
</tbody>
</table>

##### docker.dockerCopyFiles

| Parameter                                  | Description                                                                                       | Default Value |
|--------------------------------------------|---------------------------------------------------------------------------------------------------|---------------|
| `             source            `          | The source path of the file (in your local machine).                                              | None          |
| `             target            `          | The target path (inside the container).                                                           | None          |
| `             isBallerinaConf            ` | This option indicates whether the file is a [Ballerina](https://ballerina.io) config file or not. | false         |

The following code snippet, which is present in the above sample code, is used to enable the Docker copy files configuration in order to copy the `         micro-gw.conf        ` file to the Docker image as it contains the Key Manager configurations, JWT configurations, etc.

``` java
    [docker.dockerCopyFiles]
    enable = true
    [[docker.dockerCopyFiles.files]]
    source = '<MICROGW_TOOLKIT_HOME>/resources/conf/micro-gw.conf'
    target = '/home/ballerina/conf/micro-gw.conf'
    isBallerinaConf = true
```
