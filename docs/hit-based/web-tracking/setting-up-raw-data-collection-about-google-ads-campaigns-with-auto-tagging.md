# Setting up raw data collection about Google Ads campaigns with auto-tagging

The OWOX BI session data collection algorithm retrieves information about the traffic sources from Google Ads data tables in Google BigQuery. For the Ads campaigns with auto-tagging, the information is retrieved using the gclid parameter.

> **Note**  
> If you have auto-tagging enabled in your Google Ads campaigns, then setting up raw Google Ads reports **is required**. Without it, the gclid data necessary for retrieving info traffic sources in the campaigns with auto-tagging will not be available.  
>   
> If you are using manual tagging using the UTM tags, you don't need to set up additional reporting. The cost data will be distributed according to the UTM tags.  
>   
> If you use both auto-tagging and manual tagging in your Google Ads campaigns, read [this article](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/faq.md#which-values-will-be-recorded-to-the-hit-and-session-data-tables-when-gclid-auto-tagging-and-manual-utm-tagging-data-overlap-in-google-ads).

## Why collect raw Google Ads campaign data

When you enable [auto-tagging](https://support.google.com/analytics/answer/1033981?hl=en&ref_topic=1308612) in your Google Ads campaigns, the final URLs contain only the gclid parameter. Campaign names, keywords, and other parameters can be retrieved only by bringing together in BigQuery additional reports with raw data from Google Ads.

The campaign data is also required for the session attributed ad cost calculation in the `attributedAdCost` field.

Before you create a user behavior data pipeline, upload data from all your Google Ads accounts. If you don't do it, historical data on tagging will not automatically update.

If you didn't connect a new Google Ads to Data Transfer, you can write to us at [bi@owox.com](mailto:bi@owox.com) and we'll help you update your data.

You can view all your linked Google Ads accounts in Google Analytics. Go to **Admin** → **View settings**:![image9.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging/01/public)

## How to get these Google Ads reports

There are two methods to get the Ads campaigns data to your "owoxbi\_sessions" session data tables:

**Method 1. Configure automatic transfer of Google Ads reporting data into Google BigQuery using the [BigQuery Data Transfer](https://cloud.google.com/bigquery/transfer/) service.**

Pros of the method: 

-   It's a native integration. You need only a few clicks to set it up and it will collect all the raw data.
-   Automatically uploads historical data for previous months.
-   It's free.

If you have several Google Ads accounts, you can upload data from them either to a single BigQuery dataset or create a dedicated dataset for each account. The choice is up to your convenience.

**Method 2. Set up the automatic [Click Performance Report](https://developers.google.com/adwords/api/docs/appendix/reports/click-performance-report) using a Google Ads script.**

Pro: 

-   It's free.

Cons:

-   You can't configure the data period in the script. It will be retrieving reports for the previous day only.
-   You have to customize the script to make it fit your account. Feel free to use the script example from this article, but you still might need to adjust the code.

Read how to set up each of these methods below in the article. However, we recommend you use the [Data Transfer](https://cloud.google.com/bigquery/transfer/) method — It's easier and has no drawbacks compared to the Google Ads script method. 

The Google Ads reports collect the following fields to the "owoxbi\_sessions" tables:

-   `trafficSource.source: google`
-   `trafficSource.medium: cpc`
-   `trafficSource.campaign`
-   `trafficSource.keyword`
-   `trafficSource.adGroup`
-   `trafficSource.keywordMatchType`

> The values _google/cpc_ corresponds exactly to the [Google Analytics processing flow](https://support.google.com/analytics/answer/6205762?hl=en#flowchart).

> **Note**  
> If you have more than one Google Ads account, you need to set up the reports for each of them.

**Read also:** [How OWOX BI updates data about Google Ads campaigns with auto-tagging in session data tables](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#collecting-and-updating-data-about-google-ads-campaigns-with-auto-tagging-in-session-data-tables)

## Upload reports using BigQuery Data Transfer

You can activate automatic data transfers from Google Ads to Google BigQuery using the BigQuery Data Transfer Service. To do this, take the following steps:

**1.** Enroll and enable BigQuery Data Transfer Service following the [BigQuery documentation](https://cloud.google.com/bigquery/docs/enable-transfer-service).

**2.** Set up automatic data transfer into Google BigQuery, as described in this [documentation](https://cloud.google.com/bigquery/docs/adwords-transfer). If you have several Google Ads accounts connected to a [Manager Account (MCC)](https://support.google.com/google-ads/answer/6139186), you can create data transfers for each of those connected accounts [using the MCC](https://cloud.google.com/bigquery/docs/adwords-transfer#support_for_google_ads_manager_accounts). Also, note that each of these accounts will be billed separately since [data transfer is billed](https://cloud.google.com/bigquery/transfer/#bigquery-data-transfer-service-pricing) $2.50 per unique Customer ID — `ExternalCustomerID` in the 'Customer' table.

Data uploading will start once you save the settings or at the scheduled time.

> If you need to import historical data from Google Ads to Google BigQuery, create a [backfill request](https://cloud.google.com/bigquery/docs/working-with-transfers#manually_trigger_a_transfer_or_backfill) in BigQuery Data Transfer Service.  
> **Note**, OWOX BI requires `gclid` data from the Click Performance Report that can not be retrieved via Data Transfer for more than 90 days back. Therefore, you should **schedule a backfill for a period that is within the last 90 days**.

**3.** In OWOX BI:

\- Go to your pipeline page.

\- On the **Settings tab**, in the **Session data collection** section, click **Change settings**.

**![edit1\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging/02/public)**

\- On the **Settings for session collection based on raw hit data** screen, switch on **Google Ads reports** and click **Change settings**.

**![edit2\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging/03/public)**

\- On the opened screen **Data from Google Ads campaigns with auto-tagging**, set the **Data upload priority** to **Auto-tagging (GCLID values)**, and **BigQuery Data Transfer** as a **Google Ads reporting** **method**. Then specify the BigQuery project and dataset BigQuery Data Transfer will upload data to. Confirm your selection by clicking **Save Settings** on the current screen. 

![save1\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging/04/public)

\- Once you get back to the **Settings for session collection based on raw hit data** screen, click **Save settings** one more time to apply the changes to your pipeline settings.

![save2\_en.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging/05/public)

## Upload reports using Google Ads scripts

You can use the following two scripts to export Google Ads reports to BigQuery:

-   [Script](https://developers.google.com/adwords/scripts/docs/solutions/bigquery-exporter?hl=en) to export data for a single account
-   [Script](https://developers.google.com/adwords/scripts/docs/solutions/mccapp-bigquery-exporter) for a Google Ads manager account (MCC).

**How to add a script?**

1.  In Google Ads, in the upper-right of the interface, click **Tools**.
2.  In the **Bulk Actions** column, click **Scripts**: ![Ads\_scripts\_1-2.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging/06/public)
3.  Click the **plus** button to add a script: ![Ads\_scripts\_3.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging/07/public)
4.  In the script field, copy and paste the script from below the screenshot: ![Ads\_scripts\_4.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging/08/public)

The script example for a single account (not MCC). Copy it and replace **BQ project name** and **Your email** with the name of your BigQuery project and your email accordingly:

/\*\*  
 \* @name Export Data to BigQuery  
 \*  
 \* @overview The Export Data to BigQuery script sets up a BigQuery  
 \* dataset and tables, downloads a report from AdWords and then  
 \* loads the report to BigQuery.  
 \*  
 \* @author AdWords Scripts Team \[adwords-scripts@googlegroups.com\]  
 \*  
 \* @version 1.3  
 \*/  
   
var CONFIG = {  
 BIGQUERY\_PROJECT\_ID: 'BQ project name',  
 BIGQUERY\_DATASET\_ID: AdWordsApp.currentAccount().getCustomerId().replace(/-/g, '\_'),  
   
 // Truncate existing data, otherwise will append.  
 TRUNCATE\_EXISTING\_DATASET: false,  
 TRUNCATE\_EXISTING\_TABLES: true,  
   
 // Lists of reports and fields to retrieve from AdWords.  
 REPORTS: \[\],  
   
 RECIPIENT\_EMAILS: \[  
 'Your email'  
 \]  
};  
   
var report = {  
 NAME: 'CLICK\_PERFORMANCE\_REPORT', //https://developers.google.com/adwords/api/docs/appendix/reports/click-performance-report  
 CONDITIONS: '',  
 FIELDS: {'AccountDescriptiveName': 'STRING',  
 'AdFormat': 'STRING',  
 'AdGroupId': 'STRING',  
 'AdGroupName': 'STRING',   
 'AoiCountryCriteriaId': 'STRING',  
 'CampaignId': 'STRING',  
 'CampaignLocationTargetId': 'STRING',  
 'CampaignName': 'STRING',  
 'CampaignStatus': 'STRING',  
 'Clicks': 'INTEGER',  
 'ClickType': 'STRING',   
 'CreativeId': 'STRING',   
 'CriteriaId': 'STRING',   
 'CriteriaParameters': 'STRING',   
 'Date': 'DATE',  
 'Device': 'STRING',   
 'ExternalCustomerId': 'STRING',   
 'GclId': 'STRING',  
 'KeywordMatchType': 'STRING',   
 'LopCountryCriteriaId': 'STRING',   
 'Page': 'INTEGER'  
 },  
 DATE\_RANGE: new Date(new Date().setDate(new Date().getDate()-1)).toISOString().slice(0, 10).replace(/-/g, "")+','+new Date(new Date().setDate(new Date().getDate()-1)).toISOString().slice(0, 10).replace(/-/g, ""),  
 DATE: new Date(new Date().setDate(new Date().getDate()-1)).toISOString().slice(0, 10).replace(/-/g, "")  
};  
   
//Regular export  
CONFIG.REPORTS.push(JSON.parse(JSON.stringify(report)));  
   
//One-time historical export  
//for(var i=2;i<91;i++){  
// report.DATE\_RANGE = new Date(new Date().setDate(new Date().getDate()-i)).toISOString().slice(0, 10).replace(/-/g, "")+','+new Date(new Date().setDate(new Date().getDate()-i)).toISOString().slice(0, 10).replace(/-/g, "");  
// report.DATE = new Date(new Date().setDate(new Date().getDate()-i)).toISOString().slice(0, 10).replace(/-/g, "");  
// CONFIG.REPORTS.push(JSON.parse(JSON.stringify(report)));  
//}  
   
/\*\*  
 \* Main method  
 \*/  
function main() {  
 createDataset();  
 for (var i = 0; i < CONFIG.REPORTS.length; i++) {  
 var reportConfig = CONFIG.REPORTS\[i\];  
 createTable(reportConfig);  
 }  
   
 var jobIds = processReports();  
 waitTillJobsComplete(jobIds);  
 sendEmail(jobIds);  
}  
   
   
/\*\*  
 \* Creates a new dataset.  
 \*  
 \* If a dataset with the same id already exists and the truncate flag  
 \* is set, will truncate the old dataset. If the truncate flag is not  
 \* set, then will not create a new dataset.  
 \*/  
function createDataset() {  
 if (datasetExists()) {  
 if (CONFIG.TRUNCATE\_EXISTING\_DATASET) {  
 BigQuery.Datasets.remove(CONFIG.BIGQUERY\_PROJECT\_ID,  
 CONFIG.BIGQUERY\_DATASET\_ID, {'deleteContents' : true});  
 Logger.log('Truncated dataset.');  
 } else {  
 Logger.log('Dataset %s already exists. Will not recreate.',  
 CONFIG.BIGQUERY\_DATASET\_ID);  
 return;  
 }  
 }  
   
 // Create new dataset.  
 var dataSet = BigQuery.newDataset();  
 dataSet.friendlyName = CONFIG.BIGQUERY\_DATASET\_ID;  
 dataSet.datasetReference = BigQuery.newDatasetReference();  
 dataSet.datasetReference.projectId = CONFIG.BIGQUERY\_PROJECT\_ID;  
 dataSet.datasetReference.datasetId = CONFIG.BIGQUERY\_DATASET\_ID;  
   
 dataSet = BigQuery.Datasets.insert(dataSet, CONFIG.BIGQUERY\_PROJECT\_ID);  
 Logger.log('Created dataset with id %s.', dataSet.id);  
}  
   
/\*\*  
 \* Checks if dataset already exists in project.  
 \*  
 \* @return {boolean} Returns true if dataset already exists.  
 \*/  
function datasetExists() {  
 // Get a list of all datasets in project.  
 var datasets = BigQuery.Datasets.list(CONFIG.BIGQUERY\_PROJECT\_ID);  
 var datasetExists = false;  
 // Iterate through each dataset and check for an id match.  
 if (datasets.datasets != null) {  
 for (var i = 0; i < datasets.datasets.length; i++) {  
 var dataset = datasets.datasets\[i\];  
 if (dataset.datasetReference.datasetId == CONFIG.BIGQUERY\_DATASET\_ID) {  
 datasetExists = true;  
 break;  
 }  
 }  
 }  
 return datasetExists;  
}  
   
/\*\*  
 \* Creates a new table.  
 \*  
 \* If a table with the same id already exists and the truncate flag  
 \* is set, will truncate the old table. If the truncate flag is not  
 \* set, then will not create a new table.  
 \*  
 \* @param {Object} reportConfig Report configuration including report name,  
 \* conditions, and fields.  
 \*/  
function createTable(reportConfig) {  
 var tableName = reportConfig.NAME+reportConfig.DATE;  
 if (tableExists(tableName)) {  
 if (CONFIG.TRUNCATE\_EXISTING\_TABLES) {  
 BigQuery.Tables.remove(CONFIG.BIGQUERY\_PROJECT\_ID,  
 CONFIG.BIGQUERY\_DATASET\_ID, tableName);  
 Logger.log('Truncated table %s.', tableName);  
 } else {  
 Logger.log('Table %s already exists. Will not recreate.',  
 tableName);  
 return;  
 }  
 }  
   
 // Create new table.  
 var table = BigQuery.newTable();  
 var schema = BigQuery.newTableSchema();  
 var bigQueryFields = \[\];  
   
 // Add each field to table schema.  
 var fieldNames = Object.keys(reportConfig.FIELDS);  
 for (var i = 0; i < fieldNames.length; i++) {  
 var fieldName = fieldNames\[i\];  
 var bigQueryFieldSchema = BigQuery.newTableFieldSchema();  
 bigQueryFieldSchema.description = fieldName;  
 bigQueryFieldSchema.name = fieldName;  
 bigQueryFieldSchema.type = reportConfig.FIELDS\[fieldName\];  
   
 bigQueryFields.push(bigQueryFieldSchema);  
 }  
   
 schema.fields = bigQueryFields;  
 table.schema = schema;  
 table.friendlyName = tableName;  
   
 table.tableReference = BigQuery.newTableReference();  
 table.tableReference.datasetId = CONFIG.BIGQUERY\_DATASET\_ID;  
 table.tableReference.projectId = CONFIG.BIGQUERY\_PROJECT\_ID;  
 table.tableReference.tableId = tableName;  
   
 table = BigQuery.Tables.insert(table, CONFIG.BIGQUERY\_PROJECT\_ID,  
 CONFIG.BIGQUERY\_DATASET\_ID);  
   
 Logger.log('Created table with id %s.', table.id);  
}  
   
/\*\*  
 \* Checks if table already exists in dataset.  
 \*  
 \* @param {string} tableId The table id to check existence.  
 \*  
 \* @return {boolean} Returns true if table already exists.  
 \*/  
function tableExists(tableId) {  
 // Get a list of all tables in the dataset.  
 var tables = BigQuery.Tables.list(CONFIG.BIGQUERY\_PROJECT\_ID,  
 CONFIG.BIGQUERY\_DATASET\_ID);  
 var tableExists = false;  
 // Iterate through each table and check for an id match.  
 if (tables.tables != null) {  
 for (var i = 0; i < tables.tables.length; i++) {  
 var table = tables.tables\[i\];  
 if (table.tableReference.tableId == tableId) {  
 tableExists = true;  
 break;  
 }  
 }  
 }  
 return tableExists;  
}  
   
/\*\*  
 \* Process all configured reports  
 \*  
 \* Iterates through each report to: retrieve AdWords data,  
 \* backup data to Drive (if configured), load data to BigQuery.  
 \*  
 \* @return {Array.<string>} jobIds The list of all job ids.  
 \*/  
function processReports() {  
 var jobIds = \[\];  
   
 // Iterate over each report type.  
 for (var i = 0; i < CONFIG.REPORTS.length; i++) {  
 var reportConfig = CONFIG.REPORTS\[i\];  
 Logger.log('Running report %s', reportConfig.NAME);  
 // Get data as csv  
 var csvData = retrieveAdwordsReport(reportConfig);  
 //Logger.log(csvData);  
 // Convert to Blob format.  
 var blobData = Utilities.newBlob(csvData, 'application/octet-stream');  
 // Load data  
 var jobId = loadDataToBigquery(reportConfig, blobData);  
 jobIds.push(jobId);  
 }  
 return jobIds;  
}  
   
/\*\*  
 \* Retrieves AdWords data as csv and formats any fields  
 \* to BigQuery expected format.  
 \*  
 \* @param {Object} reportConfig Report configuration including report name,  
 \* conditions, and fields.  
 \*  
 \* @return {string} csvData Report in csv format.  
 \*/  
function retrieveAdwordsReport(reportConfig) {  
 var fieldNames = Object.keys(reportConfig.FIELDS);  
 var query = 'SELECT ' + fieldNames.join(', ') +  
 ' FROM ' + reportConfig.NAME + '' + reportConfig.CONDITIONS +  
 ' DURING ' + reportConfig.DATE\_RANGE;  
 Logger.log(query);  
 var report = AdWordsApp.report(query);  
 var rows = report.rows();  
 var csvRows = \[\];  
 // Header row  
 csvRows.push(fieldNames.join(','));  
   
 // Iterate over each row.  
 while (rows.hasNext()) {  
 var row = rows.next();  
 var csvRow = \[\];  
 for (var i = 0; i < fieldNames.length; i++) {  
 var fieldName = fieldNames\[i\];  
 var fieldValue = row\[fieldName\].toString();  
 var fieldType = reportConfig.FIELDS\[fieldName\];  
 // Strip off % and perform any other formatting here.  
 if (fieldType == 'FLOAT' || fieldType == 'INTEGER') {  
 if (fieldValue.charAt(fieldValue.length - 1) == '%') {  
 fieldValue = fieldValue.substring(0, fieldValue.length - 1);  
 }  
 fieldValue = fieldValue.replace(/,/g,'');  
   
 if (fieldValue == '--' || fieldValue == 'Unspecified') {  
 fieldValue = '';  
 }  
 }  
 // Add double quotes to any string values.  
 if (fieldType == 'STRING') {  
 if (fieldValue == '--') {  
 fieldValue = '';  
 }  
 fieldValue = fieldValue.replace(/"/g, '""');  
 fieldValue = '"' + fieldValue + '"';  
 }   
 csvRow.push(fieldValue);  
 }  
 csvRows.push(csvRow.join(','));  
 }  
 Logger.log('Downloaded ' + reportConfig.NAME + ' with ' + csvRows.length +  
 ' rows.');  
 return csvRows.join('\\n');  
}  
   
/\*\*  
 \* Creates a BigQuery insertJob to load csv data.  
 \*  
 \* @param {Object} reportConfig Report configuration including report name,  
 \* conditions, and fields.  
 \* @param {Blob} data Csv report data as an 'application/octet-stream' blob.  
 \*  
 \* @return {string} jobId The job id for upload.  
 \*/  
function loadDataToBigquery(reportConfig, data) {  
 // Create the data upload job.  
 var job = {  
 configuration: {  
 load: {  
 destinationTable: {  
 projectId: CONFIG.BIGQUERY\_PROJECT\_ID,  
 datasetId: CONFIG.BIGQUERY\_DATASET\_ID,  
 tableId: reportConfig.NAME + reportConfig.DATE  
 },  
 skipLeadingRows: 1  
 }  
 }  
 };  
   
 var insertJob = BigQuery.Jobs.insert(job, CONFIG.BIGQUERY\_PROJECT\_ID, data);  
 Logger.log('Load job started for %s. Check on the status of it here: ' +  
 'https://bigquery.cloud.google.com/jobs/%s', reportConfig.NAME,  
 CONFIG.BIGQUERY\_PROJECT\_ID);  
 return insertJob.jobReference.jobId;  
}  
   
/\*\*  
 \* Polls until all jobs are 'DONE'.  
 \*  
 \* @param {Array.<string>} jobIds The list of all job ids.  
 \*/  
function waitTillJobsComplete(jobIds) {  
 var complete = false;  
 var remainingJobs = jobIds;  
 while (!complete) {  
 if (AdWordsApp.getExecutionInfo().getRemainingTime() < 5){  
 Logger.log('Script is about to timeout, jobs ' + remainingJobs.join(',') +  
 ' are still incomplete.');  
 }  
 remainingJobs = getIncompleteJobs(remainingJobs);  
 if (remainingJobs.length == 0) {  
 complete = true;  
 }  
 if (!complete) {  
 Logger.log(remainingJobs.length + ' jobs still being processed.');  
 // Wait 5 seconds before checking status again.  
 Utilities.sleep(5000);  
 }  
 }  
 Logger.log('All jobs processed.');  
}  
   
/\*\*  
 \* Iterates through jobs and returns the ids for those jobs  
 \* that are not 'DONE'.  
 \*  
 \* @param {Array.<string>} jobIds The list of job ids.  
 \*  
 \* @return {Array.<string>} remainingJobIds The list of remaining job ids.  
 \*/  
function getIncompleteJobs(jobIds) {  
 var remainingJobIds = \[\];  
 for (var i = 0; i < jobIds.length; i++) {  
 var jobId = jobIds\[i\];  
 var getJob = BigQuery.Jobs.get(CONFIG.BIGQUERY\_PROJECT\_ID, jobId);  
 if (getJob.status.state != 'DONE') {  
 remainingJobIds.push(jobId);  
 }  
 }  
 return remainingJobIds;  
}  
   
   
/\*\*  
 \* Sends a notification email that jobs have completed loading.  
 \*  
 \* @param {Array.<string>} jobIds The list of all job ids.  
 \*/  
function sendEmail(jobIds) {  
 var html = \[\];  
 html.push(  
 '<html>',  
 '<body>',  
 '<table width=800 cellpadding=0 border=0 cellspacing=0>',  
 '<tr>',  
 '<td colspan=2 align=right>',  
 "<div style='font: italic normal 10pt Times New Roman, serif; " +  
 "margin: 0; color: #666; padding-right: 5px;'>" +  
 'Powered by AdWords Scripts</div>',  
 '</td>',  
 '</tr>',  
 "<tr bgcolor='#3c78d8'>",  
 '<td width=500>',  
 "<div style='font: normal 18pt verdana, sans-serif; " +  
 "padding: 3px 10px; color: white'>Adwords data load to " +  
 "Bigquery report</div>",  
 '</td>',  
 '<td align=right>',  
 "<div style='font: normal 18pt verdana, sans-serif; " +  
 "padding: 3px 10px; color: white'>",  
 AdWordsApp.currentAccount().getCustomerId(),  
 '</tr>',  
 '</table>',  
 '<table width=800 cellpadding=0 border=1 cellspacing=0>',  
 "<tr bgcolor='#ddd'>",  
 "<td style='font: 12pt verdana, sans-serif; " +  
 'padding: 5px 0px 5px 5px; background-color: #ddd; ' +  
 "text-align: left'>Report</td>",  
 "<td style='font: 12pt verdana, sans-serif; " +  
 'padding: 5px 0px 5px 5px; background-color: #ddd; ' +  
 "text-align: left'>JobId</td>",  
 "<td style='font: 12pt verdana, sans-serif; " +  
 'padding: 5px 0px 5x 5px; background-color: #ddd; ' +  
 "text-align: left'>Rows</td>",  
 "<td style='font: 12pt verdana, sans-serif; " +  
 'padding: 5px 0px 5x 5px; background-color: #ddd; ' +  
 "text-align: left'>State</td>",   
 "<td style='font: 12pt verdana, sans-serif; " +  
 'padding: 5px 0px 5x 5px; background-color: #ddd; ' +  
 "text-align: left'>ErrorResult</td>",  
 '</tr>',  
 createTableRows(jobIds),  
 '</table>',  
 '</body>',  
 '</html>');  
   
 MailApp.sendEmail(CONFIG.RECIPIENT\_EMAILS.join(','),  
 'Adwords data load to Bigquery Complete', '',  
 {htmlBody: html.join('\\n')});  
}  
   
/\*\*  
 \* Creates table rows for email report.  
 \*  
 \* @param {Array.<string>} jobIds The list of all job ids.  
 \*/  
function createTableRows(jobIds) {  
 var html = \[\];  
 for (var i = 0; i < jobIds.length; i++) {  
 var jobId = jobIds\[i\];  
 var job = BigQuery.Jobs.get(CONFIG.BIGQUERY\_PROJECT\_ID, jobId);  
 var errorResult = '';  
 if (job.status.errorResult) {  
 errorResult = job.status.errorResult;  
 }  
   
 html.push('<tr>',  
 "<td style='padding: 0px 10px'>" +  
 job.configuration.load.destinationTable.tableId + '</td>',  
 "<td style='padding: 0px 10px'>" + jobId + '</td>',  
 "<td style='padding: 0px 10px'>" + job.statistics.load?job.statistics.load.outputRows:0 + '</td>',  
 "<td style='padding: 0px 10px'>" + job.status.state + '</td>',  
 "<td style='padding: 0px 10px'>" + errorResult + '</td>',  
 '</tr>');  
 }  
 return html.join('\\n');  
}

**To make the script work, activate Google BigQuery API in the API library.**

To do that:

1.  In Google Ads, click **Advanced APIs**: ![Ads\_scripts\_5.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging/09/public)
2.  Select **BigQuery**: ![Ads\_scripts\_6.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/hit-based-setting-up-raw-data-collection-about-google-ads-campaigns-with-auto-tagging/10/public)
3.  Follow the link and enable BigQuery API in the API library.
4.  Click **Save**. Done! Google BigQuery API is activated and the script you've set up will collect Google Ads reports to BigQuery.

The following fields from Google Ads [Click Performance Report](https://developers.google.com/adwords/api/docs/appendix/reports/click-performance-report) will be used in session data tables:

-   `GclId`
-   `CampaignId`
-   `CampaignName`
-   `AdGroupId`
-   `AdGroupName`
-   `CriteriaId`
-   `CriteriaParameters`
-   `KeywordMatchType`

These fields are **required**.

> **Please note:**  
> You can use Google Ads scripts to retrieve any supported fields you need in addition to those listed above. But make sure you've included all the required fields in the report.
