# Timesketch

Timesketch is an open-source tool used in the forensics world to analyze and investigate events. It allows investigators to visualize and examine event data from various sources to identify patterns, insights, and intelligence related to a security event captured on a timeline.

By TryHackMe: https://tryhackme.com/room/dfirtimelineanalysis

<img width="1280" height="640" alt="Image" src="https://github.com/user-attachments/assets/b99100bc-4b3d-4a49-b527-1d72e08b8aa9" />

---

The key features of Timesketch include:

* **Visualization:** An interactive interface displays events chronologically, making it easier to follow the sequence of events within a timeline.
* **Search and filter:** You can search and filter events based on specific keywords, timestamps, and attributes.
* **Collaboration:** When working within a team, Timesketch supports collaboration, allowing members to share timelines and contribute towards analysis efforts seamlessly. Investigators can also create and share narrative-driven analysis pieces known as stories that document findings and insights from timelines.
* **External data source integration:** You can integrate external threat intelligence feeds and Sigma rules to enrich timeline data with more context about known threats, IOCs, and suspicious activities.

We have Timesketch on the VM for this task, accessible via `http://MACHINE_IP:8080`. Use the same credentials as previously provided in Task 4: **analyst:analyst1234**. The **Jimmy_timeline.plaso** file from Task 4 has been uploaded and indexed under the **Jimmy Supertimeline** sketch for analysis.

---

## Analyzing the Timeline Using Timesketch

Once we log into Timesketch, we are presented with the homepage, where all the investigation cases, known as sketches, reside. We will be looking at the **Jimmy Supertimeline** sketch throughout this task.

Opening the sketch leads us to the **Explore tab**, where we are presented with details about our timeline.

---

## Zoom In and Out Through Search

When working with a super timeline, narrowing your investigation using search queries will be necessary. The search will be based on the data fields found within the timeline. The standard fields found include **message, timestamp, datetime,** and **timestamp_desc**. Timesketch provides a list of pre-set search templates that we can start with to identify specific information.

Navigating to the search bar, we are presented with a list of searches based on the data types found within the timeline. This is also highlighted in the information on the side panel. To investigate the Windows XML Event entries found, select the **windows:evtx:record** search query. This will present us with a chronological arrangement of the event logs recorded on the system, and we can open an item to look at the details captured.

What if we want to narrow it down to a specific timeframe? We can add a time filter to see events from a particular range. For our study, let us set the filter between **2014-02-15 and 2014-02-17.**

<img width="2555" height="932" alt="Image" src="https://github.com/user-attachments/assets/eb2610ba-1827-4e98-b362-d42548ffdae1" />

With the results within that timeframe, we can analyze an entry from **2014-02-16 18:59:50.401**, which indicates a Remote Procedure Call (RPC) was running on the host.

Looking at the record, we can mainly be interested in the **hash** and **xml_string** fields for further investigation. We can seek a context lookup for the **SHA256 hash**, which would provide us with the options of either adding the value to threat intelligence or performing an external lookup on VirusTotal.

<img width="2555" height="932" alt="Image" src="https://github.com/user-attachments/assets/33435ef3-0e23-4332-97f9-dbb30025d1c5" />

---

## Analyzing Graphs

Another area of analysis we can look at within Timesketch is **Graphs**. In our case, we have three graphs that provide information on the logins, services, and downloads within Jimmy Wilson's system.

Let us open the **Windows services graph**. We can see how the various services are connected and how they were launched. For example, by selecting the **Microsoft Malware Protection Driver**, we can see that it has two initiators, one from the kernel mode driver and the other from boot start. We can even search the context from the details to understand the other events that preceded it. The context can be spread across **1 second up to 60 minutes** around the event.

<img width="2550" height="941" alt="Image" src="https://github.com/user-attachments/assets/71338cb9-c239-43c7-9501-27d70a2afa81" />
<img width="2555" height="940" alt="Image" src="https://github.com/user-attachments/assets/d9770d64-9ea6-4bf4-9146-237e0771949c" />

---

## Running Analyzers

Timesketch offers a robust feature known as **Analyzers** that can enhance the analysis of indexed timelines by searching, correlating, and enriching data. The analyzers can be set to auto-run on every new timeline that is added or manually triggered.

Once we have our **Jimmy_supertimeline** indexed, navigate to the side panel and click the **plus icon** next to the **Analyzers Results tab**. This will load up a page listing all the available analyzers.

<img width="2555" height="940" alt="Image" src="https://github.com/user-attachments/assets/7b59f027-fd1e-4f8d-abdb-f572f3692078" />

Select the **Jimmy_supertimeline** for analysis, and we will run two analyzers: **EVTX gap** and **Browser search terms**.

* The **EVTX analyzer** is designed to identify gaps in the Windows Event Log data, indicating periods where logging might have been disabled or events not captured.
* The **browser search analyzer** extracts search terms from browser history data, which provides insights into user activities.

<img width="2555" height="940" alt="Image" src="https://github.com/user-attachments/assets/f8df64ba-5081-4aa5-b3b2-3fddcbd3e52a" />

Clicking the **Run** button adjacent to the analyzers will initiate them against our timeline. To look at the results of the EVTX gap analyzer, ensure to refresh the Timesketch page and navigate to the **Stories tab**. Here, we will have a detailed story outlining all the gaps detected in the event logs.

<img width="2500" height="948" alt="Image" src="https://github.com/user-attachments/assets/90d00119-7302-464a-b610-1f18693bf40b" />

Navigate to the **Saved Searches tab** for the browser search results. The results will show a list of search terms used, along with the timestamps and browser utilized. This information can help us understand the user's activities and identify potential intent.

<img width="2152" height="814" alt="Image" src="https://github.com/user-attachments/assets/a8aef159-a73f-4963-8acc-18f26850f354" />

> **Note:** The copy button in Timesketch may give you an error, but you can copy everything manually too.

---

## Questions to Answer

Follow these steps to complete the investigation using Timesketch:

1. Determine **how many data types** were in the Jimmy Supertimeline sketch.
2. Identify **how many entries** were in the EVTX Gap Analysis under the Jimmy Supertimeline.
3. Find out **which search engine** Jimmy Wilson used to search for *"how to disappear without a trace."*

<img width="1347" height="561" alt="Image" src="https://github.com/user-attachments/assets/871700ec-539c-49c5-a371-9287fbd7f263" />

4. Investigate the **path of the program** that was called to initiate Microsoft Antimalware Service.
<img width="1356" height="557" alt="Image" src="https://github.com/user-attachments/assets/757e21b9-adb7-4967-927e-b6add4a37a6c" />

---

## Conclusion

Timesketch proves to be an invaluable tool for digital forensics investigations. By visualizing large volumes of event data, it enables investigators to identify patterns, detect anomalies, and uncover malicious activity more efficiently. Features such as advanced search and filtering, timeline-based exploration, graphs, and analyzers make it a versatile platform for correlating evidence and enriching investigations with contextual intelligence.
