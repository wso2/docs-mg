# Overriding Endpoints for Imported APIs

!!! note
    This information is only applicable for APIs imported from WSO2 API Manager.

In the event you change your existing backend service connection URL or add a new backend, you will need to override your backend connection URL that you provided in the respective open API definition. The following section describes how you can override your endpoint information for APIs that are imported from WSO2 API Manager.

-   [Overriding a single endpoint at runtime](#OverridingEndpointsforImportedAPIs-Overridingasingleendpointatruntime)
-   [Overriding multiple endpoints at runtime](#OverridingEndpointsforImportedAPIs-Overridingmultipleendpointsatruntime)
-   [Overriding a single endpoint at runtime which is protected by basic authentication](#OverridingEndpointsforImportedAPIs-Overridingasingleendpointatruntimewhichisprotectedbybasicauthentication)
-   [Overriding multiple endpoints at runtime which are protected by basic authentication](#OverridingEndpointsforImportedAPIs-Overridingmultipleendpointsatruntimewhichareprotectedbybasicauthentication)
-   [Overriding a single endpoint using system properties](#OverridingEndpointsforImportedAPIs-Overridingasingleendpointusingsystemproperties)
-   [Overriding multiple endpoints using system properties](#OverridingEndpointsforImportedAPIs-Overridingmultipleendpointsusingsystemproperties)

### Overriding a single endpoint at runtime

Use the following command to provide the overriding information at server start-up to override the endpoint of an API, which was imported from WSO2 API Manager.

Follow the instructions below to override a single endpoint, which is either a production or sandbox endpoint:

1.  Start WSO2 Microgateway.

    -   [**Format**](#format-single)
    -   [**Example**](#example-single)

    **Format**

    ``` java
        gateway <path-to-MGW-executable-file> --<API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL>"
    ```

        -   **`                 <endpoint-type>                ` -** Use one of the following values based on the type of endpoint.

        -   -   -   `                     prod                    ` - Use this for a production endpoint.
        -   `                     sand                    ` - Use this for a sandbox endpoint.

        -   `                               <endpoint-index>                             ` - **** The endpoint index starts from 0. Therefore, when overriding a single endpoint this value is 0.
        -   **`                 <path-to-MGW-executable-file>                `** - Enter the path to the WSO2 API Microgateway project's executable file ( `                .jar               ` ).

        **Example**

    ``` java
        gateway /home/user/petstore-project/target/petstore-project.jar  --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_0="http://wso2.com"
    ```

        ### 
        Overriding multiple endpoints at runtime

        Use the following command to provide the overriding information at server start-up to override more than one endpoint (e.g., load balance endpoint) that corresponds to an API, which was imported from WSO2 API Manager.

        Follow the instructions below to override multiple endpoints, which are either a production or sandbox endpoint:

        1.  Start WSO2 Microgateway.

        -   [**Format**](#format-multiple)
        -   [**Example**](#example-multiple)

        **Format**

        ``` java
            gateway <path-to-MGW-executable-file> --<API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint-URL-1>" --<API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL_2>"
        ```

            -   **`                   <endpoint-type> -                  `** Use one of the following values based on the type of endpoint.

            -   -   -   `                       prod                      ` - Use this for a production endpoint.
            -   `                       sand                      ` - Use this for a sandbox endpoint.

            -   

            **`                   <endpoint-index>                  `** - The endpoint index starts from 0. If there are many URLs (e.g., load-balanced or failover) , you can override them using endpoint indexes (e.g., 1, 2, 3) .

            -   **`                   <path-to-MGW-executable-file>                  `** - Enter the path to the WSO2 API Microgateway project's executable file ( `                  .jar                 ` ).

            -   -   -   `                                               `

            **Example**

        ``` java
            gateway /home/user/petstore-project/target/petstore-project.jar --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_0="http://wso2.com" --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_1="http://support.wso2.com"
        ```

            ### Overriding a single endpoint at runtime which is protected by basic authentication

            Use the following command to provide the system variables at server start-up to override the endpoint of an API, which was imported from WSO2 API Manager.

            Follow the instructions below to override a single endpoint, which is either a production or sandbox endpoint:

            1.  Optionally, define the Basic Auth credentials using system properties.

            !!! note
            This step is only applicable if your endpoint is protected by **Basic Auth credentials** and if you are **not passing the Basic Auth credentials at runtime** .

    -   [**Format**](#format1-single-base)
    -   [**Example**](#example1-single-base)

    **Format**

    ``` java
        export <API-ID>_<endpoint-type>_basic_username="<basic-auth-username>"
        export <API-ID>_<endpoint-type>_basic_password="<basic-auth-password>"
    ```

        -   `                                                                  <endpoint-type>                 ` **** **-** Use one of the following values based on the type of the endpoint. `              `
        -   `                 prod                ` - Use this for a production endpoint.
        -   `                 sand                ` - Use this for a sandbox endpoint.

        **Example**

    ``` java
        export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_username="admin"
        export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_password="admin"
    ```

        2.  Start WSO2 Microgateway.

        -   [**Format**](#format-single-base)
        -   [**Example**](#example-single-base)

        **Format**

    ``` java
        gateway <path-to-MGW-executable-file> --<API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL>" --<API-ID>_<endpoint-type>_basic_username="<basic-auth-username>" --<API-ID>_basic_password="<basic-auth-password>"
    ```

        -   **`                                   <endpoint-type>                 ` -** Use one of the following values based on the type of the endpoint.

        -   -   -   `                     prod                    ` - Use this for a production endpoint.
        -   `                     sand                    ` - Use this for a sandbox endpoint.

        -   `                               <endpoint-index>                             ` - **** The endpoint index starts from 0. Therefore, when overriding a single endpoint this value is 0.
        -   **`                 <path-to-MGW-executable-file>                `** - Enter the path to the WSO2 API Microgateway project's executable file ( `                .balx               ` ).

        -   **Basic Auth parameters**

        !!! note
        The following parameters are only required if your endpoint is **protected by Basic Auth credentials** and if you **have not defined the Basic Auth credentials using system properties** in [step 1](#OverridingEndpointsforImportedAPIs-step1) .

        -   -   -   **`                       <API-ID>_<endpoint-type>_basic_username                      `** `                      ="<basic-auth-username>"                     `

                -   **`                       <API-ID>_basic_password                      `** `                      ="<basic-auth-password>"                                           `

        !!! note
            If you do not specify a username, the username that you defined in the API definition, which you specified during the API implementation, is used.

    **Example**

    ``` java
        gateway /home/user/petstore-project/target/petstore-project.jar --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_0="http://wso2.com" --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_username="admin" --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_basic_password="admin"
    ```

        ### 
        Overriding multiple endpoints at runtime which are protected by basic authentication

        Use the following command to provide the system variables at server start-up to override more than one endpoint (e.g., load balance endpoint) that corresponds to an API, which was imported from WSO2 API Manager.

        Follow the instructions below to override multiple endpoints, which are either a production or sandbox endpoint:

        1.  Optionally, define the Basic Auth credentials using system properties.

        !!! note
        This step is only applicable if your endpoint is protected by **Basic Auth credentials** and if you are **not passing the Basic Auth credentials at runtime** .

        -   [**Format**](#format3)
        -   [**Example**](#example3)

        **Format**

        ``` java
            export <API-ID>_<endpoint-type>_basic_username="<basic-auth-username>"
            export <API-ID>_<endpoint-type>_basic_password="<basic-auth-password>"
        ```

            -   `                                   <endpoint-type>                                 ` **-** Use one of the following values based on the type of endpoint. `                `
            -   `                   prod                  ` - Use this for a production endpoint.
            -   `                   sand                  ` - Use this for a sandbox endpoint.

            **Example**

        ``` java
            export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_username="admin"
            export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_password="admin"
        ```

            2.  Start WSO2 Microgateway.

            -   [**Format**](#format-multiple-base)
            -   [**Example**](#example-multiple-base)

            **Format**

        ``` java
            gateway <path-to-MGW-executable-file> --<API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint-URL-1>" --<API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL_2>" --<API-ID>_<endpoint-type>_basic_username="<basic-auth-username>" --<API-ID>_basic_password="<basic-auth-password>"
        ```

            -   **`                                       <endpoint-type>                   ` -** Use one of the following values based on the type of endpoint.

            -   -   -   `                       prod                      ` - Use this for a production endpoint.
            -   `                       sand                      ` - Use this for a sandbox endpoint.

            -   

            **`                   <endpoint-index>                  `** - The endpoint index starts from 0. If there are many URLs (e.g., load balanced or fail over) , you can override them using endpoint indexes (e.g., 1, 2, 3) .

            -   **`                   <path-to-MGW-executable-file>                  `** - Enter the path to the WSO2 API Microgateway project's executable file ( `                  .balx                 ` ).

            -   **Basic Auth parameters**

            !!! note
            The following parameters are only required if your endpoint is **protected by Basic Auth credentials** and if you **have not defined the Basic Auth credentials using system properties** in [step 1](#OverridingEndpointsforImportedAPIs-step1a) .

            -   -   -   **`                         <API-ID>_<endpoint-type>_basic_username                        `** `                        ="<basic-auth-username>"                       `

                    -   **`                         <API-ID>_basic_password                        `** `                        ="<basic-auth-password>"                                               `

            !!! note
                If you do not specify a username, the username that you defined in the API definition, which you specified during the API implementation, is used.

        **Example**

        ``` java
            gateway /home/user/petstore-project/target/petstore-project.jar --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_0="http://wso2.com" --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_1="http://support.wso2.com" --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_username="admin" --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_basic_password="admin"
        ```

            ### Overriding a single endpoint using system properties

            Use the following command to provide the system variables as system properties to override the endpoint of an API, which was imported from WSO2 API Manager.

            Follow the instructions below to override a single endpoint, which is either a production or a sandbox endpoint:

            1.  Optionally, define the Basic Auth credentials using system properties.

            !!! note
            This step is only applicable if your endpoint is protected by **Basic Auth credentials** .

    -   [**Format**](#format2)
    -   [**Example**](#example2)

    **Format**

    ``` java
        export <API-ID>_<endpoint-type>_basic_username="<basic-auth-username>"
        export <API-ID>_<endpoint-type>_basic_password="<basic-auth-password>"
    ```

        -   `                                                                  <endpoint-type>                 ` **-** Use one of the following values based on the type of the endpoint. `                             `
        -   `                 prod                ` - Use this for a production endpoint.
        -   `                 sand                ` - Use this for a sandbox endpoint.

        **Example**

    ``` java
        export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_username="admin"
        export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_password="admin"
    ```

        2.  Set the endpoint using system properties.

        -   [**Format**](#single-format-dev)
        -   [**Example**](#single-example-dev)

        **Format**

    ``` java
        export <API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint-URL>" <path-to-MGW-executable-file>
    ```

        -   **`                                   <endpoint-type>                 ` -** Use one of the following values based on the type of the endpoint.

        -   -   -   `                     prod                    ` - Use this for a production endpoint.
        -   `                     sand                    ` - Use this for a sandbox endpoint.

        -   **`                 <endpoint-index>                `** - The endpoint index starts from 0. Therefore, when overriding a single endpoint this value is 0. **`                                 `**

        -   **`                 <path-to-MGW-executable-file>                `** - Enter the path to the WSO2 API Microgateway project's executable file ( `                .balx               ` ).

        **Example**

    ``` java
        export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_0="http://wso2.com" /home/user/petstore-project/target/petstore-project.jar
    ```

        ### Overriding multiple endpoints using system properties

        Use the following command to provide the system variables as system properties to override more than one endpoint (e.g., load balance endpoint) that corresponds to an API, which was imported from WSO2 API Manager.

        Follow the instructions below to override more than one endpoint , which is either a production or a sandbox endpoint:

        1.  Optionally, define the Basic Auth credentials using system properties.

        !!! note
        This step is only applicable if your endpoint is protected by **Basic Auth credentials** .

    -   [**Format**](#format4)
    -   [**Example**](#example4)

    **Format**

    ``` java
        export <API-ID>_<endpoint-type>_basic_username="<basic-auth-username>"
        export <API-ID>_<endpoint-type>_basic_password="<basic-auth-password>"
    ```

        -   `                                                                  <endpoint-type>                 ` **-** Use one of the following values based on the type of the endpoint. `              `
        -   `                 prod                ` - Use this for a production endpoint.
        -   `                 sand                ` - Use this for a sandbox endpoint.

        **Example**

    ``` java
        export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_username="admin"
        export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_password="admin"
    ```

        Set the endpoint using system properties. **
        **

        -   [**Format**](#format5)
        -   [**Example**](#example5)

        **Format**

    ``` java
        export <API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint-URL>" <API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint-URL-2>" <path-to-MGW-executable-file>
    ```

        -   **`                                   <endpoint-type>                 ` -** Use one of the following values based on the type of the endpoint.

        -   -   -   `                     prod                    ` - Use this for a production endpoint.
        -   `                     sand                    ` - Use this for a sandbox endpoint.

        -   **`                 <endpoint-index> -                `** The endpoint index starts from 0. If there are many URLs (e.g., load balanced or fail over) , you can override them using endpoint indexes (e.g., 1, 2, 3) .

        -   **`                 <path-to-MGW-executable-file>                `** - Enter the path to the WSO2 API Microgateway project's executable file ( `                .balx               ` ).

        **Example**

    ``` java
        export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_0="http://wso2.com" 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_1="http://support.wso2.com" /home/user/petstore-project/target/petstore-project.jar
    ```


