# Install on VM

Follow the instructions below to install the WSO2 API Microgateway related binary distributions, namely WSO2 Microgateway Toolkit and WSO2 Microgateway Runtime.

-   [Microgateway Toolkit](#InstallonVM-MicrogatewayToolkit)
-   [Microgateway Runtime](#InstallonVM-MicrogatewayRuntime)

### Microgateway Toolkit

1.  Download the [WSO2 API Microgateway toolkit distribution](https://wso2.com/api-management/api-microgateway/).

2.  Extract the archive file to a dedicated directory for the WSO2 API Microgateway Toolkit, which will hereafter be referred to as `<MGW-TK_HOME>`.
    
3.  Append the full path of the `/bin` directory of the extracted WSO2 API Microgateway toolkit distribution to the PATH environment variable.
    This allows you to run the WSO2 API Microgateway toolkit related commands from any directory location.
    <details>
    <summary>Click here for more instructions on configure path variable in your OS.</summary>  
    **Setup toolkit path in Linux/MacOS**
    1. Execute the following in your terminal.
    ``` java tab="Format"
        export PATH=$PATH:<extracted_toolkit_location>/bin
    ```
    
    **Setup toolkit path in Windows**

    Use one of the following approaches to define the paths.
    
    1.  Set the Microgateway toolkit distribution path using the following command in the terminal.
    
        ``` java tab="Format"
        setx "%path%;<extracted_toolkit_location>/bin"
        ```
    
    2.  Set the Microgateway toolkit distribution path using the UI in Windows.
    
        !!! note
            The steps involved in setting the environment variables may vary based on the version of your Windows OS.
    
        1.  Copy the path to the Microgateway Toolkit `bin` directory (`<path-to-wso2am-micro-gw-toolkit>\bin`).
        
        2.  Click **Edit the system preferences**.
        
            ![]({{base_path}}/assets/img/install-and-setup/edit-environment-variable.png)
        
        3.  Navigate to the **Advanced** tab and click **Environment Variables**.
        
            ![]({{base_path}}/assets/img/install-and-setup/Environment-variables.png)
        
        4.  Navigate to the path system variable and click **Edit**.
        
            ![]({{base_path}}/assets/img/install-and-setup/select-path-system-var.png)    
        
        5.  Click **New,** add the path of the Microgateway Toolkit `bin` directory that you copied previously and click **OK** three times to save the newly added system variable.
    
            ![]({{base_path}}/assets/img/install-and-setup/add-new-variable.png)
    </details>    
4.  Optionally, [configure WSO2 API Microgateway Toolkit with WSO2 API Manager 3.2.0]({{base_path}}/install-and-setup/configuration-for-wso2-api-manager/#configuring-the-microgateway-32x-toolkit) .

    !!! note
        This is only applicable if you are working with WSO2 API Manager 3.2.0.

### Microgateway Runtime

1.  Ensure that your system meets the following basic prerequisites.

    |        |       |
    |--------|-------|
    | Memory | 256MB |
    | Cores  | 2     |
    | OS     | Any   |

2.  Download the [WSO2 API Microgateway distribution](https://wso2.com/api-management/api-microgateway/) based on your OS and extract it.

3.  Extract the archive file to a dedicated directory for the WSO2 API Microgateway **Runtime**, which will hereafter be referred to as `<MGW_HOME>`.

4.  Append the full path of the `/bin` directory of the extracted WSO2 API Microgateway distribution to the PATH environment variable.
    This allows you to run the WSO2 API Microgateway distribution-related commands from any directory location.
    <details>
    <summary>Click here for more instructions on configure path variable in your OS.</summary>  
    **Setup runtime path in Linux/MacOS**
    
    ``` java tab="Format"
    export PATH=$PATH:<extracted_distribution_location>/bin
    ```
        
    **Setup runtime path in Windows**
    
    Use one of the following approaches to define the paths.

    1.  Set the Microgateway toolkit distribution path using the following command in the terminal.

        ``` java tab="Format"
        setx "%path%;<extracted_distribution_location>/bin"
        ```

    2.  Set the Microgateway runtime distribution path using the UI in Windows.
    </details>
5.  Optionally, [configure WSO2 API Microgateway Runtime with WSO2 API Manager 3.2.0]({{base_path}}/install-and-setup/configuration-for-wso2-api-manager/#configuring-the-microgateway-32x-runtime) .

!!! note
    This is only applicable if you are working with WSO2 API Manager 3.2.0.


