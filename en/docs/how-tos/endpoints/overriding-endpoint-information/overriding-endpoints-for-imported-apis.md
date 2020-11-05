# Overriding Endpoints for Imported APIs

!!! note
    This information is only applicable for APIs imported from WSO2 API Manager.

In the event, a backend service connection URL is added or edited, the backend connection URL provided in the respective OpenAPI definition needs to be overriden. The following section describes how this can be overriden on the APIs that are imported from the WSO2 API Manager.

#### Overriding endpoints at runtime

Use the following command to override the endpoints of an API imported from the WSO2 API Manager at server start-up.

Follow the instructions below to override the production or sandbox endpoint(s).

1.  Use the following command to start WSO2 Microgateway. Here the system variables are provided at server start-up to override the endpoint of the API, which has been added to WSO2 API Microgateway using an OpenAPI definition.

    ``` text tab="Override single endpoint"
    gateway <path-to-MGW-jar> 
    --<API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL>"
    ```
    
    ``` text tab="Override Multiple endpoints"
    gateway <path-to-MGW-jar> 
    --<API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint-URL-1>" 
    --<API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL_2>"
    ```
    
    - `<API-ID>` - ID generated for the imported API. You can find it by checking the file name of imported API located in `<project>/gen/api_definitions/`
    - `<endpoint-type>` - Use one of the following values based on the type of endpoint.
        - `prod` - Use this for a production endpoint.
        - `sand` - Use this for a sandbox endpoint.
    - `<endpoint-index>` - The endpoint index starts from 0. Therefore, when overriding a single endpoint this value is 0. If there are many URLs (e.g., load-balanced or failover), you can override them using endpoint indexes (e.g., 1, 2, 3).
    - `<path-to-MGW-jar>` - Enter the path to the WSO2 API Microgateway project's jar file ( `.jar` ).

    ``` text tab="Example 1 - single endpoint"
    gateway /home/user/petstore-project/target/petstore-project.jar  
    --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_0="http://wso2.com"
    ```
    
    ``` text tab="Example 2 - multiple endpoints"
    gateway /home/user/petstore-project/target/petstore-project.jar 
    --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_0="http://wso2.com" 
    --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_1="http://support.wso2.com"
    ```

2. If the backend is protected by basic authentication, provide username and password as follows.
    
    ``` text tab="Override single endpoint"
    gateway <path-to-MGW-jar> 
    --<API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL>" 
    --<API-ID>_<endpoint-type>_basic_username="<basic-auth-username>" 
    --<API-ID>_basic_password="<basic-auth-password>
    ```
    
    ``` text tab="Override Multiple endpoints"
    gateway <path-to-MGW-jar> 
    --<API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint-URL-1>" 
    --<API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint_URL_2>" 
    --<API-ID>_<endpoint-type>_basic_username="<basic-auth-username>" 
    --<API-ID>_basic_password="<basic-auth-password>
    ```
   
    - `<basic-auth-username>` - Provide the username                            
    - `<basic-auth-password>` - Provide the password                               
   
    ``` text tab="Example 1 - single endpoint"
    gateway /home/user/petstore-project/target/petstore-project.jar  
    --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_0="http://wso2.com"
    --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_username="admin" 
    --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_basic_password="admin"
    ```
    
    ``` text tab="Example 2 - multiple endpoints"
    gateway /home/user/petstore-project/target/petstore-project.jar 
    --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_0="http://wso2.com" 
    --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_1="http://support.wso2.com"
    --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_username="admin" 
    --60f7111f-fdc5-4cc7-b497-1cea64c6a97f_basic_password="admin"
    ```
 
    !!! Note
        If you do not specify a username, the username defined in the API definition during the API implementation will be used.

#### Overriding endpoints using system properties

Use the following command to provide the system variables as system properties to override more than one endpoint (e.g., load balance endpoint) that corresponds to an API, which was imported from WSO2 API Manager.

Follow the instructions below to override the production or sandbox endpoint(s).

1. Set the endpoint using system properties. 

    ``` java tab="Format"
    export <API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint-URL>" <API-ID>_<endpoint-type>_endpoint_<endpoint-index>="<endpoint-URL-2>" <path-to-MGW-jar>
    ```
    
    - `<API-ID>` - ID generated for the imported API. You can find it by checking the file name of imported API located in `<project>/gen/api_definitions/`
    - `<endpoint-type>` - Use one of the following values based on the type of endpoint.
       - `prod` - Use this for a production endpoint.
       - `sand` - Use this for a sandbox endpoint.
    - `<endpoint-index>` - The endpoint index starts from 0. Therefore, when overriding a single endpoint this value is 0. If there are many URLs (e.g., load-balanced or failover), you can override them using endpoint indexes (e.g., 1, 2, 3).
    - `<path-to-MGW-jar>` - Enter the path to the WSO2 API Microgateway project's jar file ( `.jar` ).
    
    ``` java tab="Example"
    export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_0="http://wso2.com" 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_endpoint_1="http://support.wso2.com" /home/user/petstore-project/target/petstore-project.jar
    ```

2. Optionally, define the Basic Auth credentials using system properties.

    !!! note
        This step is only applicable if your endpoint is protected by **Basic Auth credentials**.
    
    ``` java tab="Format"
    export <API-ID>_<endpoint-type>_basic_username="<basic-auth-username>"
    export <API-ID>_<endpoint-type>_basic_password="<basic-auth-password>"
    ```
    
    - `<basic-auth-username>` - Provide the username                            
    - `<basic-auth-password>` - Provide the password 
    
    ``` java tab="Example"
    export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_username="admin"
    export 60f7111f-fdc5-4cc7-b497-1cea64c6a97f_prod_basic_password="admin"
    ```
