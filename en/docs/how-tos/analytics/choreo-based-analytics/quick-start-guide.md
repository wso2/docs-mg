# Choreo Based Analytics Getting Started Guide

!!! note
    Refer to the [compatibility documentation]({{base_path}}/faqs/#compatibility) to confirm the supported update level for this feature.

API Microgateway offers analytics as a cloud service. Therefore, you need to register with the analytics cloud in order to use API Microgateway Analytics. Follow the instructions below to get started with analytics:

## Step 1 - Sign in to Choreo

Follow the instructions below to sign in to Choreo.

1. Navigate to Choreo using the following URL.

      <a href="https://console.choreo.dev/?apianalytics=true?utm_source=apim_docs">https://console.choreo.dev</a>.

2. Sign-in to Choreo.

      ![Choreo sign-in options]({{base_path}}/assets/img/how-tos/analytics/choreo-based-analytics/sign-in-choreo.png)

## Step 2 - Register your environment

Follow the instructions below to register your on-premise environment:

1. Click the **Settings** on the bottom left corner.

    ![Settings Menu]({{base_path}}/assets/img/how-tos/analytics/choreo-based-analytics/settings-menu.png)

2. If you are a member of multiple organizations, select the appropriate organization from the top left-hand corner.

    For more information on handling users with role-based access control in organizations, see [Role-based Access Control for API Analytics]({{base_path}}/how-tos/analytics/choreo-based-analytics/role-based-access-control).

    ![Org Selector]({{base_path}}/assets/img/how-tos/analytics/choreo-based-analytics/organization-selector.png)

3. Select the **On-prem Keys** tab and click **Generate keys**.

    ![On-prem Key]({{base_path}}/assets/img/how-tos/analytics/choreo-based-analytics/on-prem-key.png)

4. Enter a suitable name for your environment (e.g., customer1-dev).

5. Click **Generate**.

    !!! info
        The validity period of the key and the number of keys that you are allowed to generate vary based on the type of user as explained below:

        | **User Type**            | **Description**                                                    | **Validity Period**                                                                                                                  | **Number of Keys Allowed** |
        |--------------------------|--------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|----------------------------|
        | Free user                | Refers to users who do not have a valid subscription for Choreo.   | 2 weeks <br> **NOTE:** The validity of the keys can be temporarily extended by [contacting sales](https://wso2.com/contact/) and submitting a request. | 3 keys   |
        | Subscription customers   | Refers to users who have a valid subscription for Choreo.          | 1 year                                                                                                                               | Unlimited                  |

6. Copy the key that was generated before closing the dialog box.

## Step 3 - Configure the Gateway

API Analytics is delivered via the API Analytics Cloud. Therefore, the API Microgateway needs to be configured to publish analytics data into the cloud.

The Gateway configuration process varies based on the Gateway that you are using.

If your system connects to the service through a proxy server/firewall, you need to grant access to the following endpoints to access the Choreo Analytics Cloud service to publish data.

| Host                                             | Port | Protocol |
|--------------------------------------------------|------|----------|
| `analytics-prod-incoming.servicebus.windows.net` | 5671 | AMQP     |
| `analytics-prod-incoming.servicebus.windows.net` | 5672 | AMQP     |
| `analytics-event-auth.choreo.dev`                | 443  | HTTPS    |

### Configurations

Follow the instructions below to publish analytics data to the analytics cloud via the API Microgateway:

1. Open the &lt;MICRO\_GW\_HOME&gt;/conf/micro-gw.conf file and add the following configuration.

    ```toml
    [analytics.choreoAnalytics]
    enable = false
    configEndpoint = "https://analytics-event-auth.choreo.dev/auth/v1"
    authToken = ""
    ```

2. Enter the on-premise token, which you obtained via the Choreo Portal in the **Register your environment** step, as the Auth token field.

3. Restart the API Microgateway.

## Step 4 - View the Analytics Dashboards

Invoke APIs and open <a href="https://console.choreo.dev/insights/overview">Choreo Insights</a> to view the dashboards.

For more detailed information on the Choreo Insights dashboards and their usage, go to the [View API Insights](https://wso2.com/choreo/docs/insights/view-api-insights/) page of the Choreo documentation.

## What's Next?

Enterprises and companies who want to use the Choreo Insights collaboratively can utilize the organization concept in Choreo. For more information, see [Role-based Access Control for API Analytics]({{base_path}}/how-tos/analytics/choreo-based-analytics/role-based-access-control).