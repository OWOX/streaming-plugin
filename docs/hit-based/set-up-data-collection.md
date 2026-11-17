# Set up data collection

Three setup methods have a page of their own:

- [Google Tag Manager](set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md)
- [Measurement Protocol](set-up-data-collection/tracking-with-measurement-protocol.md)
- [Google Ads campaigns with auto-tagging](web-tracking/setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging.md)

**On this page**

- [Website tracking with Tealium IQ](#website-tracking-with-tealium-iq)
- [Set up tracking for AMP pages](#set-up-tracking-for-amp-pages)
- [iOS apps tracking](#ios-apps-tracking)
- [Android apps tracking](#android-apps-tracking)
- [OWOX BI Real-Time Data Streaming Setup: A Step-by-Step Guide](#owox-bi-real-time-data-streaming-setup-a-step-by-step-guide)

## Website tracking with Tealium IQ

This integration is intended for those who are using [Tealium IQ](http://tealium.com/products/tealium-iq-tag-management-system/) to send data into Google Analytics.

1.  Create new JavaScript Code extension. In it will be configured Google Analytics tracker and connected OWOX BI Streaming plugin:
    
    ```
    // Google Analytics tracker initialization snippet.
    (function(a,b){a.GoogleAnalyticsObject=b;a[b]=a[b]||function(){(a[b].q=a[b].q||[]).push(arguments)};a[b].l=1*new Date})(window,"ga");
    // Creating 'owox' tracker that will duplicate original Google Analytics hits to Google BigQuery.
    // Don't forget to define Create Only fields here, if you use any ga('create', 'UA-XXXXX-Y', 'auto', {name: 'owox'});
    // Connecting OWOX BI Streaming plugin to the tracker
    ga('owox.require', 'OWOXBIStreaming');
    // OWOX BI Streaming plugin code
    (function(){function f(g){var c=g.get("sendHitTask"),f=function(){function e(b){var a=!1;try{var d=document.createElement("img");d.src=c(!0)+"?"+b;a=!0}catch(h){}return a}function c(b){var a="https://google-analytics.bi.owox.com/"+encodeURIComponent(g.get("trackingId"));b||(a+="?tid="+encodeURIComponent(g.get("trackingId")));return a}return{send:function(b){var a;if(!(a=2036>=b.length&&e(b))){a=!1;try{a=navigator.sendBeacon&&navigator.sendBeacon(c(),b)}catch(h){}}if(!a){a=!1;var d;try{window.XMLHttpRequest&&"withCredentials"in(d=
    new XMLHttpRequest)&&(d.open("POST",c(),!0),d.setRequestHeader("Content-Type","text/plain"),d.send(b),a=!0)}catch(h){}}return a||e(b)}}}();g.set("sendHitTask",function(e){try{c(e)}catch(k){}f.send(e.get("hitPayload"))})}var c=window[window.GoogleAnalyticsObject||"ga"];c&&c("provide","OWOXBIStreaming",f)})();
    ```
    
2.  Specify your Google Analytics web-property ID (UA-XXXXX-YY) in code above

Specify your Google Universal Analytics tag as the Scope for extension:

![](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-set-up-data-collection/01/public)

1.  Set Tracker Name "owox" in Google Universal Analytics tag:
    
    ![](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-set-up-data-collection/02/public)
    
2.  Create and publish a new version of container to finish OWOX BI Streaming settings on your website.

## Set up tracking for AMP pages

To set up tracking for AMP pages (Accelerated Mobile Pages), take the following steps in **Google Tag Manager**.

1\. Create a new GTM container for AMP pages. To do that, go to the **Admin** tab, click **+** at the container column. In the container creation menu, select **AMP**:![AMP\_pages\_0.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-set-up-data-collection/03/public)

2\. In the **Tags** menu, create tags that will be sent to Google Analytics along with the data. Here, include the custom dimension  "Page Client ID" and the trigger that fits your case. For example:![AMP\_pages\_1.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-set-up-data-collection/04/public)

3\. In the **Variables** menu, in the **Built-In Variables** block, click **Configure** and check all 13 variables shown in the screenshot: ![AMP\_pages\_2.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-set-up-data-collection/05/public)

4\. Back in the **Tags** menu, create one more tag with the **Custom Image** type. As a trigger, use the same you used for the tag that is being sent to Google Analytics. In the **Image URL** field, paste this URL:

`https://google-analytics.bi.owox.com/UA-XXXXXXX-X?v=1&ds=AMP&dt={{Document Title}}&sr={{Screen Height}}x{{Screen Width}}&cid={{Page Client ID}}&tid=UA-XXXXXXX-X&dl={{Page URL}}&dr={{Document Referrer}}&ul={{Browser Language}}&t=pageview`

**Note:** The URL contains a Google Analytics Tracking ID (UA-XXXXXXX-X) in two places. When copying this URL to Google Tag Manager, make sure you've changed the "UA-XXXXXXX-X" placeholder to your Tracking ID in both places.

![AMP\_Tracking\_Custom\_Image\_URL\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-set-up-data-collection/06/public)

5\. Publish the container.

Done. With these settings, data will be sent to both Google Analytics and Google BigQuery every time the trigger fires. You can also add additional parameters to the URL if needed.

## iOS apps tracking

#### iOS SDK 

1.  Download OWOX BI Streaming plugin for iOS applications: [OBIStreaming.zip](https://storage.googleapis.com/streaming.bi.owox.com/OBIStreaming.framework.zip).
2.  Add **OBIStreaming.framework** library to your iOS application.
3.  Import the library in AppDelegate.m:  
    
    ```
    #import <OBIStreaming/OBIStreaming.h>
    ```
    
4.  Initialize streaming and define your Google Analytics web-property ID:  
    
    ```
    — (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
      [[OBIStreaming sharedInstance] setTrackerIds:[NSSet setWithArray:@[@"UA-XXXX-X"]]];
    }
    ```
    
5.  Session data requires you to define session scope custom dimension where you collect session ID.  
    
    Here is a snippet for applications using iOS Google Analytics Services SDK to set up session ID data collection. Copy it and replace **XX** with custom dimension number.
    
    ```
    - (NSString *)hitIdWithClientIdentifier:(NSString *)clientId {
        if ([clientId length] == 0) {
            return nil;
        }
        NSTimeInterval timeInterval = [[NSDate date] timeIntervalSince1970];
        return [NSString stringWithFormat:@"%@_%.0f", clientId, timeInterval * 1000];
    }
    NSString *client_id = [[[GAI sharedInstance] trackerWithTrackingId:@"UA-XXXX-X"] get:kGAIClientId];
    [tracker send:[[[GAIDictionaryBuilder createScreenView] set:[self hitIdWithClientIdentifier:client_id]
                                                         forKey:[GAIFields customDimensionForIndex:XX]] build]];
    ```
    
6.  Verify that Google Analytics hits are additionally sent to google-analytics.bi.owox.com domain and contain **&cdXX** parameter with values.

#### GTM

1.  Download OWOX BI Streaming plugin for iOS applications: [OBIStreaming.zip](https://storage.googleapis.com/streaming.bi.owox.com/OBIStreaming.framework.zip).
2.  Add **OBIStreaming.framework** library to your iOS application.
3.  Import the library in AppDelegate.m:  
    
    ```
    #import <OBIStreaming/OBIStreaming.h>
    ```
    
4.  Initialize streaming and define your Google Analytics web-property ID:  
    
    ```
    — (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
      [[OBIStreaming sharedInstance] setTrackerIds:[NSSet setWithArray:@[@"UA-XXXX-X"]]];
    }
    ```
    
5.  Session data requires you to define session scope custom dimension where you collect session ID.  
    
    Here is a snippet for applications using iOS Google Tag Manager SDK to set up GTM Function Call variable with session ID:
    
    ```
    #import "OBIStreamingGTMHitIdFunctionCallMacroHandler.h"
    
    - (void)containerAvailable:(TAGContainer *)container {
        // Important note: containerAvailable may be called from a different thread, marshall the
        // notification back to the main thread to avoid a race condition with viewDidAppear.
        dispatch_async(dispatch_get_main_queue(), ^{
            self.container = container;
            // Register a function call macro handler using the macro name defined
            // in the Google Tag Manager web interface.
            [self.container registerFunctionCallMacroHandler:[[OBIStreamingGTMHitIdFunctionCallMacroHandler alloc] init] forMacro:kOBIStreamingGTMMacroFunctionHitId];
        });
    }
    ```
    
    Import required library:
    
    -   [OBIStreamingGTMHitIdFunctionCallMacroHandler.m](https://storage.googleapis.com/streaming.bi.owox.com/OBIStreamingGTMHitIdFunctionCallMacroHandler.m)
    -   [OBIStreamingGTMHitIdFunctionCallMacroHandler.h](https://storage.googleapis.com/streaming.bi.owox.com/OBIStreamingGTMHitIdFunctionCallMacroHandler.h)
    
    You need to collect session ID custom dimension data from this variable in every Google Analytics tag fired in your application.
    
6.  Verify that Google Analytics hits are additionally sent to google-analytics.bi.owox.com domain and contain session ID custom dimension with values.

#### Google Firebase

You can also use [Google Firebase](https://firebase.google.com/features/analytics/) for data collection from your iOS app in Google BigQuery in real time.

## Android apps tracking

#### Google Firebase

You can use [Google Firebase](https://firebase.google.com/features/analytics/) for data collection from your Android app in Google BigQuery in real time.

## OWOX BI Real-Time Data Streaming Setup: A Step-by-Step Guide

### Getting started requirements

-   You are using the Google Cloud Platform and have an active project. And you have a **BigQuery Admin** or **BigQuery Data Owner** role ([how to set up GCP](https://www.owox.com/docs/storages/google-bigquery/)).
-   OWOX BI Streaming is available in your plan. If it’s not, write to your OWOX manager ([bi@owox.com](mailto:bi@owox.com)).

### Step-by-step setup

[Watch the video](https://www.youtube.com/watch?v=1w5s1B6U-34)
