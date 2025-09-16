
# MISP – Malware Information Sharing Platform

MISP (Malware Information Sharing Platform) is an open-source threat information platform that facilitates the collection, storage, and distribution of threat intelligence and Indicators of Compromise (IOCs) related to malware, cyber attacks, financial fraud, or any intelligence within a community of trusted members.

![Image](https://github.com/user-attachments/assets/7aed85cf-f57f-4c55-a40e-2d70ef9c1aa3)

Information sharing follows a distributed model, supporting closed, semi-private, and open communities (public). Additionally, threat information can be distributed and consumed by Network Intrusion Detection Systems (NIDS), log analysis tools, and Security Information and Event Management Systems (SIEM).

MISP is particularly useful for the following use cases:

* **Malware Reverse Engineering:** Sharing malware indicators to understand the functioning of different malware families.
* **Security Investigations:** Searching, validating, and using indicators to investigate security breaches.
* **Intelligence Analysis:** Gathering information about adversary groups and their capabilities.
* **Law Enforcement:** Using indicators to support forensic investigations.
* **Risk Analysis:** Researching new threats, their likelihood, and occurrences.
* **Fraud Analysis:** Sharing financial indicators to detect financial fraud.

<img width="1140" height="800" alt="Image" src="https://github.com/user-attachments/assets/03f5193d-7e04-4345-8ff0-4ab6dde03379" />

---

## What Does MISP Support?

MISP provides the following core functionalities:

* **IOC Database:** Stores technical and non-technical information about malware samples, incidents, attackers, and intelligence.
* **Automatic Correlation:** Identifies relationships between attributes and indicators from malware, attack campaigns, or analysis.
* **Data Sharing:** Supports sharing information using different distribution models among MISP instances.
* **Import & Export Features:** Facilitates import/export of events in multiple formats to integrate with NIDS, HIDS, and OpenIOC.
* **Event Graph:** Visualizes relationships between objects and attributes identified from events.
* **API Support:** Enables integration with other systems to fetch and export events and intelligence.

### Common MISP Terms

* **Events:** Collection of contextually linked information.
* **Attributes:** Individual data points associated with an event, such as network or system indicators.
* **Objects:** Custom attribute compositions.
* **Object References:** Relationships between different objects.
* **Sightings:** Time-specific occurrences of a given attribute, adding credibility.
* **Tags:** Labels attached to events/attributes.
* **Taxonomies:** Classification libraries used to tag, classify, and organize information.
* **Galaxies:** Knowledge base items used to label events/attributes.
* **Indicators:** Pieces of information that detect suspicious or malicious cyber activity.

---

## Using the System

### Dashboard

The analyst's view in MISP provides functionalities to track, share, and correlate events and IOCs during an investigation. The dashboard menu includes:

* **Home Button:** Returns to the application’s start screen, the event index page, or a custom home page.
* **Event Actions:** Provides access to create, modify, delete, publish, search, and list events and attributes.
* **Dashboard:** Allows creation of a custom dashboard using widgets.
* **Galaxies:** Shortcut to the list of MISP Galaxies.
* **Input Filters:** Controls how data is entered, including validation, regex replacements, and blocklists.
* **Global Actions:** Access information about MISP, edit profile, view manual, news, active organisations, and contribution statistics.
* **MISP Link:** Base URL of your MISP instance.
* **User Name:** Auto-generated from the email address of the logged-in user.
* **Envelope:** Access User Dashboard for notifications and proposals.
* **Log Out:** Ends your session.

<img width="1677" height="999" alt="Image" src="https://github.com/user-attachments/assets/2bbba7ba-9caf-4842-9629-5b538087706e" />

---

## Event Management

The **Event Actions** tab is where analysts create correlations by providing descriptions and attributes associated with investigations. This process has three main phases:

1. **Event Creation**
2. **Populating Events with Attributes and Attachments**
3. **Publishing**

We will follow this process using an example investigation of **Emotet Epoch 4 infection with Cobalt Strike and Spambot** from malware-traffic-analysis.net.

### Event Creation

Events store general information about an incident or investigation. Click **Add Event** to include the description, time, and risk level. Specify the desired **distribution level**:

* **Your Organisation Only:** Visible only to members of your organisation.
* **This Community Only:** Visible to users in your MISP community, including synchronised MISP servers.
* **Connected Communities:** Visible to your community plus organisations two hops away.
* **All Communities:** Freely shared with all MISP communities.

You can also define **sharing groups** for predefined lists of organisations.

![Image](https://github.com/user-attachments/assets/1b9202fc-2dab-45db-9b97-b82c2ab6b2d2)

Event details can also be populated using predefined templates, such as the **Phishing E-mail** category for our CobaltStrike investigation.

![Image](https://github.com/user-attachments/assets/39244bfc-56de-4131-8a41-1232f9a5de1c)

### Attributes & Attachments

Attributes can be added manually or imported through formats such as OpenIOC and ThreatConnect. Click **Add Attribute** and populate the fields. Key options include:

* **For Intrusion Detection System:** Marks the attribute for IDS usage unless it overrides the permitted list.
* **Batch Import:** Allows multiple attributes of the same type (e.g., IP addresses) to be added together.

In our example, we add an **Emotet Epoch 4 C2 IP address** from an IOC text file.

![Image](https://github.com/user-attachments/assets/0466b24e-6be1-42b3-b2ab-154e5c9ff6f7)

Analysts can also attach files, such as malware binaries or reports. For example, we added the **Cobalt Strike EXE binary**, marked as malware for safe handling.

<!-- Failed to upload "6.gif" -->

### Publish Event

After creation, the organisation admin reviews and publishes events, sharing them via the distribution channels set during creation.

<!-- Failed to upload "7.gif" -->

---

## Feeds & Taxonomies

### Feeds

Feeds are continuously updated sources containing indicators that can be imported into MISP. They allow analysts to:

* Exchange threat information
* Preview events with attributes and objects
* Import selected events into their instance
* Correlate attributes between events and feeds

Site Admin enables and manages feeds.

![Image](https://github.com/user-attachments/assets/b20b3580-74cc-42ef-ac31-72f63b4d4cff)

### Taxonomies

Taxonomies classify events, indicators, and threat actors using tags. They allow analysts to:

* Set events for external tools processing (e.g., VirusTotal)
* Ensure proper classification before publishing
* Enrich IDS export values

**Machine tags** have three parts:

1. **Namespace:** Defines the property
2. **Predicate:** Specifies the attached property
3. **Value:** Numerical or text details

<img width="872" height="606" alt="Image" src="https://github.com/user-attachments/assets/5fec3dbe-4cd5-4b13-b373-0f8acddf5a6c" />

Taxonomies can be enabled by the site admin under the **Event Actions** tab.

<img width="959" height="537" alt="Image" src="https://github.com/user-attachments/assets/9ca64369-9fef-411e-8ef9-1ed55fc9ddee" />

### Tagging

Tags identify events and attributes based on indicators or threats. In our **CobaltStrike event**, tags can be added via the **Tags section**, selecting global or local tags. Analysts can also create custom tags for internal usage.

![Image](https://github.com/user-attachments/assets/a0a33031-b36e-43dc-8e50-6a3fa3556c7a)

#### Tagging Best Practices

* **Event vs Attribute Level:** Tags are inheritable. Set at event level; only tag attributes if they differ.
* **Minimal Subset of Tags:**

  * **Traffic Light Protocol:** Guides intelligence sharing.
  * **Confidence:** Indicates data quality and reliability.
  * **Origin:** Source of information (manual or automated).
  * **Permissible Actions Protocol:** Advanced classification for internal searches.

---

## Scenario Event

CIRCL (Computer Incident Response Center Luxembourg) published a **PupyRAT** infection event. Investigate and correlate details with your SIEM.

**Questions:**

* Event ID for PupyRAT?
* The adversary gained \_\_\_\_\_\_ into organisations.
* IP address mapped as PupyRAT C2 server?
* Attack group from Intrusion Set Galaxy using this attack?
* Taxonomy tag with Certainty level of 50?

---

## Recap

This room introduces **MISP** for sharing malware and threat intelligence. Analysts can now document, report, and share incident information effectively.

---

## Additional Resources

Further exploration is encouraged through the following resources:

* **[[MISP Book](https://www.misp-project.org/book.html)](https://www.misp-project.org/book.html)**
* **[[MISP GitHub](https://github.com/MISP/MISP)](https://github.com/MISP/MISP)**
* **[[CIRCL MISP Training Module 1](https://www.circl.lu/services/misp-training/)](https://www.circl.lu/services/misp-training/)**
* **[[CIRCL MISP Training Module 2](https://www.circl.lu/services/misp-training/)](https://www.circl.lu/services/misp-training/)**

We credit **CIRCL** for providing guidelines supporting this room.

