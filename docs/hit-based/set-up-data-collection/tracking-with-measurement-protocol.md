# Tracking with Measurement Protocol

To collect the majority of user behavior events to BigQuery, OWOX BI, using either the [customTask](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection/owox-bi-tracking-code-integration-with-google-tag-manager.md) or [analytics.js](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection.md) method, duplicates all the data Google Analytics collects from the website and sends it to the dedicated OWOX BI access point.

For collecting offline events or the events that happen after the user’s session on the website, Google Analytics offers using [Measurement Protocol](https://developers.google.com/analytics/devguides/collection/protocol/v1/). The protocol can send any kind of user behavior data directly to Google Analytics using an HTTP request.

## OWOX BI access point

If you send certain events to Google Analytics via Measurement Protocol, you need to duplicate them to the OWOX BI access point as well. The access point is `https://google-analytics.bi.owox.com/UA-XXXXX-X`, where `UA-XXXXX-X` — is your Google Analytics tracking ID.

Without duplicating the request, the event data won’t get to BigQuery, which will result in data discrepancy with Google Analytics. Please note that to send the data to Google Analytics, you still need to send a request to `https://www.google-analytics.com/collect`.

## Sending requests

Sending requests to our access point is no different from sending them to Google Analytics: you send data as a POST or a GET request. See what the requests look like in [Google documentation](https://developers.google.com/analytics/devguides/collection/protocol/v1/reference).

### POST request (recommended method)

For a POST request, you need to specify payload data identical to the one you send to Google Analytics. Here’s an example:

`v=1&cid=555&t=pageview&dp=%2Fhome`.

For POST requests, use one of these Content-type headers:

1.  application/x-www-form-urlencoded;
2.  application/octet-stream;
3.  text/plain.

### GET request

For a GET request, specify the set of parameters in the URL. For example:

`https://google-analytics.bi.owox.com/UA-XXXXX-X?v=1&cid=555&t=pageview&dp=%2Fhome`.

## Requirements for value formatting

In general, a field value format should comply with the [Measurement Protocol’s documentation](https://developers.google.com/analytics/devguides/collection/protocol/v1/).

However, some fields should also meet specific requirements as they undergo additional processing by OWOX BI. Please see the table below.

> **Note**  
> Data that does not match the specified format cannot be processed by OWOX BI and will be written as NULL in Google BigQuery tables.

| **Full name** | **Abbreviation** | **Value type** | **Supported value format** |
| --- | --- | --- | --- |
| [Transaction Revenue](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#tr) | tr | currency | Example value:<br>1234,56<br>1,234.56<br>1.234,56<br>1 234,56 |
| [Transaction Tax](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#tt) | tt | currency | Example value:<br>1234,56<br>1,234.56<br>1.234,56<br>1 234,56 |
| [Transaction Shipping](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#ts) | ts | currency | Example value:<br>1234,56<br>1,234.56<br>1.234,56<br>1 234,56 |
| [Product Price](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pr_pr) | pr<productIndex>pr | currency | Example value:<br>1234,56<br>1,234.56<br>1.234,56<br>1 234,56 |
| [Custom Metric](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#cm_) | cm<metricIndex> | number | Example value:<br>1234,56<br>1,234.56<br>1.234,56<br>1 234,56 |
|   |   |   |   |
| [Checkout Step](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#cos) | cos | integer | Example value:<br>1<br>1.00<br>1.0000 |
| [Queue Time](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#qt) | qt | integer | Example value:<br>1<br>1.00<br>1.0000 |
| [Page Load Time](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#plt) | plt | integer | Example value:<br>1<br>1.00<br>1.0000 |
| [DNS Time](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#dns) | dns | integer | Example value:<br>1<br>1.00<br>1.0000 |
| [Page Download Time](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pdt) | pdt | integer | Example value:<br>1<br>1.00<br>1.0000 |
| [Redirect Response Time](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#rrt) | rrt | integer | Example value:<br>1<br>1.00<br>1.0000 |
| [TCP Connect Time](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#tcp) | tcp | integer | Example value:<br>1<br>1.00<br>1.0000 |
| [Server Response Time](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#srt) | srt | integer | Example value:<br>1<br>1.00<br>1.0000 |
| [DOM Interactive Time](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#dit) | dit | integer | Example value:<br>1<br>1.00<br>1.0000 |
| [Content Load Time](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#clt) | clt | integer | Example value:<br>1<br>1.00<br>1.0000 |
| [Product Position](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pr_ps) | pr<productIndex>ps | integer | Example value:<br>1<br>1.00<br>1.0000 |
| [User timing time](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#utt) | utt | integer | Example value:<br>1<br>1.00<br>1.0000 |
|   |   |   |   |
| [Content Group](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#cg_) | cg<groupIndex> | text | Example value:<br>Hello World  |
| [Custom Dimension](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#cd_) | cd<dimensionIndex> | text | Example value:<br>Hello World  |
| [Exception Description](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#exd) | exd | text | Example value:<br>Hello World  |
| [Document Referrer](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#dr) | dr | text | Example value:<br>Hello World  |
| [Product Name](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pr_nm) | pr<productIndex>nm | text | Example value:<br>Hello World  |

## Using the queueTime (&qt) parameter

By default, the time when a hit is reported equals the time when the hit occurs. However, hits may be reported with a delay. For example, in online payment, a bank confirms a transaction some time after this transaction is made by a user. To match this hit to the time it occurred, the _queueTime (&qt)_ parameter must be used.

The _&qt_ value represents the time delta (in milliseconds) between when the hit occurred and the time it was reported. By setting this parameter, the hit time (value in the _time_ field) will be calculated correctly.

> **Note**  
> The _&qt_ value must be of _integer_ type and be _greater than or equal to 0_.  
> Otherwise, the _&qt_ will be automatically assigned the _NULL_ value and the hit will be assigned a time when it was reported.

Please see how the data is processed when sending a non-zero parameter _&q_t below.

| **OWOX BI** | **Google Analytics** |
| --- | --- |
| **Hit data collection** |  |
| There are no limits for the _&qt_ value.<br>Hits will always get to the hit data table (_'streaming\_'_) in Google BigQuery regardless of the &qt value.<br>A hit will get to a data table for the date calculated considering the difference in time between the value of _&qt_ parameter and the time the hit was sent.<br>_Example: A hit, that was sent with &qt=86400000 (-24 hours) on August 10th, 2019, will get into a hit data table collected for August 9th, 2019._ | There are limits for the _&qt_ value.<br>Values greater than 4 hours may lead to hits [not being processed](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#qt). |
| **Session data collection** |  |
| For session data collection based on the [OWOX BI algorithm](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking.md#set-up-session-data-collection-based-on-owox-bi-algorithm), the hits will be assigned to the corresponding sessions (_'owoxbi\_sessions\_'_) within 30 days\*.<br>_\*After the end of the day when the hits occurred in the Google Analytics property timezone._<br>_If you need the hits are being assigned to the sessions beyond the default period (30 days), contact us at [bi@owox.com](mailto:bi@owox.com)._ | For session data collection based on the [Google Analytics algorithm](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#collecting-and-updating-the-google-analytics-based-session-data), the hits will be assigned to the corresponding sessions within 48 hours\* based on the _clientId_ and timestamp values sent in the custom dimension (_&cd_).<br>_\*After the end of the day when the hits occurred in the Google Analytics property timezone._ |

> Learn how the _qt_ parameter affects assigning transactions to user sessions from [this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/faq.md#how-does-the-qt-parameter-affect-assigning-transactions-to-user-sessions).

## Limitations

-   The request must contain the _clientId_ (_&cid_) or the userId (_&uid_) parameter. Without sending at least one of these parameters, a hit will be processed neither in OWOX BI nor in Google Analytics.
-   If you use several hits to send data of a single transaction, you should submit unique parameter values in the _actionField_ object per hit. For example, you can do it by entering different values for **Revenue**, **Tax**, or **Affiliation** in each _actionField_ object. Otherwise, out of two (or more) hits with the same parameter values in the _actionField_ object, only one hit will be recorded by GA.
    
    While OWOX BI will record all the transaction hits regardless of the received values in the _actionField_ object.
    
-   In OWOX BI Pipeline, the maximum hit payload size is **16 KB**. If exceeded this limit, hits won't be processed and sent to BigQuery.
-   In Google Analytics hit payload must be no longer than **8 KB**.

> Anytime you can check the number of transactions collected to the BigQuery tables. [Read more](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/faq.md#how-to-check-the-number-of-transaction-hits-in-bigquery-per-date)

## HTTP request example

HOST:  
https://google-analytics.bi.owox.com/UA-XXXXX-X  
  
PAYLOAD:  
  
 "v": 1,  
 "tid": "UA-XXXXX-X",  
 "cid": "111111111.111111111",  
 "t": "event",  
 "ec": "Call",  
 "ea": "Request",  
 "el": "Form №1",  
 "ev": 0,  
 "dh": "example.com",  
 "ds": "call-center",  
 "uip": "11.111.11.11",  
 "ua": "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)",  
 "dr": "https://www.google.com/",  
 "qt": 0

**Parameter breakdown example:**

| hitid | 4a44b2ce-be1b-41f3-8153-50b65461911e |
| --- | --- |
| trackingId | <br>UA-XXXXX-X<br><br> |
| clientId | 111111111.111111111 |
| type | event |
| eventInfo.eventCategory | Call |
| eventInfo.eventAction<br> | Request<br> |
| eventInfo.eventLabel<br> | Form №1 |
| eventInfo.eventValue | 0 |
| page.hostname | example.com |
| dataSource | call-center |
| device.ip | server ip |
| device.userAgent | Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html) |
| referrer | https://www.google.com/ |
