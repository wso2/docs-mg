# Configurations

The following are the WSO2 API Microgateway related configuration files.

| File name                                                                              | Description                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `toolkit-config.toml` | This file defines the Microgateway toolkit configurations and is located in the `<MGW-TK_HOME>/conf` directory.                      |
| `micro-gw.conf`       | This file defines the Microgateway runtime configurations and is located in the `<MGW-RUNTIME-HOME>/conf` directory.  For more information on configuring this file in your environment, see [configuring Docker]({{base_path}}/reference/configurations/configuring-micro-gw-conf-for-docker) and [configuring Kubernetes]({{base_path}}/reference/configurations/configuring-micro-gw-conf-for-kubernetes).               |
| `deployment.toml` for Docker       | **If you are working with Docker**, you need to use this file as the input to the WSO2 API Microgateway Toolkit in order to create a Docker image.           |
| `deployment.toml` for Kubernetes   | **If you are working with Kubernetes**, you need to use this file as the input to the WSO2 API Microgateway Toolkit in order to create Kubernetes artifacts. |
