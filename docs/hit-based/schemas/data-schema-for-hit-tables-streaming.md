# Data schema for hit tables ("streaming")

## Tables

Streamed data is separated into tables by days, according to a time zone of a view selected during the streaming setup.

The table naming is 'streaming\_YYYYMMDD'.

Hit data is available for processing within 2-5 minutes after sending.

A new table for collecting hits is created **24 hours before** the start of a new day. For example, on April 1 at 00:00, the 'streaming\_20220402' table is created to collect the hits that will be sent on April 2 between 00:00 and 23:59. This logic ensures a smooth collection of hits without loss.

> **Note!**  
> Never delete a new table for the next day.

## Rows

Each hit is written in a separate row.

## Columns

Tables contain all the fields that are sent using [The Enhanced Ecommerce plug-in](https://developers.google.com/analytics/devguides/collection/analyticsjs/enhanced-ecommerce) in Google Analytics.

| **Google BigQuery alias** | **Google BigQuery data type** | **Google Analytics dimension or metric** |
| --- | --- | --- |
| `hitId` | STRING | Hit identifier represented as UUID ([version 4](http://www.ietf.org/rfc/rfc4122.txt)) |
| `userId` | STRING | [User ID](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#uid) |
| `userPhone` | STRING | available only in Streaming, use parameter `&tel=%2B1234567890` |
| `userEmail` | STRING | available only in Streaming, use parameter `&email=mail%40example.com` |
| `userOwoxId` | STRING | Additional user identifier that lets you track audience overlapping across domains without sending the Client ID parameter. [Read more](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#collecting-owox-user-id) |
| `clientId` | STRING | [Client ID](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#cid) |
| `trackingId` | STRING | [Tracking ID / Web Property ID](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#tid) |
| `date` | STRING | [Date](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=time&jump=ga_date) |
| `traffic` | RECORD |   |
| `traffic.referralPath` | STRING | [Referral Path](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_referralpath) |
| `traffic.campaign` | STRING | [Campaign](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_campaign) |
| `traffic.source` | STRING | [Source](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_source) |
| `traffic.medium` | STRING | [Medium](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_medium) |
| `traffic.keyword` | STRING | [Keyword](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_keyword) |
| `traffic.adContent` | STRING | [Ad Content](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_adcontent) |
| `traffic.campaignId` | STRING | [Campaign ID](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#ci) |
| `traffic.gclid` | STRING | [Google Ads ID](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#gclid) |
| `traffic.dclid` | STRING | [Google Display Ads ID](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#dclid) |
| `device` | RECORD |   |
| `device.ip` | STRING | [IP Override](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#uip) |
| `device.userAgent` | STRING | [User Agent Override](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#ua) |
| `device.flashVersion` | STRING | [Flash Version](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=system&jump=ga_flashversion) |
| `device.javaEnabled` | BOOLEAN | [Java Support](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=system&jump=ga_javaenabled) |
| `device.language` | STRING | [Language](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=system&jump=ga_language) |
| `device.screenColors` | STRING | [Screen Colors](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=system&jump=ga_screencolors) |
| `device.screenResolution` | STRING | [Screen Resolution](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=system&jump=ga_screenresolution) |
| `geo` | RECORD |   |
| `geo.id` | STRING | [Geographical Override](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#geoid) |
| `geoNetwork` | RECORD | This section contains information about the geography of the user. |
| `geoNetwork.country` | STRING | The users' country, derived from their IP addresses. |
| `geoNetwork.countryIsoCode` | STRING | The user's country code. Example: US. |
| `geoNetwork.region` | STRING  | The users' region, derived from their IP addresses. |
| `geoNetwork.regionIsoCode` | STRING  | The region code in the user's country. Example: USUT. |
| `geoNetwork.city` | STRING  | The users' city, derived from their IP addresses. |
| `geoNetwork.latitude` | FLOAT  | The approximate latitude of the user's city. Derived from IP address. Locations north of the equator are represented by positive values and locations south of the equator by negative values. |
| `geoNetwork.longitude` | FLOAT  | The approximate longitude of the user's city. Derived from IP address. Locations east of the prime meridian are represented by positive values and locations west of the prime meridian by negative values. |
| `customDimensions` | RECORD |   |
| `customDimensions.index` | INTEGER | [Custom Dimension XX](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=custom_variables_or_columns&jump=ga_dimensionxx) |
| `customDimensions.value` | STRING | [Custom Dimension XX](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=custom_variables_or_columns&jump=ga_dimensionxx) |
| `customMetrics` | RECORD |   |
| `customMetrics.index` | INTEGER | [Custom Metric XX Value](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=custom_variables_or_columns&jump=ga_metricxx) |
| `customMetrics.value` | FLOAT | [Custom Metric XX Value](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=custom_variables_or_columns&jump=ga_metricxx) |
| `customGroups` | RECORD |   |
| `customGroups.index` | INTEGER | [Page Group XX](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=content_grouping&jump=ga_contentgroupxx) |
| `customGroups.value` | STRING | [Page Group XX](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=content_grouping&jump=ga_contentgroupxx) |
| `hour` | INTEGER | [Hour](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=time&jump=ga_hour) |
| `minute` | INTEGER | [Minute](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=time&jump=ga_minute) |
| `time` | INTEGER | The time on the server according to the time zone when the hit occurred (in seconds).<br> <br>**Note:**<br>If `hits.queueTime` not empty, then `hits.time` will contain the result of calculating the formula: (`hits.timestamp` - `hits.queueTime`) / 1000 in seconds.<br>For example, if you send hits from the call tracking system, then `hits.queueTime` will save the time in the queue, and `hits.timestamp` will save the time when the operator accepted the call. Therefore `hits.time` will get the time when the user made a call (calculated by the formula). |
| `timestamp` | INTEGER | Time, when OWOX BI access point receives the hit. Measured in milliseconds passed since January 1, 1970. |
| `queueTime` | INTEGER | [Queue Time](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#qt)<br>Used to collect offline / latent hits. |
| `isSecure` | BOOLEAN | [hits.isSecure](https://support.google.com/analytics/answer/3437719) |
| `isInteraction` | BOOLEAN | [Non-Interaction Hit](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#ni) |
| `currency` | STRING | [Currency Code](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_currencycode) |
| `referer` | STRING | [Full Referrer](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_fullreferrer) |
| `dataSource` | STRING | [Data Source](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=platform_or_device&jump=ga_datasource) |
| `social` | RECORD |   |
| `social.socialInteractionAction` | STRING | [Social Action](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=social_interactions&jump=ga_socialinteractionaction) |
| `social.socialInteractionNetwork` | STRING | [Social Source](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=social_interactions&jump=ga_socialinteractionnetwork) |
| `social.socialInteractionTarget` | STRING | [Social Entity](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=social_interactions&jump=ga_socialinteractiontarget) |
| `type` | STRING | [The type of hit](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#t) |
| `page` | RECORD |   |
| `page.pagePath` | STRING | [Page](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=page_tracking&jump=ga_pagepath) |
| `page.hostname` | STRING | [Hostname](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=page_tracking&jump=ga_hostname) |
| `page.pageTitle` | STRING | [Page Title](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=page_tracking&jump=ga_pagetitle) |
| `eCommerceAction` | RECORD |   |
| `eCommerceAction.action_type` | STRING | [Product Action](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pa) |
| `eCommerceAction.option` | STRING | [Checkout Options](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_checkoutoptions) |
| `eCommerceAction.step` | INTEGER | [Checkout Step](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#cos) |
| `eCommerceAction.list` | STRING | [Product Action List](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pal).<br>A list where action on product occurred (`product.isImpression=FALSE`). |
| `experiment` | RECORD | Content experiments. The value can be submitted in several ways:<br>-   using parameter _exp_, where the _experiment id_ and _variation id_ are defined and separated with a dot (_exp=4KxxxFg.1_);
-   using parameters _expid_ and _expvar_, where the _experiment id_ and _variation id_ are defined (_expid=4KxxxFg&expvar=1_);
-   using parameters [_xid_ and _xvar_](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters?hl=ru#experiments), where the _experiment id_ and _variation id_ are defined (_xid=4KxxxFg&xvar=1_);
-   using parameter _utm\_expid_ in URL, where the _experiment id_ is defined (_https:/site.com/xxx/?utm\_expid=4KxxxFg_);
-   using parameter _utm\_expid_ in URL, where the _experiment id_ and _variation id_ are defined (_https:/site.com/xxx/?utm\_expid=.4KxxxFg.1_, where _4KxxxFg is experiment id_ and _1_ is _variation id_). Applied automatically in Google Optimize. |
| `experiment.experimentId` | STRING | [Experiment ID](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#xid) |
| `experiment.experimentVariant` | STRING | [Experiment variant](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#xvar) |
| `product` | RECORD |   |
| `product.isImpression` | BOOLEAN | [hits.product.isImpression](https://support.google.com/analytics/answer/3437719) |
| `product.impressionList` | STRING | [Product List Name](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productlistname).<br>A list where a product was shown (`product.isImpression=TRUE`). |
| `product.productBrand` | STRING | [Product Brand](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productbrand) |
| `product.productPrice` | FLOAT | [Product Revenue](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_itemrevenue) |
| `product.productQuantity` | INTEGER | [Quantity](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_itemquantity) |
| `product.productSku` | STRING | [Product SKU](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productsku) |
| `product.productVariant` | STRING | [Product Variant](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productvariant) |
| `product.productCategory` | STRING | [Product Category](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productcategory) |
| `product.productName` | STRING | [Product](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productname) |
| `product.position` | INTEGER | [Product List Position](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productlistposition) |
| `product.coupon` | STRING | [Product Coupon Code](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productcouponcode) |
| `product.customDimensions` | RECORD |   |
| `product.customDimensions.index` | INTEGER | [Product Custom Dimension](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pr_cd_) |
| `product.customDimensions.value` | STRING | [Product Custom Dimension](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pr_cd_) |
| `product.customMetrics` | RECORD |   |
| `product.customMetrics.index` | INTEGER | [Product Custom Metric](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pr_cm_) |
| `product.customMetrics.value` | STRING | [Product Custom Metric](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pr_cm_) |
| `promotion` | RECORD |   |
| `promotion.promoCreative` | STRING | [Internal Promotion Creative](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_internalpromotioncreative) |
| `promotion.promoId` | STRING | [Internal Promotion ID](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_internalpromotionid) |
| `promotion.promoName` | STRING | [Internal Promotion Name](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_internalpromotionname) |
| `promotion.promoPosition` | STRING | [Internal Promotion Position](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_internalpromotionposition) |
| `promotionActionInfo` | STRING | [Promotion Action](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#promoa) |
| `transaction` | RECORD |   |
| `transaction.transactionId` | STRING | [Transaction ID](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_transactionid) |
| `transaction.transactionRevenue` | FLOAT | [Revenue](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_transactionrevenue) |
| `transaction.transactionTax` | FLOAT | [Tax](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_transactiontax) |
| `transaction.transactionShipping` | FLOAT | [Shipping](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_transactionshipping) |
| `transaction.transactionCoupon` | STRING | [Order Coupon Code](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_ordercouponcode) |
| `transaction.affiliation` | STRING | [Affiliation](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_affiliation) |
| `contentInfo` | RECORD |   |
| `contentInfo.contentDescription` | STRING | [Screen Name](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=app_tracking&jump=ga_screenname) |
| `appInfo` | RECORD |   |
| `appInfo.name` | STRING | [App Name](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=app_tracking&jump=ga_appname) |
| `appInfo.version` | STRING | [App Version](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=app_tracking&jump=ga_appversion) |
| `appInfo.id` | STRING | [App ID](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=app_tracking&jump=ga_appid) |
| `appInfo.installerId` | STRING | [App Installer ID](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=app_tracking&jump=ga_appinstallerid) |
| `exceptionInfo` | RECORD |   |
| `exceptionInfo.description` | STRING | [Exception Description](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=exceptions&jump=ga_exceptiondescription) |
| `exceptionInfo.isFatal` | BOOLEAN | [Is Exception Fatal?](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#exf) |
| `eventInfo` | RECORD |   |
| `eventInfo.eventCategory` | STRING | [Event Category](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=event_tracking&jump=ga_eventcategory) |
| `eventInfo.eventAction` | STRING | [Event Action](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=event_tracking&jump=ga_eventaction) |
| `eventInfo.eventLabel` | STRING | [Event Label](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=event_tracking&jump=ga_eventlabel) |
| `eventInfo.eventValue` | STRING | [Event Value](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=event_tracking&jump=ga_eventvalue) |
| `timingInfo` | RECORD |   |
| `timingInfo.timingCategory` | STRING | [Timing Category](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=user_timings&jump=ga_usertimingcategory)<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `timingInfo.timingVariable` | STRING | [Timing Variable](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=user_timings&jump=ga_usertimingvariable)<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `timingInfo.timingLabel` | STRING | [Timing Label](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=user_timings&jump=ga_usertiminglabel)<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `timingInfo.timingValue` | INTEGER | [User Timing](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=user_timings&jump=ga_usertimingvalue)<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `timingInfo.pageLoad` | INTEGER | [Page Load Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_pageloadtime)<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `timingInfo.DNS` | INTEGER | [Domain Lookup Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_domainlookuptime)<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `timingInfo.pageDownload` | INTEGER | [Page Download Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_pagedownloadtime)<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `timingInfo.redirectResponse` | INTEGER | [Redirection Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_redirectiontime)<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `timingInfo.TCPConnect` | INTEGER | [Server Connection Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_serverconnectiontime)<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `timingInfo.serverResponse` | INTEGER | [Server Response Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_serverresponsetime)<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `timingInfo.DOMInteractive` | INTEGER | [Document Interactive Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_dominteractivetime)<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `timingInfo.contentLoad` | INTEGER | [Document Content Loaded Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_domcontentloadedtime)<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `privacy_info` | RECORD | Added: October 4, 2022. [Read more in the changelog](https://www.owox.com/changelog/). |
| `privacy_info.ads_storage` | STRING | Whether ad targeting is enabled for a user. Possible values: Yes, No, null. |
| `privacy_info.analytics_storage` | STRING | Whether Analytics storage is enabled for a user. Possible values: Yes, No, null. |
| `privacy_info.uses_transient_token` | STRING | Whether a web user has denied Analytics storage and the developer has enabled measurement without cookies based on transient tokens in server data. Possible values: Yes, No, null. |

>  Tables contain repeated records. When selecting data, tables will be flattened over nested fields.
