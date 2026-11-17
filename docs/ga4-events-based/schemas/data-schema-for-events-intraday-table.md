# Data Schema for 'events_intraday_' Table

> **Note**  
> This table schema is fully compatible with the table schema from [native GA4 → BigQuery export](https://support.google.com/analytics/answer/7029846?hl=en).

**Tables**. Streamed data is separated into tables by days. The table naming is '**events\_intraday\_YYYYMMDD**'.

**Rows**. Each event is written in a separate row.

**Columns**. The table contains all of the following columns.

## App

> **Important**  
> The algorithm will fill the `‘app_info’` fields with NULL. The OWOX BI event-based web streaming only processes Web and Measurement Protocol requests.

| Field name | Type | Description |
| --- | --- | --- |
| `app_info` | RECORD | A record of information on the app. |
| `app_info.id` | STRING | The package name or bundle ID of the app. |
| `app_info.firebase_app_id` | STRING | The Firebase App ID associated with the app |
| `app_info.install_source` | STRING | The store that installed the app. |
| `app_info.version` | STRING | The app's versionName (Android) or short bundle version. |

## Device

| Field name | Type | Description |
| --- | --- | --- |
| `device` | RECORD | A record of device information. |
| `device.category` | STRING | The device category (mobile, tablet, desktop). |
| `device.mobile_brand_name` | STRING | The device brand name. |
| `device.mobile_model_name` | STRING | The device model name. |
| `device.mobile_marketing_name` | STRING | The device marketing name. |
| `device.mobile_os_hardware_model` | STRING | The device model information retrieved directly from the operating system. |
| `device.operating_system` | STRING | The operating system of the device. |
| `device.operating_system_version` | STRING | The OS version. |
| `device.vendor_id` | STRING | IDFV (present only if IDFA is not collected). |
| `device.advertising_id` | STRING | Advertising ID/IDFA. |
| `device.language` | STRING | The OS language. |
| `device.time_zone_offset_seconds` | INTEGER | The offset from GMT in seconds. |
| `device.is_limited_ad_tracking` | BOOLEAN | The device's Limit Ad Tracking setting.<br>On iOS14+, returns false if the IDFA is non-zero. |
| `device.web_info.browser` | STRING | The browser in which the user viewed content. |
| `device.web_info.browser_version` | STRING | The version of the browser in which the user viewed content. |
| `device.web_info.hostname` | STRING | The hostname associated with the logged event. |

## Stream and platform

| Field name | Type | Description |
| --- | --- | --- |
| `stream_id` | STRING | The numeric ID of the stream |
| `platform` | STRING | The platform on which the app was built. |

## User

| Field name | Type | Description |
| --- | --- | --- |
| `user_first_touch_timestamp` | INTEGER | The time (in microseconds) at which the user first opened the app or visited the site. |
| `user_id` | STRING | The user ID set via the setUserId API. |
| `user_pseudo_id` | STRING | The pseudonymous id (e.g., app instance ID) for the user. |
| `user_properties` | RECORD | A repeated record of user properties set with the setUserProperty API. |
| `user_properties.key` | STRING | The name of the user property. |
| `user_properties.value` | RECORD | A record for the user property value. |
| `user_properties.value.string_value` | STRING | The string value of the user property. |
| `user_properties.value.int_value` | INTEGER | The integer value of the user property. |
| `user_properties.value.double_value` | FLOAT | The double value of the user property. |
| `user_properties.value.float_value` | FLOAT | This field is currently unused. |
| `user_properties.value.set_timestamp_micros` | INTEGER | The time (in microseconds) at which the user property was last set.<br>**This field is not populated in intraday tables.** |
| `user_ltv` | RECORD | A record of Lifetime Value information about the user. This field is not populated in intraday tables. |
| `user_ltv.revenue` | FLOAT | The Lifetime Value (revenue) of the user. This field is not populated in intraday tables. |
| `user_ltv.currency` | STRING | The Lifetime Value (currency) of the user. This field is not populated in intraday tables. |

## Campaign

> **Note:**  
> This field is not populated in intraday tables. However, any custom logic can be applied to calculate it at any time with [OWOX BI Transformation](https://github.com/OWOX/models).

| Field name | Type | Description |
| --- | --- | --- |
| `traffic_source` | RECORD | Name of the traffic source. This field is not populated in intraday tables. |
| `traffic_source.name` | STRING | Name of the marketing campaign. This field is not populated in intraday tables. |
| `traffic_source.medium` | STRING | Name of the medium (organic, cpc, email, etc). This field is not populated in intraday tables. |
| `traffic_source.source` | STRING | Name of the network. This field is not populated in intraday tables. |

## Geo

| Field name | Type | Description |
| --- | --- | --- |
| `geo` | RECORD | A record of the user's geographic information. Geodata is determined based on visitors' IP addresses, which we receive only from those who have [consented](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/faq.md#how-ga4-event-based-web-streaming-works-with-google-consent-mode) to share this data. |
| `geo.continent` | STRING | The continent from which events were reported. |
| `geo.sub_continent` | STRING | The subcontinent from which events were reported. This field remains unfilled. |
| `geo.country` | STRING | The country from which events were reported. |
| `geo.region` | STRING | The region from which events were reported. |
| `geo.metro` | STRING | The metro from which events were reported. This field remains unfilled. |
| `geo.city` | STRING | The city from which events were reported. |

## Event

| Field name | Type | Description |
| --- | --- | --- |
| `event_date` | STRING | The date on which the event was logged (YYYYMMDD format in the registered timezone of your app). |
| `event_timestamp` | INTEGER | The time (in microseconds, UTC) at which the event was logged on the client. |
| `event_previous_timestamp` | INTEGER | The time (in microseconds, UTC) at which the event was previously logged on the client. |
| `event_name` | STRING | The name of the event. The [table clustering](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/faq.md#mastering-clustered-tables-in-event-based-streaming) is configured based on this field. |
| `event_params` | RECORD | A repeated record of [the parameters](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/faq.md#list-of-event-parameters-that-owox-bi-algorithm-adds-in-the-eventparams-field) associated with this event. |
| `event_params.key` | STRING | The event parameter's key. |
| `event_params.value` | RECORD | A record of the event parameter's value. |
| `event_params.value.string_value` | STRING | The string value of the event parameter. |
| `event_params.value.int_value` | INTEGER | The integer value of the event parameter. |
| `event_params.value.double_value` | FLOAT | The double value of the event parameter. |
| `event_params.value.float_value` | FLOAT | The float value of the event parameter. This field is currently unused. |
| `event_value_in_usd` | FLOAT | The currency-converted value (in USD) of the event's "value" parameter. |
| `event_bundle_sequence_id` | INTEGER | The sequential ID of the bundle in which these events were uploaded.<br>**This field is not populated in intraday tables.** |
| `event_server_timestamp_offset` | INTEGER | Timestamp offset between collection time and upload time in micros. |

## Batch Events

> **Note:**  
> These fields were added starting from January 1, 2024, initially set to 'NULL'. Actual values from received events started being parsed and saved into these fields beginning on January 22, 2025. [More details](https://www.owox.com/changelog/)

| Field name | Type | Description |
| --- | --- | --- |
| `batch_page_id` | INTEGER | A sequential number assigned to a page that increases for each subsequent page within an engagement. |
| `batch_ordering_id` | INTEGER | A monotonically increasing number that is incremented each time a network request is sent from a given page. |
| `batch_event_index` | INTEGER | A number indicating the sequential order of each event within a batch based on their order of occurrence on the device. |

## Ecommerce

| Field name | Type | Description |
| --- | --- | --- |
| `ecommerce` | RECORD | A record of information about ecommerce. |
| `ecommerce.total_item_quantity` | INTEGER | Total number of items in this event, which is the sum of items.quantity. |
| `ecommerce.purchase_revenue_in_usd` | FLOAT | Purchase revenue of this event, represented in USD with standard unit. Populated for purchase event only. |
| `ecommerce.purchase_revenue` | FLOAT | Purchase revenue of this event, represented in local currency with standard unit. Populated for purchase event only. |
| `ecommerce.refund_value_in_usd` | FLOAT | The amount of refund in this event, represented in USD with standard unit. Populated for refund event only. |
| `ecommerce.refund_value` | FLOAT | The amount of refund in this event, represented in local currency with standard unit. Populated for refund event only. |
| `ecommerce.shipping_value_in_usd` | FLOAT | The shipping cost in this event, represented in USD with standard unit. |
| `ecommerce.shipping_value` | FLOAT | The shipping cost in this event, represented in local currency. |
| `ecommerce.tax_value_in_usd` | FLOAT | The tax value in this event, represented in USD with standard unit. |
| `ecommerce.tax_value` | FLOAT | The tax value in this event, represented in local currency with standard unit. |
| `ecommerce.transaction_id` | STRING | The transaction ID of the ecommerce transaction. |
| `ecommerce.unique_items` | INTEGER | The number of unique items in this event, based on item\_id, item\_name, and item\_brand. |

## Items

| Field name | Type | Description |
| --- | --- | --- |
| `items` | RECORD | A repeated record of items included in this event. |
| `items.item_id` | STRING | The ID of the item. |
| `items.item_name` | item\_name | The name of the item. |
| `items.item_brand` | STRING | The brand of the item. |
| `items.item_variant` | STRING | The variant of the item. |
| `items.item_category` | STRING | The category of the item. |
| `items.item_category2` | STRING | The sub category of the item. |
| `items.item_category3` | STRING | The sub category of the item. |
| `items.item_category4` | STRING | The sub category of the item. |
| `items.item_category5` | STRING | The sub category of the item. |
| `items.price_in_usd` | FLOAT | The price of the item, in USD with standard unit. |
| `items.price` | FLOAT | The price of the item in local currency. |
| `items.quantity` | INTEGER | The quantity of the item. Quantity set to 1 if not specified. |
| `items.item_revenue_in_usd` | FLOAT | The revenue of this item, calculated as price\_in\_usd \* quantity. It is populated for purchase events only, in USD with standard unit. |
| `items.item_revenue` | FLOAT | The revenue of this item, calculated as price \* quantity. It is populated for purchase events only, in local currency with standard unit. |
| `items.item_refund_in_usd` | FLOAT | The refund value of this item, calculated as price\_in\_usd \* quantity. It is populated for refund events only, in USD with standard unit. |
| `items.item_refund` | FLOAT | The refund value of this item, calculated as price \* quantity. It is populated for refund events only, in local currency with standard unit. |
| `items.coupon` | STRING | Coupon code applied to this item. |
| `items.affiliation` | STRING | A product affiliation to designate a supplying company or brick and mortar store location. |
| `items.location_id` | STRING | The location associated with the item. |
| `items.item_list_id` | STRING | The ID of the list in which the item was presented to the user. |
| `items.item_list_name` | STRING | The name of the list in which the item was presented to the user. |
| `items.item_list_index` | STRING | The position of the item in a list. |
| `items.promotion_id` | STRING | The ID of a product promotion. |
| `items.promotion_name` | STRING | The name of a product promotion. |
| `items.creative_name` | STRING | The name of a creative used in a promotional spot. |
| `items.creative_slot` | STRING | The name of a creative slot. |
| `items.item_params` | RECORD | This field was added starting from January 1, 2023, initially set to 'NULL'. Actual values from received events started being parsed and saved into these fields beginning on March 13, 2024. [More details](https://www.owox.com/changelog/) |
| `items.item_params.key` | STRING |   |
| `items.item_params.value` | RECORD |   |
| `items.item_params.value.string_value` | STRING |   |
| `items.item_params.value.int_value` | INTEGER |   |
| `items.item_params.value.float_value` | FLOAT |   |
| `items.item_params.value.double_value` | FLOAT |   |

## Web

| Field name | Type | Description |
| --- | --- | --- |
| `web_info` | RECORD | A record of information for web data. |

## Privacy info

The following fields are based on [the consent status of your users when you are using consent mode](https://support.google.com/analytics/answer/9976101).

| Field name | Type | Description |
| --- | --- | --- |
| `privacy_info.ads_storage` | STRING | Whether ad targeting is enabled for a user.<br>Possible values: Yes, No, Unset |
| `privacy_info.analytics_storage` | STRING | Whether Analytics storage is enabled for the user.<br>Possible values: Yes, No, Unset |
| `privacy_info.uses_transient_token` | STRING | Whether a web user has denied Analytics storage and the developer has enabled measurement without cookies based on transient tokens in server data.<br>Possible values: Yes, No, Unset |

## Collected Traffic Source

> **Note:**  
> These fields were added starting from January 1, 2024. Actual values from received events started being parsed and saved into these fields beginning on January 22, 2025. [More details](https://www.owox.com/changelog/)

| Field name | Type | Description |
| --- | --- | --- |
| `collected_traffic_source` | RECORD | The collected\_traffic\_source RECORD contains the traffic source data that was present within the events that were collected. |
| `collected_traffic_source.manual_campaign_id` | STRING | The manual campaign id (utm\_id) that was collected with the event. |
| `collected_traffic_source.manual_campaign_name` | STRING | The manual campaign name (utm\_campaign) that was collected with the event. |
| `collected_traffic_source.manual_source` | STRING | The manual campaign source (utm\_source) that was collected with the event. Also includes parsed parameters from referral params, not just UTM values. |
| `collected_traffic_source.manual_medium` | STRING | The manual campaign medium (utm\_medium) that was collected with the event. Also includes parsed parameters from referral params, not just UTM values. |
| `collected_traffic_source.manual_term` | STRING | The manual campaign keyword/term (utm\_term) that was collected with the event. |
| `collected_traffic_source.manual_content` | STRING | The additional manual campaign metadata (utm\_content) that was collected with the event. |
| `collected_traffic_source.manual_source_platform` | STRING | The manual campaign source platform (utm\_source\_platform) that was collected with the event. |
| `collected_traffic_source.manual_creative_format` | STRING | The manual campaign creative format (utm\_creative\_format) that was collected with the event. |
| `collected_traffic_source.manual_marketing_tactic` | STRING | The manual campaign marketing tactic (utm\_marketing\_tactic) that was collected with the event. |
| `collected_traffic_source.gclid` | STRING | The Google click identifier that was collected with the event. |
| `collected_traffic_source.dclid` | STRING | The DoubleClick Click Identifier for Display and Video 360 and Campaign Manager 360 that was collected with the event. |
| `collected_traffic_source.srsltid` | STRING | The Google Merchant Center identifier that was collected with the event. |

## Session Traffic Source Last Click

> **Note:**  
> These fields were added starting from January 1, 2024, and are not populated. [More details](https://www.owox.com/changelog/)

| Field name | Type | Description |
| --- | --- | --- |
| `session_traffic_source_last_click` | RECORD | The session\_traffic\_source\_last\_click RECORD contains the last-click attributed session traffic source data across Google ads and manual contexts, where available. |
| `session_traffic_source_last_click.manual_campaign` | RECORD |   |
| `session_traffic_source_last_click.manual_campaign.campaign_id` | STRING | The ID of the last clicked manual campaign |
| `session_traffic_source_last_click.manual_campaign.campaign_name` | STRING | The name of the last clicked manual campaign |
| `session_traffic_source_last_click.manual_campaign.source` | STRING | The specific source within the platform of the last clicked manual campaign |
| `session_traffic_source_last_click.manual_campaign.medium` | STRING | The medium of the last clicked manual campaign (e.g., paid search, organic search, email) |
| `session_traffic_source_last_click.manual_campaign.term` | STRING | The keyword/search term of the last clicked manual campaign |
| `session_traffic_source_last_click.manual_campaign.content` | STRING | Additional metadata of the last clicked manual campaign |
| `session_traffic_source_last_click.manual_campaign.source_platform` | STRING | The platform of the last clicked manual campaign (e.g., search engine, social media) |
| `session_traffic_source_last_click.manual_campaign.creative_format` | STRING | The format of the creative of the last clicked manual campaign |
| `session_traffic_source_last_click.manual_campaign.marketing_tactic` | STRING | The marketing tactic of the last clicked manual campaign |
| `session_traffic_source_last_click.google_ads_campaign` | RECORD |   |
| `session_traffic_source_last_click.google_ads_campaign.customer_id` | STRING | The customer ID associated with the Google Ads account |
| `session_traffic_source_last_click.google_ads_campaign.account_name` | STRING | The name of the Google Ads account |
| `session_traffic_source_last_click.google_ads_campaign.campaign_id` | STRING | The ID of the Google Ads campaign |
| `session_traffic_source_last_click.google_ads_campaign.campaign_name` | STRING | The name of the Google Ads campaign |
| `session_traffic_source_last_click.google_ads_campaign.ad_group_id` | STRING | The ID of the ad group within the Google Ads campaign |
| `session_traffic_source_last_click.google_ads_campaign.ad_group_name` | STRING | The name of the ad group within the Google Ads campaign |
| `session_traffic_source_last_click.cross_channel_campaign` | RECORD |   |
| `session_traffic_source_last_click.cross_channel_campaign.campaign_id` | STRING | The ID of the last clicked cross-channel campaign |
| `session_traffic_source_last_click.cross_channel_campaign.campaign_name` | STRING | The name of the last clicked cross-channel campaign |
| `session_traffic_source_last_click.cross_channel_campaign.source` | STRING | The specific source within the platform of the last clicked cross-channel campaign |
| `session_traffic_source_last_click.cross_channel_campaign.medium` | STRING | The medium of the last clicked cross-channel campaign |
| `session_traffic_source_last_click.cross_channel_campaign.source_platform` | STRING | The platform of the last clicked cross-channel campaign |
| `session_traffic_source_last_click.cross_channel_campaign.default_channel_group` | STRING |   |
| `session_traffic_source_last_click.cross_channel_campaign.primary_channel_group` | STRING |   |
| `session_traffic_source_last_click.sa360_campaign` | RECORD |   |
| `session_traffic_source_last_click.sa360_campaign.campaign_id` | STRING | The ID of the last clicked SA360 campaign |
| `session_traffic_source_last_click.sa360_campaign.campaign_name` | STRING | The name of the last clicked SA360 campaign |
| `session_traffic_source_last_click.sa360_campaign.source` | STRING | The specific source within the platform of the last clicked SA360 campaign |
| `session_traffic_source_last_click.sa360_campaign.medium` | STRING | The medium of the last clicked SA360 campaign |
| `session_traffic_source_last_click.sa360_campaign.ad_group_id` | STRING | The ID of the ad group within the SA360 campaign |
| `session_traffic_source_last_click.sa360_campaign.ad_group_name` | STRING | The name of the ad group within the SA360 campaign |
| `session_traffic_source_last_click.sa360_campaign.creative_format` | STRING | The format of the creative of the last clicked SA360 campaign |
| `session_traffic_source_last_click.sa360_campaign.engine_account_name` | STRING | The name of the SA360 engine account |
| `session_traffic_source_last_click.sa360_campaign.engine_account_type` | STRING | The type of engine account containing the SA360 campaign |
| `session_traffic_source_last_click.sa360_campaign.manager_account_name` | STRING | The name of the SA360 manager account |
| `session_traffic_source_last_click.cm360_campaign` | RECORD |   |
| `session_traffic_source_last_click.cm360_campaign.campaign_id` | STRING | The ID of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.campaign_name` | STRING | The name of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.source` | STRING | The specific source of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.medium` | STRING | The medium of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.account_id` | STRING | The ID of the CM360 account |
| `session_traffic_source_last_click.cm360_campaign.account_name` | STRING | The name of the CM360 account |
| `session_traffic_source_last_click.cm360_campaign.advertiser_id` | STRING | The ID of the CM360 advertiser |
| `session_traffic_source_last_click.cm360_campaign.advertiser_name` | STRING | The name of the CM360 advertiser |
| `session_traffic_source_last_click.cm360_campaign.creative_id` | STRING | The ID of the creative of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.creative_format` | STRING | The format of the creative of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.creative_name` | STRING | The name of the creative of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.creative_type` | STRING | The creative type of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.creative_type_id` | STRING | The creative type ID of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.creative_version` | STRING | The creative version of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.placement_id` | STRING | The ID of the placement of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.placement_cost_structure` | STRING | The placement cost structure of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.placement_name` | STRING | The name of the placement of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.rendering_id` | STRING | The rendering ID of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.site_id` | STRING | The site ID of the last clicked CM360 campaign |
| `session_traffic_source_last_click.cm360_campaign.site_name` | STRING | The site name of the last clicked CM360 campaign |
| `session_traffic_source_last_click.dv360_campaign` | RECORD |   |
| `session_traffic_source_last_click.dv360_campaign.campaign_id` | STRING | The ID of the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.campaign_name` | STRING | The name of the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.source` | STRING | The specific source within the platform of the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.medium` | STRING | The medium of the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.advertiser_id` | STRING | The ID of the DV360 advertiser |
| `session_traffic_source_last_click.dv360_campaign.advertiser_name` | STRING | The name of the DV360 advertiser |
| `session_traffic_source_last_click.dv360_campaign.creative_id` | STRING | The ID of the creative of the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.creative_format` | STRING | The format of the creative of the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.creative_name` | STRING | The name of the creative of the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.exchange_id` | STRING | The exchange ID of the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.exchange_name` | STRING | The exchange name of the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.insertion_order_id` | STRING | The ID of the insertion order in the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.insertion_order_name` | STRING | The name of the insertion order in the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.line_item_id` | STRING | The ID of the line item in the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.line_item_name` | STRING | The name of the line item in the last clicked DV360 campaign |
| `session_traffic_source_last_click.dv360_campaign.partner_id` | STRING | The ID of the DV360 partner |
| `session_traffic_source_last_click.dv360_campaign.partner_name` | STRING | The name of the DV360 partner |

## Publisher

> **Note:**  
> These fields were added starting from January 1, 2024, and are not populated. [More details](https://www.owox.com/changelog/)

| Field name | Type | Description |
| --- | --- | --- |
| `publisher` | RECORD | The publisher RECORD contains information about events sourced from a publisher integration related to the display of ads, that is, AdMob. |
| `publisher.ad_revenue_in_usd` | STRING | Estimated ad revenue resulting from this event, represented in USD. |
| `publisher.ad_format` | STRING | Describes the way ads appeared and where they were located. |
| `publisher.ad_source_name` | STRING | The source network that served an ad. |
| `publisher.ad_unit_id` | STRING | The name you chose to describe this Ad unit. Ad units are containers that you place in your apps to show ads to users. |

## OWOX custom fields

| Field name | Type | Description |
| --- | --- | --- |
| `owox.event_id` | STRING | It includes the sequence number of how the event actually happened. [See how to use it](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/faq.md#how-to-get-sequence-number-of-the-event). |
| `owox.user_id` | STRING | OWOX BI user identifier taken from the custom cookie file ouid that contains a unique anonymous ID. |
| `owox.request_source` | STRING | The source of the incoming request to OWOX BI access point. Possible values: Measurement Protocol, Client Side. |
| `owox.is_bot` | BOOLEAN | The field would get a ‘true’ value if the event was generated by a bot. Or returns 'false' if a real user generates the event. |

## Debug

| Field name | Type | Description |
| --- | --- | --- |
| `debug_info` | RECORD |   |
| `debug_info.pipeline_id` | STRING | Unique ID of pipeline in OWOX BI. |
| `debug_info.request_id` | STRING | Unique ID of each request set by OWOX BI. May contain more than 1 event. |
| `debug_info.timezone` | STRING | The selected timezone in the pipeline. |
| `debug_info.raw_request` | STRING | Contains technical info. It helps OWOX BI specialists fix technical issues in case they occur. PII data were excluded according to Consent Mode. |
