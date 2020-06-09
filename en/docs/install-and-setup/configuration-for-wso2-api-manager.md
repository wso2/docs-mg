# Configuration for WSO2 API Manager

Follow the instructions below to configure the WSO2 Microgateway Toolkit and the WSO2 Microgateway Runtime in order to use it with [WSO2 API Manager](https://apim.docs.wso2.com/en/latest/) . The WSO2 Microgateway 3.1.0 supports WSO2 API Manager versions, 2.6.0 onwards (2.6.0, 3.0.0 and 3.1.0).

-   [Configuring the Microgateway 3.1.x Toolkit](#ConfigurationforWSO2APIManager-ConfiguringtheMicrogateway3.1.xToolkit)
-   [Configuring the Microgateway 3.1.x Runtime](#ConfigurationforWSO2APIManager-ConfiguringtheMicrogateway3.1.xRuntime)

#### Configuring the Microgateway 3.1.x Toolkit

1.  Add the public certificate of WSO2 API Manager to the trust store.

    !!! info
        Refer the documentation on [Importing Certificates to the API Microgateway](https://docs.wso2.com/display/MG310/Importing+Certificates+to+the+API+Microgateway+Truststore) [Truststore](https://docs.wso2.com/display/MG310/Importing+Certificates+to+the+API+Microgateway+Truststore) for more information.

    Export the public certificate of WSO2 API Manager.
    A sample command is given below.

    ``` java
        keytool -export -alias wso2carbon -file wso2carbon.crt -keystore <API-M_HOME>/repository/resources/security/wso2carbon.jks
    ```

        Import the certificate to the trust store in the Microgateway Toolkit.

    ``` java
        keytool -import -trustcacerts -alias wso2carbon2 -file wso2carbon.crt -keystore <MGW_TOOLKIT_HOME>/lib/platform/bre/security/ballerinaTruststore.p12
    ```

        2.  Change the REST API version and DCR version to be compatible with the WSO2 API Manager.

        | API Manager version | REST API version | DCR version |
        |---------------------|------------------|-------------|
        | 2.6.0               | v0.14            | v0.14       |
        | 3.0.0               | v1.0             | v0.15       |
        | 3.1.0               | v1.1             | v0.16       |

        Open the `            <MGW_TOOLKIT_HOME>/conf/toolkit-config.toml           ` file and change the `                         restVersion                       ` and **dcrVersion** accordingly.

        #### Configuring the Microgateway 3.1.x Runtime

        1.  Add the public certificate of WSO2 API Manager to the trust store.

        !!! info
        Refer the documentation on [Importing Certificates to the API Microgateway](https://docs.wso2.com/display/MG310/Importing+Certificates+to+the+API+Microgateway+Truststore) [Truststore](https://docs.wso2.com/display/MG310/Importing+Certificates+to+the+API+Microgateway+Truststore) for more information.

    Export public certificate of WSO2 API Manager.
    A sample command is given below.

    ``` java
        keytool -export -alias wso2carbon -file wso2carbon.crt -keystore <API-M_HOME>/repository/resources/security/wso2carbon.jks
    ```

        Import the certificate using the command given below, to the trust store in the Microgateway runtime.

    ``` java
        keytool -import -trustcacerts -alias wso2carbon2 -file wso2carbon.crt -keystore <MGW_RUNTIME_HOME>/runtime/bre/security/ballerinaTruststore.p12
    ```

        2.  Open the `           <MGW_RUNTIME_HOME>/conf/micro-gw.conf          ` file. Add the alias (e.g. wso2carbon2) to the truststore to corresponding configurations.
        For example, to configure the JWT authentication for API Manager JWTs, add the following JWT issuer configuration to the `           micro-gw.conf          ` .

    ``` java
        [[jwtTokenConfig]]
        issuer="https://localhost:9443/oauth2/token"
        audience="http://org.wso2.apimgt/gateway"
        certificateAlias="wso2carbon2"
        validateSubscription = false
    ```

        You can now successfully invoke APIs through the Microgateway using the JWT tokens issued by WSO2 API Manager.
