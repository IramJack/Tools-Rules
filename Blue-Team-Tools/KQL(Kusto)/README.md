# Part 1: KQL (Kusto): Introduction

This room introduces you to the **analysis of security logs with Microsoft Sentinel KQL**. As security engineers, we think of security logs as treasures because they can help us discover the hidden activities within our infrastructure. Logs are not just lines of text — they are **records of activity**, footprints left behind by users, attackers, and applications. When properly analyzed, they reveal secrets, tell stories, and expose malicious behavior hiding in plain sight.

By TryHackMe:
https://tryhackme.com/room/kqlkustointroduction
https://tryhackme.com/room/kqlkustobasicqueries

A quick background intro:

* **Microsoft Sentinel** is a cloud-native Security Information and Event Management (SIEM) product. It ingests logs from multiple sources to provide better visibility across your environment.
* **Kusto Query Language (KQL)** is the analytical tool that empowers you to reveal the hidden insights inside those logs. If you are curious and hands-on, KQL becomes your magnifying glass, microscope, and X-ray all at once.

---

## Why Logs and KQL Matter

Imagine being able to:

* **Track down rogue user activities** and identify potential breaches before they escalate into incidents.
* **Unravel complex attacks** by correlating different logs, piecing together an attacker’s every move.
* **Automate repetitive analysis tasks**, so you spend less time doing manual triage and more time defending against advanced threats.

This is the power of KQL. In this room, the focus is on understanding KQL and how it integrates with Microsoft Sentinel to make you, the analyst, a detective capable of **hunting down threats proactively** and **strengthening your digital defenses**.

<img width="1140" height="740" alt="Image" src="https://github.com/user-attachments/assets/18cbc9be-4abc-495d-b96e-615cb20cb061" />

Upon completing this training, you will gain the foundational knowledge and skills necessary to:

* Understand **Microsoft Sentinel as a SIEM solution**, its benefits, and its role in modern security operations.
* Recognize the **advantages of KQL** in Microsoft Sentinel for daily monitoring and threat hunting.
* Learn how KQL interacts with data stored within **Log Analytics workspaces** and how to query it for insights.

---

## Overview of Microsoft Sentinel

Before diving into KQL, we first need to understand Microsoft Sentinel itself.

The cloud landscape is constantly expanding. New services, apps, and users appear daily. Unfortunately, **cybercriminals also evolve**, lurking in the shadows and waiting for any misconfiguration, delay, or blind spot to exploit. Organizations that configure **Microsoft Sentinel** properly stay prepared, monitoring their environment and detecting threats before attackers succeed.

### Microsoft Sentinel Explained

Microsoft Sentinel is a **cloud-native SIEM and SOAR solution**. What this means is:

* As a **SIEM**, it centralizes logs from across your entire organization. It provides visibility, correlation, and detection of security events.
* As a **SOAR**, it goes a step further, offering **orchestration, automation, and response**. Analysts can trigger playbooks, automate workflows, and respond quickly to detected threats.

Sentinel is built on top of **Azure**, which means it scales automatically, integrates natively with Microsoft’s ecosystem, and supports cloud-first security operations.

### Microsoft Sentinel Workflow

<img width="1140" height="800" alt="Image" src="https://github.com/user-attachments/assets/83a6eaf0-44b1-4f9d-9d51-735d688ddd1f" />

At a high level, Microsoft Sentinel works by:

1. **Ingesting logs** from multiple sources (Microsoft services, custom sources, third-party tools).
2. **Storing logs** in a centralized **Azure Log Analytics workspace**.
3. **Analyzing logs** with KQL queries for detection, hunting, and investigation.
4. **Responding to threats** via alerts, playbooks, and automated workflows.

---

## Integration with Microsoft Services

One of Sentinel’s strengths is its **tight integration with Microsoft services**. With built-in connectors, security teams can connect their environment in just a few clicks. Some important integrations include:

* **Microsoft Entra ID (Azure AD)**: Integration enables advanced identity monitoring. Analysts can track suspicious login attempts, audit logs, and enforce conditional access policies. Identity is often the first target for attackers, making this integration critical.
* **Microsoft Defender Suite**: When combined with Defender for Endpoint, Defender for Office 365, and other Defender products, Sentinel gains endpoint, email, and application-level threat intelligence. This creates a **layered defense ecosystem**, where detection and automated response extend from the endpoint to the cloud.
* **Azure Logic Apps**: Sentinel leverages Logic Apps to automate workflows. For example, if a suspicious login is detected, a playbook can automatically disable the user account, alert the SOC, and open a ticket.
* **Azure Monitor**: This integration enriches Sentinel with metrics and telemetry from Azure services, giving analysts deeper insight into system performance and security anomalies.

Agent-based log collection tools further expand Sentinel’s reach, capturing logs from on-premises servers, virtual machines, or hybrid environments.

---

## Integration with Third-Party Services

Security operations rarely run on Microsoft-only stacks. Sentinel accounts for this by providing **connectors for third-party products**, making it flexible in multi-vendor environments.

* **Syslog integration**: Sentinel can receive logs from any device or application that supports Syslog, a widely adopted standard. Firewalls, routers, and Linux servers often fall into this category.
* **REST API integration**: For tools without pre-built connectors, organizations can use REST APIs to push custom data into Sentinel. This requires scripting but provides near-universal compatibility.

Examples of supported products include **Palo Alto Networks, CrowdStrike, Fortinet, McAfee, Splunk, AWS**, and many more.

> **Note**: All data ingested ends up in **Azure Log Analytics workspaces**, forming the data repository KQL queries run against. This repository may hold logs from Microsoft services, third-party tools, SaaS applications, and infrastructure components.

---

## What is KQL?

Now that we’ve laid the foundation, let’s talk about Sentinel’s **secret weapon**: **Kusto Query Language (KQL)**.

KQL is more than syntax — it’s a **security analysis toolkit**. With it, analysts can:

* Query terabytes of logs in seconds.
* Detect anomalies, failed logins, suspicious network traffic, and more.
* Transform raw logs into **actionable intelligence**.

### KQL in Action

Imagine your SOC team receives an alert of unusual login attempts. Instead of manually sifting through thousands of event logs, you run a KQL query. Within moments, you get a chart of suspicious accounts, their IP addresses, and timestamps.

<!-- Failed to upload "3.gif" -->

This is how KQL transforms data into insights, allowing your team to respond quickly.

---

## KQL Explained

Originally developed for **Azure Data Explorer**, KQL has been adopted across Microsoft products. It is now widely used in:

* **Azure Monitor**
* **Microsoft Sentinel**
* **Microsoft 365 Defender**
* **Log Analytics**

### Key Characteristics of KQL

* **Security analysis language**: Built to search, filter, and analyze log data.
* **Flexible**: Supports both simple queries (failed logins in last 24 hours) and complex ones (multi-source correlations, anomaly detection).
* **Scalable**: Optimized for huge datasets.
* **Integrated**: Works seamlessly within Microsoft Sentinel.
* **Versatile**: Used across multiple Microsoft security platforms.

<img width="1140" height="800" alt="Image" src="https://github.com/user-attachments/assets/a40a6d1d-99b8-4ee6-b433-7e8b49c2d1a8" />

---

## Example KQL Query

Here’s a simple query example:

<img width="655" height="108" alt="Image" src="https://github.com/user-attachments/assets/bef046d6-4819-4d9d-8343-4dcc5870e8d5" />

![Image](https://github.com/user-attachments/assets/6ceff157-f543-4428-ae06-8d69f5fd25c5)

**Explanation:**

* Retrieves data from the **Heartbeat table**.
* Uses `summarize` to count entries per computer.
* Orders results by highest counts.
* Limits results to the top 10.

This basic example demonstrates how quickly KQL can answer operational questions.

---

## KQL Concepts in Microsoft Sentinel

To use KQL effectively, you must understand its building blocks:

### Tables

Logs are stored in **tables**. Each table represents a specific log source:

* `SecurityEvent`: Security logs.
* `AzureActivity`: Azure service activity logs.
* `WindowsFirewall`: Firewall logs.
* `NetworkMonitoring`: Network activity logs.

Custom logs end in `_CL`.

<img width="393" height="283" alt="Image" src="https://github.com/user-attachments/assets/7f2ad47e-791a-4487-a52d-5881c97a2fa5" />

Example: `SecurityEvent` table

![Image](https://github.com/user-attachments/assets/3c82b6bb-c452-44cd-b84d-6c79c018f55a)

---

### Functions and Operators

KQL provides **functions** (like `count()` or `sum()`) and **operators** (like `where` or `!=`) to filter, transform, and analyze data.

Examples:

* `count()`, `sum()`, `avg()`, `where()`, `parse()`
* `==` (equal to), `!=` (not equal to), `<` (less than)
* `summarize`, `render`, and pipelines (`|`)

Expressions combine these into meaningful units.

<img width="760" height="97" alt="Image" src="https://github.com/user-attachments/assets/fbcab884-8ad7-46e8-8418-7ff4d2768111" />

![Image](https://github.com/user-attachments/assets/601f56d1-0697-495d-b4f5-a3d528b77b63)


The query starts with the **SecurityEvents** table, filters events from the last three days for the user JBOX00$, then projects key fields like TimeGenerated, Account, Activity, and Computer. Finally, it sorts results by TimeGenerated in descending order. 
Each step is separated by the pipe (|) operator. For easy reference, the table below summarizes commonly used KQL functions, their basic syntax, and brief descriptions, though KQL offers many more functions beyond these.

<img width="616" height="513" alt="Image" src="https://github.com/user-attachments/assets/68a0cf12-0ef9-436a-a119-9bf2d5f12eb6" />
<img width="617" height="429" alt="Image" src="https://github.com/user-attachments/assets/a93043f0-c02a-40e7-a067-99a4d37ec084" />

Kindly refer to the official KQL quick reference for more details. In summary, functions and operators perform operations on ingested logs, while expressions combine values, functions, and operators into single units, enabling security analysts to efficiently analyze and manipulate security logs.

---

### KQL Statement Structure

A KQL query typically follows this structure:

1. **Data Source**: Specify the table.
2. **Conditions**: Apply filters.
3. **Aggregations**: Summarize or calculate.
4. **Output**: Sort, format, and display.

<img width="660" height="114" alt="Image" src="https://github.com/user-attachments/assets/83577bf4-b15a-4af3-859e-7e1ed2429af6" />

![Image](https://github.com/user-attachments/assets/ee3c74f6-43db-4131-b663-f0ca120e43f6)

Example:

```kql
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize EventCount = count() by Computer
| order by EventCount desc
| limit 10
```

This query finds the top 10 devices with the most security events in the last day.

<img width="1051" height="223" alt="Image" src="https://github.com/user-attachments/assets/78e4c4a7-da96-4f3f-8fbe-045a53205fba" />

---

## Real-Life Example

**Scenario**: Investigating failed logins.

1. Search all failed logins:

<img width="585" height="66" alt="Image" src="https://github.com/user-attachments/assets/aff7754f-0b71-456b-ab5e-16ea586f1269" />

![Image](https://github.com/user-attachments/assets/6ae338c8-9990-4b9f-a487-4dd69b9be3f3)

2. Narrow to a specific user:
<img width="644" height="71" alt="Image" src="https://github.com/user-attachments/assets/8d228d3b-97a3-4490-b558-b1ef9bc791f8" />

![Image](https://github.com/user-attachments/assets/a72bf54e-0483-47e8-954d-deff674c1db3)

3. Restrict to past hour:

<img width="738" height="97" alt="Image" src="https://github.com/user-attachments/assets/ce7b6e55-5cc1-4c7c-9818-8c39ce978087" />

![Image](https://github.com/user-attachments/assets/f380993c-76f8-4412-a2ad-9c87ea124c9d)

By refining queries, you can **zoom in from broad trends to individual incidents**.

---

## KQL Technical Use Cases

KQL isn’t just theory — it has real-world applications across security operations:

* **Incident investigation**: Trace attack timelines, correlate alerts, identify root causes.
* **Malware hunting**: Search for anomalies like malicious file downloads, registry tampering, or suspicious processes.
* **Detecting lateral movement**: Flag unusual logins from multiple devices or locations.
* **Monitoring user activity**: Track privileged accounts and detect account misuse.
* **Automating operations**: Integrate KQL queries with alerts and workflows to trigger responses.
* **Alerting**: Build custom alerts using KQL criteria to stay ahead of threats.
* **Reporting**: Generate reports on compliance, threat trends, and usage metrics.

---

## Final Thoughts

KQL is more than just a query language — it is a **powerful ally** in the fight against cyber threats. With Microsoft Sentinel’s SIEM and SOAR capabilities, combined with KQL’s analytical power, security teams can transform logs into actionable intelligence, automate responses, and stay ahead of attackers.

Learning KQL is not only about writing queries — it’s about **thinking like a threat hunter**, connecting dots, and revealing patterns others might miss. The more comfortable you get with KQL, the more effective you become as a defender of your organization’s digital estate.

---

# Part 2: KQL (Kusto) — Basic Queries

Explore security log analysis with core KQL queries. These examples work across different log types; with a little practice you’ll be able to query, triage, and threat-hunt like a pro.

<img width="830" height="800" alt="Image" src="https://github.com/user-attachments/assets/bd753d67-ab73-42fb-966e-d5926a7b0f5e" />

---

## SQL vs KQL

If you know SQL, some concepts will feel familiar — but the platforms have different purposes. SQL targets structured, relational data. KQL is built for fast exploration of large volumes of structured, semi-structured, and unstructured telemetry (logs) in real time.

<img width="3500" height="1167" alt="Image" src="https://github.com/user-attachments/assets/1c7a3bb5-5012-4bb5-97e5-c03a10dfc852" />

### Quick Conversion Reference

Use this cheat sheet to map common SQL statements to their KQL equivalents while you transition.

<img width="627" height="513" alt="Image" src="https://github.com/user-attachments/assets/52310650-864b-4208-b3b2-5cddf1a4df97" />

KQL is widely used inside Microsoft services such as Azure Data Explorer, Microsoft Defender, and Microsoft Sentinel for log analytics and time-series exploration. The cheat sheet covers basics, but KQL has many advanced functions for deeper analysis.

---

## Where and Sort — Core Filtering & Ordering

Understanding KQL operators is essential for any security analyst. Operators let you filter, transform, and aggregate large log sets quickly — whether you’re monitoring performance or hunting threats. Below are the most commonly used operators and practical examples.

### `where` — filter rows by condition

The `where` operator limits rows to those matching one or more conditions. Conditions can use comparisons (==, !=, >, <), logical operators (and/or), and functions like `startswith()` and `contains()`.

**Examples**

* **All security events in the last 3 hours**
  Pulls security events from the past three hours.

<img width="844" height="523" alt="Image" src="https://github.com/user-attachments/assets/65a1550d-224c-44a0-acb7-f4dad89ea87e" />

* **Events for a specific computer**
  Returns security events for the specified computer. (If no time window is set in the query, the query editor’s global time range—usually 24 hours—applies.)

<img width="845" height="500" alt="Image" src="https://github.com/user-attachments/assets/5505efbb-586a-4ec2-b744-e18329ecabba" />

* **VM connections from a specific country**
  Lists VMConnection records from Ireland for the last 48 hours. (Scroll horizontally to see all columns.)

<img width="847" height="504" alt="Image" src="https://github.com/user-attachments/assets/02c2af56-ee08-4a0c-a63d-f3a1321f8942" />

### `sort` / `order by` — arrange results

`sort` (alias `order by`) orders result rows by one or more columns, ascending or descending.

**Examples**

* **Sort ascending by account**
  Retrieves failed logon attempts from `SecurityEvent`, filters by event ID, and sorts ascending on the `Account` column.

<img width="841" height="523" alt="Image" src="https://github.com/user-attachments/assets/5d7ef895-e5a9-4e38-867e-224de9921b14" />

* **Sort descending by computer**
  Selects machines with potential malware from `ProtectionStatus`, limits to the last hour and `ThreatStatus == "No threats detected"`, and sorts descending by `Computer`.

<img width="843" height="530" alt="Image" src="https://github.com/user-attachments/assets/48dc874e-9fe3-4217-af6b-45aa7ec42e95" />

* **Order by a process name**
  Filters `VMConnection` where `Computer == "DC11.na.contosohotels.com"` and `RemoteCountry == "France"`, then orders ascending by `ProcessName`.

<img width="847" height="537" alt="Image" src="https://github.com/user-attachments/assets/1ca3b32f-aa0a-4278-830b-4d068c9e32dc" />

---

## `search` — broad, cross-table lookup

The `search` operator scans across tables/columns when you don’t know exactly where a term lives. It’s less efficient than `where`, but useful for exploratory queries.

**Examples**

* **Search multiple tables for “error”**
  Searches `SecurityEvent`, `SecurityDetection`, and `SecurityAlert` for the last 7 days.

<img width="843" height="530" alt="Image" src="https://github.com/user-attachments/assets/1754cb90-6785-46ca-aa9a-567862c74118" />

* **Search all logs for “threat”**
  Scans every table for the keyword `threat` to surface potentially malicious entries. Expand any result to see details.

<img width="844" height="490" alt="Image" src="https://github.com/user-attachments/assets/a28b2bc4-9c78-41e6-865f-4704cfd71de8" />

* **Search a specific custom log for an IP**
  Finds all events related to `13.89.179.10` in `AzureNetworkAnalyticsIPDetails_CL` (CL = custom log).

<img width="843" height="497" alt="Image" src="https://github.com/user-attachments/assets/2ae9050d-a7bc-4fbc-a7a6-2d099a86b0f2" />

<img width="602" height="614" alt="Image" src="https://github.com/user-attachments/assets/a467d18e-df38-4e12-837e-513181465d10" />

> **Note:** `CL` suffix indicates a custom (imported) table and helps distinguish custom data from built-in tables that may share names.

---

## `let` — declare reusable variables

Use `let` to assign expressions to variables, break complex queries into readable parts, and reuse values.

**Syntax**

```kql
let VariableName = Expression;
```

**Examples**

* **Set a threshold**
  Define a threshold of `1000`, then query the `Performance` table for `CounterValue > 1000`, returning results sorted ascending.

<img width="840" height="534" alt="Image" src="https://github.com/user-attachments/assets/40fbe24e-8fec-4030-b7e6-48e646e572a6" />

* **Multiple variables / time offsets**
  Create variables to hold time offsets and filters; here the `SecurityEvent` query returns events from the last 14 days while excluding `EventID == 4688`.

<img width="841" height="538" alt="Image" src="https://github.com/user-attachments/assets/f504cc9d-a3e2-4c30-a5a8-c90469fcfe8e" />

* **Dynamic array of EventIDs**
  Build an array `EventIDs = dynamic([4624, 4625])`, then filter `SecurityEvent` on those IDs and sort by time generated.

<img width="841" height="529" alt="Image" src="https://github.com/user-attachments/assets/de9ebf83-3977-41d7-b239-88a98461bfa3" />

---

## Time functions — focus your window

Time functions are essential for time-based queries and rule creation. Combine them with other operators to slice logs precisely.

**Common functions**

* `now()` — current UTC datetime
* `ago(timespan)` — relative time (e.g., `ago(1h)`, `ago(7d)`)
* `startofday(datetime)` / `endofday(datetime)` — start/end of day
* `datetime("YYYY-MM-DD hh:mm:ss")` — literal timestamp
* `startofmonth(datetime)` — month start

**Examples**

* **Events from yesterday (up to start of today)**
  Filters `WindowsFirewall` for records in the last 24 hours but only up to the start of the current day.

<img width="843" height="497" alt="Image" src="https://github.com/user-attachments/assets/37d0e40c-69ed-454e-9b5f-4febe6019b9e" />

* **Warnings in the last 14 days**
  Retrieves up to 100 `Operation` records where `Status == "Warning"` within the last 14 days.

<img width="834" height="519" alt="Image" src="https://github.com/user-attachments/assets/8e45e7aa-2962-44b1-a6e1-1b0fb3d23d51" />

* **Failed logons outside a time window**
  Returns `EventID == 4625` (failed logon) events that occurred outside 17:00–22:00 in the past 24 hours.

<img width="839" height="517" alt="Image" src="https://github.com/user-attachments/assets/aee6e92c-3c9c-4224-8fce-10ff0db77b45" />

---

# Using the Summarize and Render Operators

## Summarize Operator

The `summarize` operator is one of the most powerful tools in KQL for aggregating log data. As the name suggests, it groups rows by shared column values and applies aggregate functions to produce insights. For security analysts, this means quickly spotting trends, identifying anomalies, and surfacing patterns hidden in large log datasets.

`summarize` is often combined with filtering (`where`), ordering (`sort`), or visualization (`render`) to deliver actionable intelligence from security events, alerts, and telemetry data.

### Examples

* **Count of Event IDs**
  The query counts how many times each EventID occurred in the SecurityEvent table.

<img width="840" height="493" alt="Image" src="https://github.com/user-attachments/assets/8d7cb4db-221c-46c3-b57a-cfca0e00d8c8" />

* **Count of Different Activities**
  Groups activities by type and calculates their occurrence counts.

<img width="839" height="493" alt="Image" src="https://github.com/user-attachments/assets/91342121-2807-48cb-a8ae-40bd814ab55c" />

* **Aggregating Across Multiple Columns**
  Searches SecurityEvent logs for EventID `4648` (“A login was attempted using explicit credentials”) over the last three days, returning counts by Process, Computer, and Account type.

<img width="840" height="531" alt="Image" src="https://github.com/user-attachments/assets/337bc0c7-a011-48b6-bd20-0302d910f027" />

---

## Render Operator

The `render` operator transforms query output into visualizations, making it easier to interpret data at a glance. It doesn’t modify results — it simply changes how they’re displayed.

### Key Notes:

* Must be the **final operator** in your query.
* Works on a **single tabular data stream**.
* Supports chart types such as:

  * Bar chart
  * Column chart
  * Pie chart
  * Area chart
  * Scatter chart
  * Time chart

### Examples

* **Pie Chart**
  Summarizes SecurityEvent logs by computer, then visualizes them as a pie chart.

<img width="841" height="514" alt="Image" src="https://github.com/user-attachments/assets/7f2097f7-c786-4d18-90e7-49243b29d642" />

* **Bar Chart**
  Aggregates average disk read bytes per second, grouped by 1-hour intervals, and renders the result in a bar chart.

<img width="832" height="523" alt="Image" src="https://github.com/user-attachments/assets/e4ea53cb-a926-408a-855a-7a6a41fce91e" />

---

# Lab-01: Investigate and Identify

### Context

Your security team receives an alert: multiple unauthorized access attempts from the account **`timadmin`**. The suspicion is that the account has been compromised. You must investigate the activity, validate the breach, and identify additional suspicious behavior.

### Role

You are logged in as:

* **Log Analytics Contributor**
* **Microsoft Sentinel Reader**

### Scenario

Your tasks are:

1. Identify the user account.
2. Review authentication events.
3. Recommend remediation steps.

> **Lab Access**
> Exit the lab from Task 3 by selecting **Leave Lab**. Open the new lab (Task 8) by clicking the **Cloud Details** button. Deployment may take \~4 minutes. Check status via **Resource groups → Select resource group → Settings → Deployments**.

<img width="845" height="103" alt="Image" src="https://github.com/user-attachments/assets/255258e4-85b8-4f83-8283-cf71a6edd392" />

---

### Step 1: Search for Security Events Related to `timadmin`

1. Log into the Azure portal with lab credentials (log out of previous sessions first).
2. Search for **Log Analytics workspaces** in the top bar.

![Image](https://github.com/user-attachments/assets/f16add8f-31db-4393-ae19-5adc354ed14f)

3. Select the workspace → Navigate to **Logs**.
4. Close the pop-up window to open the query editor.
5. If available, switch to **KQL mode** (far right). Otherwise, continue.

<img width="966" height="492" alt="Image" src="https://github.com/user-attachments/assets/1e622fa2-76e5-441a-952e-4b115d8d884c" />

**Run the query:**

<img width="857" height="454" alt="Image" src="https://github.com/user-attachments/assets/e2a9b2b1-94a7-45a5-8316-08fc13b7e3a0" />

* Review the results, focusing on the **Activity\_s** column.
* Scroll through the account activity to spot anomalies.

---

### Step 2: Examine Authentication Events

Run the following query to isolate authenticator-related events.

<img width="669" height="60" alt="Image" src="https://github.com/user-attachments/assets/8cc86a8b-746d-42c1-b134-2e3b2834d96e" />

* Use **Time Range → Custom** for broader coverage.
* Expand individual rows for event details.

![Image](https://github.com/user-attachments/assets/07e57e93-ff6e-4732-af12-1aff296b2da3)

Next, refine with `summarize` and `order by` to see logon/logoff counts by machine. This helps identify which systems might be compromised.

<img width="851" height="527" alt="Image" src="https://github.com/user-attachments/assets/8a102d58-8938-4f30-9b53-66da7189be4a" />

---

### Recommendations

Based on the findings, the following actions are recommended:

* **Credential Reset** → Require Tim to reset his admin password, ensuring it follows complexity standards.
* **Access Review** → Audit and restrict Tim’s access to critical workloads.
* **Malware Scan** → Perform a full workload scan to rule out compromise.
* **Account Suspension (if necessary)** → Disable the account if activities cannot be verified as legitimate.



