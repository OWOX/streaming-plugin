# Sample queries

- [Calculation of RFM segments](#calculation-of-rfm-segments)
- [How to get a session-level scope value for a custom dimension?](#how-to-get-a-session-level-scope-value-for-a-custom-dimension)
- [How to calculate visit start time?](#how-to-calculate-visit-start-time)
- [What checkout steps have the highest exit rate?](#what-checkout-steps-have-the-highest-exit-rate)
- [How can you calculate time between product detail view and purchase?](#how-can-you-calculate-time-between-product-detail-view-and-purchase)
- [How does user interaction with social buttons affect the conversion rate?](#how-does-user-interaction-with-social-buttons-affect-the-conversion-rate)
- [Allocation of sales by categories depending on category of the landing page](#allocation-of-sales-by-categories-depending-on-category-of-the-landing-page)
- [How do the page type, device, browser, etc. affect the page load speed or server response time?](#how-do-the-page-type-device-browser-etc-affect-the-page-load-speed-or-server-response-time)
- [Calculation of revenue from advert blocks](#calculation-of-revenue-from-advert-blocks)
- [Dependence of revenue on the number of JS-errors](#dependence-of-revenue-on-the-number-of-js-errors)
- [Share of the sessions in which goods was purchased on landing page](#share-of-the-sessions-in-which-goods-was-purchased-on-landing-page)
- [Traffic on out-of-stock product card](#traffic-on-out-of-stock-product-card)

## Calculation of RFM segments

The query returns the segment number for each user by each of these signs:

-   **R**ecency: the recency of the last purchase. The less time has passed since the last purchase, the greater the likelihood that the customer will repeat the action.
-   **F**requency: the number of purchases. The more the client purchases made, the greater the likelihood that they will make the purchase again.
-   **M**onetary: the number of purchases in cash. The more money the customer has spent, the greater the likelihood that they will make the order again.

As the data source for the calculations you can use either a the table or a View in Google BigQuery containing the following fields:

-   **userId**: a user ID in the site / ERP database
-   **revenue**: revenue per order
-   **created**: the order creation date

To calculate the RFM segments, we recommend using order confirmation data from an ERP. You can use the data uploaded via OWOX BI Pipeline as well.

  
**Dynamic parameters**

-   **NTILE( )**: the number of segments to divide users by each sign (R,F, and M)
-   **userId**: the name of the field in the table, in which the parameter userId stores
-   **projectName**: the BigQuery project containing data about orders
-   **dataSetName**: the name of a dataset containing data about orders
-   **tableName**: the name of the table with the data about orders

**SQL Query**

```sql
#standardsql
WITH
  transactions AS (
  SELECT
    userId,
    transaction.transactionId AS transactionId,
    ARRAY_AGG(transaction.transactionRevenue
    ORDER BY
      timestamp DESC) [
  OFFSET
    (0)] AS revenue,
    ARRAY_AGG(DATE(TIMESTAMP_MICROS(timestamp))
    ORDER BY
      timestamp DESC)[
  OFFSET
    (0)] AS created
  FROM  --Specify the data source
    `projectName.dataSetName.tableName_*`
  WHERE  --Select the time period
    _TABLE_SUFFIX BETWEEN FORMAT_DATE("%E4Y%m%d", DATE_ADD(CURRENT_DATE(), INTERVAL -12 MONTH))
    AND FORMAT_DATE("%E4Y%m%d", CURRENT_DATE())
    AND transaction.transactionId IS NOT NULL
    AND transaction.transactionRevenue>0
  GROUP BY
    userId,
    transaction.transactionId)
SELECT
  userId AS userId,
  R AS dimension7,
  F AS dimension8,
  M AS dimension9
FROM (
  SELECT
    Rtab.userId AS userId,
    LastDate,
    R,
    TotalPurchases,
    F,
    TotalRevenue,
    M
  FROM
    --The R segment calculation: by the recency of the latest purchase, where NTILE(5) is a number of segments
    (
    SELECT
      userId,
      LastDate,
      NTILE(5) OVER (ORDER BY LastDate DESC) R
    FROM (
      SELECT
        userId,
        MAX(created) AS LastDate
      FROM
        `transactions`
      GROUP BY
        userId )) AS Rtab
  JOIN
    --The F segment calculation: by the frequency of purchases, where NTILE(5) is a number of segments
    (
    SELECT
      userId,
      TotalPurchases,
      NTILE(5) OVER (ORDER BY TotalPurchases DESC) F
    FROM (
      SELECT
        userId AS userId,
        COUNT(DISTINCT transactionId) AS TotalPurchases
      FROM
        `transactions`
      GROUP BY
        userId )) AS Ftab
  ON
    Rtab.userId = Ftab.userId
  JOIN
    --The M segment calculation: by total orders in money equivalent, where NTILE(5) is a number of segments
    (
    SELECT
      userId,
      TotalRevenue,
      NTILE(5) OVER (ORDER BY TotalRevenue DESC) M
    FROM (
      SELECT
        userId,
        AVG(revenue) AS TotalRevenue
      FROM
        `transactions`
      GROUP BY
        userId )) AS Mtab
  ON
    Rtab.userId = Mtab.userId)
ORDER BY
  userId
```

## How to get a session-level scope value for a custom dimension?

OWOX BI collects [custom dimensions](https://support.google.com/analytics/answer/2709828?hl=en#processing) on the hit-level scope.

The query example below lets you get a session-level scope value for a custom dimension.

### Parameters to set in the request

-   **project-name** — the name of the Google BigQuery project containing user session data uploaded via OWOX BI;
-   **dataset-name** — the name of the Google BigQuery dataset containing the session data;
-   **\_TABLE\_SUFFIX** — the date range you want to view data for;
-   **index** — custom dimension index. In the example below we used index **8**.

### SQL query

```sql
SELECT sessionIdFromCd FROM (SELECT
  MAX((
    SELECT
      value
    FROM
      UNNEST (hits.customDimensions)
    WHERE
      index = 8
      AND value IS NOT NULL)) OVER(PARTITION BY sessionId) sessionIdfromCd
FROM
  `project-name.dataset-name.owoxbi_sessions_*`,
  UNNEST (hits) hits
 WHERE _TABLE_SUFFIX BETWEEN '20191213' AND '20191214'
)
  GROUP BY 1
```

| **Row** | **sessionIdFromCd** |
| --- | --- |
| 1 | 1088888882.188888881\_1584892173257 |
| 2 | 1077777772.177777772\_1584897362067 |
| 3 | 1066666663.166666667\_1584881382775 |
| 4 | 1055555558.155555550\_1584829420499 |
| 5 | 1044444448.144444441\_1584849461498 |

## How to calculate visit start time?

This SQL query shows the start time of the session.

### Parameters to set in the request

-   **project-name** — the name of the Google BigQuery project containing user session data uploaded via OWOX BI;
-   **dataset-name** — the name of the Google BigQuery dataset containing the session data;
-   **\_TABLE\_SUFFIX** — the date range you want to view data for.

### SQL query

```sql
#standardSQL
SELECT
  sessionId,
  TIMESTAMP_MILLIS(timestamp) visitStartTime
FROM (
  SELECT
    sessionId,
    hits.timestamp,
    ROW_NUMBER() OVER(PARTITION BY sessionId ORDER BY hits.timestamp ASC) rowf
  FROM
    `project-name.dataset-name.owoxbi_sessions_*`,
    UNNEST (hits) hits
    WHERE _TABLE_SUFFIX BETWEEN '20191213' AND '20191214'
)
WHERE
  rowf = 1
```

| **Row** | **sessionId** | **visitStartTime** |
| --- | --- | --- |
| 1 | 1111111111.111111113\_1576240392308 | 2019-12-13 12:33:12.308 UTC |
| 2 | 1111111111.22222223\_1576248529623 | 2019-12-13 14:48:49.623 UTC |
| 3 | 1111111111.33333333\_1576330871973 | 2019-12-14 13:41:11.973 UTC |
| 4 | 1111111111.44444447\_1576312891097 | 2019-12-14 08:40:57.557 UTC |
| 5 | 1111111111.555555555\_1576298600061 | 2019-12-14 04:38:50.172 UTC |
| 6 | 1111111111.666666666\_1576299847255 | 2019-12-14 05:04:04.939 UTC |

## What checkout steps have the highest exit rate?

The query shows exit rate for each step of checkout.

#### Dynamic parameters

-   **dataSetName** — name of Google BigQuery dataset with Google Analytics sessions data,
-   **tableName** — name of a table with the data,
-   **startDate** — start date of the analyzed period,
-   **endDate** — end date of the analyzed period.

#### Visualization example

![](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-sample-queries/01/public)

#### SQL Query

```sql
SELECT
  checkoutStep,
  date,
  exitsCount/pageviews AS exitRate
FROM (
  SELECT
    t1.checkoutStep AS checkoutStep,
    t1.date AS date,
    t1.exitsCount AS exitsCount,
    t2.pageviews AS pageviews
  FROM (
    SELECT
      hits.eCommerceAction.step AS checkoutStep,
      date,
      count (hits.eCommerceAction.step) AS exitsCount
    FROM
      TABLE_DATE_RANGE({dataSetName default="OWOXBI_Streaming" type="input"}.{tableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-08-10" type="datetime"}'), TIMESTAMP('{endDate default="2016-08-13" type="datetime"}'))
    WHERE
      hits.isExit=1
      AND hits.eCommerceAction.step IS NOT NULL
    GROUP BY
      checkoutStep,
      date) AS t1
  JOIN EACH (
    SELECT
      hits.eCommerceAction.step AS checkoutStep,
      date,
      COUNT(DISTINCT hits.hitId) AS pageviews
    FROM
      TABLE_DATE_RANGE({dataSetName}.{tableName}, TIMESTAMP('{startDate}'), TIMESTAMP('{endDate}'))
    WHERE
      hits.eCommerceAction.step IS NOT NULL
    GROUP BY
      checkoutStep,
      date) AS t2
  ON
    t1.checkoutStep=t2.checkoutStep and t1.date=t2.date
  GROUP BY
    checkoutStep,
    date,
    exitsCount,
    pageviews)
GROUP BY
  checkoutStep,
  date,
  exitRate
```

## How can you calculate time between product detail view and purchase?

As a result of the query you obtain the sum and the accumulated sum of orders by traffic sources and time between product detail view and purchase.

### **Report Example**

![](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-sample-queries/02/public)

### **Dynamic parameters**

-   **startDate** - start date of the analyzed period
-   **endDate** - end date of the analyzed period
-   **dataSetName** - name of dataset, where stored session streaming table
-   **tableName** - name of the table, where stored session Streaming data
-   **trafficSourceMedium** - list of traffic sources for analysis (use RegExp)

### **SQL-query**

```sql

SELECT
  trafficSourceMedium,
  hoursFromViewToPurchase,
  totalPurchases,
  SUM(totalPurchases) OVER (PARTITION BY trafficSourceMedium ORDER BY hoursFromViewToPurchase) cumulativeTotalPurchases
FROM (
  SELECT
    trafficSourceMedium,
    CASE WHEN timeToPurchase<0.5 THEN 0.5     WHEN timeToPurchase<1 THEN 1     WHEN timeToPurchase<2 THEN 2     WHEN timeToPurchase<3 THEN 3     WHEN timeToPurchase<5 THEN 5     WHEN timeToPurchase<8 THEN 8     WHEN timeToPurchase<12 THEN 12     WHEN timeToPurchase<18 THEN 18     WHEN timeToPurchase<24 THEN 24     WHEN timeToPurchase<48 THEN 48     WHEN timeToPurchase<72 THEN 72     WHEN timeToPurchase<96 THEN 96     WHEN timeToPurchase<120 THEN 120     WHEN timeToPurchase<144 THEN 144     WHEN timeToPurchase<168 THEN 168     WHEN timeToPurchase>=168 THEN 169 END AS hoursFromViewToPurchase,    COUNT(*) AS totalPurchases
  FROM (
    
    SELECT
      purchases.clientId AS clientId,
      purchases.hits.product.productSku AS hits.product.productSku,
      trafficSource.source+' / '+trafficSource.medium AS trafficSourceMedium,
      ROUND((purchases.hits.time-detailViews.hits.time)/3660,1) AS timeToPurchase
    FROM (
      SELECT
        clientId,
        MIN(hits.time) AS hits.time,
        hits.product.productSku
      FROM
        FLATTEN ((
          SELECT
            clientId,
            hits.hitId,
            hits.time,
            hits.eCommerceActionType,
            hits.product.productSku
          FROM
            TABLE_DATE_RANGE({dataSetName default="OWOXBI_Streaming" type="input"}.{tableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-05-01" type="datetime"}'), TIMESTAMP('{endDate default="2016-05-17" type="datetime"}'))), hits.product)
      WHERE
        (hits.eCommerceActionType = 'purchase')
        AND (hits.product.productSku IS NOT NULL)
        AND hits.product.productSku != '(not set)'
      GROUP EACH BY
        clientId,
        hits.product.productSku) AS purchases
    JOIN EACH (      SELECT
        t1.clientId AS clientId,
        t1.hits.time AS hits.time,
        t1.hits.product.productSku AS hits.product.productSku,
        trafficSource.source,
        trafficSource.medium
      FROM (
        SELECT
          clientId,
          MIN(hits.time) AS hits.time,
          hits.product.productSku
        FROM
          FLATTEN ((
            SELECT
              clientId,
              hits.hitId,
              hits.time,
              hits.eCommerceActionType,
              hits.product.productSku
            FROM
              TABLE_DATE_RANGE({dataSetName}.{tableName}, TIMESTAMP('{startDate}'), TIMESTAMP('{endDate}'))), hits.product)
        WHERE
          (hits.eCommerceActionType = 'detail')
          AND (hits.product.productSku IS NOT NULL)
          AND hits.product.productSku != '(not set)'
        GROUP EACH BY
          clientId,
          hits.product.productSku) AS t1
      JOIN EACH (
        SELECT
          clientId,
          trafficSource.source,
          trafficSource.medium,
          hits.time,
          hits.product.productSku
        FROM
          FLATTEN ((
            SELECT
              clientId,
              hits.hitId,
              hits.time,
              trafficSource.source,
              trafficSource.medium,
              hits.eCommerceActionType,
              hits.product.productSku
            FROM
              TABLE_DATE_RANGE({dataSetName}.{tableName}, TIMESTAMP('{startDate}'), TIMESTAMP('{endDate}'))), hits.product)
        WHERE
          (hits.eCommerceActionType = 'detail')
          AND (hits.product.productSku IS NOT NULL)
          AND hits.product.productSku != '(not set)'
          AND trafficSource.source IS NOT NULL
          AND trafficSource.medium IS NOT NULL
        GROUP BY
          clientId,
          hits.hitId,
          hits.time,
          trafficSource.source,
          trafficSource.medium,
          hits.eCommerceActionType,
          hits.product.productSku) AS t2
      ON
        (t1.clientId=t2.clientId)
        AND (t1.hits.time=t2.hits.time)
        AND (t1.hits.product.productSku=t2.hits.product.productSku)
      GROUP BY
        clientId,
        hits.time,
        hits.product.productSku,
        trafficSource.source,
        trafficSource.medium) AS detailViews
    ON
      (purchases.clientId = detailViews.clientId)
      AND (purchases.hits.product.productSku = detailViews.hits.product.productSku)
    WHERE
      (purchases.hits.time > detailViews.hits.time))
  GROUP BY
    trafficSourceMedium,
    hoursFromViewToPurchase
  ORDER BY
    trafficSourceMedium,
    hoursFromViewToPurchase)
HAVING
  REGEXP_MATCH(trafficSourceMedium, r'{trafficSourceMedium default=".*" type="input"}')
```

## How does user interaction with social buttons affect the conversion rate?

The query shows average conversion rate for users that had at least one social interaction on a site.

#### Dynamic parameters

-   **dataSetName** — name of Google BigQuery dataset with Google Analytics sessions data,
-   **tableName** — name of a table with the data,
-   **startDate** — start date of the analyzed period,
-   **endDate** — end date of the analyzed period.

#### Visualization example

![](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-sample-queries/03/public)

#### SQL Query

```sql
SELECT
  table1.sessionsWithSI / table1.transactionsWithSI AS conversionSI,
  table2.sessionsNoSI / table2.transactionsNoSI AS conversionNoSI
FROM (
  SELECT
    tab1.socialActions AS withSI,
    tab1.sessions AS sessionsWithSI,
    tab2.transactions AS transactionsWithSI
  FROM (
    SELECT
      1 AS socialActions,
      count (sessionId) sessions
    FROM
      TABLE_DATE_RANGE({dataSetName default="Streaming" type="input"}.{tableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-08-10" type="datetime"}'), TIMESTAMP('{endDate default="2016-08-13" type="datetime"}'))
    WHERE
      hits.social.socialInteractionAction IS NOT NULL) AS tab1
  JOIN (
    SELECT
      1 AS withTransctions,
      count (t2.transactions) AS transactions
    FROM (
      SELECT
        clientId
      FROM
        TABLE_DATE_RANGE({dataSetName}.{tableName}, TIMESTAMP('{startDate}'), TIMESTAMP('{endDate}'))
      WHERE
        hits.social.socialInteractionAction IS NOT NULL) AS t1
    JOIN (
      SELECT
        clientId,
        count (hits.transaction.transactionId) AS transactions
      FROM
        TABLE_DATE_RANGE({dataSetName}.{tableName}, TIMESTAMP('{startDate}'), TIMESTAMP('{endDate}'))
      WHERE
        hits.transaction.transactionId IS NOT NULL
      GROUP BY
        clientId) AS t2
    ON
      t1.clientId=t2.clientId) AS tab2
  ON
    tab1.socialActions=tab2.withTransctions
  GROUP BY
    withSI,
    sessionsWithSI,
    transactionsWithSI) AS table1
JOIN (
  SELECT
    tab1.noSI AS noSI,
    tab1.sessions AS sessionsNoSI,
    tab2.transactions AS transactionsNoSI
  FROM (
    SELECT
      1 AS noSI,
      count (sessionId) sessions
    FROM
      TABLE_DATE_RANGE({dataSetName}.{tableName}, TIMESTAMP('{startDate}'), TIMESTAMP('{endDate}'))
    WHERE
      hits.social.socialInteractionAction IS NULL) AS tab1
  JOIN (
    SELECT
      1 AS noSI,
      count (t2.transactions) AS transactions
    FROM (
      SELECT
        clientId
      FROM
        TABLE_DATE_RANGE({dataSetName}.{tableName}, TIMESTAMP('{startDate}'), TIMESTAMP('{endDate}'))
      WHERE
        hits.social.socialInteractionAction IS NULL) AS t1
    JOIN (
      SELECT
        clientId,
        count (hits.transaction.transactionId) AS transactions
      FROM
        TABLE_DATE_RANGE({dataSetName}.{tableName}, TIMESTAMP('{startDate}'), TIMESTAMP('{endDate}'))
      WHERE
        hits.transaction.transactionId IS NOT NULL
      GROUP BY
        clientId) AS t2
    ON
      t1.clientId=t2.clientId) AS tab2
  ON
    tab1.noSI=tab2.noSI
  GROUP BY
    noSI,
    sessionsNoSI,
    transactionsNoSI) AS table2
ON
  table1.withSI=table2.noSI
GROUP BY
  conversionSI,
  conversionNoSI
```

## Allocation of sales by categories depending on category of the landing page

As a result of the query you get orders by categories depending on the product category of the landing page. The calculations involved only those sessions in which the user has landed on the product card and bought something. Moreover, if the user bought several items from different categories, the order will be assigned to each of the categories.

**Dynamic parameters**

-   startDate - start date of the analyzed period
-   endDate - end date of the analyzed period
-   dataSetName - name of dataset, where stored session streaming table
-   tableName - name of the table, where stored session Streaming data

**SQL-query**

```sql
SELECT
  landingProductCategory,
  purchasedProductCategory,
  SUM(purchases) AS totalPurchases FROM
  (
  SELECT
    sessionId,
    hits.product.productCategory AS landingProductCategory
  FROM
    FLATTEN ( (
      SELECT
        sessionId,
        hits.product.productCategory,
        hits.eCommerceActionType,
        hits.isEntrance
      FROM
        TABLE_DATE_RANGE({dataSetName default="OWOXBI_Streaming" type="input"}.{tableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-01-11" type="datetime"}'), TIMESTAMP('{endDate default="2016-01-11" type="datetime"}'))), hits.product)
  WHERE
    hits.isEntrance = 1
    AND hits.eCommerceActionType = 'detail'
    AND NOT hits.product.productCategory IN ('(not set)', '')) t1
JOIN EACH
  (
  SELECT
    sessionId,
    hits.product.productCategory AS purchasedProductCategory,
    SUM(hits.product.productQuantity) AS purchases
  FROM
    FLATTEN ( (
      SELECT
        sessionId,
        hits.product.productCategory,
        hits.eCommerceActionType,
        hits.product.productQuantity
      FROM
        TABLE_DATE_RANGE({dataSetName default="OWOXBI_Streaming" type="input"}.{tableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-01-11" type="datetime"}'), TIMESTAMP('{endDate default="2016-01-11" type="datetime"}'))), hits.product)
  WHERE
    hits.eCommerceActionType = 'purchase'
    AND hits.product.productQuantity > 0
    AND NOT hits.product.productCategory IN ('(not set)', '')
  GROUP BY
    sessionId,
    purchasedProductCategory) t2
ON
  t1.sessionId=t2.sessionId
GROUP BY
  landingProductCategory,
  purchasedProductCategory
```

## How do the page type, device, browser, etc. affect the page load speed or server response time?

The query shows average pageload time and server responce time by dimensions: device category, page type or browser.

#### Dynamic parameters

-   **dimension** — name of a dimension (device, browser or pageType) for the metrics Average pageload time and Server response time,
-   **dataSetName** — name of Google BigQuery dataset with Google Analytics sessions data,
-   **tableName** — name of a table with the data,
-   **startDate** — start date of the analyzed period,
-   **endDate** — end date of the analyzed period.

#### Visualization example

![](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-sample-queries/04/public)

#### SQL Query

```sql
SELECT
  {dimension default="device" type="select" values="device,browser,pageType"},
  date,
  AVG(avgPageLoadTime) as avgPageLoadTime,
  AVG(avgServerResponseTime) as avgServerResponseTime
FROM (
  SELECT
    device.deviceCategory AS device,
    device.browser AS browser,
    hits.pageType AS pageType,
    date,
    hits.timingInfo.pageLoad/1000 AS avgPageLoadTime,
    hits.timingInfo.serverResponse/1000 AS avgServerResponseTime
  FROM
    TABLE_DATE_RANGE({dataSetName default="OWOXBI_Streaming" type="input"}.{tableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-08-10" type="datetime"}'), TIMESTAMP('{endDate default="2016-08-13" type="datetime"}'))
  WHERE
    hits.timingInfo.pageLoad IS NOT NULL
    AND hits.timingInfo.serverResponse IS NOT NULL
  GROUP BY
    device,
    browser,
    pageType,
    date,
    avgPageLoadTime,
    avgServerResponseTime
  ORDER BY
    date )
GROUP BY
  {dimension},
  date
ORDER BY
  date
```

## Calculation of revenue from advert blocks

As a result of the query you obtain income in context of advert blocks for the cases when the user clicked on the product in the block or added product to card from the block, and then bought the same product.

The query can also be used to calculate the profitability of different types of catalogs.

**Dynamic parameters**

-   startDate - start date of the analyzed period
-   endDate - end date of the analyzed period
-   dataSetName - name of dataset, where stored session streaming table
-   sTableName - name of the table, where stored session Streaming data
-   hTableName - name of the table, where stored hit Streaming data
-   listNames \- block name for which you want to calculate the income (defined as regular expressions, for example: BestSellers | SpecialOffers)
-   attributionType - attribution type (can take value _MAX_ - income will be assigned to the last block with which the user interacts, or _MIN_ - income will be assigned to the first block with which the user interacts)
-   dateType - date type (can be set to _purchaseDate_ - date, _numberOfWeek_ - week number, _numberOfMonth_ - number of the month.)

**SQL-query**

```sql
SELECT
  {dateType default="purchaseDate" type="select" values="purchaseDate,numberOfWeek,numberOfMonth"},
  listName,
  SUM(revenue*quantity) AS totalRevenue
FROM (
  SELECT
    sessionId,
    date AS purchaseDate,
    hits.product.productSku AS productSKU,
    hits.hitId AS hitId,
    INTEGER(CEIL((DAYOFYEAR(TIMESTAMP(INTEGER(hits.time*1000000)))-4)/7-0.01+1)) AS numberOfWeek,
    MONTH(hits.time+1000) AS numberOfMonth,
    hits.time AS time,
    hits.product.productPrice AS revenue,
    hits.product.productQuantity AS quantity
  FROM
    FLATTEN ((
      SELECT
        sessionId,
        date,
        hits.product.productSku,
        hits.eCommerceActionType,
        hits.time,
        hits.product.productPrice,
        hits.product.productQuantity,
        hits.hitId
      FROM
        TABLE_DATE_RANGE({dataSetName default="OWOXBIStreaming" type="input"}.{sTableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-02-08" type="datetime"}'), TIMESTAMP('{endDate default="2016-02-08" type="datetime"}'))), hits.product)
  WHERE
    (hits.eCommerceActionType = 'purchase')
    AND (hits.product.productSku IS NOT NULL)
  GROUP EACH BY
    sessionId,
    purchaseDate,
    productSKU,
    hitId,
    numberOfWeek,
    numberOfMonth,
    time,
    revenue,
    quantity ) AS TabPurchase
JOIN EACH
  (
  SELECT
    t2.sessionId AS sessionId,
    product.productSku,
    listName,
    {attributionType default="MAX" type="select" values="MAX,MIN"}(interactionTime) AS time FROM
    (
    SELECT
      hitId,
      time AS interactionTime,
      product.productSku,
      eCommerceAction.list AS listName
    FROM
      FLATTEN ((
        SELECT
          hitId,
          product.productSku,
          time,
          eCommerceAction.list,
          eCommerceAction.action_type
        FROM
          TABLE_DATE_RANGE({dataSetName default="OWOXBIStreaming" type="input"}.{hTableName default="streaming_" type="input"}, TIMESTAMP('{startDate default="2016-02-08" type="datetime"}'), TIMESTAMP('{endDate default="2016-02-08" type="datetime"}'))), product)
    WHERE
      (eCommerceAction.action_type IN ('click',
          'add'))
      AND (product.productSku IS NOT NULL)
      AND REGEXP_MATCH(eCommerceAction.list, r'{listNames default="bestSellers|SpecialOffers" type="input"}')) AS t1
  JOIN EACH
    (
    SELECT
      sessionId,
      hits.hitId
    FROM
      FLATTEN ((
        SELECT
          sessionId,
          hits.eCommerceActionType,
          hits.hitId,
          hits.product.productSku
        FROM
          TABLE_DATE_RANGE({dataSetName default="OWOXBIStreaming" type="input"}.{sTableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-02-08" type="datetime"}'), TIMESTAMP('{endDate default="2016-02-08" type="datetime"}'))), hits)
    WHERE
      (hits.eCommerceActionType IN ('click',
          'add')
        AND (hits.product.productSku IS NOT NULL))) t2
  ON
    t1.hitId=t2.hits.hitId
  GROUP EACH BY
    sessionId,
    product.productSku,
    listName ) AS TabClick
ON
  (TabClick.sessionId = TabPurchase.sessionId)
  AND (TabClick.product.productSku = TabPurchase.productSKU)
WHERE
  TabClick.time < TabPurchase.timeGROUP EACH BY {dateType}, listNameORDER BY {dateType} ASC
```

## Dependence of revenue on the number of JS-errors

As a result of the query you obtain income, number of orders and average proportion of JS-error  for 1 hit for user groups. The calculation is performed as follows: for each user for analyzed period was calculated "Share JS-errors on one hit," as the sum of JS errors / total number of hits.

Further all users have been divided into 10 groups, depending on the indicator "Share JS - errors on one hit." Group 1 - includes users with the lowest indicator "Share JS - errors on one hit" (ie, those users frequency of JS-error is minimal). In the 10-th group respectively included users, who often meet with JS-error (regardless of their activity on the site). It is worth noting that number of users in all groups is the same.

Then income and the number of orders is calculated for each group. Generally, the more likely a user is found with JS-errors on the site, the lower the income he brings.

**Notice!** Calculations were involved only those users, who faced JS-mistake at least once in review period.

**Dynamic parameters**

-   startDate - start date of the analyzed period
-   endDate - end date of the analyzed period
-   dataSetName - name of dataset, where stored session streaming table
-   tableName - name of the table, where stored session Streaming data
-   numberOfGroups - number of categories to be analyzed
-   eventCategoryName - event category with JS-errors

**SQL-query**

```sql
SELECT
  JSErrorGroup,
  SUM(TotalTransactions) AS TotalTransactions,
  SUM(TotalRevenue) AS TotalRevenue,
  AVG(ErrorsPerHit) AS avgErrorsPerHit
FROM (
  SELECT
    t3.clientId AS ClientId,
    JSErrorsQTY,
    TotalTransactions,
    TotalRevenue,
    TotalHits,
    JSErrorsQTY/TotalHits AS ErrorsPerHit,
    NTILE({numberOfGroups default="10" type="select" values="5,10,15,20"}) OVER (ORDER BY ErrorsPerHit ASC) AS JSErrorGroup
  FROM (
    SELECT
      t1.clientId AS ClientId,
      JSErrorsQTY,
      TotalTransactions,
      TotalRevenue
    FROM (
      SELECT
        clientId,
        COUNT(type) AS JSErrorsQTY
      FROM (TABLE_DATE_RANGE({dataSetName default="OWOXBI_Streaming" type="input"}.{tableName default="streaming_" type="input"}, TIMESTAMP('{startDate default="2015-11-14" type="datetime"}'), TIMESTAMP('{endDate default="2016-01-27" type="datetime"}')))
      WHERE
        eventInfo.eventCategory = '{eventCategoryName default="JS Errors" type="input"}'
      GROUP BY
        clientId) AS t1
    LEFT JOIN EACH (
      SELECT
        clientId,
        COUNT(transaction.transactionId) AS TotalTransactions,
        SUM(transaction.transactionRevenue) AS TotalRevenue
      FROM (
        SELECT
          clientId,
          transaction.transactionId,
          transaction.transactionRevenue
        FROM (TABLE_DATE_RANGE({dataSetName default="OWOXBI_Streaming" type="input"}.{tableName default="streaming_" type="input"}, TIMESTAMP('{startDate default="2015-11-14" type="datetime"}'), TIMESTAMP('{endDate default="2016-01-27" type="datetime"}')))
        WHERE
          eCommerceAction.action_type = 'purchase'
        GROUP BY
          clientId,
          transaction.transactionId,
          transaction.transactionRevenue)
      GROUP BY
        clientId) AS t2
    ON
      (t1.clientId = t2.clientId)) AS t3
  JOIN EACH (
    SELECT
      clientId,
      COUNT(type) AS TotalHits
    FROM (TABLE_DATE_RANGE({dataSetName default="OWOXBI_Streaming" type="input"}.{tableName default="streaming_" type="input"}, TIMESTAMP('{startDate default="2015-11-14" type="datetime"}'), TIMESTAMP('{endDate default="2016-01-27" type="datetime"}')))
    GROUP BY
      clientId) AS t4
  ON
    (t3.clientId = t4.clientId))
GROUP BY
  JSErrorGroup
ORDER BY
  JSErrorGroup
```

## Share of the sessions in which goods was purchased on landing page

As a result of the query you receive a share of the sessions, during which the user was landed on the product card and then made an order in the composition of which was goods from landing page.

**Dynamic parameters**

-   startDate - start date of the analyzed period
-   endDate - end date of the analyzed period
-   dataSetName - name of dataset, where stored session streaming table
-   tableName - name of the table, where stored session Streaming data
-   categoriesNumber - number of TOP-categories to be analyzed

**SQL-query**

```sql
SELECT
  landingProductCategory,
  shareOfSessions FROM
  (
  SELECT
    all.landingProductCategory AS landingProductCategory,
    same.totalSessions,
    all.totalSessions,
    ROUND(same.totalSessions/all.totalSessions, 2) AS shareOfSessions FROM
    (
    SELECT
      landingProductCategory,
      COUNT(*) AS totalSessions FROM
      (
      SELECT
        sessionId,
        hits.product.productSku AS sku,
        hits.product.productCategory AS landingProductCategory
      FROM
        FLATTEN ( (
          SELECT
            sessionId,
            hits.product.productCategory,
            hits.eCommerceActionType,
            hits.isEntrance,
            hits.product.productSku
          FROM
            TABLE_DATE_RANGE({dataSetName default="OWOXBI_Streaming" type="input"}.{tableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-01-11" type="datetime"}'), TIMESTAMP('{endDate default="2016-01-11" type="datetime"}'))), hits.product)
      WHERE
        hits.isEntrance = 1
        AND hits.eCommerceActionType = 'detail'
        AND NOT hits.product.productCategory IN ('(not set)',
          '')
      GROUP BY
        sessionId,
        sku,
        landingProductCategory) t1
    JOIN EACH
      (
      SELECT
        sessionId,
        hits.product.productSku AS sku
      FROM
        FLATTEN ( (
          SELECT
            sessionId,
            hits.eCommerceActionType,
            hits.product.productSku
          FROM
            TABLE_DATE_RANGE({dataSetName default="OWOXBI_Streaming" type="input"}.{tableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-01-11" type="datetime"}'), TIMESTAMP('{endDate default="2016-01-11" type="datetime"}'))), hits.product)
      WHERE
        hits.eCommerceActionType = 'purchase'
      GROUP BY
        sessionId,
        sku) t2
    ON
      t1.sessionId = t2.sessionId
    GROUP BY
      landingProductCategory
    ORDER BY
      totalSessions DESC
    LIMIT
      {categoriesNumber default='15' values='5,10,15,20,25,30' type='select'} --оставляем TOP категорий
      ) AS all
  LEFT JOIN EACH
    (
    SELECT
      landingProductCategory,
      COUNT(*) AS totalSessions FROM
      (
      SELECT
        sessionId,
        hits.product.productSku AS sku,
        hits.product.productCategory AS landingProductCategory
      FROM
        FLATTEN ( (
          SELECT
            sessionId,
            hits.product.productCategory,
            hits.eCommerceActionType,
            hits.isEntrance,
            hits.product.productSku
          FROM
            TABLE_DATE_RANGE({dataSetName default="OWOXBI_Streaming" type="input"}.{tableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-01-11" type="datetime"}'), TIMESTAMP('{endDate default="2016-01-11" type="datetime"}'))), hits.product)
      WHERE
        hits.isEntrance = 1
        AND hits.eCommerceActionType = 'detail'
        AND NOT hits.product.productCategory IN ('(not set)', '')
      GROUP BY
        sessionId,
        sku,
        landingProductCategory) t1
    JOIN EACH
      (
      SELECT
        sessionId,
        hits.product.productSku AS sku
      FROM
        FLATTEN ( (
          SELECT
            sessionId,
            hits.eCommerceActionType,
            hits.product.productSku
          FROM
            TABLE_DATE_RANGE({dataSetName default="OWOXBI_Streaming" type="input"}.{tableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-01-11" type="datetime"}'), TIMESTAMP('{endDate default="2016-01-11" type="datetime"}'))), hits.product)
      WHERE
        hits.eCommerceActionType = 'purchase'
      GROUP BY
        sessionId,
        sku) t2
    ON
      t1.sessionId = t2.sessionId
    WHERE
      t1.sku = t2.sku
    GROUP BY
      landingProductCategory) AS same
  ON
    (same.landingProductCategory = all.landingProductCategory)
  ORDER BY
    all.totalSessions DESC)
```

## Traffic on out-of-stock product card

As a result of the query you get number and share of sessions with landing page  product cards depending on the stock status ("available," "in reserve", "not available").

**Dynamic parameters**

-   startDate - start date of the analyzed period
-   endDate - end date of the analyzed period
-   dataSetName - name of dataset, where stored session streaming table
-   sTableName - name of the table, where stored session Streaming data
-   hTableName - name of the table, where stored hit Streaming data
-   dateType - date type (can be set to _d__ate_ - date, _numberOfWeek_ - week number, _numberOfMonth_ - number of the month.)
-   dimensionIndex - index of hit-scope custom dimension that stores information about the status of the products.

**SQL-query**

```sql
SELECT
  {dateType default="date" type="select" values="date,numberOfWeek,numberOfMonth"},
  productAvailability,
  numberOfSessions,
  shareOfTotalSessions FROM
  (
  SELECT
    t3.date AS date,
    t3.numberOfWeek AS numberOfWeek,
    t3.numberOfMonth AS numberOfMonth,
    t3.productAvailability AS productAvailability,
    t3.totalSessions AS numberOfSessions,
    t3.totalSessions/t4.totalSessions AS shareOfTotalSessions FROM
    (
    SELECT
      date,
      numberOfWeek,
      numberOfMonth,
      productAvailability,
      COUNT(t2.hitId) AS totalSessions FROM
      (
      SELECT
        date,
        hits.hitId,
        INTEGER(CEIL((DAYOFYEAR(TIMESTAMP(INTEGER(hits.time*1000000)))-4)/7-0.01+1)) AS numberOfWeek,
        MONTH(hits.time+1000) AS numberOfMonth
      FROM
        FLATTEN ((
          SELECT
            date,
            hits.eCommerceActionType,
            hits.time,
            hits.hitId,
            hits.isEntrance
          FROM
            TABLE_DATE_RANGE({dataSetName default="OWOXBIStreaming" type="input"}.{sTableName default="session_streaming_" type="input"}, TIMESTAMP('{startDate default="2016-02-08" type="datetime"}'), TIMESTAMP('{endDate default="2016-02-08" type="datetime"}'))), hits)
      WHERE
        hits.eCommerceActionType = 'detail'
        AND hits.isEntrance = 1
      GROUP EACH BY
        hits.hitId,
        date,
        numberOfWeek,
        numberOfMonth ) AS t1
    JOIN EACH
      (
      SELECT
        hitId,
        customDimensions.value AS productAvailability
      FROM
        FLATTEN ((
          SELECT
            sessionId,
            date,
            eCommerceAction.action_type,
            hitId,
            customDimensions.index,
            customDimensions.value
          FROM
            TABLE_DATE_RANGE({dataSetName}.{hTableName default="streaming_" type="input"}, TIMESTAMP('{startDate}'), TIMESTAMP('{endDate}'))), customDimensions)
      WHERE
        eCommerceAction.action_type = 'detail'
        AND customDimensions.index = {dimensionIndex default="20" type="input"}
        AND customDimensions.value IS NOT NULL
        AND customDimensions.value != '(not set)'
      GROUP EACH BY
        hitId,
        productAvailability) AS t2
    ON
      (t1.hits.hitId=t2.hitId)
    GROUP EACH BY
      date,
      numberOfWeek,
      numberOfMonth,
      productAvailability) AS t3
  LEFT JOIN EACH (
    SELECT
      date,
      numberOfWeek,
      numberOfMonth,
      COUNT(t2.hitId) AS totalSessions
    FROM (
      SELECT
        date,
        hits.hitId,
        INTEGER(CEIL((DAYOFYEAR(TIMESTAMP(INTEGER(hits.time*1000000)))-4)/7-0.01+1)) AS numberOfWeek,
        MONTH(hits.time+1000) AS numberOfMonth
      FROM
        FLATTEN ((
          SELECT
            date,
            hits.eCommerceActionType,
            hits.time,
            hits.hitId,
            hits.isEntrance
          FROM
            TABLE_DATE_RANGE({dataSetName}.{sTableName}, TIMESTAMP('{startDate}'), TIMESTAMP('{endDate}'))), hits)
      WHERE
        hits.eCommerceActionType = 'detail'
        AND hits.isEntrance = 1
      GROUP EACH BY
        hits.hitId,
        date,
        numberOfWeek,
        numberOfMonth ) AS t1
    JOIN EACH (
      SELECT
        hitId,
        customDimensions.value AS productAvailability
      FROM
        FLATTEN ((
          SELECT
            sessionId,
            date,
            eCommerceAction.action_type,
            hitId,
            customDimensions.index,
            customDimensions.value
          FROM
            TABLE_DATE_RANGE({dataSetName}.{hTableName}, TIMESTAMP('{startDate}'), TIMESTAMP('{endDate}'))), customDimensions)
      WHERE
        eCommerceAction.action_type = 'detail'
        AND customDimensions.index = {dimensionIndex}
        AND customDimensions.value IS NOT NULL
        AND customDimensions.value != '(not set)'
      GROUP EACH BY
        hitId,
        productAvailability) AS t2
    ON
      (t1.hits.hitId=t2.hitId)
    GROUP EACH BY
      date,
      numberOfWeek,
      numberOfMonth) AS t4
  ON
    (t3.date=t4.date))
```
