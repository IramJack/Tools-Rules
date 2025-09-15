# KQL (Kusto): Introduction

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
