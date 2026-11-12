# Schema for session data tables based on Google Analytics data ("session_streaming")

> **Important!**  
> This algorithm works with **Google Analytics Universal** data format only.

## Tables

Streamed data is separated into tables by days, according to a time zone of a Google Analytics view selected during streaming setup.

Tables naming is 'session\_streaming\_YYYYMMDD'.

Session data are generated within 24 hours (for example data for 13.04.2016 will be available not earlier than 14.04.2016).

## Rows

Each session is written to a separate row.

## Columns

The columns within the table are listed below.

| **Google BigQuery alias** | **Google BigQuery data type** | **Google Analytics dimension or metric** |
| --- | --- | --- |
| `user` | RECORD |   |
| `user.Id` | STRING | [User ID](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#collecting-and-updating-user-id-useremail-and-userphone-data) |
| `user.phone` | STRING | available only in Streaming, use parameter `&tel=%2B1234567890` |
| `user.email` | STRING | available only in Streaming, use parameter `&email=mail%40example.com` |
| `clientId` | STRING | [Client ID](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#cid) |
| `date` | STRING | Record date in "YYYY-MM-DD" format |
| `sessionId` | STRING | Session ID (value of custom dimension chosen on «Settings» tab of your Streaming pipeline) |
| `visitNumber` | INTEGER | [Count of Sessions](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=user&jump=ga_sessioncount) |
| `newVisits` | INTEGER | Visitor type(1 - new || NULL - returned) |
| `device` | RECORD |   |
| `device.deviceCategory` | STRING | [deviceCategory](https://developers.google.com/analytics/devguides/reporting/realtime/dimsmets/platform#rt:deviceCategory) |
| `device.browser` | STRING | [browser](https://developers.google.com/analytics/devguides/reporting/realtime/dimsmets/platform#rt:browser) |
| `device.browserVersion` | STRING | [browserVersion](https://developers.google.com/analytics/devguides/reporting/realtime/dimsmets/platform#rt:browserVersion) |
| `device.operatingSystem` | STRING | [operatingSystem](https://developers.google.com/analytics/devguides/reporting/realtime/dimsmets/platform#rt:operatingSystem) |
| `device.operatingSystemVersion` | STRING | [operatingSystemVersion](https://developers.google.com/analytics/devguides/reporting/realtime/dimsmets/platform#rt:operatingSystemVersion) |
| `device.mobileDeviceInfo` | STRING | Data from mobileDeviceBranding, mobileDeviceModel и mobileDeviceMarketingName. |
| `geoNetwork` | RECORD |   |
| `geoNetwork.city` | STRING | [city](https://developers.google.com/analytics/devguides/reporting/realtime/dimsmets/geonetwork#rt:city) |
| `geoNetwork.region` | STRING | [region](https://developers.google.com/analytics/devguides/reporting/realtime/dimsmets/geonetwork#rt:region) |
| `geoNetwork.country` | STRING | [country](https://developers.google.com/analytics/devguides/reporting/realtime/dimsmets/geonetwork#rt:country) |
| `trafficSource` | RECORD |   |
| `trafficSource.source` | STRING | [Source](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_source) |
| `trafficSource.medium` | STRING | [Medium](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_medium) |
| `trafficSource.campaign` | STRING | [Campaign](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_campaign) |
| `trafficSource.keyword` | STRING | [Keyword](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_keyword) |
| `trafficSource.adContent` | STRING | [adContent](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_adcontent) |
| `trafficSource.channelGrouping` | STRING | [channelGrouping](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=channel_grouping&jump=ga_channelgrouping) |
| `trafficSource.adwordsClickInfo` | RECORD |   |
| `trafficSource.adwordsClickInfo.adNetworkType` | STRING | Ad Distribution Network ([adDistributionNetwork](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=adwords&jump=ga_addistributionnetwork)) |
| `trafficSource.adwordsClickInfo.adMatchedQuery` | STRING | Search Query ([adMatchedQuery](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=adwords&jump=ga_admatchedquery)) |
| `trafficSource.adCost` | FLOAT | The total sessions cost attributed evenly between sessions according to the UTM tags 'source', 'medium', 'campaign', 'keyword', and 'content' if the tags perfectly match in all sessins |
| `trafficSource.attributedAdCost` | FLOAT | When one or more tags are unknown, the total session cost is attributed evenly between the sessions according to the known tags<br>[More on attributed ad cost](https://github.com/OWOX/transformations-plugin/blob/main/docs/templates/costs-attribution.md#calculation-examples-for-the-adcost-and-attributedadcost-fields) |
| `totals` | RECORD | All totals data is taken from GA |
| `totals.hits` | INTEGER | Total number of hits by the session |
| `totals.pageviews` | INTEGER | Total number of pageviews by the session |
| `totals.events` | INTEGER | Total number of events by the session |
| `totals.transactions` | INTEGER | Total number of transactions by the session |
| `totals.visits` | INTEGER | The total number of visits by the session |
| `totals.screenviews` | INTEGER | Total number of screenviews by the session |
| `totalsStreaming` | RECORD | All streaming totals data is taken from Hit Streaming |
| `totalsStreaming.hits` | INTEGER | `SUM(rows)` |
| `totalsStreaming.events` | INTEGER | `SUM(rows) where type='event'` |
| `totalsStreaming.transactions` | INTEGER | `SUM(rows) where action='purchase'` |
| `totalsStreaming.pageviews` | INTEGER | `SUM(rows) where type='pageview'` |
| `totalsStreaming.screenviews` | INTEGER | `SUM(rows) where type='screenview'` |
| `hits` | RECORD |   |
| `hits.hitId` | STRING | Hit identifier represented as UUID ([version 4](http://www.ietf.org/rfc/rfc4122.txt)) |
| `hits.isEntrance` | INTEGER | The first pageview or screenview hit in the session (1 || NULL) |
| `hits.isExit` | INTEGER | The last pageview or screenview hit in the sesiion (1 || NULL) |
| `hits.time` | INTEGER | Hits time |
| `hits.type` | STRING | [The type of hit](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#t) |
| `hits.pagePath` | STRING | The field is deprecated, please use `hits.page.pagePath` instead of it. |
| `hits.pageType` | STRING | TThe field is deprecated, please use `hits.page.pageType` instead of it. |
| `hits.eCommerceActionType` | STRING | The field is deprecated, please use `hits.eCommerceAction.action_type` instead of it. |
| `hits.isInteraction` | BOOLEAN | [Non-Interaction Hit](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#ni) |
| `hits.currency` | STRING | [Currency Code](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_currencycode) |
| `hits.referer` | STRING | [Full Referrer](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=traffic_sources&jump=ga_fullreferrer) |
| `hits.dataSource` | STRING | [Data Source](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=platform_or_device&jump=ga_datasource) |
| `hits.device` | RECORD |   |
| `hits.device.ip` | STRING | [IP Override](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#uip) |
| `hits.contentGroups` | RECORD |   |
| `hits.contentGroups.index` | INTEGER | [Page Group XX](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=content_grouping&jump=ga_contentgroupxx) |
| `hits.contentGroups.value` | STRING | [Page Group XX](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=content_grouping&jump=ga_contentgroupxx) |
| `hits.social` | RECORD |   |
| `hits.social.socialInteractionAction` | STRING | [Social Action](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=social_interactions&jump=ga_socialinteractionaction) |
| `hits.social.socialInteractionNetwork` | STRING | [Social Source](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=social_interactions&jump=ga_socialinteractionnetwork) |
| `hits.social.socialInteractionTarget` | STRING | [Social Entity](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=social_interactions&jump=ga_socialinteractiontarget) |
| `hits.page` | RECORD |   |
| `hits.page.pageType` | STRING | [Content grouping](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=content_grouping) or [a hit scope custom dimension](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=custom_variables_or_columns&jump=ga_dimensionxx) (to collect the data set a parameter on Page type parameter on «Settings» tab of your Streaming pipeline) |
| `hits.page.pagePath` | STRING | [Page](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=page_tracking&jump=ga_pagepath) |
| `hits.page.hostname` | STRING | [Hostname](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=page_tracking&jump=ga_hostname) |
| `hits.page.pageTitle` | STRING | [Page Title](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=page_tracking&jump=ga_pagetitle) |
| `hits.eCommerceAction` | RECORD |   |
| `hits.eCommerceAction.action_type` | STRING | [Product Action](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pa) |
| `hits.eCommerceAction.option` | STRING | [Checkout Options](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_checkoutoptions) |
| `hits.eCommerceAction.step` | INTEGER | [Checkout Step](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#cos) |
| `hits.eCommerceAction.list` | STRING | [Product Action List](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pal).<br>A list where an action on product occurred (`product.isImpression=FALSE`). |
| `hits.experiment` | RECORD | Content experiments |
| `hits.experiment.experimentId` | STRING | [Experiment ID](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#xid) |
| `hits.experiment.experimentVariant` | STRING | [Experiment variant](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#xvar) |
| `hits.product` | RECORD |   |
| `hits.product.isImpression` | BOOLEAN | [hits.product.isImpression](https://support.google.com/analytics/answer/3437719) |
| `hits.product.impressionList` | STRING | [Product List Name](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productlistname).<br>A list where a product was shown (`product.isImpression=TRUE`). |
| `hits.product.productListName` | STRING | [productListName](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productlistname) |
| `hits.product.productBrand` | STRING | [Product Brand](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productbrand) |
| `hits.product.productSku` | STRING | [Product SKU](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productsku) |
| `hits.product.productVariant` | STRING | [Product Variant](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productvariant) |
| `hits.product.productPrice` | FLOAT | [Product Price](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_itemrevenue) |
| `hits.product.localProductPrice` | FLOAT | Local Product Price converted via [Yahoo Currecy Converter](http://finance.yahoo.com/currency-converter/) |
| `hits.product.productQuantity` | INTEGER | [Quantity](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_itemquantity) |
| `hits.product.productCategory` | STRING | [Product Category](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productcategory) |
| `hits.product.productName` | STRING | [Product](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productname) |
| `hits.product.coupon` | STRING | [Product Coupon Code](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_productcouponcode) |
| `hits.promotion` | RECORD |   |
| `hits.promotion.promoCreative` | STRING | [Internal Promotion Creative](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_internalpromotioncreative) |
| `hits.promotion.promoId` | STRING | [Internal Promotion ID](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_internalpromotionid) |
| `hits.promotion.promoName` | STRING | [Internal Promotion Name](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_internalpromotionname) |
| `hits.promotion.promoPosition` | STRING | [Internal Promotion Position](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_internalpromotionposition) |
| `hits.promotionActionInfo` | STRING | [Promotion Action](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#promoa) |
| `hits.transaction` | RECORD |   |
| `hits.transaction.transactionId` | STRING | [Transaction ID](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_transactionid) |
| `hits.transaction.transactionRevenue` | FLOAT | [Revenue](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_transactionrevenue) |
| `hits.transaction.localTransactionRevenue` | FLOAT | [Local Revenue](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_localtransactionrevenue) converted via [Yahoo Currecy Converter](http://finance.yahoo.com/currency-converter/) |
| `` `hits.` ```transaction.transactionTax` | FLOAT | [Tax](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_hits.transactiontax) |
| `hits.transaction.localTransactionTax` | FLOAT | [Local Tax](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_localtransactiontax) converted via [Yahoo Currecy Converter](http://finance.yahoo.com/currency-converter/) |
| `` `hits.` ```transaction.transactionShipping` | FLOAT | [Shipping](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_hits.transactionshipping) |
| `hits.transaction.localTransactionShipping` | FLOAT | [Local Shipping](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_localtransactionshipping) converted via [Yahoo Currecy Converter](http://finance.yahoo.com/currency-converter/) |
| `` `hits.` ```transaction.transactionCoupon` | STRING | [Order Coupon Code](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_ordercouponcode) |
| `hits.transaction.affiliation` | STRING | [Affiliation](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=ecommerce&jump=ga_affiliation) |
| `hits.contentInfo` | RECORD |   |
| `hits.contentInfo.contentDescription` | STRING | [Screen Name](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=app_tracking&jump=ga_screenname) |
| `hits.appInfo` | RECORD |   |
| `hits.appInfo.name` | STRING | [App Name](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=app_tracking&jump=ga_appname) |
| `hits.appInfo.version` | STRING | [App Version](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=app_tracking&jump=ga_appversion) |
| `hits.appInfo.id` | STRING | [App ID](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=app_tracking&jump=ga_appid) |
| `hits.appInfo.installerId` | STRING | [App Installer ID](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=app_tracking&jump=ga_appinstallerid) |
| `hits.eventInfo` | RECORD |   |
| `hits.eventInfo.eventCategory` | STRING | [Event Category](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=event_tracking&jump=ga_eventcategory) |
| `hits.eventInfo.eventAction` | STRING | [Event Action](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=event_tracking&jump=ga_eventaction) |
| `hits.eventInfo.eventLabel` | STRING | [Event Label](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=event_tracking&jump=ga_eventlabel) |
| `hits.eventInfo.eventValue` | INTEGER | [Event Value](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=event_tracking&jump=ga_eventvalue) |
| `hits.timingInfo` | RECORD |   |
| `hits.timingInfo.timingCategory` | STRING | [Timing Category](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=user_timings&jump=ga_usertimingcategory) |
| `hits.timingInfo.timingVariable` | STRING | [Timing Variable](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=user_timings&jump=ga_usertimingvariable) |
| `hits.timingInfo.timingLabel` | STRING | [Timing Label](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=user_timings&jump=ga_usertiminglabel) |
| `hits.timingInfo.timingValue` | INTEGER | [User Timing](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=user_timings&jump=ga_usertimingvalue) |
| `hits.timingInfo.pageLoad` | INTEGER | [Page Load Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_pageloadtime) |
| `hits.timingInfo.DNS` | INTEGER | [Domain Lookup Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_domainlookuptime) |
| `hits.timingInfo.pageDownload` | INTEGER | [Page Download Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_pagedownloadtime) |
| `hits.timingInfo.redirectResponse` | INTEGER | [Redirection Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_redirectiontime) |
| `hits.timingInfo.TCPConnect` | INTEGER | [Server Connection Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_serverconnectiontime) |
| `hits.timingInfo.serverResponse` | INTEGER | [Server Response Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_serverresponsetime) |
| `hits.timingInfo.DOMInteractive` | INTEGER | [Document Interactive Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_dominteractivetime) |
| `hits.timingInfo.contentLoad` | INTEGER | [Document Content Loaded Time](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=site_speed&jump=ga_domcontentloadedtime) |

>  Tables contain repeated records. When selecting data, tables will be flattened over nested fields.
