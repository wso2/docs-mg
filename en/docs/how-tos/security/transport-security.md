# Transport Security

### SSL configurations

The following are the default configurations for a gateway instance. Add the following configuration block to the &lt;MGW\_RT-HOME&gt;/conf/micro-gw.conf to change default values.

``` yml tab="micro-gw.conf"
[mutualSSLConfig]
    protocolName = "TLS"
    protocolVersions = "TLSv1.2,TLSv1.1"
    ciphers="TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256, TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA256, TLS_ECDH_RSA_WITH_AES_128_CBC_SHA256,TLS_DHE_RSA_WITH_AES_128_CBC_SHA256,TLS_DHE_DSS_WITH_AES_128_CBC_SHA256, TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA, TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA,TLS_ECDH_RSA_WITH_AES_128_CBC_SHA,TLS_DHE_RSA_WITH_AES_128_CBC_SHA, TLS_DHE_DSS_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256  ,TLS_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDH_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDH_RSA_WITH_AES_128_GCM_SHA256, TLS_DHE_RSA_WITH_AES_128_GCM_SHA256,TLS_DHE_RSA_WITH_AES_128_GCM_SHA256,TLS_DHE_DSS_WITH_AES_128_GCM_SHA256  ,TLS_ECDHE_ECDSA_WITH_3DES_EDE_CBC_SHA,TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA,SSL_RSA_WITH_3DES_EDE_CBC_SHA, TLS_ECDH_ECDSA_WITH_3DES_EDE_CBC_SHA,TLS_ECDH_RSA_WITH_3DES_EDE_CBC_SHA,SSL_DHE_RSA_WITH_3DES_EDE_CBC_SHA, SSL_DHE_DSS_WITH_3DES_EDE_CBC_SHA,TLS_EMPTY_RENEGOTIATION_INFO_SCSV"
    sslVerifyClient = "optional"
```

|Heading|Description|
|-------|-----------|
|mutualSSLConfig|These are used to configure mutual SSL properties for the gateway secure listener.|

| Sub Heading| Description                      | Default value|
|------------|----------------------------------|--------------|
|protocolName| SSL Protocol to be used| "TLS"|
|protocolVersions | SSL/TLS protocols to be enabled |"TLSv1.2,TLSv1.1"|
|ciphers |List of ciphers to be used|"TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA,TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_CBC\_SHA256, TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256,TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256,TLS\_ECDH\_ECDSA\_WITH\_AES\_128\_CBC\_SHA256, TLS\_ECDH\_RSA\_WITH\_AES\_128\_CBC\_SHA256,TLS\_DHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256,TLS\_DHE\_DSS\_WITH\_AES\_128\_CBC\_SHA256, TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_CBC\_SHA,TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA,TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA, TLS\_ECDH\_ECDSA\_WITH\_AES\_128\_CBC\_SHA,TLS\_ECDH\_RSA\_WITH\_AES\_128\_CBC\_SHA,TLS\_DHE\_RSA\_WITH\_AES\_128\_CBC\_SHA, TLS\_DHE\_DSS\_WITH\_AES\_128\_CBC\_SHA,TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256,TLS\_ECDHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256 ,TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256,TLS\_ECDH\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256,TLS\_ECDH\_RSA\_WITH\_AES\_128\_GCM\_SHA256, TLS\_DHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256,TLS\_DHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256,TLS\_DHE\_DSS\_WITH\_AES\_128\_GCM\_SHA256 ,TLS\_ECDHE\_ECDSA\_WITH\_3DES\_EDE\_CBC\_SHA,TLS\_ECDHE\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA,SSL\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA, TLS\_ECDH\_ECDSA\_WITH\_3DES\_EDE\_CBC\_SHA,TLS\_ECDH\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA,SSL\_DHE\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA, SSL\_DHE\_DSS\_WITH\_3DES\_EDE\_CBC\_SHA,TLS\_EMPTY\_RENEGOTIATION\_INFO\_SCSV"|
|sslVerifyClient|The type of client certificate verification. ("require" or "optional")| "optional"|

!!! note
    WSO2 API Microgateway supports the TLSv1.3 protocol. To enable TLSv1.3, set the protocol version to "TLSv1.3" and configure the appropriate cipher suites. Additionally, ensure that you are using a Java runtime that supports TLSv1.3 (supported in later versions of Java 8, and in Java 11 and Java 17).


### Mutual SSL with Backend

!!! Note
    This feature is only available with the WUM updates and is effective from 7th October 2020 (2020-10-07) for Microgateway Toolkit 3.2.0.

To change the SSL configuration related to the backend connection, add the following configuration to the [httpClients] section in &lt;MGW\_RT-HOME&gt;/conf/micro-gw.conf file and do the necessary modifications.

``` yaml tab="micro-gw.conf"
  [httpClients.sslConfig]
    # SSL Protocol to be used
    protocolName = "TLS"
    # List of ciphers to be used
    ciphers="TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256, TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA256, TLS_ECDH_RSA_WITH_AES_128_CBC_SHA256,TLS_DHE_RSA_WITH_AES_128_CBC_SHA256,TLS_DHE_DSS_WITH_AES_128_CBC_SHA256, TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA, TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA,TLS_ECDH_RSA_WITH_AES_128_CBC_SHA,TLS_DHE_RSA_WITH_AES_128_CBC_SHA, TLS_DHE_DSS_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256  ,TLS_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDH_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDH_RSA_WITH_AES_128_GCM_SHA256, TLS_DHE_RSA_WITH_AES_128_GCM_SHA256,TLS_DHE_RSA_WITH_AES_128_GCM_SHA256,TLS_DHE_DSS_WITH_AES_128_GCM_SHA256  ,TLS_ECDHE_ECDSA_WITH_3DES_EDE_CBC_SHA,TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA,SSL_RSA_WITH_3DES_EDE_CBC_SHA, TLS_ECDH_ECDSA_WITH_3DES_EDE_CBC_SHA,TLS_ECDH_RSA_WITH_3DES_EDE_CBC_SHA,SSL_DHE_RSA_WITH_3DES_EDE_CBC_SHA, SSL_DHE_DSS_WITH_3DES_EDE_CBC_SHA,TLS_EMPTY_RENEGOTIATION_INFO_SCSV"
    # SSL/TLS protocols to be enabled
    protocolVersions = "TLSv1.2,TLSv1.1"
    # Internal keystore
    keyStorePath = "${mgw-runtime.home}/runtime/bre/security/ballerinaKeystore.p12"
    keyStorePassword = "ballerina"
    # Truststore
    trustStorePath = "${mgw-runtime.home}/runtime/bre/security/ballerinaTruststore.p12"
    trustStorePassword = "ballerina"
```

| Sub Heading| Description                      | Default value|
|------------|----------------------------------|--------------|
|protocolName| SSL Protocol to be used| "TLS"|
|protocolVersions | SSL/TLS protocols to be enabled |"TLSv1.2,TLSv1.1"|
|ciphers |List of ciphers to be used|"TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA,TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_CBC\_SHA256, TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256,TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256,TLS\_ECDH\_ECDSA\_WITH\_AES\_128\_CBC\_SHA256, TLS\_ECDH\_RSA\_WITH\_AES\_128\_CBC\_SHA256,TLS\_DHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256,TLS\_DHE\_DSS\_WITH\_AES\_128\_CBC\_SHA256, TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_CBC\_SHA,TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA,TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA, TLS\_ECDH\_ECDSA\_WITH\_AES\_128\_CBC\_SHA,TLS\_ECDH\_RSA\_WITH\_AES\_128\_CBC\_SHA,TLS\_DHE\_RSA\_WITH\_AES\_128\_CBC\_SHA, TLS\_DHE\_DSS\_WITH\_AES\_128\_CBC\_SHA,TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256,TLS\_ECDHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256 ,TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256,TLS\_ECDH\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256,TLS\_ECDH\_RSA\_WITH\_AES\_128\_GCM\_SHA256, TLS\_DHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256,TLS\_DHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256,TLS\_DHE\_DSS\_WITH\_AES\_128\_GCM\_SHA256 ,TLS\_ECDHE\_ECDSA\_WITH\_3DES\_EDE\_CBC\_SHA,TLS\_ECDHE\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA,SSL\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA, TLS\_ECDH\_ECDSA\_WITH\_3DES\_EDE\_CBC\_SHA,TLS\_ECDH\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA,SSL\_DHE\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA, SSL\_DHE\_DSS\_WITH\_3DES\_EDE\_CBC\_SHA,TLS\_EMPTY\_RENEGOTIATION\_INFO\_SCSV"|
|keyStorePath|The micro gateway key store path| "${mgw-runtime.home}/runtime/bre/security/ballerinaKeystore.p12"|
|keyStorePassword|The Password of the key store|ballerina|
|trustStorePath|The trust store that should be used for the TLS communication|${mgw-runtime.home}/runtime/bre/security/ballerinaTruststore.p12|
|trustStorePassword|The Password of the trust store|ballerina|

!!! note
    WSO2 API Microgateway supports the TLSv1.3 protocol. To enable TLSv1.3, set the protocol version to "TLSv1.3" and configure the appropriate cipher suites. Additionally, ensure that you are using a Java runtime that supports TLSv1.3 (supported in later versions of Java 8, and in Java 11 and Java 17).    
