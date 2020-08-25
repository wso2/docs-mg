# Support Custom Claims Mapping

When JWT retrieved from Multiple Identity providers, Microgateway can map the relevant claims as a supported one to validate the JWT.  The claims of the incoming authentication JWT can differ based on the Authorization server that issued the JWT token. This feature maps the incoming JWT remote claims to the local claims of the Microgateway as well as the claims expected by the back end service.  You can use custom claims mapping transformation in micro gateway when you have different keys or values of claims in your JWT token to make it as a Microgateway supported one.

In a detailed view for an example if you generate JWT with claims with a different key like claim key is ‘scp’ and value is ‘[“write”, “read”]’. But in Microgateway it only validates correctly if the key is scope and values of scope separated by space as a string. Hence through the custom claims mapping feature, you can map the remote claims to local claims to support the Microgateway.  Here remote claims come from an external provider thus local claims need to be mapped.
 
1. If the key of the remote claims is different from the custom claims, then you need to change the configuration as below.  You can define the multiple keys of the remote claim and local claim.

     Adding the following section to the micro-gw.conf file which is located in the <MGW-RUNTIME-HOME>/conf directory, you can change the configurations in the jwtTokenConfig. Here the remote claim is scp and the local claim is scope.
#### Configuring claims key
```
[[jwtTokenConfig]]
     issuer = "https://localhost:9443/oauth2/token"
     audience = "http://org.wso2.apimgt/gateway"
     certificateAlias = "wso2apim310"
     #support custom claim mapping
     claimMapperClassName = "org.wso2.micro.gateway.jwtTransformer.DefaultJwtTransformer"
     [[jwtTokenConfig.claims]]
       remoteClaim = "scp"
       localClaim = "scope" 
```
         
      
2. If the format of the remote claim value differs from the custom claims then you should write a class to transform it into the correct format and define the claim mapping class name in the config file.

     The following subsections explain how you can work with claims value transformation. Here the existing scope value is in an array and it shoud be changed to string with space.
#### Writing JWT transformer for claims value
Microgateway defines java interfaces to write the JWT value transformer. The interface for the transformer defined below. Developers can implement these interfaces to achieve custom transformation logic. 
```` java
package org.wso2.micro.gateway.jwt.transformer;
 
import java.util.Map;
 
/**
 * Defines the interface for writing the  jwt tranformation.
 */
public interface JWTValueTransformer {
 
    /**
     *  Transform the custom claims  into required format.
     * @param jwtClaims jwtClaims from given JWT
     * @return transformed JWT Claims
     */
    Map<String, Object> transformJWT(Map<String, Object> jwtClaims);
}
````
####  Writing a JWT claims value transformer
``` java
package org.mgw.jwt.jwtvaluetransformer;
 
import org.wso2.micro.gateway.jwt.transformer.JWTValueTransformer;
 
import java.util.Map;
 
/**
 * This class is for default Jwt transformer.
 */
public class customJwtTransformer implements JWTValueTransformer {
 
    @Override
    public Map<String, Object> transformJWT(Map<String, Object> jwtClaims) {
        String scope = "";
        if (jwtClaims.containsKey("scope")) {
            if (jwtClaims.get("scope") instanceof Object[]) {
                for (int i = 0; i < ((Object[]) jwtClaims.get("scope")).length; i++) {
                    scope += ((Object[]) jwtClaims.get("scope"))[i] + " ";
                }
                scope = scope.trim();
            }
            jwtClaims.put("scope", scope);
        }
        return jwtClaims;
    }
}
```

    !!! info
        Add the microgateway JWT transformer dependency.
        The java JWT transformer project will require the following dependency
        **JWT transformer dependency**
        ``` java
        <dependency>
            <groupId>org.wso2.am.microgw</groupId>
            <artifactId>mgw-jwt-transformer</artifactId>
            <version>3.2.0</version>
        </dependency>
        ```
#### Adding JWT transformer to the project 
Once JWT claims value transformer is written, then the JWT  transformer project should be built and the output jar should be placed in the <MGW-project>/lib directory. If third-party libraries are used when writing JWT claims mapping transformer, these custom jars should also be placed in the same directory.
#### Configuring claims Mapper class Name
```
[[jwtTokenConfig]]
  issuer = "https://host:port/issuer"
  audience = "http://org.wso2.apimgt/gateway"
  certificateAlias = "alias"
  #class name of JWT claims value mapper transformer.
  claimMapperClassName = "org.mgw.jwt.jwtvaluetransformer.customJwtTransformer"
```

