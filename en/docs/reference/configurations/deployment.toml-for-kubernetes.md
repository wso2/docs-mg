# deployment.toml for Kubernetes

You need to create a `deployment-config.toml` file and use it as the input to the WSO2 API Microgateway Toolkit in order to create a Docker image to be mounted on Kubernetes by including the runtime executable artifacts, so that you do not have to mount the artifacts from outside. When using this approach, it will generate the Docker images from the WSO2 API Microgateway base image by including all the API runtime artifacts as well.

### Sample file

``` toml
[kubernetes]
  [kubernetes.kubernetesDeployment]
    enable = true
    #name = ''
    #labels = '{"": ""}'
    #replicas = ''
    #enableLiveness = ''
    #initialDelaySeconds = ''
    #periodSeconds = ''
    #livenessPort = ''
    #imagePullPolicy = ''
    #imagePullSecrets = ['']
    #image = ''
    #env = ''
    #buildImage = ''
    #cmd = 'CMD gateway ${APP} --b7a.config.file=conf/micro-gw.conf'
    [kubernetes.kubernetesDeployment.copyFiles]
        enable = false
        [[kubernetes.kubernetesDeployment.copyFiles.files]]
            source = ''
            target = ''
    [kubernetes.kubernetesDeployment.livenessProbe]
        enable = false
        port = ''
        initialDelaySeconds = ''
        periodSeconds = ''
    [kubernetes.kubernetesDeployment.readinessProbe]
        enable = false
        port = ''
        initialDelaySeconds = ''
        periodSeconds = ''
    #dockerHost = ''
    #dockerCertPath = ''
    #push = ''
    #registry = 'index.docker.io/${DOCKER_USERNAME}'
    #username = ''
    #password = ''
    #baseImage = ''
    #singleYAML = ''
  [kubernetes.kubernetesService]
    enable = true
    #name = ''
    #labels = '{"": ""}'
    serviceType = 'NodePort'
    #port = ''
  #[kubernetes.kubernetesServiceHttps]
      #enable = false
      #name = ''
      #labels = '{"": ""}'
      #serviceType = ''
      #port = ''
  #[kubernetes.kubernetesServiceHttp]
      #enable = false
      #name = ''
      #labels = '{"": ""}'
      #serviceType = ''
      #port = ''
  #[kubernetes.kubernetesServiceToken]
      #enable = false
      #name = ''
      #labels = '{"": ""}'
      #serviceType = ''
      #port = ''
  [kubernetes.kubernetesIngress]
    enable = false
    #name = ''
    #labels = '{"", ""}'
    #hostname = ''
    #annotations = ''
    #path = ''
    #targetPath = ''
    #ingressClass = ''
  [kubernetes.secureKubernetesIngress]
    enable = false
    #name = ''
    #labels = '{"": ""}'
    #hostname = ''
    #annotations = ''
    #path = ''
    #targetPath = ''
    #ingressClass = ''
    #keyStorePath = '${ballerina.home}/bre/security/ballerinaKeystore.p12'
    #keyStorePassword = '$env{Keystore_Password}'
  [kubernetes.kubernetesHpa]
    enable = false
    #name = ''
    #labels = '{"": ""}'
    #minReplicas = ''
    #maxReplicas = ''
    #cpuPrecentage = ''
  [kubernetes.kubernetesSecret]
    enable = false
    #[[kubernetes.kubernetesSecret.secrets]]
      #name = ''
      #mountPath = ''
      #readOnly = false
      #data = ['']
  [kubernetes.kubernetesConfigMap]
    enable = true
    ballerinaConf = '<MICROGW_TOOLKIT_HOME>/resources/conf/micro-gw.conf'
    #[[kubernetes.kubernetesConfigMap.configMaps]]
      #name = ''
      #mountPath = ''
      #data = ['']
      #readOnly = false
  [kubernetes.kubernetesPersistentVolumeClaim]
    enable = false
    #name = ''
    #mountPath = ''
    #readOnly = ''
    #accessMode = ''
    #volumeClaimSize = ''
```

### Descriptions for the configurations

#### kubernetes.kubernetesDeployment

<table>
<thead>
<tr class="header">
<th>Parameter</th>
<th>Description</th>
<th>Default value</th>
</tr>
</thead>
<tbody>
<tr>
<td><p><code>              name             </code></p></td>
<td>Name of the deployment.</td>
<td>&lt;outputfilename&gt;-deployment</td>
</tr>
<tr>
<td><p><code>              namespace             </code></p></td>
<td>Namespace of the deployment.</td>
<td>null</td>
</tr>
<tr>
<td><p><code>              labels             </code></p></td>
<td>The labels related to the particular deployment.</td>
<td>&quot;app: &lt;outputfilename&gt;&quot;</td>
</tr>
<tr>
<td><p><code>              replicas             </code></p></td>
<td>The number of replicas.</td>
<td>1</td>
</tr>
<tr>
<td><p><code>              dependsOn             </code></p></td>
<td>The endpoints that the deployment depends on.</td>
<td>null</td>
</tr>
<tr>
<td><p><code>              enableLiveness             </code></p></td>
<td>Enable or disable the liveness probe.</td>
<td>false</td>
</tr>
<tr>
<td><p><code>              initialDelaySeconds             </code></p></td>
<td>The initial delay in seconds before performing the first probe.</td>
<td>10s</td>
</tr>
<tr>
<td><p><code>              periodSeconds             </code></p></td>
<td>The time interval between liveness probes in seconds.</td>
<td>5s</td>
</tr>
<tr>
<td><p><code>              livenessPort             </code></p></td>
<td>The port checked by the liveness probe.</td>
<td>&lt;ServicePort&gt;</td>
</tr>
<tr>
<td><p><code>              imagePullPolicy             </code></p></td>
<td>Docker image pull policy.</td>
<td>IfNotPresent</td>
</tr>
<tr>
<td><p><code>              image             </code></p></td>
<td>Docker image with tag.</td>
<td>&lt;output file name&gt;:latest</td>
</tr>
<tr>
<td><p><code>              env             </code></p></td>
<td>List of environment variables.</td>
<td>null</td>
</tr>
<tr>
<td><p><code>              buildImage             </code></p></td>
<td>Enable or disable building the Docker image.</td>
<td>true</td>
</tr>
<tr>
<td><code>             copyFiles            </code></td>
<td>Copy external files for the Docker image.</td>
<td>null</td>
</tr>
<tr>
<td><code>             dockerHost            </code></td>
<td>Docker host IP and docker PORT.<br />
Example:<br />
<code>                           tcp://192.168.99.100:2376                         </code></td>
<td>null</td>
</tr>
<tr>
<td><code>             dockerCertPath            </code></td>
<td>Docker cert path.</td>
<td>null</td>
</tr>
<tr>
<td><code>             push            </code></td>
<td>Enable or disable pushing the Docker image to the registry.<br />
This can only be enabled if <code>             buildImage            </code> parameter is set to true.</td>
<td>false</td>
</tr>
<tr>
<td><code>             registry            </code></td>
<td>Docker registry URL</td>
<td>null</td>
</tr>
<tr>
<td><code>             username            </code></td>
<td>Username for the Docker registry.</td>
<td>null</td>
</tr>
<tr class="odd">
<td><code>             password            </code></td>
<td>Password for the Docker registry.</td>
<td>null</td>
</tr>
<tr>
<td><code>             baseImage            </code></td>
<td>Base image to create the Docker image.</td>
<td>ballerina/ballerina:latest</td>
</tr>
<tr>
<td><code>             imagePullSecrets            </code></td>
<td>Image pull secrets value.</td>
<td>null</td>
</tr>
<tr>
<td><code>             singleYAML            </code></td>
<td>Enable or disable generating a single YAML file for all k8s resources.</td>
<td>false</td>
</tr>
</tbody>
</table>

#### kubernetes.kubernetesService

| Parameter                              | Description                   | Default value                 |
|----------------------------------------|-------------------------------|-------------------------------|
| `             name            `        | Name of the service.          | &lt;service name&gt;-service  |
| `             labels            `      | The labels for the service.   | "app: &lt;outputfilename&gt;" |
| `             serviceType            ` | The type of service.          | ClusterIP                     |
| `             port            `        | The port used by the service. | Port number                   |

#### kubernetes.kubernetesIngress

<table>
<thead>
<tr class="header">
<th><p>Parameter</p></th>
<th><p>Description</p></th>
<th><p>Default value</p></th>
</tr>
</thead>
<tbody>
<tr>
<td><p><code>              name             </code></p></td>
<td><p>Name of the Ingress.</p></td>
<td><p>&lt;service name&gt;-ingress</p></td>
</tr>
<tr>
<td><p><code>              labels             </code></p></td>
<td><p>The labels for the service.</p></td>
<td><p>&quot;app: &lt;outputfilename&gt;&quot;</p></td>
</tr>
<tr>
<td><p><code>              hostname             </code></p></td>
<td><p>Host name of the Ingress.</p></td>
<td><p>&lt;service name&gt;.com</p></td>
</tr>
<tr>
<td><p><code>              annotations             </code></p></td>
<td><p>Map of additional annotations.</p></td>
<td><p>null</p></td>
</tr>
<tr>
<td><p><code>              path             </code></p></td>
<td><p>Resource path.</p></td>
<td><p>/</p></td>
</tr>
<tr>
<td><p><code>              targetPath             </code></p></td>
<td><p>This URL will be used to rewrite the target URI, which defines where the traffic should be redirected.</p>
<p><br />
</p></td>
<td><p>null</p></td>
</tr>
<tr>
<td><p><code>              ingressClass             </code></p></td>
<td><p>Ingress class.</p></td>
<td><p>nginx</p></td>
</tr>
<tr>
<td><p><code>              enableTLS             </code></p></td>
<td><p>Enable or disable Ingress transport level security (TLS).</p></td>
<td><p>false</p></td>
</tr>
</tbody>
</table>

#### kubernetes.kubernetesHPA

| Parameter                                | Description                                       | Default value                 |
|------------------------------------------|---------------------------------------------------|-------------------------------|
| `             name            `          | Name of the Horizontal Pod Autoscaler             | &lt;service name&gt;-hpa      |
| `             labels            `        | The labels for the service.                       | "app: &lt;outputfilename&gt;" |
| `             minReplicas            `   | The minimum number of replicas in the deployment. | No of replicas in deployment  |
| `             maxReplicas            `   | The maximum number of replicas in the deployment. | minReplicas+1                 |
| `             cpuPrecentage            ` | CPU percentage to start scaling.                  | 50                            |

#### kubernetes.kubernetesSecret

| Parameter                            | Description                                    | Default value                |
|--------------------------------------|------------------------------------------------|------------------------------|
| `             name            `      | Name of the secret mount.                      | &lt;service\_name&gt;-secret |
| `             mountPath            ` | Path to the mount on the container.            | null                         |
| `             readOnly            `  | Denotes whether the mount is read-only or not. | true                         |
| `             data            `      | Paths to the data files.                       | null                         |

#### kubernetes.kubernetesConfigMap

| Parameter                                | Description                                                  | Default value                    |
|------------------------------------------|--------------------------------------------------------------|----------------------------------|
| `             name            `          | Name of the config map mount                                 | &lt;service\_name&gt;-config-map |
| `             mountPath            `     | Path to the mount on the container                           | null                             |
| `             readOnly            `      | Denotes whether the mount is read-only or not.               | true                             |
| `             ballerinaConf            ` | Location of the [Ballerina](https://ballerina.io) conf file. | null                             |
| `             data            `          | Paths to the data files.                                     | null                             |

#### kubernetes.kubernetesPersistentVolumeClaim

| Parameter                                  | Description                                    | Default value |
|--------------------------------------------|------------------------------------------------|---------------|
| `             name            `            | Name of the secret mount.                      | null          |
| `             annotations            `     | The metadata annotations in the map.           | null          |
| `             mountPath            `       | Path to mount on the container.                | null          |
| `             readOnly            `        | Denotes whether the mount is read-only or not. | false         |
| `             accessMode            `      | Access mode.                                   | ReadWriteOnce |
| `             volumeClaimSize            ` | Size of the volume claim.                      | null          |
