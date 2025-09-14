# Brim: Open-Source PCAP and Log File Analysis

Brim is an open-source desktop application that processes pcap files and log files. Its primary focus is on providing search and analytics. In this room, you will learn how to use Brim, process pcap files, and investigate log files to find the needle in the haystack! This room expects familiarity with basic security concepts and processing Zeek log files.

<img width="1329" height="588" alt="Image" src="https://github.com/user-attachments/assets/f9fe343f-4f31-4a9a-8117-2337c72ba618" />

## What is Brim?

Brim is an open-source desktop application that processes pcap files and log files, with a primary focus on search and analytics. It uses the Zeek log processing format and supports Zeek signatures as well as Suricata Rules for detection.

It can handle two types of data as input:

* **Packet Capture Files:** Pcap files created with tcpdump, tshark, and Wireshark-like applications.
* **Log Files:** Structured log files like Zeek logs.

Brim is built on open-source platforms:

* **Zeek:** Log-generating engine.
* **Zed Language:** Log querying language that allows performing keyword searches with filters and pipelines.
* **ZNG Data Format:** Data storage format that supports saving data streams.
* **Electron and React:** Cross-platform UI.

### Why Brim?

Ever had to investigate a large pcap file? Pcap files bigger than one gigabyte can be cumbersome for Wireshark. Processing large pcaps with tcpdump and Zeek is efficient but requires time and effort. Brim reduces the time and effort spent processing pcap files and investigating log files by providing a simple yet powerful GUI application.

### Brim vs Wireshark vs Zeek

While each tool is powerful, it is important to understand their strengths and weaknesses to achieve the best outcome. There is some overlap in functionality, but each tool offers unique value in different situations.

* **Wireshark:** Best for medium-sized pcaps.
* **Zeek:** Ideal for creating logs and correlating events.
* **Brim:** Efficient for processing multiple logs and large-scale analysis.

<img width="624" height="478" alt="Image" src="https://github.com/user-attachments/assets/2ff3af89-df18-4de0-8b7a-a91fcd5291b9" />

## The Basics

### Landing Page

When opening Brim, the landing page loads with three main sections and a file import window. It also provides quick information on supported file formats:

* **Pools:** Data resources, including imported pcap and log files.
* **Queries:** List of available queries.
* **History:** List of executed queries.

### Pools and Log Details

Pools represent imported files. Once you load a pcap, Brim processes the file, creates Zeek logs, correlates them, and displays findings in a timeline.

<img width="1314" height="652" alt="Image" src="https://github.com/user-attachments/assets/f434c9f8-7516-4b30-83cf-5284df4121a6" />

The timeline shows capture start and end dates. Brim also provides field-specific information—you can hover over fields for additional details. The above image shows a user hovering over the Zeek `conn.log` file and the `uid` value. This information is valuable for creating custom queries. The rest of the log details appear in the right pane. Results can always be exported using the export function near the timeline.

<img width="470" height="416" alt="Image" src="https://github.com/user-attachments/assets/80468b9d-d04e-422d-a6ae-6d0e356128c7" />

Each log entry can be correlated via the correlation section in the log details pane (shown in the left image). This section provides source and destination addresses, duration, and associated log files. It helps answer “Where to look next?” and find relevant events and linked evidence.

Right-clicking on a field allows you to:

* Filter values
* Count fields
* Sort (A-Z or Z-A)
* View details
* Perform WHOIS lookups on IP addresses
* Open associated packets in Wireshark

<img width="949" height="522" alt="Image" src="https://github.com/user-attachments/assets/49770c4d-8b74-4a46-aaf1-a32851046f98" />

### Queries and History

Queries help correlate findings and identify events of interest. History stores executed queries.

<img width="491" height="539" alt="Image" src="https://github.com/user-attachments/assets/5a13edb5-464b-4476-ad13-18d02e49fd97" />

Queries can include names, tags, and descriptions. Double-clicking a query passes it to the search bar and lists it under the history tab. Results appear below the search bar. For example, importing a single pcap automatically generates nine types of Zeek log files.

Brim comes with 12 premade queries under the "Brim" folder. These queries help users understand Brim’s query structure and perform quick searches using templates. You can add new queries by clicking the "+" button near the "Queries" menu.

<img width="556" height="144" alt="Image" src="https://github.com/user-attachments/assets/7eec64f8-f12e-497e-ab49-869551e51cac" />

### Default Queries

Opening the sample pcap and following the walkthrough allows you to explore the premade queries.

<img width="403" height="566" alt="Image" src="https://github.com/user-attachments/assets/2a0db977-8f4e-4437-a6d0-e60d293af55f" />

#### Reviewing Overall Activity

This query provides general information on the pcap file, useful for further investigation and custom query creation. The left image shows 20 logs generated for the sample pcap.

#### Windows-Specific Networking Activity

This query focuses on Windows networking events, detailing source/destination addresses, named pipes, endpoints, and operations. It aids investigations of events like SMB enumeration, logins, and service exploitation.

<img width="1376" height="448" alt="Image" src="https://github.com/user-attachments/assets/a30042a9-c131-42de-a527-e773d287c716" />

#### Unique Network Connections and Transferred Data

These queries list unique connections and correlate connection data, assisting in detecting anomalies, suspicious activity, and beaconing behavior.

<img width="1073" height="559" alt="Image" src="https://github.com/user-attachments/assets/b3a360fb-4148-4f2b-8ea3-b24e62139553" />

#### DNS and HTTP Methods

Queries provide DNS requests and HTTP methods, helping analysts detect anomalous traffic. You can modify queries to narrow searches (e.g., filtering HTTP POST or GET requests).

<img width="1140" height="478" alt="Image" src="https://github.com/user-attachments/assets/87f436b7-2065-4eff-9b2b-0415755733ae" />

#### File Activity

This query lists available files and identifies potential data leakage or suspicious activity. It provides MIME types, file names, and hash values (MD5, SHA1).

<img width="1194" height="309" alt="Image" src="https://github.com/user-attachments/assets/c51eb423-d0ff-47ae-86dc-75a98013405e" />

#### IP Subnet Statistics

This query lists available IP subnets, helping analysts detect out-of-scope communications and unusual IP addresses.

<img width="986" height="262" alt="Image" src="https://github.com/user-attachments/assets/5086e217-a42e-4904-b75f-6d20ac968ca0" />

#### Suricata Alerts

These queries display Suricata rule results in three formats: category-based, source/destination-based, and subnet-based.

> **Note:** Suricata is an open-source threat detection engine that functions as a rule-based IDS/IPS. It detects anomalies similar to Snort and supports the same signatures.

<img width="690" height="790" alt="Image" src="https://github.com/user-attachments/assets/3f52fdf9-a646-4b92-abc8-3ea63edb5d6e" />

---

# Brim Use Cases: Custom Queries and Threat Hunting

## Custom Queries and Use Cases

Traffic analysis has a wide variety of use cases. For a security analyst, understanding common patterns and identifying anomalies or malicious traffic is crucial. In this section, we cover both basic and advanced Brim queries, focusing on practical examples.

### Brim Query Reference

<img width="627" height="515" alt="Image" src="https://github.com/user-attachments/assets/f1d4929e-e5b7-447f-89be-da25821ac7d2" />

> **Note:** It is highly recommended to use field names and filtering options rather than relying on irregular search queries. Brim provides excellent indexing of log sources, but irregular searches perform poorly. Always leverage field filters to locate events of interest efficiently.

<img width="632" height="553" alt="Image" src="https://github.com/user-attachments/assets/bbae7035-6a31-463d-8d0b-4884c44a0523" />

<img width="621" height="436" alt="Image" src="https://github.com/user-attachments/assets/db6dc8c2-6a5f-4835-8d50-c6d503919bf4" />

---

## Threat Hunting with Brim | Malware C2 Detection

Malware campaigns, such as those using CobaltStrike, often follow a familiar pattern: an employee clicks a link, downloads a file, and network anomalies appear. Using Brim, analysts can investigate sample pcaps to detect malicious command-and-control (C2) activity.

<img width="285" height="576" alt="Image" src="https://github.com/user-attachments/assets/7515bed5-42e7-473c-b637-e884ecf9c001" />

### Initial Analysis

Start by reviewing available log files to understand the types of data artifacts present. Frequently communicated hosts can help determine where to focus.

**Query:**

```
cut id.orig_h, id.resp_p, id.resp_h | sort | uniq -c | sort -r count
```

<img width="621" height="373" alt="Image" src="https://github.com/user-attachments/assets/1032fc68-fb98-4336-afc2-427fa28274d0" />

This query highlights suspicious IPs such as `10.22.xx` and `104.168.xx`. Next, review port numbers and services before narrowing down to suspicious IPs.

**Query:**

```
_path=="conn" | cut id.resp_p, service | sort | uniq -c | sort -r count
```

<img width="563" height="299" alt="Image" src="https://github.com/user-attachments/assets/71f44824-27ac-48af-90d5-b8840e0a7ad5" />

Although no unusual ports are immediately evident, DNS activity is notable. Analyze it further:

**Query:**

```
_path=="dns" | count() by query | sort -r
```

<img width="605" height="438" alt="Image" src="https://github.com/user-attachments/assets/41c7bfe0-7c5c-453e-b712-ef5eba3688c8" />

Out-of-the-ordinary DNS queries are detected. Enrich findings using VirusTotal to identify potentially malicious domains.

<img width="678" height="931" alt="Image" src="https://github.com/user-attachments/assets/31a21acd-be58-4e42-ac12-203246b0c784" />

We identified two additional malicious IPs (`68.138.xx` and `185.70.xx`) linked to suspicious DNS queries. Investigating HTTP requests further refines our search:

**Query:**

```
_path=="http" | cut id.orig_h, id.resp_h, id.resp_p, method, host, uri | uniq -c | sort value.uri
```

<img width="949" height="243" alt="Image" src="https://github.com/user-attachments/assets/d2c8132e-4f1f-40c2-8899-ef854c620a42" />

A file download request from the suspected malicious IP is detected. Validation via VirusTotal confirms that the IP `104.xx` is associated with a file, linked to CobaltStrike C2 activity.

<img width="762" height="818" alt="Image" src="https://github.com/user-attachments/assets/9f0e347b-f418-4f50-82d0-b3c39c6b4be5" />

### Suricata Alerts

To capture low-hanging threats, Suricata logs can be leveraged:

**Query:**

```
event_type=="alert" | count() by alert.severity,alert.category | sort count
```

<img width="537" height="289" alt="Image" src="https://github.com/user-attachments/assets/dd5d76ed-dccc-45a7-827c-cec7896fbd5e" />

Suricata alerts summarize detected malicious activities. Analysts can continue investigating additional IPs for secondary C2 traffic. Skilled attackers often use multiple C2 channels, making comprehensive investigation essential.

---

## Threat Hunting with Brim | Crypto Mining

Cryptocurrencies have introduced a new threat vector: unauthorized mining (cryptojacking). Corporate systems may be exploited by external attackers or internal misuse to mine cryptocurrencies, consuming resources and affecting network performance.

Using Brim, analysts can identify anomalous traffic indicative of mining activity.

<img width="297" height="271" alt="Image" src="https://github.com/user-attachments/assets/a39dbb76-1111-4713-a34d-f5789cdcff7d" />

### Initial Analysis

Review available log files and frequently communicated hosts to detect anomalies:

**Query:**

```
cut id.orig_h, id.resp_p, id.resp_h | sort | uniq -c | sort -r
```

<img width="607" height="353" alt="Image" src="https://github.com/user-attachments/assets/0cc25f38-70b4-42b0-85b9-f0619ddc8473" />

The IP `192.168.xx` draws attention. Next, analyze port numbers and services:

**Query:**

```
_path=="conn" | cut id.resp_p, service | sort | uniq -c | sort -r count
```

<img width="606" height="366" alt="Image" src="https://github.com/user-attachments/assets/b55d5792-37ce-453a-b06f-c285826ea4cf" />

### Data Volume Analysis

Identify unusual traffic volumes to support the hypothesis of malicious activity:

**Query:**

```
_path=="conn" | put total_bytes := orig_bytes + resp_bytes | sort -r total_bytes | cut uid, id, orig_bytes, resp_bytes, total_bytes
```

<img width="906" height="286" alt="Image" src="https://github.com/user-attachments/assets/dbf1db50-a24e-41bd-bf4f-e7389645eb42" />

This query confirms massive traffic from the suspicious IP. To further validate, Suricata rules are used:

**Query:**

```
event_type=="alert" | count() by alert.severity,alert.category | sort count
```

<img width="623" height="205" alt="Image" src="https://github.com/user-attachments/assets/aaf8be01-272d-4563-bbff-c31cc19c8b40" />

Suricata alerts indicate cryptocurrency mining activity. Next, identify the destination IPs involved:

**Query:**

```
_path=="conn" | 192.168.1.100
```

<img width="734" height="271" alt="Image" src="https://github.com/user-attachments/assets/4c78dc2d-9f48-49a6-b410-c8bf27092e09" />

<img width="570" height="473" alt="Image" src="https://github.com/user-attachments/assets/d983df4f-cf32-4a26-bc37-3c5959bfb110" />

Analysis confirms the mining server. In real-world cases, multiple IPs may need investigation.

### Mapping MITRE ATT\&CK Techniques

Using Suricata alerts, analysts can map observed activities to MITRE ATT\&CK techniques:

**Query:**

```
event_type=="alert" | cut alert.category, alert.metadata.mitre_technique_name, alert.metadata.mitre_technique_id, alert.metadata.mitre_tactic_name | sort | uniq -c
```

<img width="949" height="111" alt="Image" src="https://github.com/user-attachments/assets/3050d19d-798e-4fb5-bf99-08a22faa30eb" />

Now we can identify the mapped out MITRE ATT&CK details as shown in the table below.

<img width="1233" height="137" alt="Image" src="https://github.com/user-attachments/assets/cce90ae2-7540-4ec4-b0c2-14c351a0dfd6" />

---

## Conclusion

Congratulations! You have completed the Brim room. This walkthrough covered Brim’s features, operations, and practical applications for investigating threats—from CobaltStrike C2 campaigns to cryptojacking activities.


