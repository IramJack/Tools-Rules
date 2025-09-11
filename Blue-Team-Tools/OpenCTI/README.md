# Introduction to OpenCTI

<img width="1706" height="373" alt="Image" src="https://github.com/user-attachments/assets/d547206a-c9af-4391-8787-05077f891227" />

Cyber Threat Intelligence (CTI) is often seen as a managerial puzzle. Organisations frequently struggle with how to collect, process, analyse, and present threat data in a way that makes sense. As seen in the overview, several platforms exist to address this challenge, each offering different ways to manage the vast landscape of Threat Intelligence.

## OpenCTI
OpenCTI is an open-source platform built to help organisations manage CTI. It enables the storage, analysis, visualisation, and presentation of threat campaigns, malware, and indicators of compromise (IOCs).

## Objective
Developed through a collaboration with the French National Cybersecurity Agency (ANSSI), OpenCTI’s core goal is to provide a complete tool for leveraging both technical and non-technical intelligence. It helps users map relationships between data points and their sources, giving analysts a broader context.
The platform integrates with well-known frameworks and tools such as **MITRE ATT\&CK**, **MISP**, and **TheHive**, allowing organisations to streamline intelligence workflows.

<img width="1680" height="968" alt="Image" src="https://github.com/user-attachments/assets/2ba9001d-74a1-4cbc-b1d2-e95a96009a72" />

## OpenCTI Data Model
OpenCTI is built around a structured knowledge schema, with **STIX2 (Structured Threat Information Expression)** serving as its backbone. STIX provides a standardised format for sharing threat intelligence, representing data as **entities** and **relationships** that trace back to their origins.
The platform’s architecture supports this model and ensures scalability.

<img width="1140" height="666" alt="Image" src="https://github.com/user-attachments/assets/4ba17b6b-480e-4272-a788-133873e05a5d" />

### Key services include:
* **GraphQL API** – Connects clients to the database and messaging system.
* **Write Workers** – Python processes that asynchronously write queries via RabbitMQ.
* **Connectors** – Python processes designed to ingest, enrich, and export data, creating an integrated ecosystem that strengthens intelligence sharing and analysis.

Connectors in OpenCTI fall into multiple categories:

<img width="1240" height="396" alt="Image" src="https://github.com/user-attachments/assets/28d2ec3b-0b6a-4ff0-ac39-da9368bd683e" />

For details on configuring connectors and understanding the data schema, consult the official documentation.

## OpenCTI Dashboard
When you log into OpenCTI, the **dashboard** provides an immediate overview through visual widgets. These widgets summarise entities, relationships, reports, observables, and their recent changes, giving analysts a clear snapshot of the platform’s current state.

![Image](https://github.com/user-attachments/assets/8b0e019c-a492-43f3-a3ab-658f87e82231)

## Activities & Knowledge
Entities in OpenCTI are organised into two main groups:
* **Activities** – Covers security incidents in the form of reports, making it easier for analysts to investigate.
* **Knowledge** – Links data about adversary tools, victims, campaigns, and threat actors.

## Analysis
The **Analysis tab** is central to OpenCTI. It houses reports, associated references, and analyst notes. Reports enable knowledge extraction, provide source attribution, and allow enrichment with external resources.
Example: reviewing the **Triton Software report** from MITRE ATT\&CK.

![Image](https://github.com/user-attachments/assets/6f762063-dd25-4cb4-8ed9-9dbcbef61e03)

## Events
Events capture records of suspicious or malicious activities within a network. Analysts can document findings, enrich them, and link them to broader intelligence.

![Image](https://github.com/user-attachments/assets/c7d43c87-e0be-4329-9c2d-11842b98d447)

## Observations
This section lists the **technical indicators** found during attacks—artefacts, detection rules, or other traces. Observations support threat hunting by correlating live events with intelligence feeds.

![Image](https://github.com/user-attachments/assets/532a4309-f4d1-49ab-940b-896ed3c706ab)

## Threats
The **Threats tab** contains intelligence on hostile entities and operations:
* **Threat Actors** – Individuals or groups behind malicious campaigns.
* **Intrusion Sets** – TTPs, tools, and infrastructure commonly reused by adversaries (e.g., APT groups).
* **Campaigns** – Coordinated attack series against specific victims, often with strategic objectives.

<img width="1680" height="968" alt="Image" src="https://github.com/user-attachments/assets/b873e76f-2666-4680-b477-c74b64d4790f" />

## Arsenal
The **Arsenal tab** covers the tools, malware, and methods linked to adversaries.
* **Malware** – Catalogues active malware (e.g., 4H RAT) with intelligence relationships.
* **Attack Patterns** – Lists adversary TTPs like the **Command-Line Interface** technique.
* **Courses of Action (CoA)** – Recommended countermeasures, often mapped to MITRE ATT\&CK.
* **Tools** – Includes legitimate IT/admin tools that may be abused (e.g., CMD).
* **Vulnerabilities** – Imported CVE data to highlight exploitable weaknesses.

![Image](https://github.com/user-attachments/assets/4990b4e1-f8bf-48fa-93d1-a1ea634423d1)

## Entities
Entities are grouped by sectors, countries, organisations, and individuals, providing additional context to enrich analysis.

![Image](https://github.com/user-attachments/assets/227779c7-bd3f-43c1-adc8-8524553c5b78)

## General Tabs Navigation
OpenCTI provides several tabs to explore intelligence entities. For example, the **Cobalt Strike malware entity** (under Arsenal) includes:

* **Overview Tab** – Displays ID, confidence, description, related threats, reports, and references.

<img width="1680" height="968" alt="Image" src="https://github.com/user-attachments/assets/2caf0944-96b7-4c7f-9a28-3867736c4e44" />

* **Knowledge Tab** – Shows related reports, indicators, and timelines.

![Image](https://github.com/user-attachments/assets/e73e10ae-a778-4165-a947-63711fe32ba9)

* **Analysis Tab** – Collects reports where the entity is mentioned, guiding investigation.

<img width="1680" height="968" alt="Image" src="https://github.com/user-attachments/assets/3d9523f2-0208-4536-a751-3bca23097953" />

* **Indicators Tab** – Displays IOCs tied to the entity.
* **Data Tab** – Holds files and exports linked to the threat.
* **History Tab** – Tracks all modifications to the entity.

## Investigative Scenario
As a SOC analyst, you’re tasked with investigating **CaddyWiper malware** and the **APT37 group**. Using OpenCTI, gather intelligence and answer the investigative questions.


## Room Conclusion
Great job completing the OpenCTI walkthrough.
This room showcased how OpenCTI helps analysts process threat intelligence, link data, and investigate incidents. For deeper exploration, refer to OpenCTI’s official documentation and the integrated tools mentioned throughout.


