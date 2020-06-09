# Caching

Microgateway contains the following caching mechanisms to increase the performance of the gateway.

-   [OAuth cache](#Caching-OAuthcache)
-   [Response cache](#Caching-Responsecache)

## OAuth cache

Microgateway will accept two types of OAuth tokens issued by a trusted key manager as a valid token.

1.  JWTs (Json Web Token)
2.  Opaque tokens (non-JWT)

The OAuth token is saved in OAuth cache, which is enabled by default. If a cache entry does not exist in the cache, it calls the Key Manager server. This process is carried out using Web service calls. After the Key Manager server returns the validation information, it gets stored in the Microgateway cache.

Unless an OAuth token is expired or revoked, Microgateway uses the cache to the authentication. This method reduces the number of Web service calls to the Key Manager server.

## Response cache

The API Microgateway has the ability to cache response messages for each API. Caching improves performance because the backend server does not have to process the same data for a request multiple times. You need to set an appropriate timeout period to offset the risk of stale data in the cache.
