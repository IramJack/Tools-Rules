# Abuse.ch: Tracking Malware and Botnets

**Abuse.ch** is a research project hosted by the **Institute for Cybersecurity and Engineering at the Bern University of Applied Sciences** in Switzerland. It was developed to identify and track malware and botnets through several operational platforms under the project. These platforms include:

* **Malware Bazaar:** A resource for sharing malware samples.
* **Feodo Tracker:** A resource used to track botnet command and control (C2) infrastructure linked with Emotet, Dridex, and TrickBot.
* **SSL Blacklist:** A resource for collecting and providing a blocklist for malicious SSL certificates and JA3/JA3s fingerprints.
* **URL Haus:** A resource for sharing malware distribution sites.
* **ThreatFox:** A resource for sharing indicators of compromise (IOCs).

Let us look into these platforms individually.

By TryHackMe: [https://tryhackme.com/room/threatinteltools](https://tryhackme.com/room/threatinteltools)

![Image](https://github.com/user-attachments/assets/bbfcd3b4-b3ba-463a-9bd4-c4ecb705a19f)

---

## MalwareBazaar

**MalwareBazaar** is an all-in-one malware collection and analysis database. Its key features include:

* **Malware Samples Upload:** Security analysts can upload malware samples via the web interface or API, contributing to the intelligence database.
* **Malware Hunting:** Analysts can set up alerts for tags, signatures, YARA rules, ClamAV signatures, and vendor detections to identify relevant malware samples.

**Reference:** [[MalwareBazaar](https://bazaar.abuse.ch/)](https://bazaar.abuse.ch/)

---

## FeodoTracker
**FeodoTracker** provides intelligence on botnet C\&C servers associated with Dridex, Emotet (Heodo), TrickBot, QakBot, and BazarLoader/BazarBackdoor.

Features include:
* A searchable database of known C\&C servers.
* IP and IOC blocklists and mitigation information to prevent botnet infections.

**Reference:** [[Feodo Tracker](https://feodotracker.abuse.ch/)](https://feodotracker.abuse.ch/)

---

## SSL Blacklist
**SSL Blacklist** identifies and tracks malicious SSL connections. Certificates used by botnet C2 servers are added to a **denylist** for security monitoring and mitigation.
The platform also identifies **JA3 fingerprints** to detect and block malware botnet communications at the TCP layer. Analysts can browse, download, and integrate these lists into threat hunting rulesets.

**Reference:** [[SSL Blacklist](https://sslbl.abuse.ch/)](https://sslbl.abuse.ch/)

---

## URLhaus
**URLhaus** focuses on sharing URLs used for malware distribution. Analysts can search for domains, URLs, hashes, and file types suspected to be malicious.
The tool also provides feeds associated with **country, AS number, and Top-Level Domain (TLD)**, enabling tailored threat intelligence generation.

**Reference:** [[URLhaus](https://urlhaus.abuse.ch/)](https://urlhaus.abuse.ch/)

---

## ThreatFox
**ThreatFox** allows analysts to search, share, and export **indicators of compromise (IOCs)** related to malware.

IOCs can be exported in multiple formats:
* MISP events
* Suricata IDS rulesets
* Domain host files
* DNS Response Policy Zone
* JSON files
* CSV files

**Reference:** [[ThreatFox](https://threatfox.abuse.ch/)](https://threatfox.abuse.ch/)

---

## Case Scenario
Security analysts can leverage Abuse.ch platforms for multi-source investigations. Examples include:

1. **ThreatFox Investigation:** Identify the malware alias name associated with IOC `212.192.246.30:5555`

   * **Reference:** [[ThreatFox IOC Lookup](https://threatfox.abuse.ch/)](https://threatfox.abuse.ch/)

2. **SSL Blacklist Investigation:** Determine the malware associated with JA3 Fingerprint `51c64c77e60f3980eea90869b68c58a8`

   * **Reference:** [[SSL Blacklist JA3 Lookup](https://sslbl.abuse.ch/)](https://sslbl.abuse.ch/)

3. **URLhaus ASN Investigation:** From URLHaus statistics, identify the malware-hosting network with ASN **AS14061**

   * **Reference:** [[URLhaus Statistics](https://urlhaus.abuse.ch/statistics/)](https://urlhaus.abuse.ch/statistics/)

4. **FeodoTracker Country Investigation:** Determine the country of the botnet IP address `178.134.47.166`

   * **Reference:** [FeodoTracker C\&C Lookup](https://feodotracker.abuse.ch/)

These scenarios demonstrate how analysts can cross-reference multiple sources for **thorough threat intelligence investigations**.

---

## Conclusion
Abuse.ch provides comprehensive tools for malware and botnet intelligence across multiple domains: malware collection, C\&C tracking, SSL certificate analysis, malicious URL monitoring, and IOC sharing.

By combining these platforms, security analysts can:
* Identify malware families and C\&C servers.
* Validate suspicious IPs, URLs, and SSL fingerprints.
* Apply mitigation strategies through blocklists, denylists, and feeds.

This multi-platform approach ensures a **robust, evidence-based cybersecurity investigation process**.


