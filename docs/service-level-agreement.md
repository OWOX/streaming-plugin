# OWOX BI Streaming: Service-level agreement

During the term of the OWOX BI Service Agreement, the Covered Service will provide a Service Level Objective of Monthly Uptime Percentage as **\>=96%**. 

If OWOX does not meet the Service Level Objective, and if Customer meets its obligations under this SLA, Customer will be eligible to receive the Financial Credits described below. This SLA states the Customer's sole and exclusive remedy for any failure by OWOX to meet the SLO.

### Definitions

The following definitions apply to the SLA:

-   **"Covered Service"** means [OWOX BI hit-based web streaming to Google BigQuery](https://github.com/OWOX/streaming-plugin/tree/main/docs/hit-based) and [OWOX BI GA4 events-based web streaming to Google BigQuery](https://github.com/OWOX/streaming-plugin/tree/main/docs/ga4-events-based).
-   **"Downtime"** means more than five percent of valid hits that result in no data saved to BigQuery. Downtime is measured based on the server-side Error Rate.
-   **"Downtime Period"** means a period of ten consecutive minutes of Downtime. Intermittent Downtime for a period of less than ten consecutive minutes will not be counted towards any Downtime Periods.
-   **"Error Rate"** means the number of hits that result in no data saved to Google BigQuery divided by the total number of hits during that period.
-   **"Financial Credit"** means the following:
    
    | Monthly Uptime Percentage | Percentage of invoices for OWOX BI Products that will be credited to Customer's future invoices |
    | --- | --- |
    | 90% - <96% | 10% |
    | 80% - <90% | 25% |
    | <80% | 50% |
    
-   **"Monthly Uptime Percentage"** means the total number of minutes in a month, minus the number of minutes of Downtime suffered from all Downtime Periods in a month, divided by the total number of minutes in a month.
-   **"Valid Requests"** are requests that conform to [the Documentation](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/set-up-data-collection.md), and that would normally be processed and saved to Google BigQuery without an error.
-   **"OWOX BI hit-based web streaming to Google BigQuery"** — the following restrictions apply to real-time hit collection:
    -   Maximum size of data transferred in a hit: 16 KB.
    -   The hit was sent from a region where Google has restricted its services, such as China, Crimea, Cuba, Iran, North Korea, Syria, and Vietnam.
    -   The dataset's location in the cost data pipeline and the [user behavior data pipeline](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/web-tracking.md#google-analytics--google-bigquery-create-a-user-behavior-web-streaming) do not match.

### Customer Must Request Financial Credit

In order to receive any of the Financial Credits described above, the Customer must [notify OWOX support](mailto:bi@owox.com) within thirty days from the time the Customer becomes eligible to receive a Financial Credit. The customer must also provide OWOX with log files showing the loss of hits streamed to Google BigQuery and the date and time those losses occurred. If Customer does not comply with these requirements, Customer will forfeit its right to receive a Financial Credit. If a dispute arises with respect to this SLA, OWOX will make a determination in good faith based on its system logs, monitoring reports, configuration records, and other available information.

### Maximum Financial Credit

The maximum aggregate number of Financial Credits issued by OWOX to Customers for all Downtime Periods in a single billing period will not exceed 50% of the amount due from Customers for OWOX BI Products for the applicable period. Financial Credits will be in the form of a monetary credit applied to future use of OWOX BI Products and will be applied within 60 days after the Financial Credit was requested.

### SLA Exclusions

The SLA does not apply to any errors: (i) caused by factors outside of OWOX’s reasonable control; (ii) that resulted from Customer’s software or hardware or third party software or hardware, or both; (iii) that resulted from abuses or other behaviors that violate [the Terms Agreement](https://www.owox.com/policies/terms/); or (iv) that resulted from [quotas](https://github.com/OWOX/streaming-plugin/blob/main/docs/hit-based/how-data-is-collected.md#data-collection-limitations) applied by the OWOX BI Products.
