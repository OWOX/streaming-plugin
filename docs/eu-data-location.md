# Transferring to and storing data in the EU location

If it's important for your business to store user data you collect into Google BigQuery with OWOX BI within the European Union, you can transfer the existing data from the US (United States) to the EU (European Union) multi-region locations.

In this article, we describe two possible methods of how you can do it.

To move your Google BigQuery data to a different location, use one of the following methods.

## Method 1. Use the dataset copy feature in Google Data Transfer (now in beta)

The newest method provided by Google. It lets you copy datasets in the same region or across regions. The feature is a part of [BigQuery Data Transfer Service](https://cloud.google.com/bigquery-transfer/docs/enable-transfer-service), so make sure you have it enabled.

Read the detailed guide to copying datasets in the [BigQuery documentation](https://cloud.google.com/bigquery/docs/copying-datasets).

## Method 2. Use a Google Cloud Storage bucket

You need to create new datasets located in the EU location and a Google Cloud Storage bucket, which will be the destination for the old dataset data and the source for the new datasets.

1.  Create a new dataset in the EU location as [described in Google Cloud Platform documentation](https://cloud.google.com/bigquery/docs/datasets#create-dataset).
2.  [Create a storage bucket](https://cloud.google.com/storage/docs/creating-buckets), a Google Cloud Storage container to store the table data from the US datasets.
3.  [Export the table](https://cloud.google.com/bigquery/docs/exporting-data) data into the created bucket with the following settings:

    ![The Export to Google Cloud Storage dialog: Avro format, no compression, a wildcard file name in the bucket](https://cdn.owox.com/plugins/streaming/eu-data-location/01/public)

4.  In the EU dataset, create a [table that references an external data source](https://cloud.google.com/bigquery/docs/tables#creating_a_table_that_references_an_external_data_source) and transfer your data from Google Cloud Storage.

> Each table should be exported separately.

![The Create Table form with "Create from source" selected and the exported files as the source](https://cdn.owox.com/plugins/streaming/eu-data-location/02/public)

That's it. Now your data is stored in the EU.

> After you complete the settings, contact us at [bi@owox.com](mailto:bi@owox.com?subject=Transferring%20data%20to%20the%20EU%20location). We'll need your project name and names of the tables in the EU location to check if all the processes work correctly.
