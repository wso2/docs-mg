# Generating Backend JWT 

You can use JWT Generation in API Microgateway to send a customized JWT to the backend with user preferred claims. You have three possible options when selecting a method to send a JWT to the backend.

- [Passing a token to the backend](#passing-a-token-to-the-backend)
- [Generating a JWT inside Microgateway](#generating-a-jwt-inside-microgateway)

### Passing a token to the backend

When you are using JWT authentication, you can pass an already generated JWT token in the 'backendJwt' claim in the authentication JWT token. Therefore, if the authentication token consist the 'backendJwt' claim in the payload, the token will be set on to the desired header when sending to the backend without a generation process.

####Sample payload

```json
{
    "sub": "admin@carbon.super",
    "aud": "JzCLuJq6kHIpMhK7C5OhiWN7cPsa",
    "nbf": 1595447942,
    "azp": "JzCLuJq6kHIpMhK7C5OhiWN7cPsa",
    "scope": "am_application_scope default",
    "iss": "https://localhost:9443/oauth2/token",
    "exp": 1595451542,
    "iat": 1595447942,
    "jti": "0987c6d3-f11a-402c-8eb2-b21fb7b97f4d",
    "backendJwt": "eyJ4NXQiOiJNell4TW1Ga09HWXdNV0kwWldObU5EY3hOR1l3WW1NNFpUQTNNV0kyTkRBelpHUXpOR00wWkdSbE5qSmtPREZrWkRSaU9URmtNV0ZoTXpVMlpHVmxOZyIsImtpZCI6Ik16WXhNbUZrT0dZd01XSTBaV05tTkRjeE5HWXdZbU00WlRBM01XSTJOREF6WkdRek5HTTBaR1JsTmpKa09ERmtaRFJpT1RGa01XRmhNelUyWkdWbE5nX1JTMjU2IiwiYWxnIjoiUlMyNTYifQ.eyJzdWIiOiJhZG1pbkBjYXJib24uc3VwZXIiLCJhdWQiOiJKekNMdUpxNmtISXBNaEs3QzVPaGlXTjdjUHNhIiwibmJmIjoxNTk1NDQ3OTQyLCJhenAiOiJKekNMdUpxNmtISXBNaEs3QzVPaGlXTjdjUHNhIiwic2NvcGUiOiJhbV9hcHBsaWNhdGlvbl9zY29wZSBkZWZhdWx0IiwiaXNzIjoiaHR0cHM6XC9cL2xvY2FsaG9zdDo5NDQzXC9vYXV0aDJcL3Rva2VuIiwiZXhwIjoxNTk1NDUxNTQyLCJpYXQiOjE1OTU0NDc5NDIsImp0aSI6IjA5ODdjNmQzLWYxMWEtNDAyYy04ZWIyLWIyMWZiN2I5N2Y0ZCJ9.Ewz50wbRkFnod4-l5iQzuayHG6O5820Ykg8fQcj_7mIBDAOnWCk7j2a9KOFlJtMyqUXfkchgZEOM0sN9o7GmyYPxpw7x0pjHalNlrdNX8uwnrwaRfgn7G_9d57iFEUTyo7RHfR8uYNJYzSjHgd4x-Vy0-8fZm2OErtoVA0gH7pwnhnE8x3AQOTnBKbGEsWRoR6UmRNGCDwr23GlQ5v7GbPj4puJiE5Z-p5DNOtWuWX1BOpn9IpNn3nio-9wvE9DQUssDojn2G6t8BlM9vv9gMJJT3zK64tfCoWuhTMPn74vs3zGuYy2KO7x7dtULcv2o56slFCqHAID44KuB93vS7A"
}
```

After altering the configuration, you can run API Microgateway. When you are invoking a resource with a JWT token, you will be able to obtain the generated backend JWT.

### Generating a JWT inside Microgateway

Microgateway defines an [AbstractMGWJWTGenerator abstract class](https://github.com/wso2/product-microgateway/blob/master/components/micro-gateway-jwt-generator/src/main/java/org/wso2/micro/gateway/jwt/generator/AbstractMGWJWTGenerator.java) to write a custom JWT generator class and developers can extend this abstract class to implement their logic to generate the JWT. There are two abstract methods named populateStandardClaims and populateCustomClaims where you can write your logic to populate claims in the JWT. Furthermore, you have the capability to write your own logic overriding any of the current methods in the abstract class. 

#### Writing a JWT generator

You can find a sample implementation of a JWT generator from [here](https://github.com/wso2/product-microgateway/tree/master/samples/sample-jwt-generator) where the populateCustomClaims method is used to add a new claim to the token. Developers can import the mgw-jwt-generator depenedency as a jar to their local maven repository (The jar for the corresponding dependency is at `<MGW-TOOLKIT_HOOME>/lib/dependencies` directory). 

Apart from writing a JWT generator, developers have the capability use the default [MGWJWTGeneratorImpl class](https://github.com/wso2/product-microgateway/blob/master/components/micro-gateway-jwt-generator/src/main/java/org/wso2/micro/gateway/jwt/generator/MGWJWTGeneratorImpl.java) without implementing a custom class for JWT generation and manipulate the token, tweaking the properties of the token using [JWT generator configurations](#jwt-generator-configurations).

#### Adding a custom JWT generator to the project

Once the custom JWT generator is written, the project should be built and the output jar should be placed in the `<MGW-project>/lib` directory. If third-party libraries are used when writing the JWT generator, these custom jars should also be placed in the same directory.

You can provide the classpath of the custom JWT generator in the **generatorImpl** configuration in the `<MICRO-GW-RUNTIME_HOME>/conf/micro-gw.conf` file. Rest of the configurations explained in the previous section are applied when writing a custom JWT generator as well. Since, there are getters and setters for all the configuration properties in the abstract class you can use any of the configurations to implement your logic in the custom JWT generator.
    
```yml
[jwtGeneratorConfig]
  jwtGeneratorEnabled=true
  generatorImpl="sample.jwt.generator.SampleJWTGenerator"
```

### JWT Generator Configurations

You have the ability customize the token generation from the Microgateway runtime configuration in `<MICRO-GW-RUNTIME_HOME>/conf/micro-gw.conf`.

```yml
# JWT Generator configurations
[jwtGeneratorConfig]
  # Enable jwt generator
  jwtGeneratorEnabled=false
  # Dialect prefix that can be added to the claims
  claimDialect="http://wso2.org/claims"
  # Signature algorithm used to sign the JWT token (only SHA256withRSA and NONE is supported)
  signingAlgorithm="SHA256withRSA"
  # Certificate alias from the keystore
  certificateAlias="ballerina"
  # Private key alias from the keystore
  privateKeyAlias="ballerina"
  # JWT token expiry time - ms (valid only if the jwt generator caching mechanism is disabled)
  tokenExpiry=900000
  # Restricted claims as an array that should not be included in the backend JWT token
  # Example: restrictedClaims=["claim1","claim2","claim3"]
  restrictedClaims=[]
  # Token issuer standard claim
  issuer="wso2.org/products/am"
  # Token audience standard claim
  audience=["http://org.wso2.apimgt/gateway"]
  # JWT token generator implementation
  generatorImpl="org.wso2.micro.gateway.jwt.generator.MGWJWTGeneratorImpl"
  # JWT Generator cache configurations
  [jwtGeneratorConfig.jwtGeneratorCaching]
    # Enable jwt generator token caching
    tokenCacheEnable=true
    # Token cache expiry time (ms)
    tokenCacheExpiryTime=900000
    # Token cache capacity
    tokenCacheCapacity=10000
    # Token cache eviction factor
    tokenCacheEvictionFactor=0.25
```

Basically, outer `[jwtGeneratorConfig]` configuration contains the properties and the customizations of the generated JWT token whereas the inner `[jwtGeneratorConfig.jwtGeneratorCaching]` configuration handles the caching aspects of the generated JWT tokens.

| Configuration | Description                         | Default |
|---------------|-------------------------------------|---------|
| jwtGeneratorEnabled  | Enable JWT Generation process. (Generation will be skipped even though it is enabled if 'backendJwt' claim is present in the authentication JWT token) | false |
| claimDialect | Add a claim dialect to the standard claims. (By default it is used in the standard claims generated by an APIM instance) | http://wso2.org/claims |
| signingAlgorithm | Signing algorithm used to sign the JWT. When algorithm is specified as NONE, the token will not be signed. (This configuration can only have two values 'SHA256withRSA' or 'NONE') | SHA256withRSA | 
| certificateAlias | Alias of the certificate from keystore which is included on the JWT header section 'x5t'. | ballerina | 
| privateKeyAlias | Alias of the key from the keystore which is used to sign the JWT. | ballerina | 
| tokenExpiry | Validity period of the generated JWT token. (Valid only if the jwt generator caching is disabled, else cache expiry time will be set as the token validity period)  | 900000 | 
| restrictedClaims | Array of custom claims that should be omitted in the generated JWT token. | [ ] | 
| issuer | Issuer of the generated JWT. ('iss' standard claim) | wso2.org/products/am | 
| audience | Audience array of the generated JWT. ('aud' standard claim) | ["http://org.wso2.apimgt/gateway"] | 
| generatorImpl | JWT Generator implementation. (By default a JWT generator implementation is provided in Microgateway whereas you can add your custom implementation here) | org.wso2.micro.gateway.jwt.generator.MGWJWTGeneratorImpl | 
| tokenCacheEnable | Enable JWT Generator caching which enables to cache generated JWT so that it is not generating separate JWTs for each invocation. | true | 
| tokenCacheExpiryTime | Expiry time of the JWT Generator cache | 900000 | 
| tokenCacheCapacity | Capacity of the JWT Generator cache | 10000 | 
| tokenCacheEvictionFactor | Eviction factor of the JWT Generator cache | 0.25 | 

#### Setting the header of the backend request

When you are passing a token to the backend or when you are generating a JWT inside Microgateway, you can configure the header in which the JWT token will be passed to the backend. The following configuration should be added to the Microgateway runtime configuration in `micro-gw.conf`. (By default the header is set to X-JWT-Assertion)

``` yml
[jwtConfig]
    # JWT header when forwarding the request to the backend
    header = "X-JWT-Assertion"
```
