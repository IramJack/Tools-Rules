# Log2Timeline

In digital forensics, **Log2Timeline** stands as a fundamental open-source tool that is indispensable for investigations. This task aims to provide a comprehensive understanding of Log2Timeline's commands and offer insights into creating and analyzing timelines during forensic investigations.
 
By TryHackMe: https://tryhackme.com/room/dfirtimelineanalysis

<img width="1000" height="333" alt="Image" src="https://github.com/user-attachments/assets/fc58c5cf-ac04-4e22-9bfe-de7b42013e6b" />

---

## Scenario

The forensic disk image used in this exercise was procured from the **Computer Forensic Reference Data Sets (CFReDS)** portal hosted by the **National Institute of Standards and Technology (NIST)**.

For more details and access to the dataset, visit **CFReDS**.

---

## Creating a Super Timeline with Log2Timeline

A pre-generated timeline storage file has already been prepared using a sample disk image saved on the machine from our source. However, understanding the process of creating a timeline is crucial for your investigative journey.

### Log2Timeline Basics

To create a timeline storage file with the `.plaso` extension, we need to run the following command.

⚠️ Note: In this walkthrough, the commands are shown for demonstration. On the attached machine, you do not need to run them since the timeline file has already been generated. The actual process may take hours depending on the disk image size.

For this example, we will use the image stored at:
`/home/analyst/Desktop/Timelines/Task4/2020JimmyWilson.E01`

The command is as follows:
log2timeline.py --storage-file Jimmy_timeline.plaso 2020JimmyWilson.E01

During execution, `log2timeline.py` first scans the disk image for partitions. If multiple partitions or **Volume Shadow Copies (VSS)** are found, you will be prompted to make a selection.

The **pre-processing stage** then begins, collecting information from the storage file. This step determines whether the appropriate parsers are enabled to generate the `.plaso` storage file. Afterwards, the storage process starts—this involves workers and collectors, scaled to the number of CPUs available.

---

## Timeline Creation with Log2Timeline

**log2timeline.py --storage-file Jimmy_timeline.plaso 2020JimmyWilson.E01**

...
...
...

**all**

<img width="957" height="604" alt="Image" src="https://github.com/user-attachments/assets/c9d8f05f-310e-4b08-a0e6-35cdb9364e51" />

The disk file had two partitions, and we chose to parse all of them. From the status window, we observed:

* Number of workers started
* Process IDs of worker processes
* Total number of events extracted
* The last file entry from which each worker extracted events

### Log2Timeline Parsers

Parsers provide granularity when creating targeted timelines. Without specifying individual files, simply pointing Log2Timeline at the disk image leverages parser presets to extract relevant data.

To view supported parsers:

**log2timeline.py --parsers list | more**
```

<img width="749" height="607" alt="Image" src="https://github.com/user-attachments/assets/e0ec4e6e-4ca5-4697-ad0a-39d2ac62b85f" />

### Using Parsers with Filters

As an example, let’s employ the **winevt parser** to focus only on Windows Event Logs:

**log2timeline.py --parsers winevt --artifact-filters WindowsEventLogSystem --storage-file Jimmy_WinEvent_timeline.plaso 2020JimmyWilson.E01**


<img width="958" height="479" alt="Image" src="https://github.com/user-attachments/assets/e99f5ea0-5eff-471e-9b10-a7fccf836a92" />

---

## pinfo – Inspecting Timeline Files

The `pinfo.py` utility retrieves metadata about a timeline storage file, such as:

* Storage containers processed
* Parsers used
* Number of extracted events

**pinfo.py Jimmy_timeline.plaso | more**

<img width="714" height="581" alt="Image" src="https://github.com/user-attachments/assets/0a9c5072-5458-42e2-9ce4-bd6ba25b30c6" />

For a more verbose output, include the `-v` flag:

**pinfo.py -v Jimmy_timeline.plaso**

<img width="947" height="579" alt="Image" src="https://github.com/user-attachments/assets/b89f654b-cf81-4c51-9173-05807a983cae" />

---

## psort – Post-Processing Timelines

`psort.py` is used to **filter, sort, and analyze Plaso storage files**. It can also convert them into other formats (e.g., CSV) for deeper analysis.

General usage format:

**psort.py [-a] [-o FORMAT] [-w OUTPUTFILE] [--output-time-zone TIME_ZONE] STORAGE_FILE FILTER**


To list supported output formats:

**psort.py -o list**

<img width="946" height="426" alt="Image" src="https://github.com/user-attachments/assets/5f227d46-ac76-4f5a-b175-0ba634504f61" />

### Exporting to CSV

To generate a complete timeline of Jimmy Wilson’s disk in CSV:

**psort.py -o dynamic -w Jimmy_timeline.csv Jimmy_timeline.plaso**

<img width="947" height="294" alt="Image" src="https://github.com/user-attachments/assets/7a536460-aea0-4348-895b-ffa3c0a705e4" />

This CSV file allows for chronological analysis of all extracted events.

---

## Automatic Analysis with Plugins

Plaso provides plugins for automatic timeline analysis. These plugins can extract, tag, and summarize events into meaningful reports.

To view supported plugins:

**psort.py --analysis list**

For tagging, check the parameters required:

**psort.py --analysis tagging -h**
<img width="949" height="566" alt="Image" src="https://github.com/user-attachments/assets/51a8c7e5-b571-4b58-b30c-f884e00e6890" />

To run tagging analysis using the built-in tagging file:

**psort.py -o null --analysis tagging --tagging-file tag_windows.txt Jimmy_timeline.plaso
**
<img width="948" height="238" alt="Image" src="https://github.com/user-attachments/assets/48c68f93-dd17-4d49-8157-aab40036b34c" />

This uses `tag_windows.txt` (included with Plaso’s installation) to assign event tags without printing them directly, providing a useful summary for forensic investigations.

---

##Conclusion

Log2Timeline, together with its companion tools pinfo.py and psort.py, provides forensic investigators with a powerful workflow for building, analyzing, and refining timelines from disk images. By leveraging parsers and plugins, investigators can focus on specific artifacts such as Windows Event Logs, export data into formats like CSV for deeper analysis, and even tag events for automated reporting.

Whether working with large forensic images or targeting specific artifacts, these tools help reconstruct the chronological order of events—offering critical insights into how, when, and potentially why an incident occurred.

In essence, Log2Timeline transforms raw forensic data into structured, searchable evidence that strengthens digital investigations.


