# OWOX BI tracking code integration with Google Tag Manager

To complete [setting up a user behavior data pipeline](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking.md#google-analytics--google-bigquery-create-a-user-behavior-web-streaming) from your website to Google BigQuery, you need to integrate the OWOX BI tracking code on your website. If you're using Google Tag Manager to send website event data to Google Analytics, you can also use it to integrate the OWOX BI tracking code.

> It is highly recommended to check your website's Content Security Policy before embedding the tracking code. To run [custom code](https://support.google.com/tagmanager/answer/6107167?hl=en) added by users, the two listed GTM domains must be whitelisted on your website server. Make sure your Content-Security-Policy Header includes the `script-src` directive with the `unsafe-inline` and `unsafe-eval` keywords as follows: `script-src 'unsafe-eval' 'unsafe-inline' [https://tagmanager.google.com/](https://tagmanager.google.com/)  [https://www.googletagmanager.com/](https://www.googletagmanager.com/)`. This directive is **necessary** for the correct work of the OWOX BI tracking code.

## How to set up website data collection to Google BigQuery tracking with Google Tag Manager

### Step 1: Create _customTask_ variable

1\. Go to the **Variables** > **User-Defined Variables** and click **New**.![Custom\_task\_en\_1.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/01/public)

2\. In the window opened, click the **Variable Configuration** block and select [**Custom JavaScript**](https://support.google.com/tagmanager/answer/6107167?hl=en) as a variable type.![Custom\_task\_en\_2.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/02/public)

3\. Name the variable as _customTask_**.** In the **Custom JavaScript** window, paste the OWOX BI tracking code.![Custom\_task\_en\_3.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/03/public)

You can find the code on your user behavior data pipeline under **Settings > JavaScript code for hit and session data collection** or copy it below:

```
function() { return function(tracker){ if("undefined"===typeof tracker.get("OWOXBIStreaming")){var f=tracker.get("sendHitTask"),h=function(){function d(c){var a=!1;try{document.createElement("img").src=e(!0)+"?"+c,a=!0}catch(k){}return a}function e(c){var a="https://google-analytics.bi.owox.com/"+encodeURIComponent(tracker.get("trackingId"));c||(a+="?tid="+encodeURIComponent(tracker.get("trackingId")));return a}return{send:function(c){var a;if(!(a=2036>=c.length&&d(c))){a=!1;try{a=navigator.sendBeacon&&navigator.sendBeacon(e(),c)}catch(g){}}if(!a){a=!1;var b;try{window.XMLHttpRequest&&"withCredentials"in(b=new XMLHttpRequest)&&(b.open("POST",e(),!0),b.setRequestHeader("Content-Type","text/plain"),b.send(c),a=!0)}catch(g){}}return a||d(c)}}}();tracker.set("sendHitTask",function(d){f(d);h.send(d.get("hitPayload"));tracker.set("OWOXBIStreaming",!0)})}}}
```

> Consider [this OWOX BI code implementation](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md) if you also need to submit the _clientId_ to Google Analytics via the Google Tag Manager.

4\. Click **Save** to add the code to the _customTask_ variable. 

### Step 2: Add _customTask_ variable to Universal Analytics tags

> **Note**  
> Before proceeding with this step, ensure you have created the Universal Analytics tags in your Google Analytics.

At this step, you need to add your _customTask_ to all the Universal Analytics tags (for example, goals or transaction tracking tags, etc.) that you want to use for data transferring to Google Big BigQuery. Choose one of the options below:

-   [Adding customTask via setting an additional field](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md)
-   [Adding customTask via Google Analytics Settings variable](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md)

> You can't use more than one _customTask_ in a single GTM tag. The function of each new _customTask_ overrides the function of the previous one.  
> If it's crucial for you to use several _customTask_ functions in a single tag, you can join their JavaScript code within a single _customTask_.  
> Remember, that modifying the OWOX BI tracking code can lead to data collection issues.

#### **Adding customTask via setting an additional field**

1\. Go to Universal Google Analytics tag and enter the _Edit_ mode by clicking the relevant icon. 

![GA\_settings\_variable\_3\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/04/public)

2\. Select **More settings** > **Fields to set** > **\+ Add Field**. Enter _customTask_ in the **Field name** and _{{customTask}} in the_ **Value** fields.![Custom\_task\_en\_5.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/05/public)

3\. Click **Save**.

Next, complete [steps 3 and 4](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md) described on the current page.

#### **Adding _customTask_ via Google Analytics Settings variable**

This option requires creating the [Google Analytics Settings variable](https://support.google.com/tagmanager/answer/9207621?hl=en) first. To create Google Analytics Settings variable and then add your _customTask_ to tags, complete the following actions:

1\. Go to the **Variables** > **User-Defined Variables** and click **New**.

![Custom\_task\_en\_1.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/01/public)

2\. In the window opened, click the **Variable Configuration** block and select **Google Analytics Settings** under the **Utilities** heading.

![GA\_settings\_variable\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/07/public)

3\. In the variable settings, specify the **Variable name** (you can use your Tracking ID value as a name) and **Tracking ID** – an identifier of your Google Analytics property you're using to collect data.

4\. Select **More settings** > **Fields to set**. Enter _customTask_ as a **Field name** and _{{customTask}}_ as a **Value**.

![GA\_settings\_variable\_2\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/08/public)

5\. Go to Universal Google Analytics tag and enter the _Edit_ mode by clicking the relevant icon. 

![GA\_settings\_variable\_3\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/04/public)

6\. Add the Google Analytics Settings variable to the **Google Analytics Settings** field.

![GA\_settings\_variable\_4\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/10/public)

7\. Click **Save**.

Next, complete [steps 3 and step 4](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md) described below.

### Step 3: Check up the tag work

After you edit and save Universal Analytics tags, make sure they work properly before publishing the container. 

To do it, [enable Preview Mode](https://support.google.com/tagmanager/answer/6107056) for the current workspace. Then, go to your website and make a few test clicks on a website page. To review the testing results, use GTM debug console window at the bottom of your browser. Select **Click** event in the left column, then switch to the **Variables** tab in the top navigation. Search for the _customTask_ variable in the list.  If the _customTask_ variable is configured properly, it will contain the value '_Function'_. 

![customTask\_function.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/11/public)

> If the _customTask_ variable contains the value _'Undefined'_, make sure you complete Step 1 and Step 2 described above. Please check out the possible reasons for the _customTask_ doesn't work:  
> \- [OWOX BI code snippet](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md) is copied incorrectly into the _customTask_;  
> \- [more than one](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md) [_customTask_](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md) is used in a single GTM tag;  
> \- GTM domains are [not allowed on your website server](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md).

### Step 4: Publish GTM container

If the GTM tags are configured properly, publish the container. 

Then go to your user behavior data pipeline page in OWOX BI and click the specified Google BigQuery dataset link.

![Custom\_task\_en\_6.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/12/public)

If the code is integrated correctly, a _"streaming\_"_ table will appear in the dataset. The table should receive first hit data within 2 through 5 minutes after you've updated your Google Tag Manager container.

In 24 hours after the code integration, you will see the _Data collected_ status next to the previous day's statistics under the **Hits** tab.![Custom\_task\_en\_7.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-owox-bi-tracking-code-integration-with-google-tag-manager/13/public)

After another 24 hours, you'll see a notification about the session data collected under the **Sessions** tab. The session data will also be available in your Google BigQuery dataset in a separate table.

>  

## Other data collection use cases

### Send data to Google BigQuery only and block Google Analytics tracking

This can be useful if you want not to exceed Google Analytics limits or if your website generates more hits than GA can receive and you don't need to collect incomplete data there.

If it is your case, the code snippet listed below must be applied instead of the integration code from the pipeline page. Copy the following **code for sending data to Google BigQuery only** into the _customTask:_

```
function() { return function(tracker){ if("undefined"===typeof tracker.get("OWOXBIStreaming")){var f=tracker.get("sendHitTask"),h=function(){function d(c){var a=!1;try{document.createElement("img").src=e(!0)+"?"+c,a=!0}catch(k){}return a}function e(c){var a="https://google-analytics.bi.owox.com/"+encodeURIComponent(tracker.get("trackingId"));c||(a+="?tid="+encodeURIComponent(tracker.get("trackingId")));return a}return{send:function(c){var a;if(!(a=2036>=c.length&&d(c))){a=!1;try{a=navigator.sendBeacon&&navigator.sendBeacon(e(),c)}catch(g){}}if(!a){a=!1;var b;try{window.XMLHttpRequest&&"withCredentials"in(b=new XMLHttpRequest)&&(b.open("POST",e(),!0),b.setRequestHeader("Content-Type","text/plain"),b.send(c),a=!0)}catch(g){}}return a||d(c)}}}();tracker.set("sendHitTask",function(d){h.send(d.get("hitPayload"));tracker.set("OWOXBIStreaming",!0)})}}}
```

### Collect special values to Google BigQuery only

Google Analytics's tracking code doesn't process some specific parameters like phone numbers or emails. Yet, if you need to collect this data to Google BigQuery only, you can create a separate Google Tag Manager tag with the _customTask._ Read [this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking.md#set-up-special-values-collecting-to-google-bigquery-only) to learn more.

### Collect _clientId_ to Google Analytics Custom Dimension via OWOX BI tracking code

If you run into a situation where you need to use the Google Tag Manager on the same website to integrate the OWOX BI tracking code and to submit the clientId to Google Analytics, then you can apply the adapted OWOX BI tracking code. This code allows you:

-   to prevent the conflict of multiple customTasks in a single GTM tag (i.e. when the function of each new customTask overrides the function of the previous one);
-   to submit the clientId to the Google Analytics Custom Dimension;
-   to collect hits into the Google BigQuery tables.

To do it, insert the code below into your customTask variable instead of the default OWOX BI tracking code:

```
function() {return function(tracker){if("undefined"===typeof tracker.get("OWOXBIStreaming")){tracker.set("dimension2", tracker.get("clientId")); var f=tracker.get("sendHitTask"),h=function(){function d(c){var a=!1;try{document.createElement("img").src=e(!0)+"?"+c,a=!0}catch(k){}return a}function e(c){var a="https://google-analytics.bi.owox.com/"+encodeURIComponent(tracker.get("trackingId"));c||(a+="?tid="+encodeURIComponent(tracker.get("trackingId")));return a}return{send:function(c){var a;if(!(a=2036>=c.length&&d(c))){a=!1;try{a=navigator.sendBeacon&&navigator.sendBeacon(e(),c)}catch(g){}}if(!a){a=!1;var b;try{window.XMLHttpRequest&&"withCredentials"in(b=new XMLHttpRequest)&&(b.open("POST",e(),!0),b.setRequestHeader("Content-Type","text/plain"),b.send(c),a=!0)}catch(g){}}return a||d(c)}}}();tracker.set("sendHitTask",function(d){f(d);h.send(d.get("hitPayload"));tracker.set("OWOXBIStreaming",!0)})}}}
```

> Please update the figure in _"dimension2"_ with the actual index of your Client ID parameter in Google Analytics.
