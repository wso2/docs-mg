# The Policy Hub

API developers use policies (i.e. interceptors) for performing various types of transformations and validations at the API gateway. However each time a developer is in need of an API Policy, they have to develop the policy from the scratch.

Microgateway policy hub creates a place for developer community to share their extensions with other developers and reuse existing work done by other developers.

## How it works

As per the below diagram, the developer will utilize the [Ballerina Central](https://central.ballerina.io/) as the Policy Hub ( i.e. Policy Sharing website) for the purpose of searching, listing and uploading policies (i.e interceptors.). The interceptor functions will be placed inside ballerina modules in the Ballerina Central’s Repository. Users will be able to specify which interceptor to engage per API or per Resource within the OpenAPI Definition. During the build time of a project, the specified module will be pulled from the ballerina central, and the interceptor’s functionality will be applied when the API is being invoked.

![policy hub- how it works](img/concepts/policy-hub-how-it-works.png){height="400"}

## Create a new module

As discussed earlier Microgateway is using [Ballerina Central](https://central.ballerina.io/) as the hub for sharing policies. Therefore to share a new policy, it has be ballerina module. Let's look at how to create a new ballerina module which is also a MGW policy.

1.  Create ballerina interceptor
    First of all you need to have functional and tested interceptor(s) created by following [Ballerina Message Transformation](https://docs.wso2.com/display/MG310/Message+Transformation#ballerina-interceptors) guide.
2.  Setup the ballerina environment
    This Microgateway version is released with the support for ballerina v1.1.4. Therefore first we need to download and configure that ballerina version first. Use [ballerina downloads](https://ballerina.io/downloads/archived/) to find respective ballerina 1.1.4 distribution for your operating system. Then follow [Getting Started guide](https://ballerina.io/v1-1/learn/) and install ballerina and preferred IDE extension.
3.  Initialize a new Ballerina Project and a Module
    To create a ballerina module, first we should have a ballerina project. Execute below commands to initialize a new balleina project and a module.

    **Initialize a Module**

    ``` bash
        # hello_mgw is the project name. You can give it your own name
        ballerina new hello_mgw

        # move inside the new project
        cd hello_mgw

        # hello_policy is the module name. This is the name that will be published in the policy hub
        ballerina add hello_policy
    ```

        4.  Now the module is ready to add new interceptors. Interceptor source files created in step 1. should be moved into the new ballerina module's source directory.
        5.  Build the module

        **Build Module**

    ``` bash
        # hello_policy is the module name.
        ballerina build hello_policy
    ```

        6.  You can find complete sample project with tests in [github](https://github.com/praminda/Interceptors/) .

        ## Share a module in the Hub

        Follow below steps to share the created module with other Policy Hub users.

        1.  Configure Ballerina.toml
        Ballerina central uses organization name to organize modules by module provider (similar to maven group-id). Therefore before sharing the new module we need to provide the org-name and the version of the module.
        To increase the discoverability of new module you can define search keywords you'd like to list the module. To do these, Open **Ballerina.toml** file at the root of the project directory. Then update the org-name and version attributes inside **\[project\]** table.

        **Ballerina.toml**

    ``` yml
        [project]
        org-name= "org"
        version= "0.1.0"
        keywords= ["microgateway", "interceptors"]
    ```

        2.  Push the module

        **Push Module**

    ``` bash
        # hello_policy is the module name.
        ballerina push hello_policy
    ```

        3.  Now you module will be published in [Ballerina Central](https://central.ballerina.io/) . For more information check, [Ballerina project structure](https://ballerina.io/v1-1/learn/how-to-structure-ballerina-code/) and [Publishing ballerina modules](https://ballerina.io/v1-1/learn/how-to-publish-modules/)

        ## Using Policy Hub

        Checkout step by step guide at [How to add interceptors from Policy Hub](_How_to_add_interceptors_from_Policy_Hub_)


