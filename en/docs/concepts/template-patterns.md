# Template Patterns for Swagger Definitions 

Swagger allows you to define paths templated in segments. However Ballerina recommends using only the basic path template pattern - `/{code}` and the dot suffix improvement.


The following is a list of sample patterns currently supported in WSO2 Microgateway 3.2.0 and upwards.

| Pattern                | Sample request path |
|------------------------|---------------------|
| /foo/{bar}/{baz}       | /foo/p12/e001       |
| /foo/{bar}/{baz}/store | /foo/p20/e002/store |
| /foo/{bar}.id          | /foo/baz.id         |
| /foo/{bar}.id/qux      | /foo/quz.id/qux     |

Note that you **cannot** define two resources in the same service with only a path template expression change. An example is shown below.

```
    /echo/{abc}/bar
    /echo/{xyz}/bar
```
