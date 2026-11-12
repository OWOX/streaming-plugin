# FAQ

- [How does the qt parameter affect assigning transactions to user sessions?](#how-does-the-qt-parameter-affect-assigning-transactions-to-user-sessions)
- [Which values will be recorded to the hit and session data tables when GCLID auto-tagging and manual UTM tagging data overlap in Google Ads?](#which-values-will-be-recorded-to-the-hit-and-session-data-tables-when-gclid-auto-tagging-and-manual-utm-tagging-data-overlap-in-google-ads)
- [Why does hitId have different UTM source and keyword in the hit and session data tables?](#why-does-hitid-have-different-utm-source-and-keyword-in-the-hit-and-session-data-tables)
- [Why does the same Client ID within a single domain have different OWOX User IDs?](#why-does-the-same-client-id-within-a-single-domain-have-different-owox-user-ids)
- [How to check the number of transaction hits in BigQuery per date?](#how-to-check-the-number-of-transaction-hits-in-bigquery-per-date)
- [How to submit Client ID to Google Analytics Custom Dimension?](#how-to-submit-client-id-to-google-analytics-custom-dimension)
- [Why do I need bot traffic statistics?](#why-do-i-need-bot-traffic-statistics)
- [Why does a sessionId have more than one hit with the "isEntrance = 1" parameter?](#why-does-a-sessionid-have-more-than-one-hit-with-the-isentrance--1-parameter)
- [How does the Google Analytics → Google BigQuery user behavior data pipeline in OWOX BI differ from the BigQuery Export for Google Analytics 360?](#how-does-the-google-analytics--google-bigquery-user-behavior-data-pipeline-in-owox-bi-differ-from-the-bigquery-export-for-google-analytics-360)
- [What does the at-least-once delivery principle mean while collecting hits?](#what-does-the-at-least-once-delivery-principle-mean-while-collecting-hits)
- [Which type of access does OWOX BI require and how to update the access tokens?](#which-type-of-access-does-owox-bi-require-and-how-to-update-the-access-tokens)
- [How are the data storing and unique user processing billed?](#how-are-the-data-storing-and-unique-user-processing-billed)
- [How to fix the "Cannot output multiple independently repeated fields at the same time" error?](#how-to-fixthe-cannot-output-multiple-independently-repeated-fields-at-the-same-time-error)

## How does the qt parameter affect assigning transactions to user sessions?

_From this guide, you'll learn the role of the qt parameter in creating links between transactions and user sessions._

If the delay between completing an order on a website and submitting the Measurement Protocol request exceeds a preset [session timeout](https://support.google.com/analytics/answer/2795871?hl=en) (it's 30 minutes by default), then it is essential to define the _qt_ parameter in your request to Google Analytics and OWOX BI endpoint. Both the Google Analytics and OWOX BI algorithms determine to which session this particular transaction must be assigned based on the _qt_ parameter value.

Next, we'll try to demonstrate the role of the _qt_ parameter in creating links between transactions and user sessions. Let's see an example.

A user created three sessions during a single day with the next source/medium values:

-   google/cpc,
-   google/organic

The user completed an order on a website during _Session 2._ Information about this transaction might be sent to Google Analytics and OWOX BI endpoint after the order completion.

![scenario\_0\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-faq/01/public)

Now let's see different scenarios of submitting the transaction hit via the Measurement Protocol.

**Scenario 1.** You submit the transaction hit with an undefined _qt_ parameter right after the order completion or during the preset session timeout:

![scenario\_1\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-faq/02/public)

The transaction will be assigned to the right _Session 2_.

> **Note**  
> If the user returns to the website during the session timeout and before the request with the undefined _qt_ is submitted, then a new session will start. And the transaction will be assigned to this new session. Let's see this case in detail:  
> ![scenario\_1-1\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-faq/03/public)  
> \- at 9:35 the user completed _Session 2_, and the 30-minute timing started.  
> \- at 9:40 the user returned to the website and the new _Session 5_ started.  
> \- at 9:45 the Measurement Protocol request with undefined _qt_ was sent.  
> As a result, the analytics algorithm will assign the transaction to _Session 5_.  

**Scenario 2. You submit the transaction hit with the undefined _qt_ parameter** in 95 minutes after the order completion:

![scenario\_2\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-faq/04/public)

The transaction will be assigned to the wrong _Session 3_ which will be active at the moment of the request submission.

**Scenario 3.** You submit the transaction hit with _qt=1800000_ (the equivalent of 30 minutes) in 95 minutes after the order completion:

![scenario\_3\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-faq/05/public)

In this case, the transaction must be assigned to the session that was active 30 minutes before the request is submitted. Given that our user didn't have the session at that time, the analytics services will automatically create _Session 4_ for this transaction. As a result, the transaction will be assigned to the wrong _Session 4._

_**Scenario 4.**_ You submit the transaction hit with _qt=2700000_ (the equivalent of 45 minutes) in 45 minutes after the order completion:

![scenario\_1\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-faq/06/public)

In this case, the _qt_ value reflects the exact delay between the order completion and the request submission. Therefore, the transaction will be assigned to the right _Session 2_.

To resume, if you know that there will be a delay between the order completion and the request submission, define the _qt_ parameter in your request following [these recommendations](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/tracking-with-measurement-protocol.md). If you already submit the _qt_ parameter, ensure that its value is **dynamic**. The value in the _queueTime_ field of the [hit data table](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/data-schema-for-hit-tables-streaming.md) should reflect the actual delay for each particular transaction.

> **Note**  
> If the _qt_ parameter is set to more than 4 hours, the [hit won’t get to Google Analytics](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#qt). Meanwhile, OWOX BI can associate the hit data with the right session if the hit is completed within 30 days. As a result, OWOX BI will collect more data than Google Analytics.

## Which values will be recorded to the hit and session data tables when GCLID auto-tagging and manual UTM tagging data overlap in Google Ads?

_When you use both “[Auto-tagging](https://support.google.com/google-ads/answer/1752125)” and “[Manual tagging](https://www.owox.com/blog/articles/how-to-use-utm-tags/)” in your Google Ads campaigns, then a tag overlapping problem occurs. In this case, OWOX BI will follow specific rules while processing data. Learn more below._

### Tracking values in hit data

General rule: OWOX BI saves all data to [the hit data table](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/data-schema-for-hit-tables-streaming.md) when it receives a hit on the access point. In this article, we will describe different options for processing only UTM tags and the GCLID parameter:

-   The values of the UTM tags ‘_utm\_source_’, ‘_utm\_medium_’, ‘_utm\_campaign_’, ‘_utm\_keyword_’, and ‘_utm\_content_’ specified in the ad URL will be written to the corresponding fields of the hit table: `traffic.source`, `traffic.medium`, `traffic.campaign`, `traffic.keyword` and `traffic.adContent`.
-   The value of the GCLID parameter specified in the ad URL will be written to the field `traffic.gclid`.

To better understand how this rule works in different situations, let's look at a few examples.

###### Example #1

Page URL contains UTM tags ‘_utm\_source_’, ‘_utm\_medium_’ and GCLID parameter, but does not contain UTM tags ‘_utm\_campaign_’, ‘_utm\_keyword_’ and ‘_utm\_content_’. Then the values from ‘_utm\_source_’, and ‘_utm\_medium_’ will be written to the hit table in the `traffic.source` and `traffic.medium` fields, respectively. And the fields `traffic.campaign`, `traffic.keyword` and `traffic.adContent` will get the value ‘_(not set)_’. The `traffic.gclid` field will contain the value of the GCLID parameter.

###### Example #2

The ad URL has a GCLID parameter and does not contain the ‘_utm\_source_’, ‘_utm\_medium_’, ‘_utm\_campaign_’, ‘_utm\_keyword_’, and ‘_utm\_content_’. Then the ‘_google_’ value will be written in the `traffic.source` field, and the ‘_cpc_’ value will fall into the `traffic.medium` field. And fields `traffic.campaign`, `traffic.keyword` and `traffic.adContent` will get the value ‘_(not set)_’. The value of the GCLID parameter will be written to the hit table according to paragraph #2 of the general rule.

> The values ‘_google/cpc_’ corresponds exactly to the [Google Analytics processing flow](https://support.google.com/analytics/answer/6205762?hl=en#flowchart).

###### Example #3

The ad URL does not contain the ‘_utm\_source_’, ‘_utm\_medium_’, and a GCLID parameter. Then the OWOX BI algorithm will try to fill in the `traffic.source` and `traffic.medium` fields based on other signs. For example, if a website _facebook.com_ is the source of the hit, then the `traffic.source` field would be ‘_facebook_’ and the `traffic.medium` field would be ‘_referral_’. If all attempts to determine the source of the hit have failed, these fields will be filled with ‘_(not set)_’.

### Tracking values in session data

You can independently control what values will be written to the fields of [session tables](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/schema-for-session-data-tables-collected-with-the-owox-bi-algorithm-owoxbi-sessions.md) when some UTM tags are crossed or missing. To do this, set the data download priority in the [streaming pipeline settings](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking.md#set-up-session-data-collection-based-on-owox-bi-algorithm): “Manual tagging” или “Auto-tagging”.

Please note that for the “Auto-tagging” option to work correctly, the field `traffic.gclid` must be filled in hit tables and a [table with raw data of Google Ads campaigns with auto-tagging](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking/setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging.md) must be connected.

> **Important**  
> To collect data on advertisements with auto-tagging, the _GCLID_ value should be as follows: **_gclid=XXXX_**. Any other formats of GCLID value (for example, _gclid\*XXXX_) will not be processed and written to the session data tables.

#### Manual tagging option

When the **“Manual tagging”** option is selected, the OWOX BI algorithm will **first of all** look at the values of the `traffic.campaign` and `traffic.keyword` fields in the hit tables _streaming_.

-   If the values of these fields are **not empty**, then they are written without any changes to the session tables _owoxbi\_sessions_ in the fields `trafficSource.campaign` and `trafficSource.keyword` respectively.
-   If the values of these fields are **empty** (the value ‘_(not set)_’ is specified), then in this case the OWOX BI algorithm will attempt to find the values of these fields according to the corresponding ‘_gclid_’ [in the table with raw data on Google Ads campaigns](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking/setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging.md).
    -   If data with this ‘_gclid_’ **was found**, then the found value will be written to the session tables _owoxbi\_sessions_.
    -   The ‘_(not set)_’ value will be written if not found.

#### Auto-tagging option

When the **“Auto-tagging”** option is selected, the OWOX BI algorithm, when generating session data, **first of all**, checks the values for the `trafficSource.campaign` and `trafficSource.keyword` fields for the corresponding ‘_gclid_’ in the [tables with raw data on Google Ads campaigns](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking/setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging.md).

-   If the values are **found**, then they are written to the session tables in the `trafficSource.campaign` and `trafficSource.keyword` fields.
-   If the values are **not found**, then the OWOX BI algorithm in the next iteration will try to find these data in the hit tables _streaming_ in the `traffic.campaign` and `traffic.keyword` fields.
    -   If data on this ‘_gclid_’ **is found**, then the found value will be written to the session tables _owoxbi\_sessions_.
    -   The ‘_(not set)_’ value will be written if not found.

In addition to this action, the OWOX BI algorithm performs one more action with data transformation when the “Auto-tagging” option is selected. In the session tables, the fields `trafficSource.source` and `trafficSource.medium` will contain the values ‘_google_’ and ‘_cpc_’, respectively, even if other values are stored in the hit tables in the fields `traffic.source` and `traffic.medium`.

And let's look at a few examples again to understand how this rule works in different situations.

###### Example #4

The ad page URL contains UTM tags ‘_utm\_source=111_’, ‘_utm\_medium=222_’, ‘_utm\_campaign=333_’, ‘_utm\_keyword=444_’ and parameter ‘_gclid=XXXX_’. And also in the connected table with raw data of Google Ads campaigns, other values correspond to this GCLID parameter: campaign=’_555_’ and keyword=’_666_’.

According to the logic from examples 1 and 2, the hit tables _streaming_ will contain the following values in the fields: `traffic.source` = ‘_111_’, `traffic.medium` = ‘_222_’, `traffic.campaign` = ‘_333_’, `traffic.keyword` = ‘_444_’ and `traffic.gclid` = ‘_XXXX_’.

What values will be written in the session tables:

-   **When the “Manual tagging” option is active** the same values from the hit table will be written in session tables _owoxbi\_sessions_ without changes: `trafficSource.source` = ‘_111_’, `trafficSource.medium` = ‘_222_’, `trafficSource.campaign` = ‘_333_’, `trafficSource.keyword` = ‘_444_’ and `trafficSource.gclid` = ‘_XXXX_’.
-   **When the “Auto-tagging” option is active** the following values will be recorded in the session tables _owoxbi\_sessions_: `trafficSource.source` = ‘_google_’, `trafficSource.medium` = ‘_cpc_’, `trafficSource.campaign` = ‘_555_’, `trafficSource.keyword` = ‘_666_’ and `trafficSource.gclid` = ‘_XXXX_’.

###### Example #5

The ad page URL contains UTM tags ‘_utm\_source=111_’, ‘_utm\_medium=222_’ and parameter ‘_gclid=XXXX_’. And also in the connected table with raw data of Google Ads campaigns, other values correspond to this GCLID parameter: campaign=’_333_’ and keyword=’_444_’.

According to the logic from examples 1 and 2, the hit tables _streaming_ will contain the following values in the fields: `traffic.source` = ‘_111_’, `traffic.medium` = ‘_222_’, `traffic.campaign` = ‘_(not set)_’, `traffic.keyword` = ‘_(not set)_’ and `traffic.gclid` = ‘_XXXX_’.

What values will be written in the session tables:

-   **When the “Manual tagging” option is active** the following values will be recorded in the session tables _owoxbi\_sessions_: `trafficSource.source` = ‘_111_’, `trafficSource.medium` = ‘_222_’, `trafficSource.campaign` = ‘_333_’, `trafficSource.keyword` = ‘_444_’ and `trafficSource.gclid` = ‘_XXXX_’.
-   **When the “Auto-tagging” option is active** the following values will be recorded in the session tables _owoxbi\_sessions_: `trafficSource.source` = ‘_google_’, `trafficSource.medium` = ‘_cpc_’, `trafficSource.campaign` = ‘_333_’, `trafficSource.keyword` = ‘_444_’ and `trafficSource.gclid` = ‘_XXXX_’.

###### Example #6

The ad page URL contains only the parameter ‘_gclid=XXXX_’ and does not contain any UTM tags. Also in the connected table with raw data of Google Ads campaigns, other values correspond to this GCLID parameter: campaign=’_111_’ and keyword=’_222_’.

According to the logic from examples 1 and 2, the hit tables _streaming_ will contain the following values in the fields: `traffic.source` = ‘_google_’, `traffic.medium` = ‘_cpc_’, `traffic.campaign` = ‘_(not set)_’, `traffic.keyword` = ‘_(not set)_’ and `traffic.gclid` = ‘_XXXX_’.

What values will be written in the session tables:

-   **When the “Manual tagging” option is active** the following values will be recorded in the session tables _owoxbi\_sessions_: `trafficSource.source` = ‘_google_’, `trafficSource.medium` = ‘_cpc_’, `trafficSource.campaign` = ‘_111_’, `trafficSource.keyword` = ‘_222_’ and `trafficSource.gclid` = ‘_XXXX_’.
-   **When the “Auto-tagging” option is active** the following values will be recorded in the session tables _owoxbi\_sessions_: `trafficSource.source` = ‘_google_’, `trafficSource.medium` = ‘_cpc_’, `trafficSource.campaign` = ‘_111_’, `trafficSource.keyword` = ‘_222_’ and `trafficSource.gclid` = ‘_XXXX_’.

## Why does hitId have different UTM source and keyword in the hit and session data tables?

When collecting user sessions, OWOX BI additionally updates the _source_ and _keyword_ UTM tags for the hits with _medium = referral_. In this case:

-   UTM _source_ is assigned a domain value of the referral source;
-   UTM _keyword_ is assigned a value from the UTM _term_ of the referral source.

**_Example_**  
_If the referral source looks like test.com/? utm\_term=my\_kw and the hit parameters include medium = referral, the following values will be recorded into the session data tables:_  
_source = test.com,_  
_medium = referral,_  
_keyword = my\_kw._  
_The UTM source and keyword of the hit will be ignored._

> **Note**  
> The UTM _campaign_ and _content_ are not modified and keep values from the hit parameters.

## Why does the same Client ID within a single domain have different OWOX User IDs?

As [our article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#collecting-owox-user-id) says, the OWOX User ID is a custom cookie file generated by OWOX BI using the Client ID and IP hashes. Normally, once this file is written to a third-party cookie, it remains activated on a user’s device for 364 days without any changes. It allows using the same ID across domains and subdomains.

If your statistics show that the same Client ID within a single domain has different OWOX User Identifiers, then consider the possible reasons for that:

-   **Your site is getting bot traffic.** To overcome your website protection, the bot traffic may come from multiple IPs. As a result, the combination of the given Client ID and a new IP address causes the creation of a new OWOX User ID.
-   **The 3rd party cookies are blocked in a user’s browser.** In this case, OWOX BI can’t activate the persistent cookie file on a user’s device. And if this user changes the IP address, a new OWOX User ID will be generated for the same Client ID.

## How to check the number of transaction hits in BigQuery per date?

When you submit the [Measurement Protocol request](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/tracking-with-measurement-protocol.md) to the endpoint `https://google-analytics.bi.owox.com/UA-XXXXX-X`, OWOX BI records **all** the transaction hits. While Google Analytics records only one hit out of two (or more) hits with the same parameter values in the actionField object. In other words, Google Analytics stores information only about the **unique** transaction hits.

### Getting all the transaction hits per date

This query shows the total number of transaction hits that the OWOX BI streaming pipeline collected on the selected date.

```
SELECT
  date,
  COUNT(transaction.transactionId) transactions
  FROM
  `Project.Dataset.owoxbi_sessions_20210513`,
  UNNEST(hits) hits
  WHERE
  transaction.transactionId IS NOT NULL
  GROUP BY 1
  ORDER BY
  1 ASC
```

Update the following parameters with your own values:

_Project_ is the name of your GBQ project,

_Dataset_ is the name of your GBQ dataset,

_owoxbi\_sessions\_20210513_ is the name of the BigQuery table on the selected date.

### Getting the unique transaction hits per date

This query shows how many unique transaction hits are in transaction data that the OWOX BI streaming pipeline collected on the selected date.

```
SELECT
  date,
  COUNT (DISTINCT transaction.transactionId) transactions
  FROM `Project.Dataset.streaming_20210513`
  GROUP BY date
  ORDER BY date
```

Update the following parameters with your own values:

_Project is the name of your GBQ project;_

_Dataset_ is the name of your GBQ dataset;

_streaming\_20210513_ is the name of the BigQuery table on the selected date.

## How to submit Client ID to Google Analytics Custom Dimension?

The _customTask_ method is one of the most convenient ways to submit the Client ID parameter to Google Analytics. You'll need to complete to main steps:

1.  Create a custom parameter in Google Analytics.
2.  Add the _customTask_ to the tracking code either directly or via Google Tag Manager.

Find the detailed instructions in our [blog](https://www.owox.com/blog/use-cases/google-analytics-client-id/#how-transmit).

> **Note**  
> If you plan to use the Google Tag Manager on the same website to submit the _clientId_ to Google Analytics and to [integrate the OWOX BI tracking code](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md), then follow [these recommendations](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md).

## Why do I need bot traffic statistics?

When you need to evaluate your ad efficiency, you may use such metrics as the number of clicks and pageviews, the average time on page and the bounce rate, etc. All these metrics are supposed to reflect the real users' activity on your website. Meanwhile, automated traffic takes more than half of internet traffic nowadays, the [experts found](https://assets.barracuda.com/assets/docs/dms/Bot_Attacks_report_vol1_EN.pdf). So it is getting harder to collect realistic statistics and build an objective evaluation report. And you will probably have to make additional efforts to identify and exclude the “bad bot” activity from the website data.

For instance, you can apply automatic filtering of the bot traffic in Google Analytics. It works for the known bots but does not allow you to assess the real impact of bots on incoming traffic and detect new patterns of bots’ behavior that automatic algorithms can not identify.

If you notice a sudden spike in users from one particular region, abnormally high bounce rate, or want to identify the ​​click fraud, it is worth analyzing raw website data.

A unique feature of the [OWOX BI streaming pipeline](https://github.com/OWOX/streaming-plugin/tree/main/docs/hit-based) is that it collects raw, non-sampled data about user behavior on your website. Collected statistics include both human and non-human activity ([hits](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/data-schema-for-hit-tables-streaming.md)). During the users' session collection, OWOX BI identifies bot traffic using specific algorithms. However, unlike Google Analytics, OWOX BI does not exclude bot traffic from your data but marks the relevant sessions with _“true”_ value in the _"__device.isBot"_ field of [session data tables](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/schema-for-session-data-tables-collected-with-the-owox-bi-algorithm-owoxbi-sessions.md). The given approach has an edge over automatic filtering and enables you to:

-   automatically identify known bots without loss of data;
-   assess the impact of bots on your website traffic;
-   detect the patterns of bots’ behavior;
-   decide which traffic is a bot and which is not, according to your own rules;
-   develop mechanisms that will diminish “bad bot” activity in your particular situation (e.g.,  specific user or IP address exclusion);
-   revise your method of assessing bot traffic at any time and update the marketing reports even for the past periods based on the historical data imported by OWOX BI.

To conclude, having raw website data at your disposal gives you the ability to overcome the “bad bot” issues and enhance the quality of your ad performance analysis.

## Why does a sessionId have more than one hit with the "isEntrance = 1" parameter?

The "`isEntrance = 1`" parameter is assigned to each session.

Two sessions will have the same session ID if Google Analytics ends the session at midnight.

This causes that several session IDs may have 2 hits with the "`isEntrance = 1`" parameter.

## How does the Google Analytics → Google BigQuery user behavior data pipeline in OWOX BI differ from the BigQuery Export for Google Analytics 360?

The main differences are the following:

1.  **The Google Analytics → Google BigQuery pipeline does not require the Google Analytics 360 account.**
2.  **Storing and querying processing data collected with the GA→GBQ pipeline is cheaper.**  
    The pipeline collects [hit](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/data-schema-for-hit-tables-streaming.md) and [session](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/schema-for-session-data-tables-collected-with-the-owox-bi-algorithm-owoxbi-sessions.md) data into 2 tables and the tables contain only necessary information, which significantly decreases BigQuery expenses. The BigQuery Export table for [Google Analytics 360](https://www.owox.com/google-analytics-360/) contains over 40 columns with AdSense, Google Ads, DoubleClick, and other data that is rarely required.
3.  **With the GA→GBQ pipeline, your hit data appears in BigQuery in 5 minutes.**  
    The BigQuery Export for Google Analytics 360 can take up to 24 hours.
4.  **OWOX BI does not place limitations on the number of hits transmitted or on user metrics, and the allowable hit size is twice as large at 16 KB.** This means you get a complete picture of the actions users take on your site.  
    Below, you can see the current Google Analytics 360 [limits](https://developers.google.com/analytics/devguides/collection/analyticsjs/limits-quotas) on data collection:
    
    -   200,000 hits per user per day;
    -   500 hits per session;
    -   200 user parameters and 200 indicators (custom dimensions and metrics);
    -   The maximum hit size is 8 KB.
    
    Data above these limits isn’t processed and doesn’t get into Google Analytics.
    

> In our [blog](https://www.owox.com/blog/articles/owox-bi-and-google-analytics-360-comparison/), you can find a detailed comparison of OWOX BI and Google Analytics 360 in terms of
> 
> -   data import to Google BigQuery,
> -   cost data import to Google Analytics,
> -   ad campaigns attribution,
> -   building reports,
> -   cost of use.

## What does the at-least-once delivery principle mean while collecting hits?

OWOX BI uses the at-least-once delivery concept to ensure collecting **all hits** you send to our endpoint. It means in some cases it may be duplicates of hits with the same _'hitId'_. Typically, the share of these hits is less than 0.1%.

### Best effort de-duplication

Thus, if you operate directly to hit level data in tables like 'streaming\_YYYYMMDD', we recommend adding a specific condition to your SQL query to exclude duplicates.

Example:

```sql
WITH uniqueHistTable AS(
    SELECT
        *
    EXCEPT(rowNumber)
    FROM
        (
            SELECT
                *,
                ROW_NUMBER() OVER (PARTITION BY hitId) rowNumber
            FROM
                HITS_TABLE
        )
    WHERE
        rowNumber = 1
)

SELECT * FROM uniqueHistTable
```

> **Note:**  
> While running the [session collecting](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking.md#set-up-session-data-collection-based-on-owox-bi-algorithm) OWOX BI automatically removes all duplicates of hits.

## Which type of access does OWOX BI require and how to update the access tokens?

To enable the [collection of user behavior data](https://github.com/OWOX/streaming-plugin/tree/main/docs/hit-based), you need to grant OWOX BI access to your data sources:

-   To a [View](https://support.google.com/analytics/answer/2649553?hl=en) associated with the required property in your Google Analytics account. For authentication, use your Google account with the [Edit rights](https://support.google.com/analytics/answer/2884495?hl=en) at a View level.
-   To your Google BigQuery project. For authentication, use your Google account that is assigned the [BigQuery Data Editor and BigQuery User roles](https://www.owox.com/docs/storages/google-bigquery/) in the Google Cloud Platform service.

If your credentials to Google Analytics / Google BigQuery are changed or you’re asked to re-authenticate in service, follow the instructions below:

**Step 1.** Navigate to your Google Analytics User Behavior Data → Google BigQuery pipeline page:

**Step 2.** Click the Edit icon next to the required access record:

![access\_edit1\_us.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-faq/07/public)

**Step 3.** In the opened pop-up, select your current access or add a new one. The changes will be applied automatically.

![access\_edit2\_us.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-faq/08/public)

## How are the data storing and unique user processing billed?

The data storage and query costs are paid directly to Google in accordance with their [Google BigQuery pricing](https://cloud.google.com/bigquery/pricing). Charges are billed through your [Cloud Billing account](https://cloud.google.com/billing/docs/how-to/manage-billing-account).

OWOX BI subscription fee depends on the selected [plan](https://www.owox.com/pricing/), as well as the number of ad cost data pipelines and unique users that you've included in your subscription. If the number of unique users exceeds your subscription limit during the billing period, you'll be charged an additional cost.

## How to fix the "Cannot output multiple independently repeated fields at the same time" error?

To query nested data (the fields with the RECORD data type), use the FLATTEN function.

Please read [BigQuery reference guide](https://cloud.google.com/bigquery/docs/data#flatten) for more information.
