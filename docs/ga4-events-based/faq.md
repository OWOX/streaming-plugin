# FAQ

- [The reasons of data discrepancies between OWOX Streaming and GA4 Big Query Export](#the-reasons-of-data-discrepancies-between-owox-streaming-and-ga4-big-query-export)
- [How GA4 event-based web streaming works with Google Consent Mode](#how-ga4-event-based-web-streaming-works-with-google-consent-mode)
- [List of event parameters that OWOX BI algorithm adds in the 'event_params' field](#list-of-event-parameters-that-owox-bi-algorithm-adds-in-the-eventparams-field)
- [How OWOX BI web streaming processes and stores Events with multiple sources](#how-owox-bi-web-streaming-processes-and-stores-events-with-multiple-sources)
- [How to get sequence number of the event?](#how-to-get-sequence-number-of-the-event)
- [Mastering clustered tables in Event-based streaming](#mastering-clustered-tables-in-event-based-streaming)
- [How to Set Up Multiple OWOX BI Streamings for Accurate Data Consumption](#how-to-set-up-multiple-owox-bi-streamings-for-accurate-data-consumption)
- [What does the at-least-once delivery principle mean while collecting events?](#what-does-the-at-least-once-delivery-principle-mean-while-collecting-events)
- [Logic of Client IP collection](#logic-of-client-ip-collection)
- [Limits for incoming GA4 events in OWOX BI Streaming](#limits-for-incoming-ga4-events-in-owox-bi-streaming)
- [List of potential warnings and blocking reasons](#list-of-potential-warnings-and-blocking-reasons)
- [How to change settings in created streaming](#how-to-change-settings-in-created-streaming)

## The reasons of data discrepancies between OWOX Streaming and GA4 Big Query Export

### Potential sources of data discrepancies  

-   Settings in GTM for OWOX Streaming and GA4 (tag activation rules should be identical). Also, make sure that all custom tags used for event tracking in GA4 are duplicated in OWOX Streaming.
-   Settings related to Content Security Policy on the client side.
-   Configuration of the Referral Exclusion List.
-   Custom settings that can be in GA4 and may have been forgotten during migration to OWOX Data Streams - filters, time settings, etc. 
-   Using the Measurement Protocol: if you are sending data to GA4 via MP, it should also send data to OWOX Streaming, at our access point.
-   Limit on the amount of data in GA4 BigQuery Export (1 million events per day according to the documentation). So far, this is still not working.
-   Changing the time zone. When exporting, data is compiled for a resource over 24 hours, taking into account the time zone (documentation).
-   Using Consent Mode. If a user accepts the Consent Mode setting, the event will be sent in OWOX Streaming, but without data on user\_pseudo\_id, ga\_session\_id, IP, owox.user\_id. In the case of using GA4 and the available user\_id, the event will be sent to GA4 with the user\_id. Without user\_id GA4 will collect non-personal information. 

### List of reasons for discrepancies between OWOX Streaming/Sessions & Interface.

-   Application of sampling in the interface.
-   Metrics for sessions/microconversions/conversions by Source/Medium.
-   [Number of events](https://support.google.com/analytics/answer/11202874?sjid=11452558437667534953-EU).
-   Filters at the UI level.
-   Use of Consent Mode. For example, if data processing consent is declined, events will be sent to the streaming (we will remove client\_id values at the data collection level) without clientId. However, in the GA4 interface, this data will not be present. 
-   When using Google Signals, data is not displayed for a small sample, and there is no data in the interface at all. Also, if Google Signals are enabled in Google Analytics 4, [user deduplication occurs.](https://developers.google.com/analytics/blog/2023/bigquery-vs-ui?hl=en#google_signals)  
      
      
    

| Where could be discrepancies | How to minimize | Why the diff is normal |
| --- | --- | --- |
| -   Pageviews
-   Events
-   Sessions | -   [OWOX GA4 streaming tags](https://tagmanager.google.com/#/home) are set up on all of the same pages that GA4.
-   OWOX GA4 streaming tags have same triggers that GA4.
-   OWOX GA4 streaming fires at the [same time](https://support.google.com/tagmanager/answer/6238868?sjid=1466158157176502729-EU) with GA4.
-   OWOX GA4 [sessionization](https://github.com/OWOX/transformations-plugin/blob/main/docs/templates/merge-events-web-streaming.md#variables-list-for-transformation-template-merge-events-into-sessions) and GA4 have identical [referral exclusion list](https://support.google.com/analytics/answer/10327750?hl=en).
-   OWOX GA4 streaming and GA4 have identical cross domain tracking.
-   OWOX GA4 [sessionization](https://github.com/OWOX/transformations-plugin/blob/main/docs/templates/merge-events-web-streaming.md#variables-list-for-transformation-template-merge-events-into-sessions) and GA4 have identical [session timeout](https://support.google.com/analytics/answer/9191807?hl=en#zippy=%2Cadjust-session-timeout).
-   OWOX GA4 streaming and GA4 uses the same identifier of the client
-   OWOX GA4 [sessionization](https://github.com/OWOX/transformations-plugin/blob/main/docs/templates/merge-events-web-streaming.md#variables-list-for-transformation-template-merge-events-into-sessions) and GA4 have the same campaign timeout.
-   There is no redirects on site, that could cause troubles with UTM parameters.
-   No Filters in the UI
-   Renaming of the campaign in Google Ads. We recommend not to rename old campaigns, but to create new ones: for several days (gclid window), you will see old names in sessions, if you rename the old campaign.  | -   The new session starts if there was the first event in the day.
-   New session starts if there is more then 30 minutes between two events. You could set up this parameter in the sessionization template.
-   New session starts if there is event named “session\_start” and there wasn’t no events before this event.
-   New session starts if there is was more than 10 seconds between event named “session\_start” and previous event.
-   New session starts if there is was change in the UTM markup.
-   DataTransfer has no date, that could be connected with a click, so we can not match data with the new campaign, and taking the first one in the list.  |
| -   events | -   Settings in GTM for OWOX Streaming and GA4 (tag activation rules must be the same). It is also necessary to ensure that all custom tags used to transmit events in GA4 are also duplicated in OWOX Streaming.
<br>-   Check that the settings related to the [Content Security Policy](https://www.owox.com/changelog/) on the client side do not block the sending of hits to OWOX Streaming.
<br>-   Referral exclusion list settings in GA4 that are in the main stream should be duplicated in Data Stream OWOX
<br>-   [Filter settings](https://support.google.com/analytics/answer/13296761?hl=en) in GA4 that are in the main stream must be duplicated in Data Stream OWOX.
<br>-   If using the [Measurement Protocol for GA4](https://support.google.com/analytics/answer/9900444?hl=en), you also need to [transfer data to OWOX Streaming](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-your-server-using-the-measurement-protocol) to the appropriate access point.
-   Follow the limit on the amount of data in [GA4 BigQuery Export](https://support.google.com/analytics/answer/9823238?hl=en#limits) (1 million events per day according to the documentation). Consider the export settings (which data streams are specified in the settings, as well as whether event filters are applied)
-   Check the [time zone settings](https://support.google.com/analytics/answer/1010249?hl=en) in GA4 and OWOX Streaming. When exporting, a 24-hour resource download is created, taking into account the time zone.
-   Consider [Consent Mode settings](https://support.google.com/tagmanager/answer/10718549?hl=en) for GA4 and OWOX Streaming. | -   Delay in data collection, [comparisons should be made on data older than 72 hours](https://developers.google.com/analytics/blog/2023/bigquery-vs-ui?hl=ru#data_collection_delay).
-   [Limit](https://support.google.com/analytics/answer/11202874?sjid=11452558437667534953-EU) on the amount of data in GA4 BigQuery Export. ( )
-   Consent Mode settings for GA4 and OWOX Streaming. If the user accepts the Consent Mode setting, the event will be sent in OWOX Streaming, but without data on user IDs, the event will not be sent in GA4. |

## How GA4 event-based web streaming works with Google Consent Mode

In this article, we describe how the OWOX BI algorithm sends event-based user behavior data into “events\_intraday\_” tables ([data schema](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/schemas/data-schema-for-events-intraday-table.md)) according to the Google Consent Mode.

Previously, Google implemented a consent mode in Google Tag Manager [with two new settings for tags](https://support.google.com/tagmanager/answer/10718549?hl=en): **ad\_storage** (for advertising tools) and **analytics\_storage** (for Google Analytics). Depending on the values (“denied” or “granted”), the OWOX BI algorithm properly processes user behavioral data in compliance with GDPR requirements.

### Recording the users’ consent mode status in the 'privacy\_info' field

The OWOX BI algorithm writes a value (Yes, No or Null) for the '**privacy\_info.ads\_storage**', '**privacy\_info.analytics\_storage**', '**privacy\_info.uses\_transient\_token**' fields at the level of each event.

| Google Consent Mode Parameter | GTM-tags consent status | Values in OWOX BI event tables | Description |
| --- | --- | --- | --- |
| G100 | ad\_storage = denied;<br>analytics\_storage = denied; | privacy\_info.ads\_storage = No;<br>privacy\_info.analytics\_storage = No;<br>privacy\_info.uses\_transient\_token = null | No consent, neither for marketing nor for statistics cookies |
| G101 | ad\_storage = denied;<br>analytics\_storage = granted | privacy\_info.ads\_storage = No;<br>privacy\_info.analytics\_storage = Yes;<br>privacy\_info.uses\_transient\_token = null | Consent to statistical, but not to marketing cookies |
| G110 | ad\_storage = granted;<br>analytics\_storage = denied | privacy\_info.ads\_storage = Yes;<br>privacy\_info.analytics\_storage = No;<br>privacy\_info.uses\_transient\_token = null | Consent to marketing, but not to statistics cookies |
| G111 | ad\_storage = granted;<br>analytics\_storage = granted | privacy\_info.ads\_storage = Yes;<br>privacy\_info.analytics\_storage = Yes;<br>privacy\_info.uses\_transient\_token = null | Consent to both marketing and statistics cookies |
| Parameter missing or has any other value | ad\_storage = unset;<br>analytics\_storage = unset | privacy\_info.ads\_storage = null;<br>privacy\_info.analytics\_storage = null;<br>privacy\_info.uses\_transient\_token = null | Unknown consent |

With the raw data collected by OWOX BI, you will always be sure that you comply with the laws and regulations regarding the processing of user data.

### PII data processing according to Google Consent Mode

> **Important!**  
> During data streaming, OWOX BI does not cut off users' personal data in the fields like custom '`event_params`' and others. Only you manage on your side (including Google Tag Manager, gtag.js, or Measurement protocol settings) which user data will be sent through the OWOX BI access point.

Let's look at cases below:

-   **The user has not given consent: analytics\_storage = denied’**  
    In this case, OWOX BI cuts and does not store the following personal data in Google BigQuery:
    -   `user_pseudo_id` (clientId);
    -   `ga_session_id`;
    -   `ip`;
    -   `owox.user_id`.
-   **The user has given their consent: analytics\_storage = 'granted'**  
    In this case, the OWOX BI algorithm stores all data in full in Google BigQuery.
-   **The parameter `gcs` missing or has an invalid value**  
    The OWOX BI algorithm will process the `ouid` cookie according to the logic described for the analytics\_storage = 'granted' case.

> **Note:**  
> The Google Help Center [provides instructions](https://support.google.com/analytics/answer/9976101) on how to set up consent mode in Google Tag Manager.

## List of event parameters that OWOX BI algorithm adds in the 'event_params' field

Events can contain a list of parameters, which we can split into two groups:

1.  Parameters that are passed strictly with the key name '**&ep.\***';
2.  Auto-created parameters.

In this article, you can see the full list of parameters that are automatically added by the OWOX BI algorithm in the '`event_params`' field.

> **Important**  
> To prevent losing your custom '`event_params`', please avoid using the reserved names from the table below when sending these events with the key name '`&ep.*`'.  
>   
> **Starting June 10, 2024,** only values determined by the OWOX BI algorithm will be recorded in the '`event_params`' field. Client values will not be recorded in the '`event_params`', but they can always be found in the '`debug_info.raw_request`' field.  
>   
> For [sending data using the Measurement Protocol](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-your-server-using-the-measurement-protocol), there are no such restrictions.

| Name (event\_params.key) | Description |
| --- | --- |
| `source` | Name of the traffic source of the event. If the event has multiple sources, then the data is stored in additional fields [following this logic](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/faq.md#how-owox-bi-web-streaming-processes-and-stores-events-with-multiple-sources). |
| `medium` | Name of the medium (organic, CPC, email, etc.) |
| `campaign` | Name of the marketing campaign |
| `content` | Value of the utm\_content campaign tracking parameter |
| `term` | Value of the utm\_term campaign tracking parameter (keyword) |
| `campaign_id` | Identifier of the marketing campaign |
| `page_referrer` | The full referring URL |
| `page_location` | A page on the website specified by the path |
| `page_title` | The page's title |
| `gclid` | Value of the Google Click Identifier ([GCLID](https://support.google.com/google-ads/answer/9744275?hl=en)) |
| `dclid` | Value of Display Click Identifier |
| `msclkid` | Value of Microsoft Click Identifier |
| `fbclid` | Facebook Click Identifier used by Facebook for tracking |
| `screen_resolution` | Resolution of users' screens |
| `ip` | IP address of a user device (compatible with [Consent Mode](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/faq.md#how-ga4-event-based-web-streaming-works-with-google-consent-mode)) |
| `wbraid` | Used for web-to-app measurement (for iOS 14+). [More details](https://www.owox.com/changelog/) |
| `gbraid` | Used for app-to-app measurement (for iOS 14+). [More details](https://www.owox.com/changelog/) |
| `measurement_id` | GA4 stream which was used to send an event. [More details](https://www.owox.com/changelog/) |

Please note, that the '`event_params`' field could have additional parameters with the source/medium info on the Event level. [Read more in this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/faq.md#how-owox-bi-web-streaming-processes-and-stores-events-with-multiple-sources).

## How OWOX BI web streaming processes and stores Events with multiple sources

In this article, we delve into the intricacies of storing data on sources at the event level and explore the boundless possibilities for customization during session data collection.

As we collect raw user behavior data from websites, we encounter scenarios where a single event may contain information from multiple sources, revealing the origin of the user. For instance:

-   From request parameters: `source1`/`medium1`/`camp1`
-   From page location: `source1`/`medium1`/`camp4`
-   From document referrer: `source2`/`medium2`/`keyword5`

Ensuring accurate data for reports with session cost attribution necessitates the precise determination of values for the `source` and `medium` fields at the session level. This entails meticulous processing of data for each event comprising a session. As the priority of sources in the list may vary depending on business objectives, the processing logic for events with multiple source information must cater to the needs of all OWOX BI clients.

After extensive consideration, we proudly present a powerful solution that empowers you to effectively overcome any business challenges.

### Comprehensive data storage at the Event level

1.1. We embrace a comprehensive approach by storing full data at the level of each event. Parameters like `source`, `medium`, `campaign`, `content`, `term`, and `campaign_id` in the `event_params` field are diligently filled using GTM URL variables ('cs' as a source, 'cm' as a medium, 'cn' as a campaign, etc.).

1.2. Parameters such as `gclid`/`dclid`/`msclkid` found in the URL are stored in event\_params, but in separate fields. Crucially, default `source`/`medium` values remain unaltered in this scenario.

1.3. To provide even more insights, we record additional parameters in `event_params` based on their origin:

-   From request parameters: `rp_source`, `rp_medium`, ...
-   From page location: `pl_source`, `pl_medium`, `pl_campaign`, ...
-   From document referrer: `ref_source`, `ref_medium`, `ref_keyword`, ...

> **Note:**  
> OWOX Streaming for GA4 will furnish you with comprehensive data on the sources from which users originate; however, native data export from Google Analytics 4 to BigQuery will not include this information.

### Customization in Transformations

With this enriched data at the event level, marketers gain unparalleled flexibility in customizing their analytics. The ["Merge Events into Sessions" template](https://github.com/OWOX/transformations-plugin/blob/main/docs/templates/merge-events-web-streaming.md), designed to gather sessions from events, empowers users to harness specific source data tailored to their unique business requirements.

Simultaneously, data on other sources remains intact, offering clear insights into why a particular source was selected. All historical data is preserved, allowing seamless modifications at the session level when needed.

## How to get sequence number of the event?

Use SQL-code below to get sequence number of event:

```

SELECT
  event_name,
  SPLIT(owox.event_id,'/')[ORDINAL(1)] as event_id,
  SPLIT(owox.event_id,'/')[ORDINAL(2)] as part,
  SPLIT(owox.event_id,'/')[SAFE_ORDINAL(3)] as auto_event
FROM
  `{{project}}.{{dataset}}.events_intraday_{{date}}`
```

## Mastering clustered tables in Event-based streaming

Clustered tables stand as a fundamental element in optimizing data processing within Google BigQuery, playing a pivotal role in enhancing query performance and cost efficiency specifically when querying OWOX BI's Event-based Streaming data. This article serves as a comprehensive guide to unravel the intricacies of clustered tables, addressing key questions to empower users with a nuanced understanding.

### What are clustered tables in Google BigQuery?

Clustered tables represent a sophisticated feature within BigQuery, enabling users to define a column sort order using clustered columns. This mechanism significantly enhances query performance and cost-effectiveness. By sorting storage blocks based on clustered columns, queries can scan only relevant blocks, reducing the need to scan the entire table or table partition. For a deeper dive, you can just [read the official documentation](https://cloud.google.com/bigquery/docs/clustered-tables).

### What is the clustering field for the 'events\_intraday\_YYYYMMDD' table?

The clustering is configured based on the `event_name` field.

### From what date are clustered tables 'events\_intraday\_YYYYMMDD' created?

As of November 1, 2023, all 'events\_intraday\_YYYYMMDD' tables are automatically created with clustering.

### Do tables created before November 1, 2023, have clustering based on the 'event\_name' field?

No, tables created before this date do not have clustering. The new functionality does not impact previously created tables and their data.

### Can I query data from both clustered and non-clustered tables in a single SQL query?

Yes, it's possible. However, the true benefits of clustered tables are realized when queries exclusively use clustered tables.

### Can the clustering field be changed for the 'events\_intraday\_YYYYMMDD' tables?

Absolutely. For any customisation in your Event-based Streaming, reach out to our support team at [bi@owox.com](mailto:bi@owox.com).

### Do Transformation templates utilize queries to 'events\_intraday\_YYYYMMDD' tables that already have clustering?

All Transformation templates utilizing data from 'events\_intraday\_YYYYMMDD' tables function seamlessly. No adjustments are needed in the template code.

### Is there a reduction in storage space for clustered tables in BigQuery?

No, the act of clustering itself does not affect the overall size of the tables. However, clustering is instrumental in optimizing data processing costs within clustered tables, assuming that queries are composed accurately.

### How to write queries for clustered tables?

Explore examples and best practices for querying clustered tables [in the official BigQuery documentation](https://cloud.google.com/bigquery/docs/querying-clustered-tables).

## How to Set Up Multiple OWOX BI Streamings for Accurate Data Consumption

### Introduction

Proper configuration is essential when using multiple Streamings in OWOX BI to ensure accurate data consumption and avoid unexpected costs. This article provides a step-by-step guide on setting up multiple Streamings to prevent duplicate data entries and ensure precise billing.

### Understanding the Challenge

Setting up multiple Streamings that write to the same dataset can lead to inflated data consumption calculations. Here's why:

1.  **Independent Calculations:** OWOX BI [calculates data consumption separately for each Streaming instance](https://www.owox.com/docs/getting-started/consumption-units/).
2.  **Data Duplication:** Writing to the same dataset causes overlapping data entries, leading to duplicate counts.

For instance, if Streaming #1 and Streaming #2 each write 1,000 unique `owox.user_id` entries daily to the same dataset, OWOX BI will count these entries twice, resulting in an inflated consumption total of 4,000 instead of 2,000.

### Steps to Configure Multiple Streamings Correctly

To ensure accurate data consumption calculations, follow these steps to configure each Streaming with a unique BigQuery dataset:

1.  **Create and Configure Datasets During Streaming Setup:**
    -   When [setting up a new Streaming instance in OWOX BI](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/setup.md#how-to-set-up-ga4-event-based-web-streaming-client-side--server-side), you can **create a unique dataset** directly within the setup interface.
    -   Ensure the dataset you select or create does not already contain `events_intraday_*` tables to avoid any data overlap.
    -   For instance, assign each Streaming to its corresponding unique dataset, such as `Streaming_dataset1`, `Streaming_dataset2`, etc., making sure they are empty of any `events_intraday_*` tables before linking.
2.  **Verify Configuration:**
    -   Double-check that each Streaming is correctly configured to its unique dataset.
    -   Monitor data entries to ensure no duplication occurs across datasets.

### Benefits of Using Unique Datasets

-   **Accurate Data Consumption:** Each Streaming's data is calculated independently, preventing inflated consumption totals.
-   **Transparent Billing:** Avoid unexpected costs by ensuring accurate data tracking and billing.
-   **Efficient Data Management:** Unique datasets help organize and manage data more effectively.

## What does the at-least-once delivery principle mean while collecting events?

OWOX BI uses the at-least-once delivery concept to ensure collecting **all events** you send to our endpoint. It means in some cases it may be duplicates of events with the same _'owox.event\_id'_. Typically, the share of these events is less than 0.1%.

### Best effort de-duplication

Thus, if you operate with data in tables like 'events\_intraday\_YYYYMMDD', we recommend adding a specific condition to your SQL query to exclude duplicates.

Example:

```sql
WITH unique_events_intraday_table AS (
  SELECT
    * EXCEPT(row_number)
  FROM (
    SELECT
      *,
      ROW_NUMBER() OVER (PARTITION BY owox.event_id) row_number
    FROM
      EVENTS_INTRADAY_TABLE
  )
  WHERE
    row_number = 1
)
SELECT
  *
FROM
  unique_events_intraday_table
```

## Logic of Client IP collection

At OWOX BI, we ensure the accuracy and reliability of your website visitor data through our refined method of Client IP collection.

### Current methodology

Regardless of the network configuration your website visitors are coming through, we consistently collect the **Client IP from the first entry in the 'x-forwarded-for' list**. This approach is applied uniformly in both scenarios:

-   **Without a Proxy Server:** When visitors connect directly to your site, the 'x-forwarded-for' header may not be present or only contain a single IP address. In such cases, this IP is considered the Client IP.
-   **With a Proxy Server:** In environments where visitors are behind a proxy (or multiple proxies), the 'x-forwarded-for' header lists the originating IP address(es) of the client connecting to the proxy, which then forwards the request to your site. By capturing the **first** entry in this list, we ensure we're identifying the original visitor's IP address, maintaining the integrity of your Geo-Data.

## Limits for incoming GA4 events in OWOX BI Streaming

There is only one **limit for incoming requests = 130kB**. One request could include any number of events with any number of parameters with any number of symbols.

## List of potential warnings and blocking reasons

This article will explore situations where OWOX BI GA4 Streaming might fail to record your data in BigQuery and reasons for potential blocks. We'll provide specific actions to rectify each issue and ensure proper data collection.

### Warnings

#### No access to BigQuery datasets for OWOX BI service account

| Reason | Consequence | Actions required |
| --- | --- | --- |
| The OWOX BI service account currently lacks access to your Google BigQuery datasets. OWOX BI is unable to collect data into your BigQuery dataset. | Data loss | -   Follow [these instructions](https://www.owox.com/docs/storages/google-bigquery/) to grant access to this dataset for the OWOX BI service account.
-   Wait 20-30 minutes for the changes to take effect.
-   Ensure that you no longer experience data loss. |

#### No billing account in GCP

| Reason | Consequence | Actions required |
| --- | --- | --- |
| Your current Google Cloud project has no billing account. OWOX BI is unable to collect data into your BigQuery dataset. | Data loss | -   Go to the [Google Cloud Billing page](https://console.cloud.google.com/billing/linkedaccount) as soon as possible, and link your current Project with an active billing account to access the full set of services and increased usage limits.
-   Wait 20-30 minutes for the changes to take effect.
-   Ensure that you no longer experience data loss. |

#### Free tier billing in GCP

| Reason | Consequence | Actions required |
| --- | --- | --- |
| Your current Google Cloud project uses Free tier billing. OWOX BI is unable to collect data into your BigQuery dataset. | Data loss | -   Go to the [Billing Accounts page](https://console.cloud.google.com/billing) as soon as possible, and choose a paid billing account to access the full set of services and increased usage limits.
-   Wait 20-30 minutes for the changes to take effect.
-   Ensure that you no longer experience data loss. |

#### BigQuery dataset not found

| Reason | Consequence | Actions required |
| --- | --- | --- |
| OWOX BI could not find the BigQuery dataset; it either does not exist, or there is no access to it. | Data loss | -   Go to the [Google Cloud Console](https://console.cloud.google.com/bigquery) as soon as possible, and make sure that your BigQuery dataset exists. Then share it with the OWOX BI service account by following [these instructions](https://www.owox.com/docs/storages/google-bigquery/).
-   Wait 20-30 minutes for the changes to take effect.
-   Ensure that you no longer experience data loss. |

#### GCP project not found

| Reason | Consequence | Actions required |
| --- | --- | --- |
| OWOX BI could not find the Google Cloud Project; it either does not exist, or there is no access to it. | Data loss | -   Go to the [Google Cloud Console](https://console.cloud.google.com/bigquery) as soon as possible, and make sure that your Project exists.
-   Share the dataset with the OWOX BI service account by following [these instructions](https://www.owox.com/docs/storages/google-bigquery/).
-   Wait 20-30 minutes for the changes to take effect.
-   Ensure that you no longer experience data loss. |

### Blocking reasons

#### OWOX BI project is blocked

| Reason | Consequence | Actions required |
| --- | --- | --- |
| Your current OWOX BI Project is blocked. | Data loss | -   Go to the [Project settings](https://bi.owox.com/ui/none/projects/billing/) as soon as possible, and activate your project.
-   Wait 20-30 minutes for the changes to take effect.
-   Ensure that you no longer experience data loss. |

#### Streaming is not available in your billing plan

| Reason | Consequence | Actions required |
| --- | --- | --- |
| Event-based streaming is unavailable in your current billing plan at OWOX BI. | Data loss | -   Go to the [Project settings](https://bi.owox.com/ui/none/projects/billing/) as soon as possible, and upgrade your plan.
-   Wait 20-30 minutes for the changes to take effect.
-   Ensure that you no longer experience data loss. |

## How to change settings in created streaming

The event-based website streaming is a powerful tool for collecting user behavioral data from your website to BigQuery. During the streaming setup, you specify the required settings, such as the time zone, and destination dataset. Once the streaming is activated, the data will be streamed from your website to the BigQuery tables ‘events\_intraday\_YYYYMMDD’.

However, there may be cases when you need to change one of the streaming settings. You need to delete the existing streaming and create a new one with the required settings. Then, please choose one of the sending options and set it up.

To delete the existing streaming, click the “...” icon and choose the “**Delete**” option.

![01-delete.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-faq/01/public)

To create a new event-based streaming, you can use [this step-by-step guide](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/setup.md#how-to-set-up-ga4-event-based-web-streaming-client-side--server-side).
