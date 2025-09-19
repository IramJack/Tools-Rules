# Redline

Many tools can aid a security analyst or incident responder in performing memory analysis on a potentially compromised endpoint. One of the most popular tools is **Volatility**, which allows an analyst to dig deep into memory artifacts from an endpoint. However, this process can be time-consuming. During triage, time is critical, and analysts often need to perform a quick assessment to determine the nature of a security event.

That is where the **FireEye tool Redline** comes in. Redline provides analysts with a **30,000-foot view (10 kilometers high view)** of a Windows, Linux, or macOS endpoint. With Redline, you can analyze a potentially compromised endpoint through its memory dump and various file structures. The tool comes with an intuitive **Graphical User Interface (GUI)**, making it easier to identify signs of malicious activity.

### Key Capabilities of Redline:

* Collect registry data (Windows hosts only)
* Collect running processes
* Collect memory images (before Windows 10)
* Collect browser history
* Search for suspicious strings
* And much more!

Installing Redline is straightforward—simply run the MSI file and follow the installation wizard.

---

## Data Collection

Now that you have an overview of Redline, let’s move to the **Data Collection** stage. There are three main ways to collect data:

<img width="566" height="240" alt="Image" src="https://github.com/user-attachments/assets/118c3a86-2c93-430d-82ab-cd75218486a1" />

1. **Standard Collector** – Configures the script to gather the minimum data needed for analysis. This is the **preferred method** in most triage scenarios, as it usually completes in just a few minutes.
2. **Comprehensive Collector** – Configures the script to collect the maximum amount of data from the host. This process can take **an hour or more** and is best used when a **full analysis** is required.
3. **IOC Search Collector (Windows only)** – Collects data that matches Indicators of Compromise (IOCs) defined in the **IOC Editor**. Use this method if you want to run data collection against known IOCs gathered from threat intelligence, incident response, or malware analysis.

Before proceeding, launch Redline (the icon is conveniently placed on the taskbar).

<img width="116" height="95" alt="Image" src="https://github.com/user-attachments/assets/82d9b5f5-d2b5-48cc-a3d3-33cc3cd5f267" />

In this task, we will use the **Standard Collector method**.

* From Redline, click **Create a Standard Collector**.
* Choose the target platform (for our case, select **Windows**).

<img width="640" height="424" alt="Image" src="https://github.com/user-attachments/assets/e2eaf606-3aef-404f-bc16-2290c18375c5" />

Under **Review Script Configuration**, click **Edit your script**. Here, you’ll be presented with five tabs: **Memory, Disk, System, Network, and Other**.

---

### Memory

<img width="854" height="558" alt="Image" src="https://github.com/user-attachments/assets/424f5481-458a-4b44-bb91-e78e4d311f3c" />

You can configure memory data collection, such as:

* Process listings
* Driver enumeration (Windows only)
* Hook detection (pre-Windows 10)

**Note:** For this exercise, **uncheck Hook Detection** and make sure **Acquire Memory Image** is also unchecked.

---

### Disk

Collect data on disk partitions, volumes, and file enumeration.

<img width="854" height="382" alt="Image" src="https://github.com/user-attachments/assets/05f7862d-b5ee-45b7-8ada-78ec2d5bbcc9" />

---

### System

Provides system information, including:

* Machine and OS details
* System restore points (Windows <10)
* Registry hives (Windows only)
* User accounts (Windows, macOS)
* Groups (macOS only)
* Prefetch cache (Windows only)

<img width="640" height="220" alt="Image" src="https://github.com/user-attachments/assets/06dd4359-cf0f-48b3-b1a3-de54ce189608" />

---

### Network

Supports Windows, macOS, and Linux. Useful for collecting **network activity** and **browser history**, vital for identifying malicious downloads or suspicious connections.

<img width="854" height="292" alt="Image" src="https://github.com/user-attachments/assets/0a0d9260-9165-4cb1-a503-508675a7e6ee" />

---

### Other

<img width="857" height="400" alt="Image" src="https://github.com/user-attachments/assets/1ecc2dd0-f60c-45dd-9e0b-7261488403b8" />

Collect additional data such as running services, tasks, and file hashes.

---

After configuring your script:

* Click **OK**.
* Under **Save Your Collector To**, click **Browse** and select a folder.

<img width="1211" height="829" alt="Image" src="https://github.com/user-attachments/assets/153df108-de69-411f-bd9f-93d8631c2795" />

**Note:** The folder must be **empty**. In this example, we’ll use the `Analysis` folder.

You will then see the **Collector Instructions**.

<img width="601" height="361" alt="Image" src="https://github.com/user-attachments/assets/cffad28d-1922-4ee4-b403-83a9bb4d0498" />

Inside the folder, you’ll find a script named **RunRedlineAudit.bat**. Run this script as **Administrator** to collect data.

<img width="1012" height="698" alt="Image" src="https://github.com/user-attachments/assets/b02433ff-78c6-42ca-b0eb-e598e211dd33" />

A command prompt window will open during execution and close once complete.

<img width="640" height="423" alt="Image" src="https://github.com/user-attachments/assets/cfc9234f-f120-4b1a-8ba4-a073f5785614" />

The process typically takes **15–20 minutes**. Once finished, a new file named **AnalysisSession1.mans** will appear inside the `Sessions` folder. Double-click it to import into Redline.

<img width="1029" height="499" alt="Image" src="https://github.com/user-attachments/assets/4ccc380d-5d5d-4b58-a1f3-45ebe19ba3e8" />

**Tip:** If multiple runs are performed, the filenames will increment (e.g., `AnalysisSession1.mans`, `AnalysisSession2.mans`).

---

## The Redline Interface

With your first analysis file ready, double-click **AnalysisSession1.mans**. Data import may take up to **10 minutes**.

<img width="624" height="457" alt="Image" src="https://github.com/user-attachments/assets/f249172b-7c59-424d-9d79-ca95df97d204" />

When complete, you’ll be presented with the main analysis interface:

<img width="1777" height="955" alt="Image" src="https://github.com/user-attachments/assets/4ced703d-504c-4b72-87ea-924b95cb0496" />

On the left panel, you’ll find categories of **Analysis Data**.

### Key Sections:

* **System Information** – Machine, BIOS, OS, and user details.
* **Processes** – Includes attributes like Process Name, PID, Path, Arguments, Parent, and Username. Expanding this tab reveals:

  * **Handles** – References to objects/resources such as files or registry keys.
  * **Memory Sections** – Investigate unsigned memory sections (unsigned DLLs may indicate malicious activity).
  * **Strings** – Captured string data.
  * **Ports** – Review inbound/outbound connections for signs of **C2 communication** or data exfiltration.

⚠️ Pay attention to **system processes** (e.g., `explorer.exe` or `notepad.exe`). These should not typically have outbound connections.

Other important sections include:

* **File System** (not included in this analysis session)
* **Registry**
* **Windows Services**
* **Tasks** (threat actors often create scheduled tasks for persistence)
* **Event Logs** (great for spotting suspicious PowerShell usage or account activity)
* **ARP and Route Entries** (not included in this session)
* **Browser URL History** (not included in this session)
* **File Download History**

---

### Timeline Analysis

The **Timeline** feature is crucial for reconstructing an attack. It records file actions (created, modified, accessed, etc.).

<img width="188" height="213" alt="Image" src="https://github.com/user-attachments/assets/ba8463f3-8a5b-4b40-a1fd-66fe1818523e" />

If you know when compromise occurred, use **TimeWrinkles™** to filter timeline events around that period.

<img width="480" height="285" alt="Image" src="https://github.com/user-attachments/assets/058d29af-19b3-4d91-98c7-69db2c03f1b2" />
<img width="346" height="143" alt="Image" src="https://github.com/user-attachments/assets/add8ec5d-1c01-4aa0-8fa2-664b49079df7" />

Use **TimeCrunches™** to reduce repetitive or irrelevant data (e.g., hiding events that occurred within the same minute).

<img width="478" height="282" alt="Image" src="https://github.com/user-attachments/assets/9a249fe4-1922-4391-b5fc-70e5b012cbfd" />
<img width="352" height="512" alt="Image" src="https://github.com/user-attachments/assets/e7e768c5-9815-4e49-9338-9a8f8f596232" />

---

For more detailed instructions, refer to the official **Redline User Guide**:
 [[Redline User Guide (PDF)](https://fireeye.market/assets/apps/211364/documents/877936_en.pdf)](https://fireeye.market/assets/apps/211364/documents/877936_en.pdf)

---

# Standard Collector Analysis

By now, you should be familiar with some of the data collection terms and techniques covered in the previous task. With this knowledge, it’s time to investigate what the intruder has planted on the computer.

**Note:** For this task, you must manually run the **Redline Collector** on the workstation and use the collected data to answer the questions.

### Questions to Answer

* Provide the **Operating System** detected for the workstation. Check the System Information section for additional useful details.
* Identify the **suspicious scheduled task** created on the computer.
* Find the **message left by the intruder** in the scheduled task.
* Locate the new **System Event ID** created by the intruder, with the source name `THM-Redline-User` and Type `ERROR`. Provide the Event ID number and its message.
* The intruder downloaded a file containing the flag for Question 8. Provide the **full URL** of the website.
* Provide the **full file path** (including filename) where the file was downloaded.
* Share the **message left by the intruder** in the file.

---

# IOC Search Collector

Earlier, we briefly introduced the **IOC Search Collector** during the Data Collection task. Now let’s explore its capabilities in more detail.

But first, let’s recap what an **IOC** is.

An **Indicator of Compromise (IOC)** is an artifact of potential compromise or intrusion on a host or network. Security analysts rely on IOCs during threat hunting and incident response. Examples of IOCs include:

* File hashes (MD5, SHA1, SHA256)
* IP addresses
* C2 domains
* File size, name, or path
* Registry keys

One useful tool for creating IOC files is **IOC Editor** by FireEye. You can find its guide here:
👉 [[IOC Editor User Guide (PDF)](https://fireeye.market/assets/apps/S7cWpi9W//9cb9857f/ug-ioc-editor.pdf)](https://fireeye.market/assets/apps/S7cWpi9W//9cb9857f/ug-ioc-editor.pdf)

**Note:** The IOC Editor officially supports **Windows 7** as the latest OS, which is the same version installed in the provided VM. However, FireEye also provides **OpenIOC Editor**, which supports Windows 10 and may be worth exploring.

**Tip:** Before proceeding, close Redline to free system resources while working with IOC Editor.

With IOC Editor, you can create, modify, and share IOC text files with others in the InfoSec industry. In this example, we will examine an IOC for a **keylogger** created using IOC Editor.

**Note:** The screenshots below guide you through the process. You don’t need to create the IOC file in this task—you’ll do that in the next one.

Open **IOC Editor**, conveniently pinned to the taskbar next to Redline.
**Note:** It may take \~60 seconds to launch.

1. Create a directory to store your IOC file (IOC Directory).
2. Create the IOC file.

<img width="734" height="374" alt="Image" src="https://github.com/user-attachments/assets/4549c4bc-ea04-47fd-ae26-52e6ed011200" />

### Keylogger Indicators in IOC Editor

<img width="1228" height="822" alt="Image" src="https://github.com/user-attachments/assets/56c6f357-5515-451b-8b1c-e64f3bd89fe0" />

The IOC file contains the following values:

* **Name:** Keylogger (Keylogger.ioc)
* **Author:** RussianPanda
* **File Strings:** psylog.exe, RIDEV\_INPUTSINK
* **File MD5:** 791ca706b285b9ae3192a33128e4ecbb
* **File Size:** 35400

![Image](https://github.com/user-attachments/assets/3fa79b55-6c42-4ffd-8068-5751867c3ab7)

To add an IOC value, select an item and enter the value directly.

<img width="194" height="97" alt="Image" src="https://github.com/user-attachments/assets/f9716335-268d-4809-8535-4c0e73613867" />

You can also add values within **Properties**. Only the **Content** and **Comment** fields are editable.

<img width="683" height="428" alt="Image" src="https://github.com/user-attachments/assets/70aa7fe0-4db1-4f74-b6c0-71b2b80169c2" />

After entering the value, click **Save**.

<img width="263" height="92" alt="Image" src="https://github.com/user-attachments/assets/b823d23b-0477-4d62-9050-893d1ae6bd5a" />

**Note:** Right-click an item for more options.

<img width="383" height="223" alt="Image" src="https://github.com/user-attachments/assets/a4edf4c8-adb7-4279-945e-a95f065484f5" />

Now that the IOC file is created and saved, let’s return to the IOC Search Collector in Redline.

**Note:** If you closed Redline, relaunch it. You may now close IOC Editor to free system resources.

The IOC Search Collector ignores data that does not match your gathered IOCs. However, you can still configure it to collect additional information. As the Redline User Guide states, the quality of IOC analysis depends on the data collected in the session.

<img width="257" height="86" alt="Image" src="https://github.com/user-attachments/assets/a33c47c7-a89c-4db8-aae6-a6add6956caa" />

To create an IOC Search Collector:

1. Click **Browse…** and select your `.ioc` file.
2. Redline will automatically load it into the **Indicators** section.

<img width="1717" height="844" alt="Image" src="https://github.com/user-attachments/assets/b12abc84-eac6-4da4-a351-9adaa3bfdf93" />

Redline differentiates between:

* **Unsupported Search Terms:** Ignored by Redline (no hits).
* **Supported Search Terms:** Recognized and actively searched.

After reviewing the IOCs, click **Next**.

Next, configure the script for data collection by selecting **Edit your script**.

<img width="572" height="640" alt="Image" src="https://github.com/user-attachments/assets/d13699e0-79c3-474e-bbf0-a3be41f879aa" />

When finished, save the script, then in **Save Your Collector To**, select an empty folder where your analysis file and `RunRedlineAudit.bat` will be saved.

Execute the `.bat` file as before, then wait for the analysis to complete.

<img width="879" height="800" alt="Image" src="https://github.com/user-attachments/assets/ea2ed043-310c-49d7-b2a9-e7cfb5f675c8" />

When finished, you’ll see the `.mans` file (e.g., `AnalysisSession1`). Double-click it to open in Redline.

<img width="802" height="100" alt="Image" src="https://github.com/user-attachments/assets/5a5d7022-a3cb-47be-a9a2-d7d48ea6adbf" />

If Redline doesn’t generate an IOC report automatically, create one manually by selecting **Create a New IOC Report** and importing your `.ioc` file.

Once generated, you’ll see the **Hits**. Expand the rows for details.

<img width="1729" height="496" alt="Image" src="https://github.com/user-attachments/assets/3717ec74-dad7-437f-9aa8-1d12ef9bb9a3" />

For example, a false positive may appear, such as a hit on `chrome.dll`.

<img width="400" height="502" alt="Image" src="https://github.com/user-attachments/assets/45c7a6da-e1e5-4463-949d-c9a36621c708" />

This match occurred because `chrome.dll` contained the string `RIDEV_INPUTSINK` from our IOC file. It’s important to build precise IOC artifacts to reduce false positives.

The file with the highest number of hits is likely the actual target.

<img width="943" height="505" alt="Image" src="https://github.com/user-attachments/assets/3561c87c-95b1-4ffd-8e2c-1234e5e34d07" />

You are now ready to answer the related questions and apply these techniques in the upcoming task.

---

# IOC Search Collector Analysis

**Scenario:**
You are tasked with threat hunting at **Osinski Inc.** The company suspects an intrusion where the attacker used a tool to perform lateral movement, possibly via a **pass-the-hash** attack.

**Task:**
Find the malicious file planted on the victim’s computer using **IOC Editor** and **Redline IOC Search Collector**.

### Known Artifacts for the File

* **File Strings:** `20210513173819Z0w0= <?<L<T<g=`
* **File Size:** 834936 bytes

**Note:** Use the existing Redline session:
`C:\Users\Administrator\Documents\Analysis\Sessions\AnalysisSession1`

### Questions to Answer

* Provide the **path and filename** of the matched file.
* Provide the **directory path** (without filename).
* Who is the **owner** of the file?
* What is the **subsystem** of the file?
* Provide the **Device Path** where the file resides.
* Provide the **SHA-256 hash** of the file.
* The attacker masqueraded the real filename. Can you identify it using the hash?

---

# Endpoint Investigation

**Scenario:**
A Senior Accountant named **Charles** reports that he can’t access his spreadsheets or other files. His wallpaper was also replaced with a ransom note, indicating his files have been encrypted.

This requires urgent investigation!

**Task:**
Navigate to the **Endpoint Investigation** folder on your Desktop and double-click the `AnalysisSession1.mans` file. The data will automatically import into Redline.

**Note:** Allow up to 10 minutes for all data to load.

### Questions to Answer

* Identify the **product name** of the machine.
* What is the name of the **note left on the Desktop** for Charles?
* Find the Windows Defender service. What is the **name of its service DLL**?
* What is the filename of the **zip file manually downloaded** by the user?
* Provide the filename of the **malicious executable dropped** on the Desktop.
* What is the **MD5 hash** of this malicious executable?
* What is the **name of the ransomware**?

---

# Conclusion

This walkthrough demonstrated how **Redline** can guide investigations into compromised hosts. However, the accuracy of the analysis depends heavily on the type of data collected.

Redline can collect and analyze various artifacts, including:

* Running processes
* Services
* Files
* Registry structures
* Event logs

When solving tasks, you may have noticed that the **Timeline** feature is particularly helpful for tracking when attacks began and what actions followed.

### References

* [[Redline User Guide (PDF)](https://fireeye.market/assets/apps/211364/documents/877936_en.pdf)](https://fireeye.market/assets/apps/211364/documents/877936_en.pdf)
* [[IOC Editor User Guide (PDF)](https://fireeye.market/assets/apps/S7cWpi9W//9cb9857f/ug-ioc-editor.pdf)](https://fireeye.market/assets/apps/S7cWpi9W//9cb9857f/ug-ioc-editor.pdf)

