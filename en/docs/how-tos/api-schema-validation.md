# API Schema Validation

The API schema based request and/or response validator validate requests and responses coming to the API Microgateway. This validator validates requests and responses based on the API schema that is defined in the API definition. You can enable request validation, response validation, or both request and response validation. Whenever a request comes to the API Microgateway that request should be validated against the OpenAPI definition file of the requested API. If the request is valid, it is directed to the backend. If the request is invalid, error code 400 is returned. Whenever a response comes from the backend that response is validated against the OpenAPI definition file of the requested API. If the response is valid, it is directed to the user and if the response is invalid, error code 500 is returned.

When validating requests/responses, the validator gets the payload of the request/response and compares it with the relevant model defined in the OpenAPI definition file of the API. If there are any conflicts, the validator will identify that the request/response is an invalid request/response.

## Request validation

-   Sending a valid request

![send a valid request]({{base_path}}/assets/img/how-tos/sending-a-valid-request.png)

-   Sending an invalid request

![send an invalid request]({{base_path}}/assets/img/how-tos/sending-an-invalid-request.png)

## Response validation

-   The backend sends a valid response

![Validate a valid response]({{base_path}}/assets/img/how-tos/response-validation-valid.png)

-   The backend sends an invalid response

![validate an invalid response]({{base_path}}/assets/img/how-tos/response-validation-invalid.png)

## Enable the schema validation

-   **To enable request validation:**

Copy following configuration from **default-micro-gw.conf.template** to **micro-gw.conf** file.

``` java
[validationConfig]
    enableRequestValidation = true
```

-   **To enable the response validation:**

    Copy following configuration from **default-micro-gw.conf.template** to **micro-gw.conf** file.

``` java
[validationConfig]
    enableResponseValidation = true
```

   Both request and response validation can be enabled by adding the both of the configurations to **micro-gw.conf** file.

   Copy all the .jar files which are inside the **&lt;tool-kit-home&gt;/lib/dependencies/validation** to the **&lt;project&gt;/lib** directory before building the project.

!!! note
    Limitations
    
    Micro-gateway currently supports only validation of application/json schemas. Furthermore the schema validation is limited to the validation of required fields.

## Sample - Request Validation

``` yml tab="Example"
paths:
  /pet:
     post:
       tags:
          - pet
       summary: Add a new pet to the store
       description: Add a new pet to the store
       operationId: addPet
       requestBody:
         description: Create a new pet in the store
         content:
           application/json:
             schema:
               $ref: '#/components/schemas/Pet'
       required: true
     responses:
       '200':
          description: Successful operation
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Pet'
       '405':
          description: Invalid input

components:
  schemas:
    Pet:
      required:
         - name
         - photoUrls
      type: object
      properties:
        id:
          type: integer
          format: int64
          example: 10
        name:
          type: string
          example: doggie
        photoUrls:
          type: array
          xml:
            wrapped: true
          items:
            type: string
            xml:
              name: photoUrl
      status:
        type: string
        description: pet status in the store
        enum:
          - available
          - pending
          - sold
```

``` java tab="Request Curl"
 TOKEN=eyJhbGciOiJSUzI1NiIsICJ0eXAiOiJqd3QiLCAia2lkIjoiYmFsbGVyaW5hIn0.eyJzdWIiOiJhZG1pbiIsICJpc3MiOiJodHRwczovL2xvY2FsaG9zdDo5MDk1L2FwaWtleSIsICJpYXQiOjE1ODUyOTY3MjgsICJqdGkiOiJkYThiYjBlOS05NzdhLTQ2ZDUtYjFmMS1hYjgxYWQyYjcyNjkiLCAia2V5dHlwZSI6IlBST0RVQ1RJT04iLCAiYWxsb3dlZEFQSXMiOltdfQ.MRJd1uZ-ya0DuFtgGfZS58aWsRTIgxvrporLbvp2_vUtTtkvMRwuKfp7nr-m1gHXMGE1JoE23xhio6aLPGaP9yF8DOUm8XS_FXaXT48zn9-pNIX1nRt4SHd_5wimE6IAzSeORlJ7oLkskwQ8iVHXm4KjzonyhIrQX1JLW6y4dQNEF9BFkZf2jvs1laa-saruSZEsi99zg6MnCWfFr4vbkSzozNidKQLmO_eXr06WaojT1xl8wSOuKy-GwTCBUhIsAq2bQvRNP_Zx7eKmFphwycJZOuObu7M0EgFUNRBQuLUcOmPUEI8W1w6cYNO-RSAnegMoVOL47m7GHwftM6fWYg
    
 curl -k -X POST 'https://localhost:9095/api/v3/pet' -H "accept: application/json" -H "Content-Type: application/json" -H "api_key: $TOKEN" -d "{\"id\":0, \"photoUrls\":[\"string\"], \"status\":\"string\"}"   
```

``` java tab="Response from the validator"
{"fault":{"code":400, "message":"Bad Request", "description":"#/status: string is not a valid enum value, #: required key [name] not found, "}}
```


