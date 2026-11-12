# Data sending options

- [Sending Data From a Website Using Google Tag in Google Tag Manager](#sending-data-from-a-website-using-google-tag-in-google-tag-manager)
- [Sending Data From Your Server Using the Measurement Protocol](#sending-data-from-your-server-using-the-measurement-protocol)
- [Sending Data From a Website Using On-Site gtag.js Code](#sending-data-from-a-website-using-on-site-gtagjs-code)
- [Sending Data From a Website Using Custom HTML Tag in Google Tag Manager](#sending-data-from-a-website-using-custom-html-tag-in-google-tag-manager)
- [Sending Data From a Website to a Server-Side GTM Container](#sending-data-from-a-website-to-a-server-side-gtm-container)

## Sending Data From a Website Using Google Tag in Google Tag Manager

### Overview

Use this instruction if you need to simultaneously collect event-based user behavior data from a website in both Google Analytics 4 and Google BigQuery.

These settings will not be displayed in the OWOX BI interface. For any questions about data collection in the GA4 event format, you can contact the OWOX manager ([bi@owox.com](bi@owox.com)).

### Requirements

Before configuring data collection through the Google Tag (previously named GA4 Configuration tag), check the following statements: 

-   You have created a new Google Analytics 4 property ([setup instructions](https://support.google.com/analytics/answer/9304153)) and you've sent all website events to Google Analytics 4 via Google Tag Manager ([setup instructions](https://support.google.com/tagmanager/answer/9442095?sjid=6942779237360507067-EU)).
-   In the OWOX BI interface, you've created a new GA4 event-based web streaming ([step-by-step guide](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/setup.md#how-to-set-up-ga4-event-based-web-streaming-client-side--server-side)).

### Step-by-step setup

> **IMPORTANT**  
> We recommend creating a separate Google Analytics 4 property ([setup instructions](https://support.google.com/analytics/answer/9304153)) for OWOX data before proceeding to the next steps.

#### Step 1. Creating a new web stream in Google Analytics 4 settings

1.1. Open Google Analytics 4 and choose your Property. Then go to the **Admin** page and open the "**Data streams**" subpage.

![01.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/01/public)

1.2. Click the "**Add stream**" button and select "**Web**".

![02.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/02/public)

1.3. Specify the name of the web stream **OWOX GA4**

![04.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/03/public)

Fill in other settings and hit the button "**Create stream**".

After that, you will be redirected to the new stream page, where you will see the following message. This message is expected, just ignore it.

![image\_\_1\_.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/04/public)

1.4. (Optional) If Custom events are specified in the settings of the current web stream, then they must be duplicated in the newly created OWOX GA4 web stream.

![image.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/05/public)

#### Step 2. Creating a new Google tag in Google Tag Manager

2.1. Open the **Google Tag Manager** and go to the **Tags** section. Create a new Google tag by clicking on the '**New**' button.

![01.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/06/public)

2.2. Click on the '**Tag Configuration**' section.

![02.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/07/public)

2.3. Choose the '**Google Analytics**' tag type.

![03.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/08/public)

2.4. Choose the '**Google Tag**'.

![04.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/09/public)

2.5. Name the tag **OWOX GA4 Configuration**.

![05.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/10/public)

2.6. Specify the **same trigger** that is specified for the existing Google tag. See the screenshot below just as an example (choose your firing triggers):

![06.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/11/public)

2.7. Specify the MEASUREMENT ID from the previously created **OWOX GA4** web stream.

![07.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/12/public)

2.8. Open the “**Configuration settings**” line. And add a new parameter '**server\_container\_url**':

![08.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/13/public)

2.9. Go to your Steaming page, open the **Settings** tab, and copy the URL (`'https://streaming.bi.owox.com/ga4/...'`') from the '**Website data stream**' section:

![16.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/14/public)

> **Note**  
> To create a new GA4 event-based web streaming, follow [this step-by-step guide](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/setup.md#how-to-set-up-ga4-event-based-web-streaming-client-side--server-side).

2.10. Paste this link in the “**Value**” field:

![09.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/15/public)

> **Note**  
> In the Configuration settings, you can use 'Configuration Settings Variable' instead of adding manually new parameter. [Learn more about this type of variable](https://support.google.com/analytics/answer/13438166?hl=en).

2.11. Click the '**Save**' button.

> **IMPORTANT**  
> Use your new 'OWOX GA4 Configuration' tag only for OWOX BI Streaming.![googletag.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/16/public) To check it, please go to the Webstream details page, find the Google Tag section and open Configure Tag Settings. If you see that the web stream is connected to another Google Tag, instead of the OWOX GA4 Configuration tag, please create a new web stream to ensure that the OWOX GA4 Configuration tag is connected only to OWOX BI Streaming.

#### Step 3 (optional). Setting up custom events for OWOX BI Streaming

The Google Tag (previously named GA4 Configuration tag) only includes [automatically collected events](https://support.google.com/analytics/answer/9234069?hl=en) and [GA4 Enhanced Measurement events](https://support.google.com/analytics/answer/9216061?sjid=9354740649059395789-EU) that are active in your Web stream in GA4. **For any additional custom events**, such as e-commerce events, **you'll need to create separate duplicate tags with the same triggers** and specify the OWOX GA4 Configuration tag**.** Here's an example of a list of tags:

![38new.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/17/public)

> **Note**  
> Google implements Event settings variables in Google Tag Manager, so you can easily manage all your custom events. [Learn more about how to define event-level settings](https://support.google.com/analytics/answer/13438771?hl=en&ref_topic=12403939&sjid=7266959550788531338-EU).

#### Step 4. Publish a Google Tag Manager container

To start sending data to the OWOX BI access point, publish a container with a new tag.

Website user behavior data will appear in Google BigQuery in the “events\_intraday\_” tables. The [data schema of the “events\_intraday\_” table](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/schemas/data-schema-for-events-intraday-table.md) is fully compatible with native export from Google Analytics 4 to Google BigQuery.

> **Note:**  
> 1\. The session\_id values may differ for events that are sent in different streams.  
> 2\. If a configuration tag created to send events to an OWOX BI access point is deleted, then the entire set of tags for custom events that contain this configuration tag must also be deleted. Otherwise, in Google Analytics 4 the event data will be duplicated.

## Sending Data From Your Server Using the Measurement Protocol

The OWOX BI GA4 event-based web streaming allows you to enhance web streams by sending events directly to the OWOX BI Access point via HTTP requests.

### Before you start

Before you start sending events, check the following statements:  

-   In the OWOX BI interface, you've created a new GA4 event-based web streaming ([step-by-step guide](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/setup.md#how-to-set-up-ga4-event-based-web-streaming-client-side--server-side)).

### How it works

-   Use a **unique** access point URL, e.g. 'https://streaming.bi.owox.com/ga4/…**/mp**'. Note that there is no slash at the end.  
    This URL you can find on the **Settings tab** on the Streaming page:  
    ![17.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/18/public)
-   The access point processes each request sent to it and stores it in the `debug_info.raw_request` field as it is without changes.
-   You can send **any events**, for example:
    -   Regardless of when these events happened (events from yesterday can be sent today with the actual date and time).
    -   Any events with your list of `event_params` fields can be sent. The OWOX BI algorithm does not automatically add [parameters](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/faq.md#list-of-event-parameters-that-owox-bi-algorithm-adds-in-the-eventparams-field) (with the key name '**&ep.\***') to events sent via the Measurement Protocol. Geodata is also not determined automatically; see the `ip_override` parameter in Optional parameters.

### Request requirements

-   According to the official documentation, the request must comply with the Measurement Protocol (Google Analytics 4).
-   The `events[]` array must contain at least one object.
-   Parameter `client_id` is required for data blending with session data. OWOX BI algorithm saves this parameter in the `user_pseudo_id` field.

### Optional parameters

-   **Query parameters** (`api_secret` and `measurement_id`). If you send it, we will ignore it.
-   **Transaction sources**. To transfer transaction source information for use in marketing reports, include relevant fields about the source in the `params` field. This data will then be stored in the final table. Here's an example:
    
    {
      "client\_id":"client\_id",
      "events":\[
        {
          "name":"campaign\_details",
          "params":
            {
              "campaign\_id":"google\_1234",
              "campaign":"Summer\_fun",
              "source":"google",
              "medium":"cpc",
              "term":"summer+travel",
              "content":"logolink"
            }
        }
      \]
    }
    
-   **Geodata.** By default, geo fields (`geo.continent`, `geo.country`, `geo.region`, `geo.city`) are empty for events sent via the Measurement Protocol, because the request comes from your server, not from the visitor's device. The IP address of your server is never used to determine geodata.  
      
    To fill geo fields, pass the visitor's IP address in the `ip_override` parameter at the top level of the request body (next to `client_id`, not inside `events[].params`). OWOX BI will determine geodata for the event based on this IP address. If `ip_override` is missing, geo fields stay `null`. Pass the IP address only for visitors who have consented to share this data, in line with your privacy policy.
    
    {
      "client\_id":"123.456",
      "ip\_override":"8.8.8.8",
      "events":\[
        {
          "name":"purchase",
          "params":
            {
              "transaction\_id":"T12345",
              "value":25.42,
              "currency":"USD"
            }
        }
      \]
    }
    

### Issues

If the request structure does not match, data parsing will not occur. You can see the original request in the `debug_info.raw_request` field.

## Sending Data From a Website Using On-Site gtag.js Code

### Overview

The [Google tag (gtag.js)](https://developers.google.com/tag-platform/gtagjs?hl=en) is a single tag you can add to every page of your website and use `gtag()` commands to send data to Google products (e.g. Google Analytics 4). OWOX BI GA4 events-based streaming supports sending data via gtag.js. In this article, we describe two options for customizing the Google tag.

### Requirements

Before configuring data collection through the gtag.js, check the following statements:

-   Google Analytics 4 should be already implemented on your website via Google tag (gtag.js) ([setup instructions](https://developers.google.com/analytics/devguides/collection/ga4/tag-options)).
-   In the OWOX BI interface, you've created a new GA4 event-based web streaming ([step-by-step guide](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/setup.md#how-to-set-up-ga4-event-based-web-streaming-client-side--server-side)).
-   You have access to the HTML for your web pages. Ask your web developer to perform these steps if you're unable to complete the steps yourself. 

### Setup options

#### Option A. Data is collected simultaneously in both Google Analytics 4 and OWOX BI GA4 events-based web streaming to Google BigQuery

**Step 1.** Following [this instruction](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-a-website-using-google-tag-in-google-tag-manager), create a new web stream 'OWOX BI Stream' in Google Analytics 4 settings. In the next step, you will need the ’MEASUREMENT ID’ of this stream.

**Step 2.** Find each page of your website where you use the [gtag config command](https://developers.google.com/tag-platform/gtagjs/reference?hl=en#config). Add one more `gtag ('config')` command with the parameter `'transport_url'`. As a value of this parameter specify the link to the OWOX BI access point:

```
gtag('config', 'G-XXXXXX-2', { 
	'transport_url': 'https://streaming.bi.owox.com/ga4/...',
	'groups': 'owox'
});
```

Replace `'G-XXXXXX-2'` with the ’MEASUREMENT ID’ of the new ‘OWOX BI Stream’.

Replace `'https://streaming.bi.owox.com/ga4/...'` with the full URL, which you can find on the **Settings tab** of your Streaming page:

![16.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/19/public)

> **Note**  
> We also recommend using the `'groups'` parameter. This will help you with sending custom events in the next step.

So you should get something like this:

```
gtag('config', 'G-XXXXXX-1', { 
	'groups': 'ga'
});

gtag('config', 'G-XXXXXX-2', { 
	'transport_url': 'https://streaming.bi.owox.com/ga4/...',
	'groups': 'owox'
});
```

First (default) gtag config:

-   Replace `'G-XXXXXX-1'` with a ‘MEASUREMENT ID of your default stream in Google Analytics 4 settings.
-   Add parameter `'groups'` with value `'ga'`.

Second gtag config:

-   Should be added below after the default gtag config command.
-   And this gtag has two parameters: `'transport_url'` and `'groups'`.

**Step 3 (Optional).** If you send custom events to Google Analytics 4, then you should start sending these custom events to the web stream ‘OWOX BI Stream’ which you created in the first step. To do this, add the parameter `'send_to'` to each custom event. As a value of this parameter write webstream ‘MEASUREMENT ID’:

```
gtag('event', 'generate_lead', { 'send_to': 'G-XXXXXX-2' });
```

or specify group name (from gtag config):

```
gtag('event', 'generate_lead', { 'send_to': ['owox'] });
```

**Recommended method:** send event data to the specified group. You should get something like this:

```
gtag('config', 'G-XXXXXX-1', { 
	'groups': 'ga'
});

gtag('config', 'G-XXXXXX-2', { 
	'transport_url': 'https://streaming.bi.owox.com/ga4/...',
	'groups': 'owox'
});

gtag('event', 'generate_lead', { 'send_to': ['ga','owox'] });
gtag('event', 'login', { 'send_to': ['ga','owox'], 'user_id': 1, 'room': 'public' });
gtag('event', 'logout', { 'send_to': ['ga','owox'] });
```

**Step 4.** Save all changes and deliver them to the server from which your site is loaded. Data collection may take up to 30 minutes to begin.

#### Option B. Data is collected ONLY into OWOX BI GA4 events-based web streaming to Google BigQuery and is not streamed to Google Analytics 4

**Step 1.** To **the existing gtag config** command add the `'transport_url'` parameter. As a value of this parameter specify the link to the OWOX BI access point:

```
gtag('config', 'G-XXXXXX-1', { 
	'transport_url': 'https://streaming.bi.owox.com/ga4/...'
});
```

Replace `'G-XXXXXX-1'` with the 'MEASUREMENT ID' of your default stream in Google Analytics 4 settings.  
Now all events will only be sent to the OWOX BI access point and will no longer be sent to Google Analytics.

**Step 2.** Save the changes and deliver them to the server from which your site is loaded. Data collection may take up to 30 minutes to begin.

## Sending Data From a Website Using Custom HTML Tag in Google Tag Manager

> **Important!**  
> Learn more about the advantages and disadvantages of this data collection method. If you are unsure that you correctly understand the specifics of setting up the Custom HTML tag, we advise you to choose another option – the [GA4 configuration tag](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-a-website-using-google-tag-in-google-tag-manager).

### Overview

Your website has a standard JavaScript code for collecting and sending events to Google Analytics 4. Sending data from the website to the Google Analytics servers is implemented by [Navigator.sendBeacon()](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/sendBeacon) JavaScript method, which is supported by all modern browsers. 

This method can be modified for **simultaneously sending events to the Google Analytics and OWOX BI access points**. As a result, the data collected by OWOX BI will completely match the data in Google Analytics 4.

The Navigator.sendBeacon() method can be modified using the Javascript code below on this page, which is loaded into your website using the Custom HTML Tag in Google Tag Manager. This option has pros and cons compared to [sending data via the GA4 configuration tag](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-a-website-using-google-tag-in-google-tag-manager).

#### Benefits of using the custom HTML tag

You will set up sending data in three simple steps in the Google Tag Manager interface. This is the main advantage of this method compared to the setting GA4 configuration tag because:

-   In Google Analytics 4, you don't need to create a new data stream to get a separate MEASUREMENT ID for OWOX BI streaming.
-   In Google Tag Manager, you don't need to duplicate the GA4 config tag and copy all tags for your custom events (even new tags you will create in the future).  
    As a result, in OWOX BI streaming, you will get the same data as in Google Analytics 4. For example, even 'clientId' and 'sessionId' will be the same.

#### Potential risks of using the Custom HTML Tag

There is a slim chance of stopping the data sending, which means their irretrievable loss. We know three reasons why this might happen:

-   Sending data to Google Analytics 4 and the OWOX BI access point can be blocked by other Javascript code snippets on the web page. If they also modify the default behavior of the Navigator.sendBeacon() method.
-   Data will not be sent from [older browser versions](https://caniuse.com/?search=navigator.sendBeacon) that do not support the Navigator.sendBeacon() method.
-   Google may change the server address where events are sent, and sending data to Google Analytics 4 may be interrupted. In this case, you should change the Javascript code in the Custom HTML tag.

### Requirements

Before configuring data collection through the Custom HTML Tag, check the following statements:

-   One Google Analytics 4 counter should be already implemented on your website via Google Tag Manager ([setup instructions](https://support.google.com/analytics/answer/9304153)).
-   In the OWOX BI interface, you've created a new GA4 event-based web streaming ([step-by-step guide](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/setup.md#how-to-set-up-ga4-event-based-web-streaming-client-side--server-side)).
-   The 'Send to server container' option is disabled in the GA4 Configuration tag settings.
-   You are not using Javascript code that modifies Navigator.sendBeacon() method on your website. If you use it, write to [bi@owox.com](mailto:bi@owox.com), and we will help you modify our Javascript code for sending events correctly from the website.

### Step-by-step setup

#### Step 1. Create a new Custom HTML Tag

1.1. Go to Google Tag Manager to the tags section. Create a new Custom HTML Tag.

1.2. Name the tag, e.g. **OWOX GA4 Custom HTML**.

1.3. Copy the Javascript code below:

```
!function(){if(navigator.sendBeacon&&-1!==navigator.sendBeacon.toString().indexOf("native code"))try{var a=window.navigator.sendBeacon;window.navigator.sendBeacon=function(){var n,o,t=Array.prototype.slice.call(arguments);return t&&t[0].match(/analytics\.google\.com|google-analytics\.com.*v\=2\&/)&&(n=(t[0].split("?")[1]||document.location.search).replace(/(^\?)/,"").split("&").map(function(n){return this[(n=n.split("="))[0]]=decodeURIComponent(n[1]),this}.bind({}))[0],t[0]=["https://streaming.bi.owox.com/ga4/...",(o=n,Object.keys(o).map(function(n){return n+"="+encodeURIComponent(o[n])}).join("&"))].join("?"),a.apply(this,t)),a.apply(this,arguments)}}catch(n){return a.apply(this,arguments)}}();
```

Paste it to the ‘HTML’ section. Find ‘**https://streaming.bi.owox.com/ga4/…**’ and replace it with a full link to the OWOX BI access point, which you can copy from the **Settings tab** on the Streaming page:

![16.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/20/public)

1.4. Add the ‘All pages’ firing trigger.

1.5. And you will get settings like on the screen below:

![01\_new.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/21/public)

Click the ‘Save’ button and go to the next step.

#### Step 2. Set up tag sequence

2.1. Go to your ‘Google Analytics 4 Configuration Tag’.

2.2. Open the 'Advanced Settings -> Tag Sequencing’ section and enable the option ‘Fire a tag before…’.

Specify your new tag ‘**OWOX GA4 Custom HTML**’.

![02.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/22/public)

2.3. Save the changes.

#### Step 3. Publish GTM container

To start sending data to the OWOX BI access point, publish a container with a new tag.

## Sending Data From a Website to a Server-Side GTM Container

### Overview

This guide explains how to install and configure the [OWOX BI GA4 Web Streaming template tag](https://tagmanager.google.com/gallery/#/owners/OWOX/templates/sgtm-owox-ga4-bigquery) on your server-side Google Tag Manager (sGTM) container.

> **Note:**  
> These settings are not visible in the OWOX BI interface. If you have any questions regarding data collection in sGTM, feel free to contact your OWOX manager at [bi@owox.com](mailto:bi@owox.com).

### Requirements

Before you begin configuring data collection using the Google Tag (previously known as the GA4 Configuration tag), ensure the following prerequisites are met:

-   You have an **active server-side GTM container**.
-   In the OWOX BI interface, you’ve already created a **GA4 event-based web streaming** ([step-by-step guide](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/setup.md#how-to-set-up-ga4-event-based-web-streaming-client-side--server-side)).

### Step-by-Step Setup

#### Step 1: Add OWOX Tag Template to Your GTM Workspace

**1.1.** Open the [OWOX BI GA4 Web Streaming template tag](https://tagmanager.google.com/gallery/#/owners/OWOX/templates/sgtm-owox-ga4-bigquery) and click ‘**Add to workspace**’.

![01.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/23/public)

Alternatively, follow these steps:

-   Open your GTM account and select the server-side Container.
-   Navigate to the Templates page. Under the ‘T**ag Templates**’ section, click ‘**Search Gallery**’.  
    ![02.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/24/public)
-   Use the search term ‘**owox**’ to find the ‘**OWOX BI GA4 Web Streaming To Google BigQuery**’ template by OWOX.  
    ![03.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/25/public)
-   Click ‘**Add to workspace**’.  
    ![04.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/26/public)

**1.2.** Confirm your action to add the template to your Workspace.

![05.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/27/public)

#### Step 2: Create and Configure a New Tag

**2.1.** In the GTM interface, go to **Tags** in the left navigation menu and click ‘**New**’.

![06.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/28/public)

**2.2.** Name your new tag (choose any descriptive name you prefer).

**2.3.** Set up **Tag Configuration**:

**2.3.1.** Choose the **tag type**: ‘OWOX BI GA4 Web Streaming To Google BigQuery’.

![07.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/29/public)

**2.3.2.** In the ‘**Data Stream URL**’ field, paste your **OWOX BI Access Point URL**. You can copy this URL from the OWOX BI Streaming settings page.

![08.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/30/public)

**2.4.** In the Triggering section of your tag, set up a trigger. **Select a trigger** appropriate to the events you want to track.

![09.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/31/public)

> **Important:**  
> To ensure accurate data collection, use the same triggers you use for your GA4 tag. For example, if your GA4 tag fires on page views, button clicks, or form submissions, your OWOX BI tag should use those same triggers.

#### Step 3: Preview and Test

**3.1.** Enable Preview Mode. Click ‘**Preview**’ in the top-right corner of your sGTM container.

![10.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/32/public)

**3.2.** Test Thoroughly. Trigger the events or conditions that should fire your tag. Use the sGTM debug view to confirm that the tag is firing correctly and that the expected data is being sent.

#### Step 4: Publish Changes

Once you’ve successfully tested the tag, click ‘Submit’ in the top-right corner to publish the changes to your live container.

![11.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-data-sending-options/33/public)

If you have any questions or need assistance, our Support team is here to help! Contact us at [bi@owox.com](mailto:bi@owox.com).
