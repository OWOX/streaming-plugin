# Set up web tracking on the website

- [Google Analytics → Google BigQuery: Create a user behavior web streaming](#google-analytics--google-bigquery-create-a-user-behavior-web-streaming)
- [Set up session data collection based on OWOX BI algorithm](#set-up-session-data-collection-based-on-owox-bi-algorithm)
- [Creating a referral exclusion list for session data collection](#creating-a-referral-exclusion-list-for-session-data-collection)
- [Set up special values collecting to Google BigQuery only](#set-up-special-values-collecting-to-google-bigquery-only)
- [Set up collecting  email and phone data](#set-up-collecting--email-and-phone-data)

## Google Analytics → Google BigQuery: Create a user behavior web streaming

The Google Analytics→Google BigQuery pipeline collects raw non-sampled data about user behavior on your website, both at hit and session levels.

The collected hit data is uploaded to BigQuery tables in real-time. Session data gets to BigQuery within the following day.

> **Note:**  
> When you create a hit and session data pipeline, OWOX BI automatically creates a cost data pipeline from Google Analytics to Google BigQuery.

### What you need before creating a pipeline

1.  To use Google BigQuery and create a streaming pipeline in OWOX BI, you need a [Google Cloud Platform project](https://cloud.google.com/bigquery/sign-up) with [activated billing](https://support.google.com/cloud/answer/6288653). Read more about how to set up Google BigQuery [in this article](https://www.owox.com/docs/storages/google-bigquery/).
2.  To create a user behavior data pipeline, you need a subscription to the plan **Growth**, **Professional**, **Marketing Data**, **Reports & Attribution**, or **Enterprise** in OWOX BI. [Session cost attribution](https://github.com/OWOX/transformations-plugin/blob/main/docs/templates/costs-attribution.md#calculation-examples-for-the-adcost-and-attributedadcost-fields) for this pipeline is not available in the **Marketing Data** subscription.

### How to create a pipeline

To create an OWOX BI hit-based web streaming take the following steps:

**Step 1.** On the [Workspace page](https://bi.owox.com/ui/app/none/workspace), click the ‘**New**’ button and select the Streaming option in a drop-down menu:

![01-nnnew.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/01/public)

Or, if your workspace page is empty, you will see a screen with three major blocks. Click the ‘**\+ New streaming**’ button.

![02-nnnew.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/02/public)

**Step 2**. As a streaming type, select ‘**Hit-based web streaming**’:

![03-nnnew.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/03/public)

And click the '**Create & Setup**' button to proceed to the next step.

**Step 3**. Provide access to Google Analytics:![Create\_a\_Pipeline\_3.\_Provide\_access\_to\_GA.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/04/public)

**Step 4**. Select the Google Analytics property and view as the data source.

Here, specify the Google Analytics view you want to gather data to. You need to have **Edit** [permission](https://support.google.com/analytics/answer/2884495?hl=en) for this Google Analytics property.

Note that you can set up _only one_ pipeline per property.![Create\_a\_Pipeline\_4.Select\_GA\_view.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/05/public)

**Step 5**. Provide access to the Google BigQuery account you want to upload Google Analytics data to:![Create\_a\_Pipeline\_5.\_Provide\_Access\_to\_BQ.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/06/public)

**Step 6**. Select a Google BigQuery dataset.

First, select the Google BigQuery project that contains the dataset you want to gather data to. Then, select the dataset.

If needed, you can create a new dataset right here.

You can set up _only one_ OWOX BI pipeline for each BigQuery dataset. Creating several pipelines for one dataset may cause the hit data to be overwritten.![Create\_a\_Pipeline\_6.Select\_BQ\_dataset.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/07/public)

**Step 7**. Integrate the tracking code on your website. 

The method of implementing the tracking code depends on your project. Read about the tracking with Google Tag Manager, analytics.js, Measurement Protocol, Tealium IQ, and AMP pages, Android and iOS apps tracking in the [list of the tracking code implementation methods](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection.md).

Find it on the created pipeline's page: **Settings** > **JavaScript code for hit and session data collection**:![Pipeline\_Tracking\_Code\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/08/public)

Done. The Google Analytics Real-Time Data → Google BigQuery streaming pipeline is all set.

A dataset with hit data tables will appear in your BigQuery project in the first minute of the next hour. Session data will get to BigQuery in 12 hours after the beginning of the next day.  

> **Please note:**   
> In OWOX BI pipeline, the maximum hit payload size is 16 KB. The hits exceeding this limit won't be processed and sent to BigQuery.  
> In Google Analytics, the maximum hit payload size [is 8 KB](https://developers.google.com/analytics/devguides/collection/protocol/v1/reference).

## Set up session data collection based on OWOX BI algorithm

### Basic settings

The session data collection in OWOX BI will be enabled automatically once you have the [hit data collection set up](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking.md#google-analytics--google-bigquery-create-a-user-behavior-web-streaming).

The first "owoxbi\_sessions" table with session data will appear in Google BigQuery within 48 hours. All the following tables will be appearing within 12 hours after the end of the day the sessions happened. The "owoxbi\_sessions" tables' structure is described in [this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/schema-for-session-data-tables-collected-with-the-owox-bi-algorithm-owoxbi-sessions.md).

You can always turn off or on the session data collection using the switcher in the **Settings** tab on the pipeline page. Note that if you've turned the session data collection off, and then turned it on later, the historical data won't be collected — OWOX BI will upload session data for the previous day only.

![Session\_data\_collection\_toggle\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/09/public)

### Advanced settings

You can upload from Google Ads to BigQuery the data on traffic sources in the campaigns with auto-tagging by retrieving it from the `gclid` parameter. To do that, go to **Settings** > **Change settings,** and in the **Data from Google Ads campaigns with auto-tagging** section, set up the automatic reports.

> **Note**  
> These settings are required if you use Google Ads with auto-tagging. If you don't use auto-tagging, you can skip these settings.  
>   
> If you use both auto-tagging and manual tagging in your Google Ads campaigns, read [this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/faq.md#which-values-will-be-recorded-to-the-hit-and-session-data-tables-when-gclid-auto-tagging-and-manual-utm-tagging-data-overlap-in-google-ads).

![Sessions\_Google\_Ads\_setup\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/10/public)

The setup guide is in the article "[Collect raw data from Google Ads campaigns with auto-tagging](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking/setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging.md)".

> **Important:**  
> If you run Google Ads campaigns with auto-tagging, setting up the Google Ads reports in OWOX BI **is required** to get the data on traffic sources from these campaigns to BigQuery.

**Referral exclusion list** helps you keep accurate cross-subdomain tracking and fight off the referral spam. If you have a referral exclusion list in Google Analytics, just copy it to OWOX BI.

Read the details in [this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking.md#creating-a-referral-exclusion-list-for-session-data-collection).![Domains2eng.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/11/public)

You can also set the **campaign and session** **timeout** (the time periods after which a campaign or a session is automatically terminated) and collect data on the visited **page type** to the `hits.pageType` field, set the corresponding fields in the session data collection settings:![Campaign\_timeout\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/12/public)

### Setting up session data collection in OWOX BI for the users who have session data collection based on Google Analytics enabled

1\. In OWOX BI, open the **Settings** tab in a user behavior data pipeline.

2\. In the **Session data collection** section, click **Change settings**.

3\. Select **Based on hit data**:

![Session\_data\_settings\_GA\_\_1\_.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/13/public)

> **Important:**  
> After that, you won’t be able to turn on the old based on Google Analytics algorithm in the interface, and OWOX BI will begin collecting the "owoxbi\_sessions" tables only. If you want to collect both types of tables to compare the algorithms, write us at [bi@owox.com](mailto:bi@owox.com), and we’ll turn on the “session\_streaming\_” tables collection for you.  
> Also, note that you don't need anymore to specify the custom dimensions collecting Session ID and User ID in the settings.

4\. In the **Settings** tab, copy the updated tracking code and implement it on your website using one of the [integration methods](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection.md):

![Data\_collection\_code.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/14/public)

Done. Now session data will be collected to the  "owoxbi\_sessions" in Google BigQuery.

## Creating a referral exclusion list for session data collection

By default, a referral automatically creates a new session. If a referral source is on the exclusion list, the traffic that comes to your site from the excluded domain doesn’t create a new session. 

> **Note:**  
> If a link connects pages within the same domain (for example, it leads from `www.example.com` to the subdomain page `www.start.example.com`), proceeding by this link won't create a new session, instead, the current session will continue.

When collecting session data based on Google Analytics data in OWOX BI Pipeline, you can exclude referral traffic in the Google Analytics interface.

If you use the OWOX BI algorithm to collect session data based on non-sampled hit data, just add the domains you want to exclude to the referral exclusion list right on your pipeline page in OWOX BI.

> **Important:**  
> Your referral exclusion lists in OWOX BI and Google Analytics must be identical, otherwise there may be discrepancies in collected session data.  
> Find your Google Analytics referral exclusion list by going to: **Admin** → **Property** → **Tracking** **info** → **Referral Exclusion List**:![image14.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/15/public)

### Why you might need to exclude referrals

**For processing payments.** If a user needs to process payment using a third-party shopping cart while purchasing products at your site, adding the payment processor's domain to the exclusion list will prevent starting a new session once a user comes back to your site.

**For accurate cross-subdomain tracking.** If your site users need to move between several subdomains while interacting with your business, add your domain to the exclusion list, so user migration between your subdomains will be happening within a single session always when it should be a single session.

**For fighting off referral spam.** Referral spam is traffic sent by spam bots hiding behind different domain names and IP addresses. Such traffic is bad for your site metrics and indexing, as it increases the bounce rate and decreases the average session time on your site. Adding suspicious domains to the exclusion list, you'll filter out spam domains and will be able to get the tracking data that's relevant to your business.

### How to add domains to the referral exclusion list

1\. On your pipeline page, click **Change settings**:

![Session\_data\_collection\_switcher\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/16/public)

2\. In the **Referral exclusion list** section, click the edit icon:

![Session\_data\_collection\_settings-1.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/17/public)

3\. Type in or paste domain names as `example.com`. Use commas to separate them from one another. These domains won't be tracked as referrals and that won't start new sessions.

![Domains2eng.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/18/public)

> **Note:**  
> The referral exclusion list uses CONTAINS matching. This means all subdomains containing the domain name will not be tracked as referrals. For example, if you exclude the `example.com` domain, the `start.example.com` subdomain will be excluded too.

4\. Click **Save list**. The added domains will appear on the list.

You can delete the domains from the list using the **Delete** button to the right of the domain name: ![Domains3eng.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/19/public)

To apply the deletion, click **Save list**.

## Set up special values collecting to Google BigQuery only

If you need to collect to Google BigQuery the data that Google Analytics's tracking code can't process (for example, personal data like phone numbers or emails), you can create a separate Google Tag Manager tag and use a [customTask](https://developers.google.com/analytics/devguides/collection/analyticsjs/tasks) to collect these specific values only directly to Google BigQuery.

To do this:

1.  In a Google Tag Manager container, create a **Custom JavaScript**\-type customTask variable. In this variable, add **the code for sending data to Google BigQuery only:**  
    
    ```
    
     function() { return function(tracker){  if("undefined"===typeof tracker.get("OWOXBIStreaming")){var f=tracker.get("sendHitTask"),h=function(){function d(c){var a=!1;try{document.createElement("img").src=e(!0)+"?"+c,a=!0}catch(k){}return a}function e(c){var a="https://google-analytics.bi.owox.com/"+encodeURIComponent(tracker.get("trackingId"));c||(a+="?tid="+encodeURIComponent(tracker.get("trackingId")));return a}return{send:function(c){var a;if(!(a=2036>=c.length&&d(c))){a=!1;try{a=navigator.sendBeacon&&navigator.sendBeacon(e(),c)}catch(g){}}if(!a){a=!1;var b;try{window.XMLHttpRequest&&"withCredentials"in(b=new XMLHttpRequest)&&(b.open("POST",e(),!0),b.setRequestHeader("Content-Type","text/plain"),b.send(c),a=!0)}catch(g){}}return a||d(c)}}}();tracker.set("sendHitTask",function(d){h.send(d.get("hitPayload"));tracker.set("OWOXBIStreaming",!0)})}}}
    ```
    
2.  Create one or more **Custom JavaScript** variables with the code specifically written for collecting these data from the website.  
    **Note:** The JavaScript code in these variables depends solely on the data you want to get from the website, so writing this code must be done from your side.
3.  Create a separate tag that will track the values of these variables:  
    ![customTask\_code\_GBQ\_only\_value\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/20/public)
4.  Add to the tag you've created a Custom Dimension for each of the custom variables you want to track and set values for these dimensions in the **Dimension Value**:![customTask\_code\_GBQ\_only\_custom\_dimension\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/21/public)
5.  Save the tag and publish the container.

Done! Now, this tag will be sending the special values to your Google BigQuery.

## Set up collecting  email and phone data

Email and telephone dimensions are not supported by Google Analytics, but you can use them to collect personal data from your users in Google BigQuery.

1\. If you are using Google Tag Manager, make the following settings to collect email and telephone data to BigQuery

![](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-web-tracking/22/public)

The **Value** fields here must contain variables to which custom dimensions collect phone numbers and emails from the website.

These may be variables like "Custom JavaScript code" or "Data layer variable", but you can use other types as well. Before publishing the new GTM container version, make sure these variables generate correct values.

How to get values of these parameters is explained [in Google Tag Manager Help Center](https://support.google.com/tagmanager/answer/6106899).

2\. If you are using analytics.js code on the website, have to use [set](https://developers.google.com/analytics/devguides/collection/analyticsjs/command-queue-reference#set) command: `ga('set', "&tel", "+12345678900");` and `ga('set', "&email", "bi@owox.com");`

3\. If using Measurement Protocol add following parameters with URL encoded values: `&tel=%2B12345678900` and `&email=bi%40owox.com`
