# Adding Extensions

### Startup extension

In WSO2 API Microgateway, a startup extension can be used to implement any requirement during the server starts up. By writing customized code inside the startup extension you can do anything when the server starts up.

For example, you can load whatever you want into memory by defining your own object model in your customized code. The `startup_extension.bal` file, which is inside the `<PROJECT_HOME>/extensions` directory, can be extended using the custom code. The following sub-sections explain how you can work with the start-up extensions to define your own model.

The following is an example of a startup extension that you can use to make a call to the public Petstore API `pet/{petid}` resource and store the response against the ID in a global map when the server starts up.

1.  Write the customized Java startup extension.
    For example let's write an extension to store the response from Petstore API against the PetId.

    **Write the extension to store the response from Petstore API**

    ``` java
    package org.wso2.apimgt.gateway.cli.startup;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.wso2.apimgt.gateway.cli.constants.RESTServiceConstants;
    import org.wso2.apimgt.gateway.cli.utils.RESTAPIUtils;

    import java.io.IOException;
    import java.net.HttpURLConnection;
    import java.net.URL;
    import java.util.HashMap;
    import java.util.Map;

    /**
    *  This class is to load the response into memory by calling the public Petstore API against id.
    */
     class StoreResponse {
        private static final Logger log = LoggerFactory.getLogger("ballerina");
        private static Map<Integer, String> responseHashMap = new HashMap<>();

        public static void readDataToMemory() {
            URL url;
            HttpURLConnection urlConn = null;
            try {
                for (int i = 2; i < 3; i++) {
                    String urlStr = "https://petstore.swagger.io/v2/pet/" + i;
                    url = new URL(urlStr);
                    urlConn = (HttpURLConnection) url.openConnection();
                    urlConn.setDoOutput(true);
                    urlConn.setRequestMethod(RESTServiceConstants.GET);
                    int responseCode = urlConn.getResponseCode();
                    log.debug("Response code: {}", responseCode);
                    if (responseCode == 200) {
                        String responseStr = RESTAPIUtils.getResponseString(urlConn.getInputStream());
                        responseHashMap.put(i,responseStr);
                        log.trace("Response body: {}", responseStr);
                    } else {
                        throw new Exception("Error occurred " + responseCode);
                    }
                 }
            } catch (IOException e) {

            // handle exception
             } catch (Exception e) {
                e.printStackTrace();
             } finally {
                 if (urlConn != null) {
                    urlConn.disconnect();
                 }
             }
         }
     }
    ```

2.  Define the class of the the customized extension as a startup extension.
By default, the `startup_extension.bal ` file is an empty function. You can modify this class to achieve the custom logic by writing a Java startup extension.

**Write the customized startup extension in startup\_extension.bal file**

``` java
import ballerinax/java;

# Represents the startup extension filter.
#
#
public function startupExtension() {
    loadDataToMemory();
}

# This function is used to load response from petstore API by id into memory during the server starts up.
#
function loadDataToMemory() = @java:Method {
name: "readDataToMemory",
class: "org.wso2.apimgt.gateway.cli.startup.StoreResponse"
} external;
```

3. Add the startup extension to the project.
   1.  Build the startup extension project.
       After the building process is complete, you will derive a JAR as an output.
   2.  Place the latter mentioned JAR in the `<MGW-project>/lib` directory.

    !!! note
        If your startup extension consists of any third-party libraries, you should also place these custom JARs in the `MGW-project>/lib` directory.

### Extension filter

In WSO2 API Microgateway, the users can customize and extend the request and response handling process using the extension filter object. The extension filter object is defined in the `extension_filter.bal` file, which is located in the `<MGW-HOME>/extension` directory.

!!! info
    It is written in Ballerina 1.2.7 so follow API docs in [Ballerina documentation](https://ballerina.io/learn/api-docs/ballerina/) for language guide.

The following is an example of an extension filter to customize the throttled out responses (e.g. error code, error message, and error description).

**Writing a customized extension filter**

``` java
import ballerina/http;
import wso2/gateway;


# Represents the extension filter, which used to customize and extend the request and response handling
#
#
public type ExtensionFilter object {
    # Request filter function.
    # + caller - http caller object.
    # + request - http request object.
    # + context - http filter context object.
    # + return - Whether the filter has passed or not
    public function filterRequest (http:Caller caller, http:Request request, http:FilterContext context) returns
    boolean {
        return true;
    }

    public function filterResponse(http:Response response, http:FilterContext context) returns boolean {
        var failed =  context.attributes[gateway:FILTER_FAILED];
        if (failed is boolean) {
            if (failed) {
                    int statusCode = <int>context.attributes[gateway:HTTP_STATUS_CODE];
                    if(statusCode == gateway:THROTTLED_OUT) {
                        setThrottleFailureResponse(response, context );
                     } 
                     return true;
                     //return gateway:createFilterResult(false, statusCode, errorMessage);
            }
        } else {
        //Nothing to handle
            return true;
        }
        return true;
    }

};

# This method can be used to send custom error message when message throttled out
#
function setThrottleFailureResponse(http:Response response, http:FilterContext context) {
    int statusCode = <int>context.attributes[gateway:HTTP_STATUS_CODE];
    string errorDescription = <string>context.attributes[gateway:ERROR_DESCRIPTION];
    string errorMesssage = <string>context.attributes[gateway:ERROR_MESSAGE];
    int errorCode = <int>context.attributes[gateway:ERROR_CODE];
    response.statusCode = statusCode;
    response.setContentType(gateway:APPLICATION_JSON);
    json payload = {fault : {
      code : errorCode,
      message : errorMesssage,
      description : errorDescription
    }};
    response.setJsonPayload(payload);
}
```
