# Consumption Units: Client-Side Users, Server-Side Users

> **Important**  
> The `Client-Side Users` and `Server-Side Users` consumption units apply exclusively to the **OWOX BI Streaming** product.

## OWOX BI Streaming: Terms of Usage

The OWOX BI Streaming product automatically tracks user activity on your website in real-time and collects the data in your data destination (Google BigQuery).

OWOX BI Streaming supports **two types** of data streaming: **Client-Side** and **Server-Side**.

Consumption Units for Streaming represent the **count of unique users** and are determined by the selected [**type of streaming**](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/setup.md#how-to-set-up-ga4-event-based-web-streaming-client-side--server-side):

-   **Client-Side Users**;
-   **Server-Side Users**.

## How OWOX BI calculates unique users

### What is a unique user in OWOX BI?

A unique user is a website visitor associated with a unique identifier whose BigQuery data has been processed in OWOX BI within one month.

### How unique users are identified and calculated in OWOX BI Streaming

#### OWOX BI GA4 Events-based

In [OWOX BI GA4 Events-based](https://github.com/OWOX/streaming-plugin/tree/main/docs/ga4-events-based) streaming, unique users are calculated using the `owox.user_id` field, depending on the `privacy_info` and `request_source` fields within the OWOX BI Streaming ID. Consider three cases:

**Formula for events with user's consent**

```sql
count(distinct(owox.user_id)) WHERE privacy_info.analytics_storage = 'Yes' or privacy_info.analytics_storage is null
```

**Formula for events without user's consent**

Some events don't have either `owox.user_id` or any other identifiers. The cause is [PII data processing](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/faq.md#how-ga4-event-based-web-streaming-works-with-google-consent-mode) according to Google Consent Mode.

```sql
count(distinct(owox.event_id))/10 WHERE privacy_info.analytics_storage = 'No'
```

**Formula for events sent via Measurement Protocol**

The `owox.user_id` and `privacy_info.analytics_storage` fields are always null for events sent via the Measurement Protocol.

OWOX BI calculates unique users within the current monthly calculation period as follows:

1.  If an event’s non-empty `cid`, `uid`, or `ouid` matches an identifier of the same type in any Client-Side event, the Measurement Protocol event does not add another user.
2.  Among the remaining Measurement Protocol events, events with the same `cid`, `uid`, or `ouid` are counted as **one user**, regardless of how many events were sent.
3.  An event with multiple identifiers adds no more than one user. If any of its identifiers has already appeared in another eligible Measurement Protocol event, the event does not add another user.
4.  If an event has no `cid`, `uid`, or `ouid`, OWOX BI uses `owox.event_id` for deduplication. Events with the same `owox.event_id` are counted once.

For events without `cid`, `uid`, or `ouid`:

```sql
count(distinct(owox.event_id)) WHERE owox.request_source = 'Measurement Protocol'
```

#### OWOX BI GAU Hit-based

In [OWOX BI GAU Hit-based](https://github.com/OWOX/streaming-plugin/tree/main/docs/hit-based) streaming, unique users are calculated using the `userId` and/or `clientId` fields within the OWOX BI Streaming ID\*.

Only the `userId` (&uid) parameter values are used for calculation here. The userId values sent in custom dimensions are not taken into account. `clientId` (&cid) for non-consent users are also taken into account.

\* — If more than one user (identified by different [Client IDs](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters)) had logged in using the same User ID, they count as one user. The exceptions are 1) users that weren't logged in and 2) User IDs with more than 100 different Client IDs associated with them. In these cases, unique users are identified only by the Client ID.

### How unique users are calculated in OWOX BI projects with more than one Streaming

If your OWOX BI project has more than one Streaming, the subscription plan considers the unique users of all your Streaming during the reporting period (month).

**Example:** Your OWOX BI project has two [OWOX BI GA4 events-based web streamings to Google BigQuery](https://github.com/OWOX/streaming-plugin/tree/main/docs/ga4-events-based) and one [OWOX BI GAU Hit-based streaming](https://github.com/OWOX/streaming-plugin/tree/main/docs/hit-based). At the end of the billing period, you get these unique users processing statistics:

| Streaming | Unique users |
| --- | --- |
| OWOX BI GA4 events-based web streaming to Google BigQuery | 500,000 + 390,000 = 890,000 |
| OWOX BI GAU Hit-based streaming | 100,000 |
| **Total of the unique users** (in both streaming) | **990,000** (890,000 + 100,000) |

> **NOTE:**  
> Unique users are calculated each day in the UTC time zone, thus the count of users a day may vary depending on your time zone.

> **IMPORTANT:**  
> On the OWOX BI side, total Consumption Units are stored without detailing specific identifiers. If part of the data is changed or deleted on the client's side, this will not change the already calculated totals in OWOX BI.
