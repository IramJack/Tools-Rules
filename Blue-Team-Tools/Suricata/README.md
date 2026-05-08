# Suricata
<img width="1055" height="867" alt="image" src="https://github.com/user-attachments/assets/dd0a8607-1e4c-46d8-af4d-b0ea0add92ef" />

---

# Understanding Suricata

## What Exactly is Suricata?

Suricata is a powerful open-source engine designed for network monitoring, traffic analysis, and threat detection. Whether you feed it live network traffic or imported PCAP files, it can inspect packets and generate structured logs in the form of **EVE JSON** outputs.

These EVE JSON logs contain details about events where network traffic matched specific detection rules. Whenever a rule is triggered, Suricata records an **alert**. These rules are commonly authored by security researchers and detection engineers, helping defenders identify malicious behavior, suspicious activity, and network anomalies.

Besides passive monitoring, Suricata can also operate actively by blocking or dropping malicious traffic in real time.

---

## Working Modes in Suricata

Suricata supports three major operating modes, each designed for a different security purpose.

| Mode                                  | Input                | Purpose                                                                                                                                                           |
| ------------------------------------- | -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Network Security Monitoring (NSM)** | Live Traffic         | Captures and logs traffic events defined in the `suricata.yaml` configuration file. For example, if TLS logging is enabled, only TLS-related events are recorded. |
| **Intrusion Prevention System (IPS)** | Live Traffic         | Detects and immediately blocks traffic that matches defined rules or policy conditions.                                                                           |
| **Intrusion Detection System (IDS)**  | Live Traffic & PCAPs | Analyzes network traffic and generates alerts without blocking packets. Commonly used for threat hunting and investigations.                                      |

Threat intelligence groups such as Emerging Threats contribute continuously updated rules covering everything from informational events to malware and phishing activity. In IDS mode, Suricata leverages these rules to identify suspicious or malicious traffic patterns that analysts can later pivot from during investigations.

---

## Where Should a Suricata Sensor Be Positioned?

One of the common deployment questions is where to place a Suricata sensor within a network.

The answer depends entirely on the visibility and telemetry requirements of the organization. A sensor can sit internally within the network or externally near the perimeter firewall.

The Emerging Threats team generally recommends deploying Suricata in IDS mode **before** the perimeter firewall. This placement allows defenders to capture inbound connection attempts and observe activity targeting the environment.

Suricata itself is maintained through the collaborative efforts of the Open Information Security Foundation alongside independent contributors and third-party developers. Thanks to partnerships with Emerging Threats, the engine benefits from a constantly evolving open-source ruleset.

An important strength of Suricata is that anyone can create custom detection rules. Rules may remain private for internal use or be shared publicly with the wider community.

---

# Breaking Down the Suricata Rule Structure

Suricata processes packets and generates alerts whenever traffic satisfies a rule condition.

According to the official documentation, every Suricata rule consists of three primary components:

| Component   | Function                                              |
| ----------- | ----------------------------------------------------- |
| **Action**  | Defines what happens once the rule matches            |
| **Header**  | Specifies protocol, IPs, ports, and traffic direction |
| **Options** | Describes the detailed matching logic                 |


While Suricata itself does not enforce a strict order for these components, most community rule writers follow the standards outlined in the Suricata Community Style Guide.

---

# Suricata Rule Format — Action Section

The **action** tells Suricata how to respond when traffic satisfies the rule.

Consider the following example:

```bash
alert http any any -> any any (msg:"HTTP GET Request Containing Rule in URI"; flow:established,to_server; http.method; content:"GET"; http.uri; content:"rule"; fast_pattern; classtype:bad-unknown; sid:1000000; rev:1;)
```

The action here is:

```bash
alert
```

This means Suricata will create an alert entry whenever matching traffic is identified.

Supported rule actions include:

* `alert` → Generate an alert
* `pass` → Ignore further inspection
* `drop` → Block traffic and generate alert
* `reject` → Reject traffic and notify sender
* `rejectsrc` → Reject source
* `rejectdst` → Reject destination
* `rejectboth` → Reject both ends of the communication

Typically:

* **IDS mode** relies heavily on `alert`
* **IPS mode** frequently uses `drop`

---

# Suricata Rule Format — Header Section

The header tells Suricata *where* to inspect traffic.

Example header:

```bash
http any any -> any any
```

This section defines:

* Protocol
* Source and destination IPs
* Ports
* Direction of communication

---

## Protocol Definition

Suricata supports multiple protocols such as:

* TCP
* UDP
* ICMP
* HTTP
* TLS
* SMB

In this example, the rule targets HTTP traffic.

---

## Source and Destination

The following section:

```bash
any any -> any any
```

means:

> Any IP using any port communicating to any IP using any port.

---

## Traffic Direction

Traffic flow operators determine communication direction:

* `->` → One-way flow
* `<-` → Reverse direction
* `<>` → Bidirectional flow

---

## Optimizing Rule Performance

Although the rule:

```bash
http any any -> any any
```

works correctly, it is inefficient because it forces Suricata to inspect too much traffic.

To improve efficiency, Suricata provides reusable variables called **rule-vars** inside `suricata.yaml`.

Example variables:

```yaml
HOME_NET: "[192.168.0.0/16,10.0.0.0/8,172.16.0.0/12]"
EXTERNAL_NET: any
```

Using these variables narrows the inspection scope:

```bash
alert http $HOME_NET any -> $EXTERNAL_NET any
```

This approach improves performance significantly.

---

# Suricata Rule Format — Options Section

The options block contains the actual detection logic.

Unlike the header, this section defines *what content* should trigger the rule.

The Suricata Community Style Guide recommends arranging options in the following order:

1. `msg`
2. `flow`
3. Payload detection logic
4. `reference`
5. `classtype`
6. `sid`
7. `rev`

---

## Example Rule

```bash
alert http any any -> any any (msg:"HTTP GET Request Containing Rule in URI"; flow:established,to_server; http.method; content:"GET"; http.uri; content:"rule"; fast_pattern; reference:url,developer.mozilla.org/en-US/docs/Web/HTTP/Methods/GET; classtype:bad-unknown; sid:1000000; rev:1;)
```

---

## Metadata Breakdown

| Option      | Purpose                                 |
| ----------- | --------------------------------------- |
| `msg`       | Human-readable description of the alert |
| `reference` | External context or technical reference |
| `classtype` | Classification of the activity          |
| `sid`       | Unique rule identifier                  |
| `rev`       | Rule revision number                    |

---

## Flow Keywords

Example:

```bash
flow:established,to_server;
```

This means:

* Traffic belongs to an established TCP session
* Traffic is moving toward the server

---

## Sticky Buffers and Payload Matching

Suricata supports optimized payload parsing through **sticky buffers**.

These buffers allow rules to target normalized protocol fields instead of raw packet data.

Example HTTP sticky buffers:

| Sticky Buffer | Content | Modifier       |
| ------------- | ------- | -------------- |
| `http.method` | `GET`   | None           |
| `http.uri`    | `rule`  | `fast_pattern` |


The `fast_pattern` keyword is especially important because it improves rule performance by helping Suricata optimize matching operations.

---

# Practical Exercise — Working with Dalton

To begin testing rules, access the Dalton instance:

```bash
http://MACHINE_IP/dalton
```

Dalton will be used later to validate rules against PCAP traffic.

<img width="1085" height="632" alt="image" src="https://github.com/user-attachments/assets/c47dbca0-316d-40bc-aba6-79fbd630775e" />

---

## Download Required Files

Inside the AttackBox environment:

```bash
scp -r suricata@MACHINE_IP:pcaps .
```

Password:

```bash
password
```

<img width="890" height="509" alt="image" src="https://github.com/user-attachments/assets/5c6c5a31-4cd6-4f90-8975-7038c719a09b" />


The downloaded files should now exist under:

```bash
pcaps/
```

<img width="578" height="112" alt="image" src="https://github.com/user-attachments/assets/8a7c51d3-4d5b-44e5-a229-8b35b9fe0160" />

---

# Building a Custom Suricata Rule

The goal of this exercise is to create a rule capable of detecting communication with the `ip-api` geolocation service.

---

## Step 1 — Define the Rule Action

Since we want alert generation only, the action will be:

```bash
alert
```

---

## Step 2 — Analyze the PCAP

Open the PCAP file:

```bash
ip-check.pcapng
```

using Wireshark.

Wireshark provides:

* Packet List Pane
* Packet Details Pane
* Packet Bytes Pane

[12.png]

After inspecting packet number 4:

* Protocol: HTTP
* Source IP: `10.127.0.26`
* Destination IP: `200.95.112.1`
* Destination Port: `80`

Since the source belongs to a private network range, it maps to:

```bash
$HOME_NET
```

The destination maps to:

```bash
$EXTERNAL_NET
```

Traffic direction:

```bash
-> 
```

[13 Table.png]

Final header:

```bash
http $HOME_NET any -> $EXTERNAL_NET any
```

---

# Constructing the Rule Options

Following the style guide:

| Keyword     | Value                                     |
| ----------- | ----------------------------------------- |
| `msg`       | `msg:"TryHackMe - HTTP Query to ip-api";` |
| `flow`      | `flow:established,to_server;`             |
| `reference` | `reference:url,ip-api.com/docs;`          |
| `classtype` | `classtype:misc-activity;`                |
| `sid`       | `sid:1000000;`                            |
| `rev`       | `rev:1;`                                  |

[14 Table.png]

---

## Identifying Payload Content

By reviewing the HTTP request:

[15.png]

We can map relevant fields to sticky buffers:

| Sticky Buffer | Content                 |
| ------------- | ----------------------- |
| `http.method` | `content:"GET";`        |
| `http.uri`    | `content:"/json";`      |
| `http.host`   | `content:"ip-api.com";` |

[16 Table.png]

---

# Final Rule

```bash
alert http $HOME_NET any -> $EXTERNAL_NET any (msg:"TryHackMe - HTTP Query to ip-api"; flow:established,to_server; http.method; content:"GET"; http.uri; content:"/json"; http.host; content:"ip-api.com"; reference:url,ip-api.com/docs; classtype:misc-activity; sid:1000000; rev:1;)
```

Save the rule inside:

```bash
pcaps/suricata.rules
```

[17.png]

---

# Testing the Rule with Dalton

## What is Dalton?

Dalton is a web-based platform designed for testing PCAP files against IDS engines such as:

* Suricata
* Snort
* Zeek

[18.png]

It helps validate:

1. Rule syntax correctness
2. Whether the rule successfully detects the target traffic

---

# Running the Test

Inside the AttackBox:

1. Open:

```bash
http://MACHINE_IP/dalton/coverage/suricata/
```

[19.png]

2. Upload:

   * `ip-check.pcapng`

3. Disable:

   * `Use a defined ruleset`

4. Enable:

   * `Use Custom Rules`

5. Paste your custom rule

6. Click **Submit**

---

# Reviewing Dalton Results

Once processing completes, Dalton displays several useful tabs.

---

## Alerts Tab

Displays generated alerts whenever rules match traffic.

[20.png]

---

## Alert Details

Shows the exact packet responsible for the alert.

Helpful for:

* Validating detections
* Identifying false positives

[21.png]

---

## EVE JSON

Displays parsed JSON records including:

* Alerts
* Metadata
* Protocol logs
* Anomalies

This helps determine how Suricata interprets the traffic internally.

[22.png]

---

## Alert Debug

Provides unofficial visibility into Suricata buffers and parsing behavior.

Useful during advanced rule debugging.

[23.png]

---

## Debug Tab

Contains Dalton execution logs and confirms:

* Which PCAP was analyzed
* Execution details
* Rule loading information

[24.png]

---

# Additional PCAP Resources

If you want more traffic samples for analysis and detection engineering practice, explore:

* Triage
* ANY.RUN
* Malware Traffic Analysis
* VX-Underground
* AlienVault

[25.png]

---

# Contributing to the Open-Source Detection Community

Working with Suricata also means becoming part of the wider open-source detection engineering ecosystem.

Ways to collaborate with the Emerging Threats team include:

* Reporting false positives
* Submitting custom signatures
* Sharing malware samples
* Reporting broken detections
* Contributing PCAP captures

Communication channels include:

* Email: `support@emergingthreats.net`
* Twitter/X: `@ET_Labs`
* ET Discourse forums

[26.png]

