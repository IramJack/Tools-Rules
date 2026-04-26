# Splunk

Splunk is a widely used Security Information and Event Management (SIEM) platform that helps security analysts search through, visualize, and investigate log data. The real power of Splunk lies in its Search Processing Language (SPL), which turns massive amounts of raw data into actionable insights. In this guide, you'll learn how to create search queries, apply filters, and transform results to extract valuable information.

<img width="257" height="257" alt="Image" src="https://github.com/user-attachments/assets/f44691f5-504a-4326-8ec1-6924fb2b86e0" />

---

# Splunk Deployment and Log Forwarding 

## Understanding Splunk Deployment

Before building your environment, you need to grasp how Splunk components fit together. The diagram below shows the main pieces and how they communicate in a typical SOC setup.

<img width="887" height="165" alt="Image" src="https://github.com/user-attachments/assets/c5ac84e5-64af-492b-ba02-0972d858986c" />

### Core Components

#### Forwarders

Splunk forwarders are small, low‑overhead agents installed on endpoints or servers. Their only job is to collect logs and ship them to your Splunk instance. They are critical whether you have a handful of machines or thousands.

- **Universal forwarder** sends raw data as‑is.
- **Heavy forwarder** can filter or parse data before forwarding.

#### Indexers

Indexers take incoming data from forwarders, process it, and store it. In a SOC, indexers turn raw logs into searchable events. When data hits an indexer, it gets parsed, transformed, and written into an **index** – a structured container that makes correlation and analysis fast.

#### Search Head

The search head is what analysts use to run queries and investigate logs. It sends search requests to indexers and displays results via dashboards, reports, and alerts. Analysts can also build visualizations and correlations to spot trends or suspicious behavior. Beyond being a graphical interface, the search head performs **search‑time parsing**: it extracts fields and transforms raw data on the fly during a search.

> In this lab, the same Splunk instance acts as both indexer and search head. In larger deployments, organizations often separate these roles – for example, a cluster of indexers handling high throughput and dedicated search heads for visualization.

Splunk favors search‑time parsing, meaning raw events stay untouched and fields are extracted only when you search. This gives flexibility without reindexing but consumes more CPU on the search head.

#### Deployment Server

<img width="690" height="244" alt="Image" src="https://github.com/user-attachments/assets/e5535ecb-7c4a-4b95-9218-843facd1df08" />

Managing a few forwarders manually is fine, but doing it for hundreds or thousands is a nightmare. A **Splunk deployment server** is an optional central controller for multiple forwarders. Imagine needing to push configuration changes to servers spread across the country – the deployment server lets you do that remotely, without logging into each machine.

In a SOC, this simplifies large‑scale log collection. It keeps all forwarders in sync with the latest inputs, outputs, and settings.

---

## Installing Splunk on Linux

Splunk runs on all major operating systems. Installation is quick – usually under ten minutes. Normally you'd create an account and download the latest package.

<img width="887" height="220" alt="Image" src="https://github.com/user-attachments/assets/b65a62e2-39c2-4b8c-b940-27a589005d51" />

### Running the Install

First, go to the downloads folder, switch to root, and extract the tarball into `/opt`. We'll use the `tar` command shown below.

```bash
cd Downloads/splunk
ubuntu@coffely:~/Downloads/splunk$ ls
splunk_installer.tgz splunkforwarder.tgz
ubuntu@coffely:~/Downloads/splunk/$ sudo su
root@coffely:~/Downloads/splunk/#
root@coffely:~/Downloads/splunk/# tar xvzf splunk_installer.tgz -C /opt
splunk/
splunk/splunk-9.0.3-dd0128b1f8cd-linux-2.6-x86_64-manifest
...
```

### Starting Splunk for the First Time

The extraction places all binaries and files on disk. Now move into `/opt/splunk/bin` and launch Splunk with `./splunk start --accept-license`. You'll be asked to set an admin username and password.

```bash
root@coffely:~/Downloads/splunk/# cd /opt/splunk/bin
root@coffely:/opt/splunk/bin# ./splunk start --accept-license
```

### Accessing the Web Interface

Well done – Splunk is now running on your Linux machine. To open the web UI, use the VM's browser and go to `http://coffely:8000`. If you're on the VPN, you can reach it at `http://MACHINE_IP:8000`. Port `8000` is the default. Log in with the credentials you just created.

<img width="887" height="376" alt="Image" src="https://github.com/user-attachments/assets/45258d07-1b56-483d-9364-e518d658adb1" />

---

## Command‑Line Management

After installation, you'll need some essential CLI commands. All of these are run from `/opt/splunk/bin`.

- `./splunk start` – Launches Splunk. If already running, it does nothing.
- `./splunk stop` – Shuts it down.
- `./splunk restart` – Stops and starts again. Handy after changing configuration files.

```bash
root@coffely:/opt/splunk/bin# ./splunk start
```
### More Useful Commands

- `./splunk enable boot-start` – Makes Splunk start automatically when the system boots.
- `./splunk status` – Shows whether Splunk is running and reports any errors.

```bash
root@coffely:/opt/splunk/bin# ./splunk status
```
- `./splunk add oneshot /path/to/logfile -index yourindex` – Injects a single log entry into an index. Good for testing or adding isolated events.

```bash
root@coffely:/opt/splunk/bin# ./splunk add oneshot /path/to/logfile -index yourindex
```
- `./splunk search "your query"` – Runs a search from the command line using SPL.

```bash
root@coffely:/opt/splunk/bin# ./splunk search coffely
```

- `./splunk help` – Lists all available CLI commands with brief descriptions.

These are just a sample. Administrators can use the CLI to efficiently manage and troubleshoot Splunk.

---

## Setting Up the Universal Forwarder

Getting data into Splunk is a key step. Proper configuration ensures logs are indexed correctly and become searchable. Splunk can accept data from nearly any source: operating systems, web servers, IDS/IPS, etc.

In production, forwarders run on remote machines and send data to a central Splunk server. In this lab, we'll install the forwarder on the same machine as Splunk.

### Installing the Forwarder

Download the universal forwarder from Splunk's website – it's available for all platforms.

<img width="887" height="224" alt="Image" src="https://github.com/user-attachments/assets/ad080abe-af9d-4814-a3ba-656e64843be0" />

Go back to `/home/ubuntu/Downloads/splunk`. Stay as root, then extract the forwarder tarball into `/opt`.

```bash
ubuntu@coffely:~/Downloads/splunk/$ ls
splunk_installer.tgz splunkforwarder.tgz
ubuntu@coffely:~/Downloads/splunk/$ sudo su
root@coffely:~/Downloads/splunk/#
root@coffely:~/Downloads/splunk/# tar xvzf splunkforwarder.tgz -C /opt
splunkforwarder/
splunkforwarder/swidtag/
...
```
### Starting the Forwarder

Switch to `/opt/splunkforwarder/bin` and issue `./splunk start --accept-license`. You'll set up credentials.

Both Splunk Enterprise and the forwarder normally use port `8089` for management (splunkd). Because they run on the same machine, you'll get a port conflict. When asked, change the forwarder's port to `8090` so both can coexist.

```bash
root@coffely:~/Downloads/splunk/# cd /opt/splunkforwarder/bin
root@coffely:/opt/splunkforwarder/bin# ./splunk start --accept-license
This appears to be your first time running this version of Splunk.
```
Now both Splunk and the forwarder are running. Next, we'll tell the forwarder what logs to send.

---

## Configuring the Forwarder to Send Data

The forwarder needs a destination. We'll configure both sides: Splunk (receiver) and the forwarder (sender).

### Splunk Receiver Setup

Log into Splunk, go to **Settings**, then **Forwarding and receiving**.

<img width="492" height="328" alt="image" src="https://github.com/user-attachments/assets/2e5295be-b9b8-468b-b5a9-bb6dfde5f5b4" />

You'll see options for both sending and receiving. We want to **receive** data from the Linux endpoint, so click **+ Add new** under **Receive data**.

<img width="887" height="304" alt="image" src="https://github.com/user-attachments/assets/adb8834e-2ce9-4b1c-8366-865570cbfde6" />

The default receive port is `9997`. You can change it, but we'll stick with `9997`. Click **Save**.

<img width="887" height="251" alt="image" src="https://github.com/user-attachments/assets/677933ee-1388-4143-8615-36854e35521f" />

Port `9997` is now open and waiting. You can later disable or delete this from the same page. Leave it active for now.

<img width="887" height="168" alt="image" src="https://github.com/user-attachments/assets/f9615cbf-3c58-4b25-bbab-d46e3aa150c7" />

### Creating an Index

Go back to **Settings** → **Indexes**. We'll make a fresh index to hold the incoming data. If you don't specify an index, Splunk uses `main`.

<img width="492" height="329" alt="image" src="https://github.com/user-attachments/assets/9c002d68-f7cf-4371-89aa-28e37f091153" />

The **Indexes** page lists all existing indexes (both user‑created and system‑internal). Internal indexes track Splunk's own performance. Metadata like size, event count, and storage path are shown. Click **New Index**.

<img width="887" height="229" alt="image" src="https://github.com/user-attachments/assets/2eead396-9a49-43c7-b963-1aad4dc872d9" />

Name it `linux_host` and accept the default settings. Save.

<img width="492" height="358" alt="image" src="https://github.com/user-attachments/assets/d672d656-c5ba-4422-a3d1-5c36caba44d5" />

### Forwarder Destination Configuration

Back on the Linux terminal, tell the forwarder where to send data:

```bash
root@coffely:/opt/splunkforwarder/bin# ./splunk add forward-server MACHINE_IP:9997
```
### Selecting Linux Logs

Linux keeps its logs under `/var/log`. We'll ingest `syslog` into Splunk. The same method works for other log files.

```bash
root@coffely:/opt/splunkforwarder/bin# ls /var/log
```
Now monitor the syslog file and point it to our `linux_host` index:

```bash
root@coffely:/opt/splunkforwarder/bin# ./splunk add monitor /var/log/syslog -index linux_host
```
### Checking Input Configuration

You can examine `inputs.conf` to confirm the settings. This file defines what data Splunk collects. You can edit it manually to add or change inputs.

```bash
root@coffely:/opt/splunkforwarder/bin# cat /opt/splunkforwarder/etc/apps/search/local/inputs.conf
```
### Generating Test Logs with `logger`

The `logger` tool adds test messages to syslog. Because we're monitoring syslog, any message we create will appear in Splunk.

```bash
root@coffely:/opt/splunkforwarder/bin# logger "coffely-has-the-best-coffee-in-town"
root@coffely:/opt/splunkforwarder/bin# tail -1 /var/log/syslog
```

To verify receipt, go to **Search & Reporting** and run:

```
index=linux_host "coffely-has-the-best-coffee-in-town"
```

<img width="887" height="308" alt="image" src="https://github.com/user-attachments/assets/5109e54c-d1a0-4e1e-9209-9862416455fb" />

---

## Forwarding Windows Logs (Example)

We've successfully ingested Linux logs. In real life, you'll often need logs from Windows servers, email gateways, firewalls, etc. **Log aggregation** – collecting and normalizing logs from many sources – lets analysts correlate across systems.

Our lab is Linux‑only, so this section is a **read‑along** example for Windows.

Assume you have a Windows host. Install the universal forwarder there, and during installation specify your Splunk instance's IP as the indexer. (You cannot follow along here because we have only one VM, but the process is worth knowing.)

Using PowerShell, you can define which logs to forward. For example, to monitor Security events:

```powershell
PS C:\> cd "Program Files\SplunkUniversalForwarder\bin"
PS C:\Program Files\SplunkUniversalForwarder\bin> .\splunk.exe add monitor C:\Windows\System32\winevt\Logs\Security.evtx
Added monitor of 'C:\Windows\System32\winevt\Logs\Security.evtx'.
```

After that, Search & Reporting would show the Windows Security events.

<img width="887" height="356" alt="image" src="https://github.com/user-attachments/assets/b3d68c61-7d74-4209-b591-737e2219b5cc" />

### Using a Deployment Server for Windows

You can also turn your Splunk instance into a **deployment server** to manage forwarders via the UI.

```bash
root@coffely:/opt/splunk/bin# ./splunk enable deploy-server
Deployment Server is enabled.
root@coffely:/opt/splunk/bin# ./splunk restart
```
Back in Splunk, go to **Settings** → **Data Inputs**, scroll to **Forwarded inputs**, and click **+ Add new** beside **Windows Event Logs**.

<img width="887" height="156" alt="image" src="https://github.com/user-attachments/assets/bdf66dbf-18c0-4866-a8cb-9fa3c9442328" />

Your Windows machine appears under **Available hosts**. Select it and assign a **server class name** – a way to group deployment clients.

<img width="887" height="380" alt="image" src="https://github.com/user-attachments/assets/146ad661-3d29-4f9a-96eb-ab63fb33afe5" />

Choose which log types to forward: here **Application**, **Security**, **Setup**, and **System** are selected.

<img width="887" height="328" alt="image" src="https://github.com/user-attachments/assets/68a5b252-13b6-4856-a8d4-7ff0ccb167eb" />

Finally, create a new index named `windows`, review the input, and submit – similar to earlier steps.

<img width="887" height="312" alt="image" src="https://github.com/user-attachments/assets/c1c67105-36a5-45a1-a141-0805622ecdc2" />

Check the `windows` index in Search & Reporting.

---

## Ingesting Web Server Logs

The Linux VM also hosts a development website at `http://coffely.thm:8080` (accessible within the VM). You need to configure Splunk to receive Apache access logs so you can track orders and coffee sales.

<img width="1708" height="649" alt="Image" src="https://github.com/user-attachments/assets/7fc5f785-b756-43fd-a7ab-d7dca7288dd8" />

The site will eventually accept online coffee orders. The backend logs every request, response, and order. Let's ingest those logs.

Apache stores access logs at `/var/log/apache2/access.log`. You could use the command line, but let's try the Splunk web interface.

### Adding the Data Source

In Splunk, go to **Settings** → **Add Data**.

<img width="492" height="328" alt="Image" src="https://github.com/user-attachments/assets/6635da39-4982-4312-8ebb-4a5ecb5640c1" />

Scroll down and pick **Monitor**.

<img width="887" height="240" alt="Image" src="https://github.com/user-attachments/assets/a7c7918e-2333-40c4-a323-621ca27762f2" />

Select **Files & Directories** and enter `/var/log/apache2/access.log`. Notice you can index once or monitor continuously. Choose continuous monitoring so new entries arrive as they happen.

<img width="887" height="314" alt="Image" src="https://github.com/user-attachments/assets/3e3f6f82-9add-4b12-a41e-6bae846f8bda" />

Now set the **source type**. Open the dropdown, select **Web**, then **access_combined**.

<img width="492" height="407" alt="Image" src="https://github.com/user-attachments/assets/16a51c76-3689-4562-95c6-793d50603654" />

Assign a name to the **host** field – change it to `coffelyweb`. Then create a new index named `web` (just like before).

<img width="492" height="286" alt="Image" src="https://github.com/user-attachments/assets/f6de12f3-a267-424c-873f-9a4a1393aa72" />

Review everything, submit, and start searching. Initially you'll see no data. Generate some traffic by opening a browser and visiting `http://coffely.thm:8080`. Click around to create log entries.

<img width="887" height="324" alt="Image" src="https://github.com/user-attachments/assets/88a9be64-4936-4f28-8db6-d6e88f3beb60" />

Success – Apache access logs are now flowing into Splunk.

<img width="887" height="340" alt="Image" src="https://github.com/user-attachments/assets/264beffe-99d3-4323-a609-094b8c38094e" />
---

# Splunk's Basics

## The Three Main Splunk Components

As we mentioend earlier, Splunk relies on three core components that work together to enable efficient data searching and analysis: Forwarder, Indexer, and Search Head.

<img width="920" height="309" alt="Image" src="https://github.com/user-attachments/assets/74ec0ae3-3e52-4863-90f7-ccb9b32f6f92" />

### 1. Splunk Forwarder

The Forwarder is a lightweight agent installed on the system you want to monitor. Its job is to collect data and forward it to the Splunk instance. Because it uses minimal system resources, it doesn't impact endpoint performance. Typical data sources include:

- Web servers generating HTTP traffic logs
- Windows machines producing Event Logs, PowerShell logs, and Sysmon data
- Linux hosts generating system-level logs
- Databases logging connection requests, responses, and errors

<img width="920" height="560" alt="Image" src="https://github.com/user-attachments/assets/7fda6c79-a51f-4448-80c9-1e496bf71c09" />

The forwarder gathers data from these log sources and transmits it to the Indexer.

### 2. Splunk Indexer

The Indexer is responsible for processing incoming data from forwarders. It parses and standardizes the data into field-value pairs, categorizes it, and stores the results as events. This makes the data easy to search and analyze later.

<img width="920" height="560" alt="Image" src="https://github.com/user-attachments/assets/9ce2082e-bbc8-4300-93b7-bb8d24eb069e" />

Once the Indexer has normalized and stored the data, the Search Head can be used to query it.

### 3. Search Head

The Search Head is the interface within Splunk's Search & Reporting App where users can run searches against indexed logs. Searches are written in SPL (Search Processing Language), a powerful query language designed specifically for Splunk. When you execute a search, the request goes to the Indexer, and matching events are returned as field-value pairs.

<img width="1164" height="275" alt="Image" src="https://github.com/user-attachments/assets/72e53c1e-d9c0-4000-9e0d-d047008cfc93" />


The Search Head also lets you turn results into tables and visualizations like pie charts, bar charts, and column charts.


<img width="485" height="523" alt="Image" src="https://github.com/user-attachments/assets/2972959e-1211-44b9-8ffb-93efa3bd3ce4" />

----------------

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

----------------------------

## Adding Data to Splunk

Splunk can ingest almost any type of data. According to Splunk's documentation, when you add data, it is processed and broken into individual events. Data sources include event logs, web logs, firewall logs, and more. They are organized into categories. The chart below from Splunk's documentation shows each data source category.

<img width="1093" height="459" alt="Image" src="https://github.com/user-attachments/assets/6ca1976b-bf8a-4e1e-93b1-73d81bf3a0fa" />


### Uploading Data from Your Local Machine

We'll use the **Upload** option to load a log file from our computer. The upload process consists of five simple steps:

1. **Select Source** – Choose the log file and specify the data source type.
2. **Select Source Type** – Indicate the format of the logs (e.g., JSON, syslog).
3. **Input Settings** – Pick the index where the logs will be stored and assign a HOSTNAME to the logs.
4. **Review** – Double‑check all your settings.
5. **Done** – Finish the upload. Your data will be successfully ingested and ready for analysis.

<img width="1905" height="904" alt="Image" src="https://github.com/user-attachments/assets/a0d5c0dc-5fe2-4e2e-9c2d-d7586805c317" />

-------------------------------


# Search & Reporting in Splunk

## Overview of the Search & Reporting App

Splunk’s **Search & Reporting** app is the default interface you see on the home page for searching and analyzing data. It comes packed with features that help analysts get the most out of their search experience. When you open the app, several key functionalities become available:

- **Search Head** – The area where analysts enter queries to filter or aggregate log data.
- **Time Picker** – Offers various options to define the time range for your search.
- **Search History** – Stores previously used Splunk search queries for quick reuse.
- **Data Summary** – Gives a summary of available hosts, sources, and sourcetypes.

<img width="887" height="394" alt="Image" src="https://github.com/user-attachments/assets/8bcfff25-65a2-4d27-9260-c5a8d6dddca7" />

## Running Your First Search

We'll work with the `windowslogs` index here. Think of an index as a Splunk database or container that organizes your data. Start by entering your first query using the search bar and set the time range to **All time**.

<img width="887" height="104" alt="Image" src="https://github.com/user-attachments/assets/41db3394-1fd5-45da-a6ef-0c9900718781" />

> **Note:** Both `index=windowslogs` and `index = windowslogs` are valid syntax in Splunk.

## The Fields Sidebar

On the left panel of the Splunk search interface, you'll find the **Fields Sidebar**. It has two main sections: one for selected fields and another for interesting fields. The sidebar also gives you quick insights, including top values and raw values for each field.

- **Selected Fields** – These are the default extracted fields. You can add more fields by clicking on them and toggling the "Selected" option.
- **Interesting Fields** – Splunk automatically pulls interesting fields it discovers and shows them here for further exploration.
- **Numeric Fields `#`** – Fields that contain numerical values are marked with this symbol.
- **Alpha‑numeric Fields `α`** – Fields containing string (text) values are marked with the alpha symbol.
- **Count** – The number of events that contain the listed field.
- **More available fields** – If additional fields exist, you can access and select them from this area.

<img width="887" height="419" alt="Image" src="https://github.com/user-attachments/assets/61e37075-d41e-424c-9a61-1569fc15fffb" />

---

## Search Operators

Behind every search in Splunk is the **Search Processing Language (SPL)**. SPL combines commands, functions, and operators that let you filter, transform, and analyze data from your ingested logs. In short, SPL allows you to search massive amounts of data, narrow down results with filters, and format the output. Let's see how to use it.

### Free‑Text Search

The simplest way to search is to use free‑text. For example:

```
index=windowslogs alice
```

This query finds all events containing the keyword `alice` (case‑insensitive). Free‑text searches are great when you don't know field names or just want to quickly hunt for a unique keyword. For more complex searches, you'll need search operators and parsed field‑value pairs.

### Search Operators

Splunk operators are the building blocks of any search query. They help you filter, remove, and refine results based on specific criteria. Below we cover relational, logical, and wildcard operators. Keep in mind that for filters to work, events must already be parsed into fields.

#### Relational Operators

These operators compare two expressions to determine relationships like equality, inequality, greater than, or less than. Here are some examples.

<img width="984" height="355" alt="Image" src="https://github.com/user-attachments/assets/6f20cbd6-3773-4b11-8831-06dcabe95b70" />

Let's try a hands‑on example using the `!=` operator. We want to find all event logs in our index where the `AccountName` field is **not** equal to `System`. Use the query below (remember to set the time range to **All time**):

```
index=windowslogs AccountName!=SYSTEM
```

In the screenshot below (and in your own Splunk instance), you'll see that we've successfully filtered out events containing the `AccountName` value `SYSTEM`.

<img width="887" height="412" alt="Image" src="https://github.com/user-attachments/assets/cf9d1971-8e5c-4579-b173-d96095102757" />

#### Logical Operators

Splunk supports the following logical operators, which connect or modify conditions and work on Boolean (true/false) values.

<img width="980" height="290" alt="Image" src="https://github.com/user-attachments/assets/2cfbd4f2-bf53-48ae-adbd-84e3293e4dd8" />

Let's get more practice. We'll append to the previous query to search for events that have the `AccountName` field equal to `James`. This tells Splunk to exclude `SYSTEM` and then show only events from `James`:

```
// The AND operator is implied, so both queries work
index=windowslogs AccountName!=SYSTEM AND AccountName=James
index=windowslogs AccountName!=SYSTEM AccountName=James
```

<img width="887" height="306" alt="Image" src="https://github.com/user-attachments/assets/96235c2e-9787-48af-899e-76c55e559b76" />

#### Wildcards and CIDR Search

Splunk supports wildcards and CIDR notation for IP addresses when you need partial matches or subnet searches. Examples:

<img width="985" height="204" alt="Image" src="https://github.com/user-attachments/assets/8ca4c7cc-1c41-4b06-8b5c-281645b50bea" />

<img width="887" height="346" alt="Image" src="https://github.com/user-attachments/assets/c8bb8bdb-3f60-4bb1-a54c-89d54dcabeba" />

### Order of Evaluation

#### Quotes

Quotation marks `""` define exact phrases or strings. Wrap text in quotes, and Splunk treats it as a single value. Quotes can also escape search operators. For instance:

- `index=windowslogs failed login` – Searches for events containing both `failed` and `login` in any order.
- `index=windowslogs "failed login"` – Searches for the exact phrase `failed login` (word order matters).
- `index=windowslogs "TO BE OR NOT TO BE"` – Searches for the exact phrase that includes the words `NOT` and `OR`.

#### Parentheses

Use parentheses to group conditions and control how a search is applied. Because `OR` takes precedence over `AND`, parentheses help set the correct order. For example, suppose you want events containing `alice` and `bob` together, or `charlie` alone:

<img width="981" height="216" alt="Image" src="https://github.com/user-attachments/assets/5fa5e3dd-5fc1-414b-ad7c-eb2b61dc6033" />

---

## Filtering Results

Your network can generate thousands of logs per minute, all ingested into your SIEM. Searching for anomalies without filters quickly becomes overwhelming. SPL lets you apply filters using search commands, narrowing results to only the most relevant events.

In Splunk, commands are linked with the pipe symbol `|`. Each pipe passes the output of one command to the next, letting you refine results step by step. Here are some useful filtering commands.

### `fields`

The `fields` command includes or excludes specific fields from your results. To exclude a field, use a minus sign `-` before its name. The plus sign `+` can explicitly include a field but isn't required. By default, `fields` includes any fields listed after it. Try this in your Splunk instance to highlight specific fields – it's very handy when logs contain hundreds of fields.

```
index=windowslogs | fields host User SourceIp
```

<img width="887" height="305" alt="Image" src="https://github.com/user-attachments/assets/68f053a9-f769-451b-8614-af22448e6fd6" />

### `dedup`

The `dedup` command removes duplicate values from your search results. For example, if logs contain seven distinct IP addresses in the `SourceIp` field, the results will return seven events – one per unique IP. This command is useful for subsearches and cleaning identical events (e.g., Microsoft 365 often sends 50 events for a single activity).

```
index=windowslogs
| fields EventID User Image Hostname SourceIp
| dedup SourceIp
```

<img width="887" height="341" alt="Image" src="https://github.com/user-attachments/assets/c893f5aa-4a97-4f42-9178-9f9e7e1a51eb" />

### `rename`

The `rename` command changes a field's name in your search results. This improves readability, especially when original field names are too long or unsuitable for SOC reports.

```
index=windowslogs
| fields EventID User Image Hostname SourceIp
| rename User as Employee
```

`rename` is also useful for flattening JSON or XML subfields. For a JSON log like `{"request": {"path": "/admin", "ip": "10.0.0.2"}}`, Splunk creates `request.path` and `request.ip`. If you don't want the prefix, remove it as shown:

```
index=jsondata
| rename request.* as *   // request.path -> path; request.ip -> ip
```

<img width="887" height="368" alt="Image" src="https://github.com/user-attachments/assets/0041b7bb-67ab-4f0a-8f24-114e662b4076" />

### `regex`

The `regex` command filters search results using regular expressions (PCRE, Perl Compatible Regular Expressions). This is useful when you need events matching a specific pattern rather than an exact keyword.

```
index=windowslogs | regex Image = "\.exe$"
```

The query above applies a regex to the `Image` field, returning only events where the value ends with `.exe` (the `$` anchors the match to the end of the string). Regex is irreplaceable for complex searches, especially on custom or poorly parsed data sources.

<img width="887" height="321" alt="Image" src="https://github.com/user-attachments/assets/19a6a05e-caca-46d3-a059-26db87820be6" />

---

## Structuring Results

SPL also provides commands to organize and structure your search results. Raw results can be overwhelming, and some fields may not be relevant to your investigation. These commands help you filter, order, and format results.

### `table` Command

The `table` command selects only the fields you want to see and displays them in a clean, readable format. This is useful for building timelines, investigating specific hosts or users, or comparing multiple fields. The query below creates a table from the named fields, organized by timestamp.

```
index=windowslogs | table _time EventID Hostname SourceName
```

<img width="887" height="276" alt="Image" src="https://github.com/user-attachments/assets/c9174684-bed0-4206-b39c-969588c63299" />

### Useful Structuring Commands

Other commands can be used alone or combined with `table` to focus on the data you care about. See examples below.

<img width="982" height="254" alt="Image" src="https://github.com/user-attachments/assets/feb1b812-f0e0-458a-8757-0f7597757172" />

The `table` command can create timelines that help analysts reconstruct event sequences. For example, list all actions on the host `Salena.Adam` in chronological order, excluding system noise or adding columns as needed.

```
index=windowslogs Hostname=Salena.Adam
| table _time Hostname EventID Category
| reverse
```

<img width="887" height="393" alt="Image" src="https://github.com/user-attachments/assets/7ec60161-a25e-4085-b24b-ed747126aaf1" />

### Subsearches

Imagine reviewing Sysmon process creation events. You want to know the logon context: did the process come from a remote session (LogonType 3/10) or a service (LogonType 5)? Sysmon doesn't log `LogonType`, so you need to correlate two data sources: Sysmon Event ID 1 (process creation) and Security Event ID 4624 (logon). The `LogonId` field links them:

- Get `Image`, `User`, `LogonId` from Sysmon (EventID=1)
- Use `LogonId` to find the corresponding logon event (EventID=4624)
- Retrieve `LogonType` and `IpAddress` from that logon event

Splunk **subsearches** (with the `join` keyword) let you correlate across multiple sources in one search, building a unified table with process and logon details.

<img width="887" height="444" alt="Image" src="https://github.com/user-attachments/assets/8da0f6b0-b438-4c4c-b528-a793de6ce2a6" />

```
index=windowslogs EventID=1
| join LogonId
    [ search index=windowslogs EventID=4624
    | rename TargetLogonId as LogonId
    | fields LogonId LogonType IpAddress]
| table _time Image User LogonType IpAddress
```

Let's break down the query:

<img width="982" height="235" alt="Image" src="https://github.com/user-attachments/assets/352f4c5a-54d1-4d0f-a3db-9550e8dd9263" />

Subsearches are powerful but don't perform well on very large datasets. Many subsearch queries can be replaced with more efficient `stats` and `eval` commands (covered next). Still, if you need to enrich data source A with fields from data source B, the `subsearch + join` combination works well.

---

## Transforming Commands

Transforming commands turn raw event data into summaries, statistics, and visualizations. Instead of viewing every individual log, they help analysts aggregate, count, and analyze patterns across many events. Searches that use these commands are called **transforming searches**.

### General Transforming Commands

<img width="986" height="184" alt="Image" src="https://github.com/user-attachments/assets/52300853-4167-4f37-8b86-02ac3c947c94" />

### `highlight`

The `highlight` command visually marks chosen field values when viewing raw log data. Switch the view from **List** to **Raw** to see the effect.

```
index=windowslogs | highlight User EventID Image "Process accessed"
```

<img width="887" height="301" alt="Image" src="https://github.com/user-attachments/assets/991b6e1e-923c-4eda-8d58-460813fd79fc" />

### `stats`

The `stats` command is a workhorse. It calculates statistics like counts, sums, and averages on fields within your search results. This is excellent for summarizing large volumes of data to identify trends or anomalies.

<img width="988" height="309" alt="Image" src="https://github.com/user-attachments/assets/9a51164a-7525-4585-948e-a9337ed799ca" />

Try the following example, which returns the total number of occurrences for each `EventID` and sorts them in ascending order.

```
index=windowslogs | stats count by EventID | sort EventID
```

<img width="887" height="255" alt="Image" src="https://github.com/user-attachments/assets/78fe30c7-26e7-4a01-aebf-caf9ba060cc5" />

### `chart`

The `chart` command returns results in a table, which you can then use to create visualizations. It uses many of the same functions as `stats`. Here we visualize the count of events containing the `User` field.

```
index=windowslogs | chart count by User
```

<img width="887" height="360" alt="Image" src="https://github.com/user-attachments/assets/817da7de-13e0-46a5-a591-1d446f40d151" />

### `timechart`

The `timechart` command shows how data changes over time. It's great for spotting trends, peaks, and anomalies. In the example below, we track process activity over time: remove null `Image` values and create a time‑based area chart showing the top five most frequent process images in 30‑minute intervals.

```
index=windowslogs Image!="" | timechart span=30m count by Image limit=5
```

<img width="887" height="341" alt="Image" src="https://github.com/user-attachments/assets/41d64c64-8f36-4b75-9d1b-3647cfbe1602" />

---

## Data Enrichment and Field Manipulation

### `iplocation`

The `iplocation` command enriches your search results with geographic information about IP addresses. It uses Splunk's built‑in geolocation tables to add fields like `City`, `Region`, and `Country`.

```
index=windowslogs | iplocation SourceIp | stats count by Country
```

<img width="887" height="190" alt="Image" src="https://github.com/user-attachments/assets/76dce462-107d-4a59-be88-fbd5e7bc8d83" />

### `lookup`

The `lookup` command enriches events using external data sources. It matches a field in your search to a corresponding field in a CSV file or lookup table. In this example, a CSV called `user_roles` associates `Hostname` with an employee `UserRole`.

```
index=windowslogs
| lookup user_roles Hostname OUTPUT UserRole
| stats count by Hostname UserRole
```

<img width="887" height="262" alt="Image" src="https://github.com/user-attachments/assets/3e58755b-a263-4e5a-b55c-2690b72d5f4f" />

### `eval`

The `eval` command is one of the most versatile tools in Splunk. It creates new fields, modifies existing ones, and performs calculations directly within searches. It can make data more readable and prepare fields for visualizations. The example below creates a new field `LogonTypeDesc` to give descriptive names to numeric `LogonType` values.

```
index=windowslogs
| eval LogonTypeDesc = case(LogonType == 3, "Network Logon", LogonType == 5, "Service")
| stats count by LogonType LogonTypeDesc
```

The query assigns:
- `Network Logon` when `LogonType` is 3
- `Service` when `LogonType` is 5

<img width="887" height="240" alt="Image" src="https://github.com/user-attachments/assets/4b650f5b-3411-4c99-bd2f-6ba391193ef6" />

---

## Anomaly Detection

Sometimes you'll investigate a dataset with many different events (e.g., VPN logins) and need to quickly identify outliers – events that look suspicious compared to the rest. Imagine a dataset of 2,000 VPN logins with just four fields: time, username, source IP, and source country. How would you spot logins from unexpected countries if basic field statistics show no anomalies?

<img width="887" height="423" alt="Image" src="https://github.com/user-attachments/assets/6dd02790-81cd-4136-a845-83c237dd501b" />

### Detecting Outliers by Country

For a US‑based user, logging in from the US is expected, but for an EU‑based user it might be a sign of intrusion. To create aggregated statistics per user, start with the search below. The `eventstats` command is similar to `stats` but preserves raw events for further processing. The `where` command is like `search` but more powerful. Even though the query looks complex, read the descriptions carefully. After you run it, you'll see two potentially compromised users.

<img width="887" height="260" alt="Image" src="https://github.com/user-attachments/assets/08e24484-0a37-4410-a458-a629b5c83899" />

<img width="986" height="306" alt="Image" src="https://github.com/user-attachments/assets/f58dd240-5562-4be1-b595-30ab9c81738e" />

In summary, we found two potentially compromised users: `kbrown` and another one (you'll identify in the task). Both logged in from anomalous countries only once – a strong signal of either VPN usage or a breach. Examine the query yourself and answer the first two task questions:

```
index=vpnlogs
| eventstats count as logins_by_user by user 
| eventstats count as logins_by_user_country by user src_country 
| eval country_freq=logins_by_user_country/logins_by_user
| where country_freq < 0.1
| table _time user src_ip src_country country_freq
```

### Detecting Outliers by Hour

Following a similar approach, you can hunt for logins during unusual hours. However, this is harder because you must account for different time zones and employee habits. Some log in strictly during working hours, while others log in at night. To handle this, calculate a few variables:

- `typical_hour` – The average hour (e.g., 13:30 UTC) when the employee logs in.
- `stdev_hour` – How predictable the login hour is; `0` means most predictable.  
  `stdev_hour=2` means the employee is expected to log in at 13:30 UTC ± 2 hours.
- `zscore` – The number of standard deviations between the observed and typical login hour.  
  `zscore=3` means the login hour was 3 times more anomalous for that specific user.

<img width="887" height="342" alt="Image" src="https://github.com/user-attachments/assets/e13882a2-f383-4026-90cb-7184dd4d2840" />

### ML and Impossible Travel

More advanced detections, such as **Impossible Travel**, are built on the same basic commands. All you need is SPL knowledge, some math, and threat context (e.g., IP geolocation from `iplocation` or threat intelligence from lookup tables). You can even experiment with the `fit` and `apply` commands, which leverage machine learning algorithms to train on your data and improve future outlier detection.

---
