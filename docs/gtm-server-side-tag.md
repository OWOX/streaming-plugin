# Google Analytics 4 RawData to BigQuery – OWOX Tag for GTM Server-Side

### Step-by-step: How to add OWOX Tag

[The OWOX Tag for GTM Server-Side](https://tagmanager.google.com/gallery/#/owners/OWOX/templates/ga4-rawdata-to-bigquery) allows you to automatically forward GA4 events to a BigQuery table if you use GTM SS as a Server-Side Tag Manager for GA4 events so that you can collect GA4 raw data for further processing within your data warehouse. This allows you to define new events without adjusting the schema in BigQuery first.

#### Step 1. Create an appropriate table in your Google BigQuery

Firstly, you need to create a Google BigQuery table with a specified schema for collecting raw data into it. Follow these three simple steps.

##### 1.1. Create a dataset in a GBQ project

![v2-01.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/01/public)

##### 1.2. Choose a data location

![v2-02.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/02/public)

##### 1.3. Create a table with the following schema

| Field name | Type | Mode |
| --- | --- | --- |
| rawDataOwox | STRING | REQUIRED |
| createdAt | TIMESTAMP | REQUIRED |

The fastest way is to run the SQL code below in GBQ:

```
CREATE TABLE IF NOT EXISTS [project_name].[dataset_name].ga4RawDataOwox (rawDataOwox STRING NOT NULL, createdAt TIMESTAMP NOT NULL) OPTIONS(description="GA4 Raw data transferred directly to GBQ from my server-side GTM using OWOX tag")
```

![v2-03.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/03/public)

> **Note!**  
> You need to insert your `[project_name].[dataset_name]`

#### Step 2. Adding the tag from Templates to your Workspace

We have published OWOX Tag in the Templates Gallery. Therefore, you can find it and add it to your Workspace in a few steps.

##### 2.1. Open Search Gallery

Go to Templates→Tag Templates→Search Gallery in your Server-side GTM container

![v2-04.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/04/public)

##### 2.2. Find and choose the “Google Analytics 4 RawData to BigQuery” tag

![v2-05-new.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/05/public)

##### 2.3. Add it to your workspace:

![v2-06-new.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/06/public)

#### Step 3. Adding the tag

Go to Tags and add this tag. Choose a needed trigger and set the path to your table in GBQ.

> **Note!**  
> Check the checkbox “Enable logging” only for debugging.

![v2-07-new.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/07/public)

> As long as your Server container runs in the same GCP project with the BigQuery table, you are all set. You don’t need to do anything in terms of authentication, as the default service account of App Engine has the fullest access to any BigQuery tables added to the project.

#### Step 4. Publishing

Submit and Publish the changes and test the flow in Preview mode. If everything is correct, you’ll see a fired tag “**Google Analytics 4 RawData to BigQuery**”:

![v2-08-1-new.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/08/public)

And you will see the data in the Google BigQuery table:

![v2-08-2-new.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/09/public)

### How to use this data

**Unlock a better way to work with your marketing data...**

In the demo, you will discuss a complex and clear analytics workflow to achieve higher ROI and lower ad spending with accurate reporting and insights. After the demo, you’ll receive a customized solution for reaching your business goals.

[BOOK A DEMO](https://www.owox.com/demo/)

**...or write your own SQL code**

Write SQL code to use this data in a convenient structure for your reports. Example:

```
SELECT
 JSON_EXTRACT_SCALAR(rawDataOwox, '$.page_referrer') AS page_referrer,
 JSON_EXTRACT_SCALAR(rawDataOwox, '$.page_title') AS page_title,
 JSON_EXTRACT_SCALAR(rawDataOwox, '$.event_name') AS event_name,
 JSON_EXTRACT_SCALAR(rawDataOwox, '$.user_agent') AS user_agent
FROM `[project_name].[dataset_name].ga4RawDataOwox`
```

![v2-09.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/10/public)

Contact us [bi@owox.com](mailto:bi@owox.com) to use our tools (and professional services) to transform raw data into business-ready data.

### Possible issues

#### BigQuery insert error: BigQuery API has not been used in a project

**Problem:** You have not enabled “BigQuery API” in your GCP project. If so, you’ll see the following error in your console:

![v2-10.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/11/public)

**How to solve:** Follow the suggested link and enable API for your GCP project.

![v2-11.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/12/public)

#### BigQuery insert error: Access Denied

**Problem:** The GCP project with your Server-Side GTM container differs from the GCP project with the GBQ table set in tag settings. If so, you’ll see the following error in a console:

![v2-12.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/13/public)

**How to solve:** If you want to write to a BigQuery table in _another_ GCP project, you need to do the following.

1.  Find the _App Engine default service account_ (under [APIs & Services / Credentials](https://console.cloud.google.com/apis/credentials)) of the Server container project.  
    ![v2-13.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/14/public)
2.  Copy the email address of this service account to the clipboard.
3.  In the [IAM](https://console.cloud.google.com/iam-admin/iam) of the GCP project with the BigQuery table add a new member.
4.  Set the email address of the member to the email address in the clipboard.
5.  Set the role of this new member to **BigQuery Data Editor**.  
    ![v2-14.jpg](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/gtm-server-side-tag/15/public)
