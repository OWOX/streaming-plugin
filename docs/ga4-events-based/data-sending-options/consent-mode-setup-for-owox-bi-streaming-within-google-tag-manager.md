# Consent Mode Setup for OWOX BI Streaming within Google Tag Manager

If you’re using OWOX BI Streaming via Google Tag Manager, this guide will help you configure Consent Mode correctly. It explains each step to ensure accurate data collection and compliance with privacy regulations, providing a clear path to successful implementation.

## Basic Context

In today's regulatory landscape, websites must request and secure user consent before collecting data. This process involves three key stages:

-   **Stage #1:** Requesting and saving the visitor’s consent choice.
-   **Stage #2:** Processing the consent response and implementing technical configurations accordingly.
-   **Stage #3:** Updating consent settings if users revise their choices during website navigation.

We’ll explain each stage in detail, ensuring you can seamlessly integrate OWOX BI Streaming with Consent Mode and handle it with Google Tag Manager.

### OWOX BI Streaming + Google Consent Mode

OWOX BI Streaming customers are typically familiar with Google services such as Google Tag, GA4, Cloud, BigQuery, and others. In Google Tag Manager, the Google Consent Mode functionality modifies tag behavior for Google products based on the user’s consent choice. Simultaneously, the OWOX BI algorithm sends event-based user behavior data to the `events_intraday_` tables following Google Consent Mode, using a `&gcs` parameter. [Read more in this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/faq.md#how-ga4-event-based-web-streaming-works-with-google-consent-mode).

> **NOTE**  
> The `&gcs` parameter is an additional parameter sent with requests to Google, allowing them to model conversions (e.g., Google Ads, Floodlights) and track visitor behavior (e.g., GA4). For more details, refer to [the official Google documentation](https://developers.google.com/analytics/devguides/collection/gtagjs/cookies-user-id#gcs_parameter) on the `&gcs` parameter.

The Google Tag Manager serves as the key integration tool, managing consent handling and ensuring the correct tags are triggered at the right time.

![00.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/01/public)

## Before You Start

Before setting up Consent Mode, ensure the following prerequisites are in place:

-   **Google Tag:** Installed on your website.
-   **Google Analytics 4 (GA4):** Integrated and actively collecting data.
-   **OWOX BI Event-based streaming:** Created and activated according to [this guide](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/setup.md#how-to-set-up-ga4-event-based-web-streaming-client-side--server-side).
-   **Google Tag Manager + OWOX BI access point:** Data sending is correctly configured following [this guide](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-a-website-using-google-tag-in-google-tag-manager).

Once these prerequisites are met and your website data is sent to BigQuery, you can proceed to configure Consent Mode and verify that your data collection works as expected.

## Stage #1. Request and Record the User’s Consent Choice

Research and choose a technical solution to request and record the user’s consent choice.

**Option #1. Implement a custom solution:** Use JavaScript or other tools that integrate with your existing infrastructure. Watch this short video from Google to help you implement your consent collection solution: [Google Consent Video](https://www.youtube.com/watch?v=MqAEbshMv84).

[Watch the video](https://www.youtube.com/watch?v=MqAEbshMv84)

**Option #2. Choose a pre-built solution:** Use a Consent Management Platform (CMP), such as **OneTrust**, **CookieYes**, **Cookiebot**, etc.

CMPs are designed to display a consent popup/window, record the user’s consent choice, and allow tools like Google Tag Manager to dynamically block or allow tags based on this consent. A CMP provides a cookie popup/window for website visitors where they can: **Accept all**, **Reject all**, or **Customize** their choice.

![01.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/02/public)

> **IMPORTANT**  
> The consent request must take top priority during page loading and should be implemented first. All other actions (e.g., sending analytics data, setting advertising cookies, or others) depend on the user’s consent and cannot run until permission is granted.

Each option has its pros and cons. The choice depends on your context and available resources for supporting the selected solution. Implement your choice following the official instructions. In this article, we will use examples with the CMP **OneTrust**.

## Stage #2. Processing Consent

### Step 2.1. Enable Consent Overview in Workspace Settings

**2.1.1.** Open the ‘**Admin**’ tab and navigate to ‘**Container Settings**’.

![02.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/03/public)

**2.1.2.** In the popup that appears, enable the checkbox ‘**Enable consent overview**’.

![03.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/04/public)

**2.1.3.** **Save** these changes.

![04.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/05/public)

### Step 2.2. Updating Consent Settings for the ‘OWOX GA4 Configuration’ Tag

> **NOTE**  
> This tag should already be created in your GTM according to [this guide](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-a-website-using-google-tag-in-google-tag-manager).

There are two configuration options depending on the behavior you want when the user has not yet provided consent:

**🟢 Option 1: Send data to OWOX BI Streaming** (immediately after the page loads, regardless of whether consent has been given):

1.  Open **Advanced settings**.
2.  Locate the ‘**Consent settings**’ section.
3.  Select the option ‘**Not set**’ or ‘**No additional consent required**’.  
    ![05.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/06/public)

**🔴 Option 2: Do not send data to OWOX BI Streaming until consent is provided:**

1.  Open **Advanced settings**.
2.  Locate the ‘**Consent settings**’ section.
3.  Select the option ‘**Require additional consent for tag to fire**’ and choose ‘**analytics\_storage**’  
    ![06.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/07/public)

### Step 2.3. Creating New Variables in Google Tag Manager

**2.3.1.** Create a new variable to store information from the cookie that records the current consent status.

For example, if you are using CMP **OneTrust**, this cookie is called ‘**OptanonConsent**’. In our example, we create a variable named ‘**OneTrust – OptanonConsent**’ and configure it as shown in the screenshot below.

![07.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/08/public)

**2.3.2.** Create variables for each consent type (**ad\_storage**, **analytics\_storage**, **functionality\_storage**, **personalization\_storage**, **security\_storage**).

For example, we create a variable named ‘**CM\_analytics\_storage**’, where its name clearly indicates the consent type it stores.

Set the default value to `denied`. If the Regular Expression matches the main variable ‘**OneTrust – OptanonConsent’**, the variable ‘**CM\_analytics\_storage**’ will update its default value to `granted`. The variable configuration example is shown in the screenshot below.

![08.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/09/public)

Repeat this process to create variables for the other consent types.

### Step 2.4. Install the Simo Ahava template tag from Gallery

**2.4.1.** Open the [Consent Mode (Google + Microsoft tags) template](https://tagmanager.google.com/gallery/#/owners/gtm-templates-simo-ahava/templates/consent-mode) and click ‘**Add to workspace**’.

![09.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/10/public)

Alternatively, you can go to the **Templates** page and click the ‘**Search Gallery**’ button in the ‘**Tag Templates**’ section.

![10.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/11/public)

Use the search term ‘**simo**’ to find ‘**Consent Mode (Google + Microsoft tags)**’ by gtm-templates-simo-ahava.

![11.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/12/public)

Click ‘**Add to workspace**’.

![12.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/13/public)

**2.4.2.** Confirm your action to **add the template** to your Workspace.

![13.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/14/public)

### Step 2.5. Create a New Tag ‘Default Consent Mode Tag’

**2.5.1.** Navigate to the ‘**Tags**’ page and click the ‘**New**’ button.

![14.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/15/public)

**2.5.2.** Select the Tag Type – ‘**Consent Mode (Google + Microsoft tags)**’ which is a Simo Ahava template.

![15.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/16/public)

**2.5.3.** Name the tag ‘**Default Consent Mode Tag**’.

![16.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/17/public)

**2.5.4.** Set the ‘**Consent Command**’ to ‘**Default**’.

![17.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/18/public)

**2.5.5.** Set ‘**Wait for update**’ to **1000 milliseconds**.

![18.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/19/public)

**2.5.6.** Set ‘**Regions**’ to ‘**All**’.

![19.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/20/public)

**2.5.7.** In the ‘**Consent Settings**’ section, fill in the fields for ‘**Required for Google services**’ and ‘**Other signals**’ with the values of the previously created variables, as shown in the screenshot below.

![20.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/21/public)

**2.5.8.** In the ‘**Other Settings**’ section, enable the checkboxes ‘**Pass Ad Click Information…**’ and ‘**Push dataLayer Event**’, as shown in the screenshot below.

![21.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/22/public)

**2.5.9.** Select the trigger ‘**Consent Initialization – All Pages**’.

![22.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/23/public)

**2.5.10.** Click the ‘**Save**’ button.

![23.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/24/public)

### Step 2.6 (Optional). Verify That Everything Is Set Up Correctly

**2.6.1.** On the **Workspace** screen, open **Preview**.

![24.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/25/public)

**2.6.2.** When your website loads in Preview mode, select ‘**Consent Default**’ in the left sidebar. In the ‘**Output**’ section under the ‘**Consent**’ tab, you should see the current consent state as `denied`.

![25.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/26/public)

**2.6.3.** Next, in the sidebar, select ‘**gtm\_consent\_default**’ (this is the event that pushed the default consent settings into the dataLayer, as we enabled the necessary checkbox in Step 2.5.8). On the ‘**Consent**’ tab, a new column called ‘**Current State**’ will appear, where all values should also be `denied`.

![26.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/27/public)

**2.6.4.** Another check can be performed on the **Summary** page. Open **Hit Details** and locate the `gcs` parameter in the table of parameters; its value should be `G100`.

![27.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/28/public)

## Stage #3. Updating Consent State

Users can change their consent choice at any time, so we need to implement a logic that automatically adjusts to updated consent. Typically, when visitors see the consent popup, they click on one of the options (**Accept all** or **Reject all**) and continue browsing. Therefore, clicking these buttons should trigger an event (in our example, we’ll name it ‘**ConsentBannerClosed**’), which will activate a trigger and run the ‘**Update Consent Mode Tag**’. Let’s describe each step of this logic implementation in detail.

### Step 3.1. Creating a New Tag: ‘Update Consent Mode Tag’

**3.1.1.** To do this quickly, open the ‘**Default Consent Mode Tag**’ and duplicate it.

![28.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/29/public)

**3.1.2.** Rename the new tag to ‘**Update Consent Mode Tag**’.

![29.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/30/public)

**3.1.3.** In the ‘**Consent Command**’ field, select ‘**Update**’ instead of ‘Default’.

![30.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/31/public)

**3.1.4.** There’s no need to change the variable list. Ensure it remains the same as in the ‘**Default Consent Mode Tag**’.

### Step 3.2. Creating a New Trigger for the ‘Update Consent Mode Tag’

**3.2.1.** Open the triggers list and click the “**+**” icon in the top-right corner.

![31.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/32/public)

**3.2.2.** Name the new trigger: ‘**Consent Banner Closed Trigger**’.

![32.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/33/public)

**3.2.3.** Choose the trigger type: **Custom Event**.

![33.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/34/public)

**3.2.4.** Enter the Event Name: ‘**ConsentBannerClosed**’.

![34.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/35/public)

**3.2.5.** **Save** the trigger.

![35.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/36/public)

### Step 3.3. Save the configuration for the new ‘Update Consent Mode Tag’

![36.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/37/public)

### Step 3.4 (Optional). Verify That Everything Is Set Up Correctly

**3.4.1.** Open **Preview mode** again. On your website, click ‘**Accept all**’ in the consent popup to verify that the consent status changes from the default `denied` to the new status `granted`.

**3.4.2.** In the left sidebar, locate the ‘**ConsentBannerClosed**’ event. This is the event triggered after closing the consent banner.

![37.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/38/public)

**3.4.3.** In the ‘**Tags Fired**’ section, click on the name of the tag ‘**Update Consent Mode Tag**’.

![38.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/39/public)

**3.4.4.** In the list of properties, you will see that the variables created in Step 2.3.2 are passed here. To view the actual values of these variables, select ‘**Display Variables as Values**’ in the top menu.

![39.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/40/public)

**3.4.5.** The variable values should be `granted`, confirming that everything worked correctly.

![40.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/41/public)

**3.4.6.** Lastly, check the status on the ‘**Consent**’ tab. The table will display the default status and the updated status. As shown in the screenshot below, everything was processed correctly.

![41.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-consent-mode-setup-for-owox-bi-streaming-within-google-tag-manager/42/public)

## Troubleshooting

### Why are events being collected in Google Analytics 4 instead of OWOX BI Streaming after setting up Consent Mode?

This issue might be related to the Consent Mode configuration in Google Tag Manager. Specifically, if the website visitor has not yet accepted Consent Mode, data is not sent to OWOX BI Streaming because the trigger for the ‘**OWOX GA4 Configuration**’ tag does not fire. This tag is crucially important because it includes the OWOX BI access point (the `server_container_url` parameter).

To avoid this issue try one of these options:

-   **Option #1:** Set up Consent Mode in GTM from scratch following the instructions provided in this article.
-   **Option #2:** Alternatively, check in **Preview** mode to ensure that the trigger for the ‘**OWOX GA4 Configuration**’ tag is firing correctly. If the trigger does not fire, investigate and resolve the cause. Also, check that you are guided by **Step 2.1.** and **Step 2.2.** of this guide.

If you have any questions, feel free to contact our Support team at [bi@owox.com](mailto:bi@owox.com).
