# Set up GA4 events-based streaming

- [How to Set Up GA4 Event-Based Web Streaming (Client-Side / Server-Side)](#how-to-set-up-ga4-event-based-web-streaming-client-side--server-side)
- [How to Set Up Same Origin Streaming](#how-to-set-up-same-origin-streaming)
- [Set Up a Website Subdomain to Collect First-Party Data with ITP 2.2 Compliance](#set-up-a-website-subdomain-to-collect-first-party-data-with-itp-22-compliance)

## How to Set Up GA4 Event-Based Web Streaming (Client-Side / Server-Side)

[Watch the video](https://www.youtube.com/watch?v=1w5s1B6U-34)

This article describes event-based web streaming setup steps. 

### Getting started requirements

-   You are using the Google Cloud Platform and have an active project. And you have a **BigQuery Admin** or **BigQuery Data Owner** role ([how to set up GCP](https://www.owox.com/docs/storages/google-bigquery/)).
-   OWOX BI Streaming is available in your plan. If it’s not, write to your OWOX manager ([bi@owox.com](mailto:bi@owox.com)). 

### Streaming types

OWOX BI offers two types of event-based streaming: **Client-side** and **Server-side**. Review the key differences to choose the best option for your needs.

|   | Client-Side Streaming | Server-Side Streaming |
| --- | --- | --- |
| **Use cases:** | Overcoming the limitations of GA4 native export.<br>Track user behavior across multiple domains. | Overcoming the limitations of GA4 native export.<br>Bypass limitations in tracking traffic sources and transactions, such as those imposed by ITP restrictions on Apple devices.<br>Bypass AdBlockers for more reliable data collection. |
| **`[ouid](https://www.owox.com/docs/getting-started/consumption-units/)` based on:** | Third-party cookies. | First-party cookies. |
| **Setup steps:** | Create the Streaming following the instructions below. | First, create the Streaming using the instructions below. Then, contact our support team at [bi@owox.com](mailto:bi@owox.com) to switch from Client-Side to Server-Side streaming. |

### Step-by-step setup

**Step 1.** On the [Workspace page](https://bi.owox.com/ui/app/none/workspace), click the ‘**New**’ button and select the Streaming option in a drop-down menu:

![01.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/01/public)

Or, if your workspace page is empty, you will see a screen with three major blocks. Click the ‘**\+ New streaming**’ button.

![02.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/02/public)

**Step 2.** As a streaming type, select ‘**GA4 Event-based web streaming**’:

![03.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/03/public)

Click the '**Create & Setup**' button.

**Step 3.** The new **Client-Side streaming** has been successfully created in ‘**Draft**’ status.

To start collecting user behavior data from your website, you'll need to configure streaming settings, including the timezone, and destination dataset.

![04.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/04/public)

> **HOT TO CHANGE STREAMING TYPE**  
> If you need to switch from Client-Side to Server-Side streaming, please contact our support team at [bi@owox.com](mailto:bi@owox.com).

**Step 4.** Set up a timezone.

4.1. Click on the ‘**Timezone**’ section.

![05.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/05/public)

4.2. In the opened dialog, specify the timezone

![06.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/06/public)

4.3. Click the ‘**Save**’ button to apply your changes.

![07.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/07/public)

4.4. If all is done correctly, the dialog will close, and in the ‘Timezone’ section you will see a green marker with the timezone title.

![08.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/08/public)

**Step 5.** Set up a destination dataset.

5.1. Click on the ‘**Destination dataset**’ section.

![09.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/09/public)

5.2. In the opened dialog, where you can choose one of the shared datasets in the list. If this list is empty, or you didn’t find the desired dataset, you can click the ‘**Grant access…**’ button and add a new dataset [following the instructions](https://www.owox.com/docs/storages/google-bigquery/).

![10.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/10/public)

Ensure the dataset you select or create does not already contain `events_intraday_*` tables to avoid any data overlap. Read more about [setting up multiple Streaming instances for accurate data consumption](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/faq.md#how-to-set-up-multiple-owox-bi-streamings-for-accurate-data-consumption).

5.3. Click the ‘**Save**’ button to close the second dialog.

![11.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/11/public)

5.4. If all is done correctly, the dialog will close, and in the ‘Destination dataset’ section you will see a green marker with the name project.dataset.

![12.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/12/public)

**Step 6.** Activate this pipeline by clicking on the ‘**Activate**’ button.

![13.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/13/public)

The streaming will change status from ‘Draft’ to ‘Active’.

![14.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/14/public)

**Step 7.** Open the '**Data sending options**’ section.

![15.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/15/public)

Choose how to send data from your website to the OWOX BI access point and follow the instructions below:

-   Instructions on how to [configure your GTM container](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-a-website-using-google-tag-in-google-tag-manager)
-   Instructions on how to [change the standard gtag.js file](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-a-website-using-on-site-gtagjs-code)
-   Instructions on how to [send events from your server](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-your-server-using-the-measurement-protocol)

**The final step.** After successfully setting up the data-sending process to the OWOX BI access point and initiating it, you will see the first [‘events\_intraday\_YYYYMMDD’ table](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/schemas/data-schema-for-events-intraday-table.md) in your BigQuery dataset.

If you have any questions about streaming setup steps, please reach out to us at [bi@owox.com](mailto:bi@owox.com).

## How to Set Up Same Origin Streaming

This article explains how to set up Same Origin Streaming with OWOX BI to improve attribution accuracy and gain clear insights into your marketing campaigns and know your audience better.

> **Note:**  
> As a consequence of [ITP](https://webkit.org/blog/9521/intelligent-tracking-prevention-2-3/), short cookie lifespans in browsers like Safari and Chrome on iOS result in a growing share of `(direct)/(none)` traffic in analytics. Updating these mechanisms like “[Cap cookie lifetimes to 7 days for responses from third-party IP addresses](https://github.com/WebKit/WebKit/pull/5347)” only exacerbates the situation and makes previous solutions (e.g. subdomains) much less effective. This breaks the connection between user sessions and their original traffic sources, leading to misattributed conversions, skewed reports, and ineffective budget decisions.  
>   
> **Same Origin Streaming solves this problem** by extending cookie lifespans and enabling accurate user tracking, giving you reliable data for better decision-making.

### Getting Started Requirements

Before you begin, ensure you meet the following prerequisites:

1.  **Google Cloud Platform (GCP):**
    -   You must use GCP and have an active project.
    -   Ensure you have the **BigQuery Admin** or **BigQuery Data Owner** role ([how to set up GCP](https://www.owox.com/docs/storages/google-bigquery/)).
2.  **OWOX BI Streaming Availability:** Confirm that OWOX BI Streaming is included in your plan. If not, contact your OWOX manager at [bi@owox.com](mailto:bi@owox.com).
3.  **Access to Website Server Settings or CDN:** You’ll need administrative access to configure your server or CDN.

### Step-by-Step Setup Guide

#### Step 1: Create Client-Side Streaming

Start by setting up Client-Side Streaming. For detailed instructions, follow our [How to Set Up Client-Side Streaming](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/setup.md#how-to-set-up-ga4-event-based-web-streaming-client-side--server-side) guide.

![14-newwww.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/16/public)

#### Step 2: Switch to Server-Side Streaming

Contact the OWOX BI Support Team in chat or at [bi@owox.com](mailto:bi@owox.com) and r**equest to switch your streaming type from Client-Side to Server-Side**. Once confirmed, you can proceed with the next steps.

![14-newwww-server-side.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/17/public)

#### Step 3: Configure Your Server

**3.1. Route Traffic to the OWOX BI Streaming Endpoint**

Set up your server, CDN, or load balancer to route traffic from your website to the OWOX Streaming endpoint.

-   Use the endpoint URL provided by OWOX BI: `{https://streaming.bi.owox.com/ga4/...}`  
    ![14-newwww-server-side2222.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/18/public)
-   Create a path on your domain: For example, set up `www.example.com/owox` to forward traffic to the OWOX endpoint.

> **Example Workflow:**  
> 1\. The browser sends POST/GET requests to `www.example.com/owox` with Google Analytics 4 data.  
> 2\. Your server forwards these requests to the OWOX Streaming endpoint, ensuring headers and data remain intact.  
> 3\. OWOX BI processes the requests and sends a response.  
> 4\. Ensure your server modifies redirect URLs in the response headers (e.g., `Location`, `Content-Location`) to match your domain path (`example.com/owox`).

**3.2. Add the Server URL in Google Tag Manager or gtag**

-   **For Google Tag Manager** ([full guide](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-a-website-using-google-tag-in-google-tag-manager)):
    -   In your tag configuration, add a new parameter: `server_container_url`.
    -   Enter the server URL path (e.g., `www.example.com/owox`).
-   **For gtag** (Optional, [full guide](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-a-website-using-on-site-gtagjs-code)):
    -   Locate the `gtag('config')` command on your website.
    -   Add the `transport_url` parameter with your server URL as the value.

**3.3. Verify Configuration**

Visit your website and confirm that events previously sent to `streaming.bi.owox.com` are now sent to `example.com/owox`.

**You’re all done!**

### Common Implementation Challenges and Solutions

1.  **Routing Errors:** Double-check your server paths and proxy configurations to ensure traffic is forwarded correctly.
2.  **Header Adjustments:** The proxy server modifies the URL address in HTTP headers such as `Location`, `Content-Location`, and `Refresh` headers to match the original server's URL address instead of the proxy server's URL address.
3.  **Passing IP Information for GEO Tracking:** To ensure accurate GEO data for Same Origin Streaming, including server-side GTM (ssGTM), make sure the `x-forwarded-for` header is correctly configured to pass the user’s IP address.  
    
    > **IMPORTANT NOTE**  
    > This header allows the system to identify the user’s geographical location accurately. Without it, GEO-based analytics and personalization might be affected.
    

### Post-Implementation Testing and Results

After implementing Same Origin Streaming, monitor the following metrics to measure its effectiveness:

-   **Returning Users:** Expect a significant increase in correctly identified returning users.
-   **Direct/None Traffic:** Consider reducing `direct/none` sessions in your analytics.
-   **Transaction Attribution:** Verify that more transactions are accurately attributed to their original sources.

If you have any questions during the setup process, feel free to contact the OWOX BI Support Team at [bi@owox.com](mailto:bi@owox.com).

## Set Up a Website Subdomain to Collect First-Party Data with ITP 2.2 Compliance

To collect first-party data with compliance to [ITP 2.2 requirements](https://webkit.org/blog/8828/intelligent-tracking-prevention-2-2/) (see also other [known limitations](https://www.cookiestatus.com/) in browsers), OWOX BI allows you to collect user behavior data using a subdomain of your website (e.g., **owox**.your-domain.com).

### Step 1. Add a new A-DNS record

> **Important!**  
> OWOX BI Streaming is not compatible with the Cloudflare proxy. To ensure accurate collection of first-party data (client IP address and geolocation) of the event origin and proper SSL provisioning and renewal on your custom domain, you must set the DNS records' proxy status to "DNS Only" in Cloudflare.

Go to your domain administration panel and add a new DNS entry with the following settings:

-   Name: **owox**
-   Type: A
-   IP address: enter the IP address you received from the OWOX manager
-   TTL: 1h

### Step 2. Make sure the subdomain works as expected

Within 1-24 hours, the new subdomain **owox**.your-domain.com should resolve to the specified IP address. You can check it out at [nslookup.io](https://www.nslookup.io/). If everything is ok, you will see the info below:

![Frame\_2021.png](https://imagedelivery.net/zKr-4bdC5CBGL2DuuEmvYw/plugins/streaming/ga4-events-based-setup/19/public)

### Step 3. Setting up data collection

According to [the instructions for setting up OWOX BI streaming](https://github.com/OWOX/streaming-plugin/blob/main/docs/ga4-events-based/data-sending-options.md#sending-data-from-a-website-using-google-tag-in-google-tag-manager), specify the address of the access point with your subdomain (**owox**.your-domain.com) in the ‘Server Container URL’ field.
