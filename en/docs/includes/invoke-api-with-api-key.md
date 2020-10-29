
Execute the following cURL command to Invoke the API using the API key.

 ``` bash tab="Format"
 curl -X GET "https://<MGW-runtime-hostname>:<MGW-runtime-port>/<API-context>/<API-resource>" -H "accept:application/xml" -H "api_key:$TOKEN" -k
 ```
 
 ``` bash tab="Example"
 curl -X GET "https://localhost:9095/v2/pet/1" -H "accept: application/xml" -H "api_key:$TOKEN" -k
 ```
 
