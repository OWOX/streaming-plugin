# Schema for session data tables collected with the OWOX BI algorithm ("owoxbi_sessions")

> **Important!**  
> This algorithm works with **Google Analytics Universal** data format only.

## Tables

Streamed data [is formed based on the OWOX BI algorithm](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#session-data-collection-based-on-the-owox-bi-algorithm) and separated into tables by days according to a time zone of a Google Analytics view selected during the streaming setup. Each table has a unique name in the format _'owoxbi\_sessions\_YYYYMMDD'_.

[Read more about session data collection and update times](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#session-data-collection-based-on-the-owox-bi-algorithm)

## Rows

Each session is written to a separate row.

## Columns

The columns within the table are listed below.

| **Google BigQuery alias** | **Google BigQuery data type** | **Description** |
| --- | --- | --- |
| `user` | RECORD | This section contains user info |
| `user.id` | STRING | Overridden User ID |
| `user.phone` | STRING | The user phone number. Available only in OWOX BI tables, use parameter &tel |
| `user.email` | STRING | The user email. Available only in OWOX BI tables, use parameter &email. |
| `user.owoxId` | STRING | Additional user identifier that lets you track audience overlapping across domains without sending the Client ID parameter.<br>[Read more](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#collecting-owox-user-id) |
| `clientId` | STRING | This anonymously identifies a particular user, device, or browser instance. For the web, this is generally stored as a first-party cookie with a two-year expiration. For mobile apps, this is randomly generated for each particular instance of an application install. |
| `date` | STRING | Record date in "YYYY-MM-DD" format |
| `sessionId` | STRING | An identifier for this session. Formed from the clientId and timestamp values. |
| `visitNumber` | INTEGER | The session number of this user over the last six months. If it is the first session, then this field is set to 1. [Learn more](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#session-data-collection-based-on-the-owox-bi-algorithm) |
| `newVisits` | INTEGER | The total number of new users in the session. If this is the first visit, this value is 1, otherwise null |
| `landingPage` | STRING | A URL address of the first hit in the session without any query parameters such as UTM tags. This field helps unite the same landing pages from the sessions with different sources. |
| `device` | RECORD | This section contains information about the user devices |
| device.deviceCategory | STRING | The type of the user's device:<br>\-tv;<br>\- game console;<br>\- robot;<br>\- set-top box;<br>\- robot mobile;<br>\- unknown;<br>\- hacker;<br>\- virtual reality;<br>\- anonymizer<br>[Full list and definitions](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#the-definitions-of-device-categories-in-the-owoxbisessions-tables) |
| `device.browser` | STRING | The browser used |
| `device.browserVersion` | STRING | The version of the browser used |
| `device.operatingSystem` | STRING | The operating system of the device |
| `device.operatingSystemVersion` | STRING | The version of the operating system |
| `device.mobileDeviceInfo` | STRING | The branding, model, and marketing name used to identify the mobile device |
| `device.ip` | STRING | [IP Override](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#uip)<br>User IP address |
| `device.userAgent` | STRING | [User Agent Override](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#ua)<br>The User Agent of the browser |
| `device.isBot` | BOOLEAN | 'true' if the session is started by a bot.<br>'false' if the session is started by a real user |
| `device.language` | STRING | [Language](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=system&jump=ga_language)<br>Language in the ISO-639 format |
| `geoNetwork` | RECORD | This section contains information about the geography of the user |
| `geoNetwork.country` | STRING | The country from which sessions originated, based on IP address |
| `geoNetwork.countryIsoCode` | STRING | Country ISO code |
| `geoNetwork.region` | STRING | The region from which sessions originate, derived from IP addresses |
| `geoNetwork.regionIsoCode` | STRING | Region ISO code |
| `geoNetwork.city` | STRING | Users' city, derived from their IP addresses |
| `geoNetwork.latitude` | FLOAT | The approximate latitude of the user's city. Derived from IP address. Locations north of the equator are represented by positive values and locations south of the equator by negative values. |
| `geoNetwork.longitude` | FLOAT | The approximate longitude of the user's city. Derived from IP address. Locations east of the prime meridian are represented by positive values and locations west of the prime meridian by negative values. |
| `trafficSource` | RECORD | This section contains information about the Traffic Source from which the session originated |
| `trafficSource.channelGrouping` | STRING | The Default Channel Group associated with an end user's session.<br>This field is null for now. |
| `trafficSource.source` | STRING | The source of the traffic source. Could be the name of the search engine, the referring hostname, or a value of the utm\_source URL parameter. |
| `trafficSource.medium` | STRING | The medium of the traffic source from the utm\_medium URL parameter. Could be "organic", "cpc", "referral" etc. |
| `trafficSource.campaign` | STRING | The campaign value. Usually set by the utm\_campaign URL parameter. |
| `trafficSource.keyword` | STRING | The keyword of the traffic source, usually set when the trafficSource.medium is "organic" or "cpc". Can be set by the utm\_term URL parameter. For Google Ads campaigns may be a descriptive string for the targeting criterion. |
| `trafficSource.adContent` | STRING | The ad content of the traffic source. Can be set by the utm\_content URL parameter. |
| `trafficSource.adGroup` | STRING | The value of an ad group for one or more ads that target a shared set of keywords |
| `trafficSource.keywordMatchType` | STRING | The type of searching trigger that controls how your ads will appear |
| `trafficSource.adwordsClickInfo` | RECORD | This section will contain information about the Google Ads click info if there is any associated with this session.<br>This field is null for now. |
| `trafficSource.adwordsClickInfo.placementDomain` | STRING | Shows where was placed the Google Ads advertisement that started the session.<br>This field is null for now. |
| `trafficSource.adwordsClickInfo.adNetworkType` | STRING | The network (Content, Search, Search partners, etc.) used to deliver the ads.<br>This field is null for now. |
| `trafficSource.adwordsClickInfo.adMatchedQuery` | STRING | Currently unavailable. The match type (Phrase, Exact, Broad, etc.) applied for users' search term. Ads on the content network are identified as "Content network".<br>This field is null for now. |
| `trafficSource.gclid` | STRING | Specifies the Google Ads Id |
| `trafficSource.dclid` | STRING | Specifies the Google Display Ads Id |
| `trafficSource.isTrueDirect` | BOOLEAN | Possible values: _null, true_.<br>The field is assigned _true_ if the source of the session was Direct, e.g.:<br>-   a user typed a website URL into a browser;
-   a user came to a website via a bookmark. 
<br>**Note:**<br>If a user first arrives on a website through the paid traffic source, leaves opened a page, and returns to the same page when the first [session has timed out](https://support.google.com/analytics/answer/2731565?hl=en#time-based-expiration&zippy=%2C%D1%81%D0%BE%D0%B4%D0%B5%D1%80%D0%B6%D0%B0%D0%BD%D0%B8%D0%B5%2Cin-this-article), then a new session will start. And the `trafficSource.isTrueDirect` field will be _true_ for this new session. |
| `trafficSource.adCost` | FLOAT | The total sessions cost is attributed evenly between sessions according to the UTM tags 'source', 'medium', 'campaign', 'keyword', and 'content' if the tags perfectly match in all sessions.<br>The value from the _adCost_ field will also be assigned to the _attributedAdCost_ field for these sessions. [See the example](https://github.com/OWOX/transformations-plugin/blob/main/docs/templates/costs-attribution.md#calculation-examples-for-the-adcost-and-attributedadcost-fields) |
| `trafficSource.attributedAdCost` | FLOAT | When one or more tags are unknown, the total session cost is attributed evenly between the sessions according to the known tags. <br>If some value has already been assigned to such sessions (for example, by assigning a value from the _[adCost](https://github.com/OWOX/transformations-plugin/blob/main/docs/templates/costs-attribution.md#calculation-examples-for-the-adcost-and-attributedadcost-fields)_ field), the new value will be added to the existing one. [See the example](https://github.com/OWOX/transformations-plugin/blob/main/docs/templates/costs-attribution.md#calculation-examples-for-the-adcost-and-attributedadcost-fields) |
| `totals` | RECORD | Contains nested fields with aggregated session data. Analog to the totalsStreaming field |
| `totals.hits` | INTEGER | The total number of hits within the session. Analog to the totalsStreaming.hits field. The field values may differ from the values in the "[session\_streaming](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/schemas/data-schema-for-hit-tables-streaming.md)" tables |
| `totals.events` | INTEGER | The total number of events within the session. Analog to the totalsStreaming.events field. The field values may differ from the values in the "session\_streaming" tables |
| `totals.transactions` | INTEGER | The total number of ecommerce transactions within the session. Analog to thetotalsStreaming.transactions field. The field values may differ from the values in the "session\_streaming" tables |
| `totals.pageviews` | INTEGER | The total number of pageviews within the session. Analog to the totalsStreaming.pageviews field. The field values may differ from the values in the "session\_streaming" tables |
| `totals.screenviews` | INTEGER | The total number of screenviews within the session. Analog to the Streaming.screenview field. The field values may differ from the values in the "session\_streaming" tables |
| `totals.isInteraction` | BOOLEAN | This value is TRUE for sessions with interaction events. The value is FALSE if there are no interaction events in the session.<br>Analog to the totalsStreaming.isInteraction field. The field values may differ from the values in the "session\_streaming" tables |
| `totals.visits` | INTEGER | For the sessions with interactions, the value equals 1 (isInteraction = true). Otherwise – 0.<br>The field values may differ from the values in the "session\_streaming" tables |
| `totalsStreaming` | RECORD | Contains nested fields with aggregated session data |
| `totalsStreaming.hits` | INTEGER | Total number of hits within the session |
| `totalsStreaming.events` | INTEGER | Total number of events within the session |
| `totalsStreaming.transactions` | INTEGER | Total number of ecommerce transactions within the session |
| `totalsStreaming.pageviews` | INTEGER | Total number of pageviews within the session |
| `totalsStreaming.screenviews` | INTEGER | Total number of screenviews within the session |
| `totalsStreaming.isInteraction` | BOOLEAN | This value is TRUE for sessions with interaction events. The value is FALSE if there are no interaction events in the session. |
| `customDimensions` | RECORD | This section contains any user-level or session-level custom dimensions that are set for a session. This is a repeated field and has an entry for each dimension that is set.<br>This field is null for now. |
| `customDimensions.index` | INTEGER | The index of the custom dimension.<br>This field is null for now. |
| `customDimensions.value` | STRING | The value of the custom dimension.<br>This field is null for now. |
| `hits` | RECORD | This row and nested fields are populated for any and all types of hits |
| `hits.hitId` | STRING | An identifier for this hit |
| `hits.isEntrance` | INTEGER | If this hit was the first pageview or screenview hit of a session, this is set to 1 |
| `hits.isExit` | INTEGER | If this hit was the last pageview or screenview hit of a session, this is set to 1 |
| `hits.time` | INTEGER | The time on the server according to the time zone when the hit occurred (in seconds).<br> <br>**Note:**<br>If `hits.queueTime` not empty, then `hits.time` will contain the result of calculating the formula: (`hits.timestamp` - `hits.queueTime`) / 1000 in seconds.<br>For example, if you send hits from the call tracking system, then `hits.queueTime` will save the time in the queue, and `hits.timestamp` will save the time when the operator accepted the call. Therefore `hits.time` will get the time when the user made a call (calculated by the formula). |
| `hits.timestamp` | INTEGER | Time, when OWOX BI access point receives the hit. Measured in milliseconds passed since January 1, 1970. |
| `hits.queueTime` | INTEGER | [Queue Time](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#qt)<br>Used to collect offline / latent hits. |
| `hits.hour` | INTEGER | The hour in which the hit occurred (0 to 23) |
| `hits.minute` | INTEGER | The minute in which the hit occurred (0 to 59) |
| `hits.type` | STRING | [The type of hit](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#t) |
| `hits.isSecure` | BOOLEAN | This field is deprecated |
| `hits.pagePath` | STRING | Analog to the hits.page.pagePath field |
| `hits.pageType` | STRING | Analog to the hits.page.pageType field |
| `hits.eCommerceActionType` | STRING | Analog to the hits.eCommerceAction.action\_type field |
| `hits.isInteraction` | BOOLEAN | If this hit was an interaction, this is set to true. If this was a non-interaction hit (i.e., an event with interaction set to false), this is false. |
| `hits.currency` | STRING | The code of local currency for all transaction currency values |
| `hits.referer` | STRING | The referring page, if the session has a goal completion or transaction. If this page is from the same domain, this is blank. |
| `hits.referralPath` | STRING | The path of the referring URL (e.g., document.referrer). If someone places on their webpage a link to the property, this is the path of the page containing the referring link. |
| `hits.dataSource` | STRING | The data source of a hit. By default, hits sent from analytics.js are reported as "web" and hits sent from the mobile SDKs are reported as "app". |
| `hits.device` | RECORD | This section contains information about the user devices |
| `hits.device.ip` | STRING | The device IP address |
| `hits.device.userAgent` | STRING | The User Agent of the browser |
| `hits.device.flashVersion` | STRING | The version of Flash, including minor versions, supported by users' browsers |
| `hits.device.javaEnabled` | BOOLEAN | A boolean, either Yes or No, indicating whether Java is enabled in users' browsers |
| `hits.device.language` | STRING | The language, in ISO-639 code format (e.g., en-gb for British English), provided by the HTTP Request for the browser |
| `hits.device.screenColors` | STRING | The color depth of users' monitors, retrieved from the DOM of users' browsers. For example, 4-bit, 8-bit, 24-bit, or undefined-bit. |
| `hits.device.screenResolution` | STRING | Resolution of users' screens, for example, 1024x738 |
| `hits.geo` | RECORD | Contains nested fields |
| `hits.geo.id` | STRING | he geographical location of the user. The geographical ID should be a two letter country code or a criteria ID representing a city or region. This parameter takes precedent over any location derived from IP address, including the IP Override parameter. An invalid code will result in geographical dimensions to be set to '(not set)'. |
| `hits.customGroups` | RECORD | [Page Group XX<br>](https://developers.google.com/analytics/devguides/reporting/core/dimsmets#view=detail&group=content_grouping&jump=ga_contentgroupxx)Contains nested fields with content group on a property |
| `hits.customGroups.index` | INTEGER | The content group index on a property |
| `hits.customGroups.value` | STRING | The content group value on a property |
| `hits.contentGroups` | RECORD | Analog to the hits.customGroups field |
| `hits.contentGroups.index` | INTEGER | Analog to the hits.customGroups.index field |
| `hits.contentGroups.value` | STRING | Analog to the hits.customGroups.value field |
| `hits.social` | RECORD | This section is populated for each hit with type = "social" |
| `hits.social.socialInteractionAction` | STRING | The social action passed with the social tracking code (Share, Tweet, etc.) |
| `hits.social.socialInteractionNetwork` | STRING | The the network passed with the social tracking code (Google+, Twitter, etc.) |
| `hits.social.socialInteractionTarget` | STRING | For social interactions, this is the URL (or resource) which receives the social network action |
| `hits.page` | RECORD | This section is populated for each hit with type = pageview |
| `hits.page.pagePath` | STRING | The URL path of the page |
| `hits.page.pageType` | STRING | Website page type |
| `hits.page.hostname` | STRING | The hostname of the URL |
| `hits.page.pageTitle` | STRING | The page title |
| `hits.eCommerceAction` | RECORD | This section contains all of the hits that occurred during the session. This is a repeated field and has an entry for each hit that was collected. |
| `hits.eCommerceAction.action_type` | STRING | [Product Action](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#pa)<br>Can be one of: detail, click, add, remove, checkout, checkout\_option, purchase, refund.<br> <br>Also, this field can take the same value as the _promotionActionInfo_ field. |
| `hits.eCommerceAction.option` | STRING | This field is populated when a checkout option is specified. For example, a shipping option such as option = 'Fedex'. |
| `hits.eCommerceAction.step` | INTEGER | This field is populated when a checkout step is specified with the hit |
| `hits.eCommerceAction.list` | STRING | The list or collection to which a product belongs |
| `hits.experiment` | RECORD | This row and the nested fields are populated for each hit that contains data for an experiment |
| `hits.experiment.experimentId` | STRING | The ID of the experiment |
| `hits.experiment.experimentVariant` | STRING | The variation or combination of variations present in a hit for an experiment |
| `hits.product` | RECORD | This row and nested fields will be populated for each hit that contains Enhanced Ecommerce PRODUCT data |
| `hits.product.isImpression` | BOOLEAN | TRUE if at least one user viewed this product (i.e., at least one impression) when it appeared in the product list |
| `hits.product.impressionList` | STRING | The name of the product list in which the product appears |
| `hits.product.productListName` | STRING | Currently gets only the 'null' values. The correct values will appear with the upcoming updates. |
| `hits.product.productBrand` | STRING | The brand associated with the product |
| `hits.product.productSku` | STRING | Product SKU |
| `hits.product.productVariant` | STRING | Product Variant |
| `hits.product.productPrice` | FLOAT | Price of the product in the currency of your Google Analytics view |
| `hits.product.localProductPrice` | FLOAT | Product price in the actual transaction currency |
| `hits.product.productQuantity` | INTEGER | The quantity of the product purchased |
| `hits.product.productCategory` | STRING | Product Category |
| `hits.product.productName` | STRING | Product Name |
| `hits.product.position` | INTEGER | The product's position in a list or collection |
| `hits.product.coupon` | STRING | Code for the product-level coupon (Enhanced Ecommerce) |
| `hits.product.customDimensions` | RECORD | This section is populated for all hits containing product scope Custom Dimensions |
| `hits.product.customDimensions.index` | INTEGER | The product scope Custom Dimensions index |
| `hits.product.customDimensions.value` | STRING | The product scope Custom Dimensions value |
| `hits.product.customMetrics` | RECORD | This section is populated for all hits containing product scope Custom Metrics |
| `hits.product.customMetrics.index` | INTEGER | The product scope Custom Metrics index |
| `hits.product.customMetrics.value` | STRING | The product scope Custom Metrics value |
| `hits.promotion` | RECORD | This row and nested fields are populated for each hit that contains Enhanced Ecommerce PROMOTION information |
| `hits.promotion.promoCreative` | STRING | The text or creative variation associated with the promotion |
| `hits.promotion.promoId` | STRING | Promotion ID |
| `hits.promotion.promoName` | STRING | Promotion Name |
| `hits.promotion.promoPosition` | STRING | Promotion position on the site |
| `hits.promotionActionInfo` | STRING | Enhanced Ecommerce PROMOTION action information |
| `hits.transaction` | RECORD | This section is populated for each hit with type = transaction |
| `hits.transaction.transactionId` | STRING | The transaction ID of the e-commerce transaction |
| `hits.transaction.transactionRevenue` | FLOAT | Revenue from the transaction in the currency of your Google Analytics view |
| `hits.transaction.localTransactionRevenue` | FLOAT | Revenue from the transaction in the actual transaction currency |
| `hits.transaction.transactionTax` | FLOAT | Transaction tax in the currency of your Google Analytics view |
| `hits.transaction.localTransactionTax` | FLOAT | Transaction tax in the actual transaction currency |
| `hits.transaction.transactionShipping` | FLOAT | Shipping price for the transaction in the currency of your Google Analytics view |
| `hits.transaction.localTransactionShipping` | FLOAT | Shipping price for the transaction in the actual transaction currency |
| `hits.transaction.transactionCoupon` | STRING | The coupon code associated with the transaction |
| `hits.transaction.affiliation` | STRING | The affiliate information passed to the ecommerce tracking code |
| `hits.contentInfo` | RECORD | This section will be populated for each hit with type = "appview" |
| `hits.contentInfo.contentDescription` | STRING | The description of the content being viewed as passed to the SDK |
| `hits.appInfo` | RECORD | This section will be populated for each hit with type = "appview" or "exception" |
| `hits.appInfo.name` | STRING | The application name |
| `hits.appInfo.version` | STRING | The application version |
| `hits.appInfo.id` | STRING | The application ID |
| `hits.appInfo.installerId` | STRING | ID of the installer (e.g., Google Play Store) from which the app was downloaded. By default, the app installer ID is set based on the PackageManager#getInstallerPackageName method. |
| `hits.eventInfo` | RECORD | This section is populated for each hit with type = "event" |
| `hits.eventInfo.eventCategory` | STRING | The event category |
| `hits.eventInfo.eventAction` | STRING | The event action |
| `hits.eventInfo.eventLabel` | STRING | The event label |
| `hits.eventInfo.eventValue` | STRING | The event value |
| `hits.timingInfo` | RECORD | This section contains all user timing variables.<br>The fields are automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.timingInfo.timingCategory` | STRING | For easier reporting purposes, this is used to categorize all user timing variables into logical groups.<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.timingInfo.timingVariable` | STRING | Used to add flexibility to visualize user timings in the reports.<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.timingInfo.timingLabel` | STRING | The name of the resource's action being tracked.<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.timingInfo.timingValue` | INTEGER | Total number of milliseconds for user timing.<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.timingInfo.pageLoad` | INTEGER | Total time (in milliseconds), from pageview initiation (e.g., a click on a page link) to page load completion in the browser, the pages in the sample set take to load.<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.timingInfo.DNS` | INTEGER | The total time (in milliseconds) all samples spent in DNS lookup for this page.<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.timingInfo.pageDownload` | INTEGER | The total time (in milliseconds) to download this page among all samples.<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.timingInfo.redirectResponse` | INTEGER | The total time (in milliseconds) all samples spent in redirects before fetching this page. If there are no redirects, this is 0.<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.timingInfo.TCPConnect` | INTEGER | Total time (in milliseconds) all samples spent in establishing a TCP connection to this page.<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.timingInfo.serverResponse` | INTEGER | The total time (in milliseconds) the site's server takes to respond to users' requests for all samples; this includes the network time from users' locations to the server.<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.timingInfo.DOMInteractive` | INTEGER | The time (in milliseconds), including the network time from users' locations to the site's server, the browser takes to parse the document (DOMInteractive). At this time, users can interact with the Document Object Model even though it is not fully loaded.<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.timingInfo.contentLoad` | INTEGER | The time (in milliseconds), including the network time from users' locations to the site's server, the browser takes to parse the document and execute deferred and parser-inserted scripts (DOMContentLoaded). When parsing of the document is finished, the Document Object Model (DOM) is ready, but the referenced style sheets, images, and subframes may not be finished loading. This is often the starting point of Javascript framework execution, e.g., JQuery's onready() callback.<br>The field is automatically filled in for the hit types 'timing' and 'adtiming'. |
| `hits.customDimensions` | RECORD | This section contains any hit-level custom dimensions. This is a repeated field and has an entry for each dimension that is set. |
| `hits.customDimensions.index` | INTEGER | The index of the custom dimension |
| `hits.customDimensions.value` | STRING | The value of the custom dimension |
| `hits.customMetrics` | RECORD | This section contains any hit-level custom metrics. This is a repeated field and has an entry for each metric that is set. |
| `hits.customMetrics.index` | INTEGER | The index of the custom metric |
| `hits.customMetrics.value` | INTEGER | The value of the custom metric |
| `hits.exceptionInfo` | RECORD | Contains nested fields with the info on exceptions |
| `hits.exceptionInfo.description` | STRING | The description for the exception |
| `hits.exceptionInfo.isFatal` | BOOLEAN | Specifies whether the exception was fatal |

> **Note:**  
> The fields with custom dimensions and metrics don't have level scopes as they do in Google Analytics (hit-level scope, session-level scope). They are being collected on hit level only. The session-level fields are not being filled in yet.
