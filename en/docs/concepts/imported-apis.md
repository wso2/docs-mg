# Imported APIs

Importing APIs into your Microgateway environtments is one other way of creating APIs in a Microgateway environment. This is a useful feature when you have WSO2 API Manager running separately as API Management platform in your environment. In this case, you can create your APIs from API Manager's publisher portal and use `         micro-gw import        ` command to import the API definition and the API configuration into Microgateway.

Import can happen in two modes.

## Single API Import

This mode is used to import a single specific API from WSO2 API Manager to Microgateway. Example usage would be scaling a single API in a WSO2 API Manager environment. Check the step by step guide [here](_Importing_a_Single_API_from_WSO2_API_Manager_) .

## API group (Label) import

This mode can be used when developer need to expose a labeled group of APIs in a single Microgateway. Step by Step guide can be found in [here](_Importing_a_Group_of_APIs_from_WSO2_API_Manager_) .
