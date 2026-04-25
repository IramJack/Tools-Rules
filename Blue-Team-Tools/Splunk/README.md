# Understanding the Basics

Splunk is a widely used Security Information and Event Management (SIEM) platform that helps security analysts search through, visualize, and investigate log data. The real power of Splunk lies in its Search Processing Language (SPL), which turns massive amounts of raw data into actionable insights. In this guide, you'll learn how to create search queries, apply filters, and transform results to extract valuable information.

<img width="257" height="257" alt="Image" src="https://github.com/user-attachments/assets/f44691f5-504a-4326-8ec1-6924fb2b86e0" />

## The Three Main Splunk Components

Splunk relies on three core components that work together to enable efficient data searching and analysis: Forwarder, Indexer, and Search Head.

<img width="920" height="309" alt="Image" src="https://github.com/user-attachments/assets/74ec0ae3-3e52-4863-90f7-ccb9b32f6f92" />

### Splunk Forwarder

The Forwarder is a lightweight agent installed on the system you want to monitor. Its job is to collect data and forward it to the Splunk instance. Because it uses minimal system resources, it doesn't impact endpoint performance. Typical data sources include:

- Web servers generating HTTP traffic logs
- Windows machines producing Event Logs, PowerShell logs, and Sysmon data
- Linux hosts generating system-level logs
- Databases logging connection requests, responses, and errors

<img width="920" height="560" alt="Image" src="https://github.com/user-attachments/assets/7fda6c79-a51f-4448-80c9-1e496bf71c09" />

The forwarder gathers data from these log sources and transmits it to the Indexer.

### Splunk Indexer

The Indexer is responsible for processing incoming data from forwarders. It parses and standardizes the data into field-value pairs, categorizes it, and stores the results as events. This makes the data easy to search and analyze later.

<img width="920" height="560" alt="Image" src="https://github.com/user-attachments/assets/9ce2082e-bbc8-4300-93b7-bb8d24eb069e" />

Once the Indexer has normalized and stored the data, the Search Head can be used to query it.

### Search Head

The Search Head is the interface within Splunk's Search & Reporting App where users can run searches against indexed logs. Searches are written in SPL (Search Processing Language), a powerful query language designed specifically for Splunk. When you execute a search, the request goes to the Indexer, and matching events are returned as field-value pairs.

<img width="1164" height="275" alt="Image" src="https://github.com/user-attachments/assets/72e53c1e-d9c0-4000-9e0d-d047008cfc93" />

The Search Head also lets you turn results into tables and visualizations like pie charts, bar charts, and column charts.

<img width="485" height="523" alt="Image" src="https://github.com/user-attachments/assets/2972959e-1211-44b9-8ffb-93efa3bd3ce4" />

## Navigating the Splunk Interface

When you first log into Splunk, you'll see the default home screen.

<img width="2102" height="786" alt="Image" src="https://github.com/user-attachments/assets/9b15a0e7-7333-46c2-8380-0e7aebd8e2bf" />

Let's break down the main sections.

### Splunk Bar

The top panel is called the Splunk Bar.

<img width="1542" height="37" alt="Image" src="https://github.com/user-attachments/assets/17cdfac3-211d-4c2a-a4bb-0bd009d80bb9" />

It contains the following options:

- **Messages**: Check system-level alerts and notifications.
- **Settings**: Adjust your Splunk instance configuration.
- **Activity**: Monitor ongoing search jobs and processes.
- **Help**: Access tutorials and official documentation.
- **Find**: Search across the current app.

The Splunk Bar also lets you switch between installed Splunk apps without using the Apps panel.

### Apps Panel

Below that is the Apps Panel, which lists all installed apps. Every Splunk installation includes the default **Search & Reporting** app.

<img width="272" height="213" alt="Image" src="https://github.com/user-attachments/assets/caff5cdb-907c-4efc-b4de-ac71b37713c7" />

You can also change apps directly from the Splunk Bar, bypassing the Apps Panel entirely.

<img width="367" height="35" alt="Image" src="https://github.com/user-attachments/assets/35499dc9-3a7b-4cb9-a9fc-7ab2d0fac8bb" />

### Explore Splunk Section

This area provides quick links for adding data, installing new Splunk apps, and accessing Splunk documentation.

<img width="1018" height="244" alt="Image" src="https://github.com/user-attachments/assets/4c722adb-b9c4-4f12-8dd5-851824ba953d" />

### Home Dashboard

By default, no dashboards are shown here. You can pick from various dashboards available in your Splunk instance using the dropdown menu or the dashboards listing page.

<img width="1548" height="660" alt="Image" src="https://github.com/user-attachments/assets/a6733a31-17ba-4c51-9ec3-982b6c159647" />

You can also create your own dashboards and add them to the Home Dashboard. Custom dashboards appear under the **Yours** tab, separate from other dashboards.

## Adding Data to Splunk

Splunk can ingest almost any type of data. According to Splunk's documentation, when you add data, it is processed and broken into individual events. Data sources include event logs, web logs, firewall logs, and more. They are organized into categories. The chart below from Splunk's documentation shows each data source category.

<img width="1093" height="459" alt="Image" src="https://github.com/user-attachments/assets/6ca1976b-bf8a-4e1e-93b1-73d81bf3a0fa" />

For this walkthrough, we'll work with VPN logs. Clicking the **Add Data** link on the Splunk home screen brings up the following view.

<img width="984" height="786" alt="Image" src="https://github.com/user-attachments/assets/cf133ac2-f72b-4b50-ba84-764937e97031" />

### Uploading Data from Your Local Machine

We'll use the **Upload** option to load a log file from our computer. The upload process consists of five simple steps:

1. **Select Source** – Choose the log file and specify the data source type.
2. **Select Source Type** – Indicate the format of the logs (e.g., JSON, syslog).
3. **Input Settings** – Pick the index where the logs will be stored and assign a HOSTNAME to the logs.
4. **Review** – Double‑check all your settings.
5. **Done** – Finish the upload. Your data will be successfully ingested and ready for analysis.

<img width="1905" height="904" alt="Image" src="https://github.com/user-attachments/assets/a0d5c0dc-5fe2-4e2e-9c2d-d7586805c317" />

-------------------------------
