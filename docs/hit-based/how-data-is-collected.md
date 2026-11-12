# How data is collected

- [Collecting and updating User ID, userEmail, and userPhone data](#collecting-and-updating-user-id-useremail-and-userphone-data)
- [Collecting and updating the Google Analytics-based session data](#collecting-and-updating-the-google-analytics-based-session-data)
- [Data collection limitations](#data-collection-limitations)
- [Source/medium for direct hits in session streaming based on Google Analytics](#sourcemedium-for-direct-hits-in-session-streaming-based-on-google-analytics)
- [Session data collection based on the OWOX BI algorithm](#session-data-collection-based-on-the-owox-bi-algorithm)
- [Collecting OWOX User ID](#collecting-owox-user-id)
- [Differences between the session data collection algorithms](#differences-between-the-session-data-collection-algorithms)
- [Collecting and updating data about Google Ads campaigns with auto-tagging in session data tables](#collecting-and-updating-data-about-google-ads-campaigns-with-auto-tagging-in-session-data-tables)
- [The definitions of device categories in the "owoxbi_sessions" tables](#the-definitions-of-device-categories-in-the-owoxbisessions-tables)
- [How hits-based website streaming works with Google Consent Mode](#how-hits-based-website-streaming-works-with-google-consent-mode)

## Collecting and updating User ID, userEmail, and userPhone data

> **Note:**  
> This article uses collection of User ID to the `user.Id` field as an example of how all three — `user.Id`, `userEmail`, and `userPhone` — fields are being collected and updated by OWOX BI.

### What is User ID and why you need it

User ID is a unique user identifier that lets you associate hit data from different sessions and devices with this one unique user. This data helps you better understand each user’s interaction with your website.

### Collecting User ID data into hit data tables

In [hit data tables](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/data-schema-for-hit-tables-streaming.md), user identifiers are stored in the `userId` field. The field contains the [`&uid`](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#uid) parameter values from the requests that send Google Analytics data from your website.

### Collecting User ID data into session data tables

In [session data tables](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/schema-for-session-data-tables-collected-with-the-owox-bi-algorithm-owoxbi-sessions.md), user identifiers are stored in the `user.Id` field. OWOX BI sends this data to BigQuery in a `&uid` parameter [in the tracking code](https://support.google.com/analytics/answer/3123666?hl=en). 

> **Please note:**  
> If your Google Analytics → Google BigQuery pipeline is still using the deprecated session data collection method based on Google Analytics Core API, then the User ID data is being sent in a `[&cd](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#cd_)` parameter, a custom dimension of a User-level scope.  
> Additionally, if the `user.Id` field values are sent in both the `&uid` (User ID) and the [`&cd`](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#cd_) (custom dimension) parameters, only the value of the `&cd` parameter will get to the `user.Id` field.

### Updating historical data

OWOX BI can retroactively identify unique users who visited your site without a User ID within last 30 days and update their info in session data tables for this period. The update happens only if an unidentified user with the same Client ID logs in and gets a User ID during their next site visit.

Here’s how it works:

1.  You have a Client ID associated with the "null" `user.Id` value in session data tables.
2.  During the next site visit, the user with this Client ID logs in and gets associated with a User ID.
3.  All the “null" `user.Id` values associated with this Client ID in sessions will be rewritten getting the value from the new User ID.
4.  These updates happen daily and affect the data in session data tables for last 30 days.
5.  The updates don’t affect non-”null” `user.Id` values.

**Example:**

-   Session 1 — `clientId`: 35009a79 — `user.Id`: NULL
-   Session 2 — `clientId`: 35009a79 — `user.Id`: as8knl

Here, in the first session, the user wasn’t logged in. For the second session, the same user (identified by Client ID) logged in and become associated with a unique User ID "as8knl".

With the next data update, the "NULL" `user.Id` value from the previous session will be retroactively replaced with the “as8eknlll” value from the second session associated with this Client ID.

However, this will happen only if the previous session happened within 30 days before the second one.

**Another example:**

-   Session 1 — `clientId`: 35009a79 — `user.Id`: as8knl
-   Session 2 — `clientId`: 35009a79 — `user.Id`: rb451f

Here, in the first session, the user with a Client ID “35009a79” was associated with the User ID “as8knl”. For the second session, the same Client ID was logged in with another User ID — “rb451f”. In this case, UID1 in previous sessions will not be replaced with “rb451f”.

Basically, this one Client ID will be treated as two different users, one for each session.

> **Please note:**  
> If your pipeline uses the deprecated session data collection method based on Google Analytics Core API, the `user.Id` field is being updated only when you use a custom dimension (`&cd`) to collect User ID values. Due to GA Core API limitations, the User ID values sent in the `&uid` parameter can’t be updated retroactively.

### Recommendations for setting up passing the User ID parameter to the 'owoxbi\_sessions\_' tables

To upload the `user.Id` parameter data via Google Tag Manager, you need to create a [user-defined variable](https://support.google.com/tagmanager/answer/7683362) that will generate the `user.Id` parameter value. After that, go to your tag settings and in **Fields to Set** select `user.Id` as **Field name**:![image6.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-how-data-is-collected/01/public)

In the **Value** field, set the variable that generates the `user.Id` value.

### How to setup sending User ID values in a custom dimension

> Note: This guide is relevant only to the deprecated session data collection method based on Google Analytics Core API.  
> We recommend you using the OWOX BI algorithm. With it, the User ID data is being sent directly from the BigQuery hit data tables, so you won't need any custom dimensions as workarounds — you get all User ID values in the `&uid` parameter. Read more about the benefits of the new algorithm [in this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#differences-between-the-session-data-collection-algorithms) or write to us at [bi@owox.com](mailto:bi@owox.com?subject=Turn%20on%20the%20new%20session%20data%20collection%20method) and we'll connect the new algorithm to your pipeline.

The standard Google Analytics algorithm doesn’t reveal user identifiers at the session level, but it does reveal [custom dimensions and metrics](https://support.google.com/analytics/answer/2709828?hl=en). This makes sending User ID parameters in a custom dimension the only way to collect User ID data at the session-level.

#### Step 1. Create a custom dimension in Google Analytics

1\. On the **Admin** page, find the **Custom definitions** block and click **Custom dimensions**:

![Admin - custom definitions](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-how-data-is-collected/02/public)

2\. Click **New custom dimension**:

![New custom dimension](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-how-data-is-collected/03/public)

3\. Add the dimension name, set the **Scope** to _User_, and click **Create**:

![custom dimension settings](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-how-data-is-collected/04/public)

#### Step 2. Add the custom dimension in OWOX BI

1\. In OWOX BI, go to the **Settings** tab in your Google Analytics → Google BigQuery pipeline.

2\. In the **Session data collection** block, make sure you have the **Based on Google Analytics** option selected, and click **Edit settings**:

![Google analytics settings data collection](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-how-data-is-collected/05/public)

3\. In the **Select User ID** drop-down list, select the custom dimension you have created:

![GA custom dimension user id session data](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-how-data-is-collected/06/public)

That’s it. Now you get User ID values in the created custom dimension.

## Collecting and updating the Google Analytics-based session data

> **Important:**  
> This article describes the deprecated session data collection method based on Google Analytics Core API. This method is still operational, but will not receive any updates in the future. Read the relevant info about collecting and updating session data based on the new OWOX BI algorithm [in this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#session-data-collection-based-on-the-owox-bi-algorithm).

### When will the data become available in Google BigQuery?

The first data table will be generated within **12 hours** after the end of the day in the time zone set for the Google Analytics view.

> **Please note:  
> **  
> The first session data table might be incomplete due to the processing [delay](https://support.google.com/analytics/answer/6015078#processing_time_and_status) in Google Analytics. A fully consistent session streaming table will become available in 48 hours.

The following data is updated daily for all generated session streaming tables:

-   Cost data (attributedAdCost) for the previous 90 days.
-   User data (userId) for the previous month.

## Data collection limitations

### User behavior data collection

OWOX BI algorithm does not have the data collection limitations as those Google Analytics has: no 10 million hits per month, 200,000 hits per user per day, or 500 hits per session limits. All your data will make it from the website to Google BigQuery tables.

OWOX BI collects to Google BigQuery the complete and accurate user behavior data directly from your website, doesn't depend on Google Analytics Core Reporting API, and calculates sessions based on raw non-sampled hit data.

### Hit size

OWOX BI processes hits with the size up to 16 KB. The hits exceeding this limit won't be processed and sent to BigQuery.

In Google Analytics, the maximum hit payload size [is 8 KB](https://developers.google.com/analytics/devguides/collection/protocol/v1/reference).

### Sending data via Measurement Protocol

When [sending data with Measurement Protocol](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/tracking-with-measurement-protocol.md) and collecting it via OWOX BI, you can avoid creating a separate session for the events extended in time. To do this you can specify the `&qt` (`queueTime`) parameter in your HTTP-request. This is a time period in milliseconds between the moment the hit happened and the moment it was sent.

The maximum value of the `&qt` parameter is 30 days.

While OWOX BI can process hits with the `&qt` parameter's value more than 4 hours, Google Analytics can't. Note this, since such hits will be collected to Google BigQuery via OWOX BI but won't be tracked by Google Analytics.

The hits will get to a hit data table in BigQuery regardless of the `&qt` parameter's value.

## Source/medium for direct hits in session streaming based on Google Analytics

In hit streaming tables, the traffic.source and traffic.medium fields contain values taken directly from the UTM tags in the pagePath ([&dr](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters?hl=en#dr) and [&dl](https://developers.google.com/analytics/devguides/collection/analyticsjs/field-reference?hl=en#location) parameters) for a given hit.

In the session tables based on Google Analytics, the data in the trafficSource.source and trafficSource.medium fields is based on the session data in Google Analytics.

Since the Last Non-Direct Click attribution model is applied by default in Google Analytics reports, direct traffic is ignored, and 100% of the conversion value is attributed to the last non-direct channel that a customer clicked through from before buying or converting. All Direct visits are ignored within 6 months after the non-direct visit.

  
Thus, it would be incorrect to compare referral data in the session streaming to the data in the hit streaming.

## Session data collection based on the OWOX BI algorithm

Collect raw, non-sampled session data from your website to Google BigQuery instead of using standard Google Analytics session data.

So, let's see how it works.

### Session formation

Sessions are being formed by the same [algorithm](https://support.google.com/analytics/answer/6205762?hl=en#flowchart) used by Google Analytics. However, they are being calculated by the OWOX BI algorithm based on the `clientId` and `timestamp` parameters from the _last_ user interaction.

### Session expiration triggers

A session is closed and a new session begins:

-   at the end of each day (12 AM) in the linked Google Analytics view timezone;
-   when a user gets to the website from another domain listed in the [referral exclusion list](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking.md#creating-a-referral-exclusion-list-for-session-data-collection);
-   after a predefined period of inactivity (the default timeout of 30 minutes can be changed in the OWOX BI interface);
-   when a user returns to your website via another ad that contains utm tags different from the first hit in the session.

### Filters and exceptions

-   Sessions will contain raw unfiltered data. That's why we recommend that you select an unfiltered Google Analytics view when setting up data collection to Google BigQuery.
-   By default, your domain is automatically added to the referral exclusion list: as the result, traffic from subdomains won't appear in the reports.

### User ID data collection

All User ID data is being sent in the **&uid** parameter — you don't need to create custom dimensions. Using the OWOX BI algorithm, you also can and update historical data on the collected User IDs. Read more in [Collecting and updating User ID data](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#collecting-and-updating-user-id-useremail-and-userphone-data).

### Identifying traffic sources

The OWOX BI algorithm attributes sessions to traffic sources by applying the Last Non-Direct Click attribution model, the same as in Google Analytics.

The Last Non-Direct model ignores all direct traffic and attributes sessions to the last non-direct traffic mediums: referral, cpc, organic, and others.

To track the actual traffic source, we have added the `trafficSource.isTrueDirect` field of your _owoxbi\_sessions_ table. The field indicates if the source of the session started as a direct site visit, or is it follows the session generated by an ad source. 

### Determining visitNumber

To determine the session number (_visitNumber_), OWOX BI counts all the sessions of a specific _clientId_ over the last six months from the session collection date. This can be expressed by the formula:  
**_visitNumber_ of a session = (total number of sessions of a specific _clientId_ over the last six months) + 1**

_Example_  
Let’s imagine we have the following statistics for a specific _clientId_:  
May 18 — one session  
May 19 — one session  
May 20 — one session  
November 16 — one session  
November 17 — two sessions

-   When collecting sessions for November 16, OWOX BI will count sessions of the _clientId_ from May 18 to November 16 (six months from the session collection date). According to the given statistics, the _clientId_ made 3 sessions before November 16. Therefore, the session on November 16 will be assigned the _visitNumber=4_.
-   When collecting sessions for November 17, OWOX BI will count sessions of the _clientId_ from May 19 to November 17 (six months from the session collection date). According to the given statistics, the _clientId_ made 3 sessions before November 17. And since on November 17 two more sessions were recorded, the first of them will be assigned _visitNumber=4_, the second — _visitNumber=5_.

### Google Ads auto-tagging

In the cases of Google Ads auto-tagging, the trafficSource. fields, providing information about the traffic source from which the session originated, are retrieved from AdWords data tables uploaded to Google BigQuery using the Google Ads API.

There are several ways to set up automatic uploads of Google Ads reporting data into Google BigQuery. For more information about how to collect Google Ads data in BigQuery, [read our article on it](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking/setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging.md).

> **Please note:**   
> Make sure you have configured automatic uploads of Google Ads data into Google BigQuery. Otherwise, the auto-tagging won't be available.

### Session calculation when sending data via Measurement Protocol

Hits sent via Measurement Protocol with the value of the _&qt_ parameter up to 30 days will retrospectively get to the session data table (_bi\_sessions_) for the corresponding date and will be assigned to the correct session.

The hits will get to a hit data table in BigQuery regardless of the _&qt_ parameter's value.

If the _&qt_ parameter is not specified and the hit doesn't fall into the closest session timeout for a specific user, then this hit will create a new session in the _"owoxbi\_sessions"_ table.

[Learn more about the _queueTime_ parameter](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/tracking-with-measurement-protocol.md)

> **Note**  
> Sending the _&qt_ parameter with a value of more than 4 hours will cause the hit to not get to Google Analytics. This will lead to data discrepancy: OWOX BI will get more data than Google Analytics.  
> Set the _&qt_ parameter value to more than 4 hours only if you need the events sent via Measurement Protocol to get to the sessions during which they've happened.

### Data collection and update times

The first session data table will be available within 48 hours after the data collection setup. The following tables will be formed within 12 hours after the end of the day according to your Google Analytics view time zone.

| **Type of data** | **When the data is updated in session data tables in Google BigQuery** |
| --- | --- |
| All session data computed based on hit data tables, except cost data (adCost, attributedAdCost) and Google Ads campaigns data with auto-tagging (gclid). | Daily update within 12 hours after the start of the next day. |
| Cost data (adCost, attributedAdCost). This includes all advertising cost data imported into Google Analytics. | Daily update of the session data table collected during the previous day:<br>\- once a day after 6 p.m. + historical data update during a preset actualization period. |
| Cost data (adCost, attributedAdCost). This includes all advertising cost data imported into Google BigQuery via the connected Ad Service → Google BigQuery pipelines. |  <br>Сhanges in cost data will be reflected in the _adCost_ and _attributed AdCost_ fields within 6 hours after this data is updated in the CostData\_ tables. |
| Google Ads data about campaigns with auto-tagging enabled (gclid values). | Two checking periods a day for the session data table collected during the previous day: after 6 a.m. and after 6 p.m.<br>During the checking periods, the Google Ads data is being checked every 15 minutes until two successive checks show the data in the source table and in the session table are synchronized.<br>[More about the Ads data uploading and updates in session data tables](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking/setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging.md). |

See the structure of the session data table in [this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/schema-for-session-data-tables-collected-with-the-owox-bi-algorithm-owoxbi-sessions.md).

## Collecting OWOX User ID

OWOX User ID is an additional user identifier available in the Google Analytics→Google BigQuery pipeline. With it, you can now track audience overlapping across domains without sending the Client ID parameter.

Google Analytics's [cross-domain tracking](https://support.google.com/analytics/answer/1034342?hl=en) lets you track audience overlapping only if the users move between websites using direct cross-links. The Google Analytics tracking code sends Client IDs in the URL parameters of the link.

Now, with OWOX User ID, you can track audience overlapping across any websites that have the OWOX BI pipelines set up, even if these websites aren't linked directly with cross-links.

Here are some examples of how you can use OWOX User ID:

-   Analyze how audiences overlap across domains in different Google Analytics→Google BigQuery pipelines
-   Have access to relevant anonymous user IDs in cases when Google Analytics cookies are replaced or deleted
-   Track customer journeys on all your websites using a single identifier

### How it works

When collecting user behavior data from your website in the Google Analytics→Google BigQuery pipeline and [session data based on OWOX BI algorithm](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#session-data-collection-based-on-the-owox-bi-algorithm), our service creates a custom cookie file `ouid` that contains a unique anonymous ID (the combination of the clientId and IP hashes) for each identified user.

In data tables, the ID values are stored in the `userOwoxId` field:

![OWOX\_User\_ID.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-how-data-is-collected/07/public)

> **Note:**  
> Collecting OWOX BI User ID is impossible when you collect [session data based on Google Analytics data](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#collecting-and-updating-the-google-analytics-based-session-data).

What else you should know about OWOX User ID:

-   These IDs are created by OWOX BI upon receiving the first hit event from a user and written to a third-party cookie;
-   The created cookie with an ID is used across domains and subdomains and is never replaced;
-   The expiration time for the cookies is 364 days. After that, the OWOX User ID file will be automatically deleted.

### Security

OWOX User ID does not compromise your data in any way. Please note that:

-   OWOX BI does not process, analyze, or otherwise use the data you collect;
-   Audience overlapping can be viewed by OWOX User ID only within your own pipelines;
-   It’s impossible to explore how audiences on your website overlap the audiences on the websites of other OWOX BI users.

### Google Consent Mode compliance

If you implemented consent mode in Google Tag Manager with [two settings for tags](https://support.google.com/tagmanager/answer/10718549?hl=en) **ad\_storage** and **analytics\_storage**, therefore OWOX BI algorithm properly processes user behavioral data in compliance with GDPR requirements. Let's look at two cases:

-   **The user has not given consent: analytics\_storage = denied’**  
    This means that in the incoming hit, the `gcs` parameter is not equal to 'G111' or 'G101'. In this case, OWOX BI does not set the `ouid` cookie on the user's browser.
-   **The user has given their consent: analytics\_storage = 'granted'**  
    In this case, the OWOX BI algorithm creates an `ouid` cookie and sets the lifetime to 364 days if it has not been created before. If this cookie has already been previously stored in the user's browser, then OWOX BI extends its lifetime and keeps the current value.
-   **The parameter `gcs` missing or has an invalid value**  
    The OWOX BI algorithm will process the `ouid` cookie according to the logic described for the analytics\_storage = 'granted' case.

> **Note:**  
> The Google Help Center [provides instructions](https://support.google.com/analytics/answer/9976101) on how to set up consent mode in Google Tag Manager.

### How to manage OWOX User ID collection

The OWOX User ID collection is active by default.

If you want to turn it off:

1\. Open the **Settings** tab on your Google Analytics→Google BigQuery pipeline page in OWOX BI Pipeline. On this tab, click the menu button in the upper right corner.

2\. Uncheck **Collect OWOX User ID**.

![OWOX\_User\_ID\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-how-data-is-collected/08/public)

### Restrictions

OWOX User ID won't be collected for the site visitors who have cross-domain tracking restricted in their browser. In the Safari browser version 11 and above, the "Prevent cross-site tracking" feature is active by default.

Without OWOX User ID for these users, you won't be able to get 100% accurate data on audience overlapping across your sites.

> If your statistics show that the same Client ID within a single domain has different OWOX User Identifiers, then read [this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/faq.md#why-does-the-same-client-id-within-a-single-domain-have-different-owox-user-ids) to find out the reasons for that.

## Differences between the session data collection algorithms

OWOX BI can collect session data using one of the two methods: the first one is based on Google Analytics API while the second or based on raw hit data with the OWOX BI own algorithm.

### Why the new algorithm?

Because we want you to get complete and accurate data on user behavior at your website. Here are the benefits of using the OWOX BI algorithm:

1.  The OWOX BI algorithm doesn’t depend on GA Core Reporting API and calculates sessions based on raw non-sampled hit data
2.  No interruptions of session tables collection caused by the GA Core Reporting API limits exceeding or no access to Google Analytics. No delays caused by the session table fields import from Google Analytics
3.  OWOX BI doesn't provide limits on data uploading while [Google Analytics does](https://developers.google.com/analytics/devguides/collection/analyticsjs/limits-quotas). _All_ your data will get into Google BigQuery tables

With the new algorithm, you also can:

-   Track if a direct click is truly direct, not fetched from a paid source. With the OWOX BI algorithm, you can do it thanks to the `trafficSource.isTrueDirect` field and attribute site visits by two models: Last Non-Direct Click and Last Click
-   Аnalyze how audiences from different websites overlap by collecting an additional anonymous identifier, OWOX User ID

### When it’s time to move to the OWOX BI algorithm

-   The number of sessions on a website is close to or more than [500К](https://support.google.com/analytics/answer/2637192?hl=en) for the selected date range (works for Analytics Standard). 
-   You often stumble upon the data sampling in Google Analytics
-   You often face the 500 hits limit per session
-   You need to track the true source of a visit using the `trafficSource.isTrueDirect` field which is available only in BigQuery Export for Google Analytics 360
-   You want to unite the audiences across domains using additional user identifier [OWOX User ID](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#collecting-owox-user-id) and analyze how these audiences overlap

### What is the difference in a table data structure

Sessions tables created based on the OWOX BI algorithm have **the same structure** as the tables collected based on Google Analytics data. There are only a few differences in some fields and their values:  

1.  The `totals.*` field had prevously contained the total hit value from Google Analytics. Now this field contains has the duplicated value from the `totalsStreaming.*` field showing the total number of hits collected by OWOX BI.
2.  The tables contain the `customDimensions`, `customMetrics`, and `customGroups` fields. However, they all have the hit-level scope. It’ll be possible to define the scope of custom dimensions in further updates. 
3.  The tables contain the field `isTrueDirect` that helps you understand if a visit is direct (then the value is _true_), or its source/medium is fetched from a paid source.
4.  The tables contain the field `userOwoxId`.

### The settings needed to set up the OWOX BI algorithm

-   If you already have set up session data collection based on Google Analytics, update the tracking code once you've [set up the session data collection](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking.md#set-up-session-data-collection-based-on-owox-bi-algorithm). 
-   If using the Google Ads auto-tagging, first turn on the raw reports upload from Google Ads to BigQuery using the [Google Data Transfer](https://cloud.google.com/bigquery/docs/adwords-transfer#google_adwords_report_transformation) native integration to get the auto tag data (with gclid), then, in the session data collection in OWOX BI, show the path to the BigQuery dataset containing this reports. Skip this if using the manual tagging with the utm tags.
-   The data in the `user.id` field is collected based on the userId (&uid) parameter not using the custom dimension. If on your website you don't have the tracking and collecting of &uid, set it up using the [standard method](https://support.google.com/analytics/answer/3123666?hl=en).

### Differences in data collection

| **Stage** | **Based on Google Analytics API** | **Based on OWOX BI algorithm** |
| --- | --- | --- |
| Sessions calculation | The SessionID values, traffic source, geo, and device type data are uploaded using Google Analytics Core API. The beginning and the termination of sessions is defined by GA logic.<br>Once we get this session data, we add to session tables raw hit data from the "streaming" tables. | Sessions are formed in Google BigQuery based on raw data collected with the OWOX BI algorithm.<br>The triggers if the session beginning and termination are the same as in Google Analytics. |
| Sessions calculation when sending data via Measurement Protocol | If the value of the [&qt parameter](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#qt) sent via Measurement Protocol is greater than 4 hours, the hit will disappear and won't get to any session.<br>If the `&qt` parameter is sent with the empty value, a new separate session will automatically be created for this hit. | Hits sent via Measurement Protocol with the value of the `&qt` parameter up to 30 days will retrospectively get to the session data table ("bi\_sessions") for the corresponding date and will be assigned to the correct session.<br>The hits will get to a hit data table ("streaming") not depending on the `&qt` parameter. |
| Traffic source determination | The session source is defined according to the Last Non-Direct click model. This means all direct visits get the source assigned from the last non-direct visit for the last 6 months.<br>There’s no possibility to define whether a visit was a direct one. | Session sources are attributed to traffic sources by applying the Last Non-Direct Click attribution model, the same as in Google Analytics.<br>To track the actual traffic source, we have added values to the `trafficSource.isTrueDirect` field. It indicates if the source of the session started as a direct site visit, or is it follows the session generated by an ad source. |
| Definition of the utm values for Google Ads auto-tagging (gclid) | Defined by the Google Analytics API which has the native integration with Google Ads. | You need reports with raw Google Ads data in BigQuery. They can be easily set up with the [Google Data Transfer](https://cloud.google.com/bigquery/docs/adwords-transfer#google_adwords_report_transformation) native integration. |
| Table structure | Tables are divided by days according to the Google Analytics property timezone.<br>Every session is a different string with nested fields containing raw hit data. | No differences in the table structure. |
| The start time of session data collection for the previous day | At 5 a.m., since data become available in Google Analytics Core API at 4 a.m. according to the Google Analytics property timezone. | At 1 a.m. according to the Google Analytics property timezone. |
| Data filtration | Filtered session data from Google Analytics is used according to the current property filters. | Session data is not filtered in any way. |

## Collecting and updating data about Google Ads campaigns with auto-tagging in session data tables

If you are using [auto-tagging](https://support.google.com/google-ads/answer/1752125?co=ADWORDS.IsAWNCustomer%3Dfalse&hl=en) in your Google Ads campaigns, the page URLs contain only the gclid parameter, which stores campaign names, keywords, and other parameters.

In order to automatically retrieve these data from the gclid parameter and upload them to the [session data tables “owoxbi\_sessions”](https://github.com/OWOX/streaming-plugin/tree/main/docs/hit-based/schemas), you need to set up Google Ads report data collection to Google BigQuery, then connect the resulting dataset to your user behavior data collection pipeline.

Read how to set up report data collection and connect the data to your pipeline [in this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking/setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging.md).

As a result, the “owoxbi\_sessions” tables get values for these fields:

-   `trafficSource.source: google`
-   `trafficSource.medium: cpc`
-   `trafficSource.campaign`
-   `trafficSource.keyword`
-   `trafficSource.adGroup`
-   `trafficSource.keywordMatchType`

> The values _google/cpc_ corresponds exactly to the [Google Analytics processing flow](https://support.google.com/analytics/answer/6205762?hl=en#flowchart).

#### How does OWOX BI update the Google Ads campaign data in the session data tables?

To keep your data consistent and up to date, OWOX BI updates the ad campaigns data in the “owoxbi\_sessions” at 7:00 AM, 9:00 AM, 12:00 PM and 15:00 PM daily. Please note that data synchronization is scheduled according to the timezone of your Google Analytics view.

#### Why my Google Ads campaign data in the “owoxbi\_sessions” table doesn’t match data in Google Analytics or simply missing?

If your Google Ads campaign data collection is set up correctly, some discrepancies are still possible. Such situations occur when data in the source table has been updated after the last session data table update has ended.

In this case, data **will not be lost**. It will get to the “owoxbi\_sessions” table during the next update.

> **Note**  
> If you use both auto-tagging and manual tagging in your Google Ads campaigns, read [this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/faq.md#which-values-will-be-recorded-to-the-hit-and-session-data-tables-when-gclid-auto-tagging-and-manual-utm-tagging-data-overlap-in-google-ads).

## The definitions of device categories in the "owoxbi_sessions" tables

OWOX BI's Google Analytics → Google BigQuery user behavior data pipeline writes user device category info in the field `device.deviceCategory` of the "owoxbi\_sessions" tables.

The field gets one of 17 possible values. All these values and what each of them means is described in the table below:

| **Field value** |  **Meaning** |
| --- | --- |
| Desktop | A desktop PC or a laptop |
| Anonymized | Any device with a user agent altered by anonymization software  |
| Unknown | A case when a user agent that contains no information about the device |
| Mobile | A non-specified mobile device (it can be an eReader, a tablet, a phone, or a smartwatch) |
| Tablet | A mobile device with a screen larger than 7" |
| Phone | A mobile device with a screen smaller than 7" |
| Watch | A mobile device with a screen smaller than 2". It can be an additional screen for a phone/tablet type device. |
| Virtual Reality | A device with VR capabilities |
| eReader | A device with an eInk screen |
| Set-top box | A device that can be connected to a TV-sized screen |
| TV | Similar to Set-top box, but built into a TV |
| Game Console | Gaming systems like PlayStation and Xbox |
| Handheld Game Console | Portable game systems like the 3DS |
| Robot | Robots that visit the site |
| Robot Mobile | Robots that visit the site and seen as a Mobile visitor |
| Spy | Fake robots |
| Hacker | Defined as a Hacker if scripting is detected in the user agent string |

## How hits-based website streaming works with Google Consent Mode

In this article, we describe how the OWOX BI algorithm sends user behavior data into hit tables ([data schema](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/data-schema-for-hit-tables-streaming.md)) according to the Google Consent Mode.

Previously, Google implemented a consent mode in Google Tag Manager [with two new settings for tags](https://support.google.com/tagmanager/answer/10718549?hl=en): **ad\_storage** (for advertising tools) and **analytics\_storage** (for Google Analytics). Depending on the values (“denied” or “granted”), the OWOX BI algorithm properly processes user behavioral data in compliance with GDPR requirements.

### Recording the users’ consent mode status in the 'privacy\_info' field

> **Important!**  
> The 'privacy\_info' field has been retrospectively added to the table schema since January 1, 2022, with the value 'null'. And from October 4, 2022, this field is filled with actual data that the OWOX BI access point receives.

The OWOX BI algorithm writes a value (Yes, No or Null) for the '**privacy\_info.ads\_storage**', '**privacy\_info.analytics\_storage**', '**privacy\_info.uses\_transient\_token**' fields at the level of each hit.

| Google Consent Mode Parameter | GTM-tags consent status | Values in OWOX BI hit tables | Description |
| --- | --- | --- | --- |
| G100 | ad\_storage = denied;<br>analytics\_storage = denied; | privacy\_info.ads\_storage = No;<br>privacy\_info.analytics\_storage = No;<br>privacy\_info.uses\_transient\_token = null | No consent, neither for marketing nor for statistics cookies |
| G101 | ad\_storage = denied;<br>analytics\_storage = granted | privacy\_info.ads\_storage = No;<br>privacy\_info.analytics\_storage = Yes;<br>privacy\_info.uses\_transient\_token = null | Consent to statistical, but not to marketing cookies |
| G110 | ad\_storage = granted;<br>analytics\_storage = denied | privacy\_info.ads\_storage = Yes;<br>privacy\_info.analytics\_storage = No;<br>privacy\_info.uses\_transient\_token = null | Consent to marketing, but not to statistics cookies |
| G111 | ad\_storage = granted;<br>analytics\_storage = granted | privacy\_info.ads\_storage = Yes;<br>privacy\_info.analytics\_storage = Yes;<br>privacy\_info.uses\_transient\_token = null | Consent to both marketing and statistics cookies |
| Parameter missing or has any other value | ad\_storage = unset;<br>analytics\_storage = unset | privacy\_info.ads\_storage = null;<br>privacy\_info.analytics\_storage = null;<br>privacy\_info.uses\_transient\_token = null | Unknown consent |

With the raw data collected by OWOX BI, you will always be sure that you comply with the laws and regulations regarding the processing of user data.

### Processing of OWOX User ID according to Google Consent Mode

The actual logic behind creating the 'ouid' cookie and updating its lifetime is described [in the OWOX User ID article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#collecting-owox-user-id).
