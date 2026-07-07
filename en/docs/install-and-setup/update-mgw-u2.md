# Updating WSO2 API Microgateway via a U2 Update

When applying a U2 update to WSO2 API Microgateway (MGW), you need to follow the steps below to ensure the update is applied correctly.

## Steps to follow during a U2 update

1.  After updating to the new U2 level, navigate to the `<MGW_TOOLKIT_HOME>/lib/` directory and check for a folder named `platform`. If it is present, remove it.

    !!! note
        Do not remove the `platform.zip` file.

2.  Navigate to the `<MGW_TOOLKIT_HOME>/lib/gateway-balo/` directory. For each extracted gateway folder (for example, `gateway-2020r1-java8-3.2.0`) that has a corresponding `.balo` file of the same name, remove the extracted folder but keep the `.balo` file.

3.  Navigate to a workspace folder and initialize a new project using the following command.

    ```bash
    micro-gw init <project-name> -a <api-definition-path>
    ```

4.  From the same workspace folder, build the project using the following command.

    ```bash
    micro-gw build <project-name>
    ```

    This generates a runnable JAR file for the project inside the `target` directory of the project.

5.  Deploy the API by starting WSO2 API Microgateway using the generated JAR file from the previous step. Run this command from the `<MGW_HOME>/bin` directory.

    ```bash
    gateway <path-to-generated-JAR-file>
    ```
