# Generating Backend JWT 

You can use JWT Generation in API Microgateway to send a customized JWT to the backend with user preferred claims. You have three possible options when selecting a method to send a JWT to the backend.

- [Passing a token to the backend](#passing-a-token-to-the-backend)
- [Generating a JWT using default configurations](#generating-a-jwt-using-default-configurations)
- [Generating a JWT using a custom JWT Generator](#generating-a-jwt-using-a-custom-jwt-generator)

### Setting the header of the backend request

When you are passing a token to the backend or when you are generating a JWT inside Microgateway, you can configure the header in which the JWT token will be passed to the backend. The following configuration should be added to the Microgateway runtime configuration in `<MICRO-GW-RUNTIME_HOME>/conf/micro-gw.conf`. (By default the header is set to X-JWT-Assertion)

``` yml
[jwtConfig]
    # JWT header when forwarding the request to the backend
    header = "X-JWT-Assertion"
```

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

### Generating a JWT using default configurations

You have the ability customize the token generation from the Microgateway runtime configuration in `micro-gw.conf` without a custom JWT generator since a default JWT generator implementation is included.

####JWT Generator configuration (micro-gw.conf)

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

Basically, outer `[ jwtGeneratorConfig ]` configuration contain the properties and the customizations of the generated JWT token whereas the inner `[ jwtGeneratorConfig.jwtGeneratorCaching ]` configuration handles the caching aspects of the generated JWT tokens.

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

After altering the configuration, you can run API Microgateway. When you are invoking a resource with a JWT token, you will be able to obtain the generated backend JWT.

### Generating a JWT using a custom JWT Generator

Microgateway defines an AbstractMGWJWTGenerator abstract class to write a custom JWT generator class. The abstract class is defined as below and the developers can extend this abstract class to implement their logic to generate the JWT. There are two abstract methods named populateStandardClaims and populateCustomClaims where you can write your logic to populate claims in the JWT. Furthermore, you have the capability to write your own logic overriding any of the current methods in the abstract class. 

#### AbstractMGWJWTGenerator class

```java
package org.wso2.micro.gateway.jwt.generator;

import com.nimbusds.jwt.JWTClaimsSet;
import net.minidev.json.JSONArray;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import java.io.FileInputStream;
import java.nio.charset.Charset;
import java.security.Key;
import java.security.KeyStore;
import java.security.MessageDigest;
import java.security.PrivateKey;
import java.security.Signature;
import java.security.cert.Certificate;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Map;

/**
 *  Abstract class for generate JWT for backend claims.
 */
public abstract class AbstractMGWJWTGenerator {
    private static final Logger logger = LogManager.getLogger(AbstractMGWJWTGenerator.class);
    private static final String NONE = "NONE";
    private static final String SHA256_WITH_RSA = "SHA256withRSA";
    private String dialectURI;
    private String signatureAlgorithm;
    private String keyStorePath;
    private String keyStorePassword;
    private String certificateAlias;
    private String privateKeyAlias;
    private int jwtExpiryTime;
    private ArrayList<String> restrictedClaims;
    private boolean cacheEnabled;
    private int cacheExpiry;
    private String tokenIssuer;
    private String[] tokenAudience;
    private Map<String, Object> apiDetails;
    private List<String> defaultRestrictedClaims;

    public AbstractMGWJWTGenerator(String dialectURI,
                                   String signatureAlgorithm,
                                   String keyStorePath,
                                   String keyStorePassword,
                                   String certificateAlias,
                                   String privateKeyAlias,
                                   int jwtExpiryTime,
                                   String[] restrictedClaims,
                                   boolean cacheEnabled,
                                   int cacheExpiry,
                                   String tokenIssuer,
                                   String[] tokenAudience) {
        this.keyStorePath = keyStorePath;
        this.keyStorePassword = keyStorePassword;
        this.certificateAlias = certificateAlias;
        this.privateKeyAlias = privateKeyAlias;
        this.jwtExpiryTime = jwtExpiryTime;
        this.dialectURI = dialectURI;
        this.signatureAlgorithm = signatureAlgorithm;
        this.cacheEnabled = cacheEnabled;
        this.cacheExpiry = cacheExpiry;
        this.tokenIssuer = tokenIssuer;
        this.tokenAudience = tokenAudience;
        this.restrictedClaims = new ArrayList<>(Arrays.asList(restrictedClaims));
        defaultRestrictedClaims = new ArrayList<>(Arrays.asList("iss", "sub", "aud", "exp",
                "nbf", "iat", "jti", "application", "tierInfo", "subscribedAPIs", "keytype"));
        this.restrictedClaims.addAll(defaultRestrictedClaims);
    }

    public String getPrivateKeyAlias() {
        return privateKeyAlias;
    }

    public void setPrivateKeyAlias(String privateKeyAlias) {
        this.privateKeyAlias = privateKeyAlias;
    }

    public List<String> getDefaultRestrictedClaims() {
        return defaultRestrictedClaims;
    }

    public void setDefaultRestrictedClaims(List<String> defaultRestrictedClaims) {
        this.defaultRestrictedClaims = defaultRestrictedClaims;
    }

    public String getCertificateAlias() {
        return certificateAlias;
    }

    public void setCertificateAlias(String certificateAlias) {
        this.certificateAlias = certificateAlias;
    }

    public Map<String, Object> getApiDetails() {
        return apiDetails;
    }

    public void setApiDetails(Map<String, Object> apiDetails) {
        this.apiDetails = apiDetails;
    }

    public String[] getTokenAudience() {
        return tokenAudience;
    }

    public void setTokenAudience(String[] tokenAudience) {
        this.tokenAudience = tokenAudience;
    }

    public String getTokenIssuer() {
        return tokenIssuer;
    }

    public void setTokenIssuer(String tokenIssuer) {
        this.tokenIssuer = tokenIssuer;
    }

    public boolean isCacheEnabled() {
        return cacheEnabled;
    }

    public void setCacheEnabled(boolean cacheEnabled) {
        this.cacheEnabled = cacheEnabled;
    }

    public int getCacheExpiry() {
        return cacheExpiry;
    }

    public void setCacheExpiry(int cacheExpiry) {
        this.cacheExpiry = cacheExpiry;
    }

    public ArrayList<String> getRestrictedClaims() {
        return restrictedClaims;
    }

    public void setRestrictedClaims(ArrayList<String> restrictedClaims) {
        this.restrictedClaims = restrictedClaims;
    }

    public String getKeyStorePath() {
        return keyStorePath;
    }

    public void setKeyStorePath(String keyStorePath) {
        this.keyStorePath = keyStorePath;
    }

    public String getKeyStorePassword() {
        return keyStorePassword;
    }

    public void setKeyStorePassword(String keyStorePassword) {
        this.keyStorePassword = keyStorePassword;
    }

    public String getDialectURI() {
        return dialectURI;
    }

    public void setDialectURI(String dialectURI) {
        this.dialectURI = dialectURI;
    }

    public String getSignatureAlgorithm() {
        return signatureAlgorithm;
    }

    public void setSignatureAlgorithm(String signatureAlgorithm) {
        this.signatureAlgorithm = signatureAlgorithm;
    }

    public int getJwtExpiryTime() {
        return jwtExpiryTime;
    }

    public void setJwtExpiryTime(int jwtExpiryTime) {
        this.jwtExpiryTime = jwtExpiryTime;
    }

    /**
     * Used to generate the JWT token.
     */
    public String generateToken(Map<String, Object> jwtInfo) throws Exception {
        String jwtHeader = buildHeader();
        String jwtBody = buildBody(jwtInfo);
        String base64UrlEncodedHeader = "";
        if (jwtHeader != null) {
            base64UrlEncodedHeader = encode(jwtHeader.getBytes(Charset.defaultCharset()));
        }
        String base64UrlEncodedBody = "";
        if (jwtBody != null) {
            base64UrlEncodedBody = encode(jwtBody.getBytes());
        }
        if (SHA256_WITH_RSA.equals(signatureAlgorithm)) {
            String assertion = base64UrlEncodedHeader + '.' + base64UrlEncodedBody;
            //get the assertion signed
            byte[] signedAssertion = signJWT(assertion);
            if (logger.isDebugEnabled()) {
                logger.debug("signed assertion value : " + new String(signedAssertion, Charset.defaultCharset()));
            }
            String base64UrlEncodedAssertion = encode(signedAssertion);
            return base64UrlEncodedHeader + '.' + base64UrlEncodedBody + '.' + base64UrlEncodedAssertion;
        } else {
            return base64UrlEncodedHeader + '.' + base64UrlEncodedBody + '.';
        }
    }

    /**
     * Used to build the JWT header.
     */
    public String buildHeader() throws Exception {
        String jwtHeader = null;
        if (NONE.equals(signatureAlgorithm)) {
            StringBuilder jwtHeaderBuilder = new StringBuilder();
            jwtHeaderBuilder.append("{\"typ\":\"JWT\",");
            jwtHeaderBuilder.append("\"alg\":\"");
            jwtHeaderBuilder.append("none");
            jwtHeaderBuilder.append('\"');
            jwtHeaderBuilder.append('}');
            jwtHeader = jwtHeaderBuilder.toString();
        } else if (SHA256_WITH_RSA.equals(signatureAlgorithm)) {
            jwtHeader = addCertToHeader();
        }
        return jwtHeader;
    }

    /**
     * Used to sign the JWT using the keystore.
     */
    public byte[] signJWT(String assertion) throws Exception {
        FileInputStream is;
        is = new FileInputStream(keyStorePath);
        KeyStore keystore = KeyStore.getInstance(KeyStore.getDefaultType());
        keystore.load(is, keyStorePassword.toCharArray());
        Key key = keystore.getKey(privateKeyAlias, keyStorePassword.toCharArray());
        Key privateKey = null;
        if (key instanceof PrivateKey) {
            privateKey = key;
        }
        //initialize signature with private key and algorithm
        Signature signature = Signature.getInstance(signatureAlgorithm);
        signature.initSign((PrivateKey) privateKey);
        //update signature with data to be signed
        byte[] dataInBytes = assertion.getBytes(Charset.defaultCharset());
        signature.update(dataInBytes);

        // close the file stream
        is.close();

        //sign the assertion and return the signature
        return signature.sign();
    }

    /**
     * Used to get the expiration time of the token.
     */
    public long getTTL() {
        if (cacheEnabled) {
            return cacheExpiry;
        } else {
            return jwtExpiryTime;
        }
    }

    /**
     * Used to add "ballerina"the certificate from the keystore to the header.
     */
    public String addCertToHeader() throws Exception {
        FileInputStream is;
        is = new FileInputStream(keyStorePath);
        KeyStore keystore = KeyStore.getInstance(KeyStore.getDefaultType());
        keystore.load(is, keyStorePassword.toCharArray());
        Certificate publicCert = keystore.getCertificate(certificateAlias);

        //generate the SHA-1 thumbprint of the certificate
        MessageDigest digestValue = MessageDigest.getInstance("SHA-1");
        byte[] der = publicCert.getEncoded();
        digestValue.update(der);
        byte[] digestInBytes = digestValue.digest();
        String publicCertThumbprint = hexify(digestInBytes);
        String base64UrlEncodedThumbPrint;
        base64UrlEncodedThumbPrint = java.util.Base64.getUrlEncoder()
                .encodeToString(publicCertThumbprint.getBytes("UTF-8"));
        StringBuilder jwtHeader = new StringBuilder();
        //Sample header
        //{"typ":"JWT", "alg":"SHA256withRSA", "x5t":"a_jhNus21KVuoFx65LmkW2O_l10"}
        //{"typ":"JWT", "alg":"[2]", "x5t":"[1]"}
        jwtHeader.append("{\"typ\":\"JWT\",");
        jwtHeader.append("\"alg\":\"");
        jwtHeader.append("RS256");
        jwtHeader.append("\",");

        jwtHeader.append("\"x5t\":\"");
        jwtHeader.append(base64UrlEncodedThumbPrint);
        jwtHeader.append('\"');

        jwtHeader.append('}');

        // close the file stream
        is.close();

        return jwtHeader.toString();
    }

    /**
     * Used to build the body with claims.
     */
    public String buildBody(Map<String, Object> jwtInfo) {
        JWTClaimsSet.Builder jwtClaimSetBuilder = new JWTClaimsSet.Builder();
        Map<String, Object> claims = populateStandardClaims(jwtInfo);
        Map<String, Object> customClaims = populateCustomClaims(jwtInfo, restrictedClaims);
        for (Map.Entry<String, Object> claimEntry : customClaims.entrySet()) {
            if (!claims.containsKey(claimEntry.getKey())) {
                claims.put(claimEntry.getKey(), claimEntry.getValue());
            } else {
                if (logger.isDebugEnabled()) {
                    logger.debug("Claim key " + claimEntry.getKey() + " already exist");
                }
            }
        }
        for (Map.Entry<String, Object> claimEntry : claims.entrySet()) {
            jwtClaimSetBuilder.claim(claimEntry.getKey(), claimEntry.getValue());
        }
        JWTClaimsSet jwtClaimsSet = jwtClaimSetBuilder.build();
        return jwtClaimsSet.toJSONObject().toString();
    }

    /**
     * Used for base64 encoding.
     */
    public String encode(byte[] stringToBeEncoded) {
        return java.util.Base64.getUrlEncoder().withoutPadding().encodeToString(stringToBeEncoded);
    }

    /**
     * Helper method to hexify a byte array.
     */
    public String hexify(byte bytes[]) {
        char[] hexDigits = {'0', '1', '2', '3', '4', '5', '6', '7',
                '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};

        StringBuilder buf = new StringBuilder(bytes.length * 2);
        for (byte aByte : bytes) {
            buf.append(hexDigits[(aByte & 0xf0) >> 4]);
            buf.append(hexDigits[aByte & 0x0f]);
        }
        return buf.toString();
    }

    /**
     * Method to convert Java array to JSONArray.
     */
    public JSONArray arrayToJSONArray(Object[] objectArray) {
        JSONArray jsonArray = new JSONArray();
        jsonArray.addAll(Arrays.asList(objectArray));
        return jsonArray;
    }

    public abstract Map<String, Object> populateStandardClaims(Map<String, Object> jwtInfo);
    public abstract Map<String, Object> populateCustomClaims(Map<String, Object> jwtInfo,
                                                             ArrayList<String> restrictedClaims);
}
```

#### Writing a JWT generator

```java
package sample.jwt.generator;

import org.apache.commons.lang3.StringUtils;
import org.wso2.micro.gateway.jwt.generator.AbstractMGWJWTGenerator;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

public class SampleJWTGenerator extends AbstractMGWJWTGenerator {

    public SampleJWTGenerator(String dialectURI, String signatureAlgorithm, String keyStorePath, String keyStorePassword, String certificateAlias, String privateKeyAlias, int jwtExpiryTime, String[] restrictedClaims, boolean cacheEnabled, int cacheExpiry, String tokenIssuer, String[] tokenAudience) {
        super(dialectURI, signatureAlgorithm, keyStorePath, keyStorePassword, certificateAlias, privateKeyAlias, jwtExpiryTime, restrictedClaims, cacheEnabled, cacheExpiry, tokenIssuer, tokenAudience);
    }

    @Override
    public Map<String, Object> populateStandardClaims(Map<String, Object> jwtInfo) {
        long currentTime = System.currentTimeMillis();
        long expireIn = currentTime + getTTL();
        String dialect = this.getDialectURI();
        Map<String, Object> claims = new HashMap();
        HashMap<String, Object> customClaims = (HashMap<String, Object>) jwtInfo.get("customClaims");
        claims.put("iss", getTokenIssuer());
        if (getTokenAudience().length == 1) {
            claims.put("aud", getTokenAudience()[0]);
        } else if (getTokenAudience().length != 0) {
            claims.put("aud", arrayToJSONArray(getTokenAudience()));
        }
        claims.put("jti", UUID.randomUUID().toString());
        claims.put("iat", (int) (currentTime / 1000));
        claims.put("exp", (int) (expireIn / 1000));
        if (StringUtils.isNotEmpty((CharSequence) jwtInfo.get("sub"))) {
            claims.put("sub", jwtInfo.get("sub"));
            claims.put(dialect + "/endUser", jwtInfo.get("sub"));
        }
        if (StringUtils.isNotEmpty((CharSequence) customClaims.get("scopes"))) {
            claims.put("scopes", (customClaims.get("scopes")));
        }
        return claims;
    }

    @Override
    public Map<String, Object> populateCustomClaims(Map<String, Object> jwtInfo, ArrayList<String> restrictedClaims) {
        Map<String, Object> claims = new HashMap();
        for (String key: jwtInfo.keySet()) {
            if (key.equals("customClaims")) {
                Map<String, Object> customClaims = (Map<String, Object>) jwtInfo.get(key);
                for (String subKey: customClaims.keySet()) {
                    if (!restrictedClaims.contains(subKey)) {
                        claims.put(subKey, customClaims.get(subKey));
                    }
                }
            } else {
                if (!restrictedClaims.contains(key)) {
                    claims.put(key, jwtInfo.get(key));
                }
            }
        }
        claims.put("custom", "claim");
        return claims;
    }
}
```

!!! info
    Add the microgateway JWT generator dependency to the custom project. The JWT generator project will require the following dependency. (The jar for the corresponding dependency is at `<MGW-TOOLKIT_HOOME>/lib/dependencies`)
    
    **JWT Generator dependency**
    ```
    <dependency>
      <groupId>org.wso2.am.microgw</groupId>
      <artifactId>mgw-jwt-generator</artifactId>
      <version>3.2.0</version>
    </dependency>
    ```

#### Adding a custom JWT generator to the project

Once the custom JWT generator is written, the project should be built and the output jar should be placed in the `<MGW-project>/lib` directory. If third-party libraries are used when writing interceptors, these custom jars should also be placed in the same directory.

You can provide the classpath of the custom JWT generator in the **generatorImpl** configuration in the `micro-gw.conf` file. Rest of the configurations explained in the previous section are applied when writing a custom JWT generator as well. Since, there are getters and setters for all the configuration properties in the abstract class you can use any of the configurations to implement your logic in the custom JWT generator.
    
```yml
[jwtGeneratorConfig]
  jwtGeneratorEnabled=true
  generatorImpl="sample.jwt.generator.SampleJWTGenerator"
```
