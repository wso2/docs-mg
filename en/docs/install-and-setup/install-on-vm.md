# Install on VM

Follow the instructions below to install the WSO2 API Microgateway related binary distributions, namely WSO2 Microgateway Toolkit and WSO2 Microgateway Runtime.

-   [Microgateway Toolkit](#InstallonVM-MicrogatewayToolkit)
-   [Microgateway Runtime](#InstallonVM-MicrogatewayRuntime)

### Microgateway Toolkit

1.  Log in to the command line (Terminal on Mac & Linux).

2.  Download the [WSO2 API Microgateway toolkit distribution](https://wso2.com/api-management/api-microgateway/) .

3.  Extract the archive file to a dedicated directory for the WSO2 API Microgateway **Toolkit** , which will hereafter be referred to as `           <MGW-TK_HOME>          ` .

4.  Append the full path of the `           /bin          ` folder of the extracted WSO2 API Microgateway toolkit distribution to the PATH environment variable.
    This allows you to run the WSO2 API Microgateway toolkit related commands from any directory location.

    -   [**Linux & Mac**](#toolkit-path-unix)
    -   [**Windows**](#toolkit-path-windows)

    **Example**

    ``` java
        export PATH=$PATH:<extracted_toolkit_location>/bin
    ```

        Use one of the following approaches to define the paths.

        -   Set the Microgateway toolkit distribution path using the following command in the terminal.

        **Example**

        ``` java
            setx "%path%;<extracted_toolkit_location>/bin"
        ```

            -   Set the Microgateway toolkit distribution path using the UI in Windows.

            !!! note
            The steps involved in setting the environment variables may vary based on the version of your Windows OS.

        1.  Copy the path to the Microgateway Toolkit `                 bin                ` folder ( `                 <path-to-wso2am-micro-gw-toolkit>\bin                ` ).
        2.  Click **Edit the system preferences** .
            ![](attachments/141247030/141247034.png){width="800"} '
        3.  Navigate to the **Advanced** tab and click **Environment Variables** .
            ![](attachments/141247030/141247033.png)
        4.  Navigate to the path system variable and click **Edit.**

            ![](attachments/141247030/141247031.png)

        5.  Click **New,** add the path of the Microgateway Toolkit `                  bin                 ` folder that you copied previously and click **OK** three times to save the newly added system variable.
            ![](attachments/141247030/141247032.png)

5.  Optionally, [configure WSO2 API Microgateway Toolkit with WSO2 API Manager 3.0.0](Configuration-for-WSO2-API-Manager_141247041.html#ConfigurationforWSO2APIManager-ConfiguringtheMicrogateway3.0.xToolkit) .

    !!! note
        This is only applicable if you are working with WSO2 API Manager 3.0.0.

### Microgateway Runtime

1.  Ensure that your system meets the following basic prerequisites.

    |        |       |
    |--------|-------|
    | Memory | 256MB |
    | Cores  | 2     |
    | OS     | Any   |

2.  Log in to the command line (Terminal on Mac).

3.  Download the [WSO2 API Microgateway distribution](https://wso2.com/api-management/api-microgateway/) based on your OS and extract it.

4.  Extract the archive file to a dedicated directory for the WSO2 API Microgateway **Runtime** , which will hereafter be referred to as `           <MGW_HOME>          ` .

5.  Append the full path of the `           /bin          ` folder of the extracted WSO2 API Microgateway distribution to the PATH environment variable.
    This allows you to run the WSO2 API Microgateway distribution-related commands from any directory location.

    -   **Linux and OS X**

        **Example**

        ``` java
            export PATH=$PATH:<extracted_distribution_location>/bin
        ```

            -   **Windows**

            Use one of the following approaches to define the paths.

            -   Set the Microgateway toolkit distribution path using the following command in the terminal.

            **Example**

            ``` java
                setx "%path%;<extracted_distribution_location>/bin"
            ```

                -   Set the Microgateway runtime distribution path using the UI in Windows.

                6.  Optionally, [configure WSO2 API Microgateway Runtime with WSO2 API Manager 3.0.0](Configuration-for-WSO2-API-Manager_141247041.html#ConfigurationforWSO2APIManager-ConfiguringtheMicrogateway3.0.xRuntime) .

                !!! note
                This is only applicable if you are working with WSO2 API Manager 3.0.0.


