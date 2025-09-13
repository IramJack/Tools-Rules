# Introduction to Autopsy

Autopsy is an open-source and powerful digital forensics platform. Several features within Autopsy have been developed with funding from the Department of Homeland Security Technology. You can read more about this here.

**The official description:**
*"Autopsy is the premier open source forensics platform which is fast, easy-to-use, and capable of analysing all types of mobile devices and digital media. Its plug-in architecture enables extensibility from community-developed or custom-built modules. Autopsy evolves to meet the needs of hundreds of thousands of professionals in law enforcement, national security, litigation support, and corporate investigation."*

<img width="90" height="90" alt="Image" src="https://github.com/user-attachments/assets/93b53f2e-51be-4a50-80ea-96b3aecda80b" />

---

## Workflow Overview

Before diving into Autopsy and analyzing data, there are a few steps to perform, such as identifying the data source and what Autopsy actions to perform with the data source.

**Basic workflow:**

* Create/open the case for the data source you will investigate
* Select the data source you wish to analyze
* Configure the ingest modules to extract specific artifacts from the data source
* Review the artifacts extracted by the ingest modules
* Create the report

---

## Case Analysis | Create a New Case

To prepare a new case investigation, you need to create a case file from the data source. When you start Autopsy, you will have three options. You can create a new case file using the **New Case** option. Once you click on the "New Case" option, the Case Information menu opens, where information about the case is populated.

* **Case Name:** The name you wish to give to the case
* **Base Directory:** The root directory that will store all the files specific to the case (the full path will be displayed)
* **Case Type:** Specify whether this case will be local (Single-User) or hosted on a server where multiple analysts can review (Multi-User)

**Note:** In this room, the focus is on Single-User. Also, the room doesn't simulate a new case creation, and the given VM already has a sample case file on the Desktop (as demonstrated below) to practice covered Autopsy features.

The following screen is titled *Optional Information* and can be left blank for our purposes. In an actual forensic environment, you should fill out this information. Once you click "Finish", Autopsy will create a new case file from the given data source.

![Image](https://github.com/user-attachments/assets/797d2f8e-6d6e-4ed8-bcdf-fd43201701e0)

---

## Case Analysis | Open an Existing Case

Autopsy can also open prebuilt case files. Note that supported data sources are discussed in the next task. This part will show how to create/open case files with Autopsy.

**Note:** Autopsy case files have a `.aut` file extension.

In this room, you will import a case. To open a case, select the **Open Case** option. Navigate to the case folder (located on the Desktop) and select the `.aut` file you wish to open. Next, Autopsy will process the case files and open the case.

![Image](https://github.com/user-attachments/assets/9ff48069-2178-4834-8e8b-a4a6e14ebcc9)

**Note:** If Autopsy cannot locate the disk image, a warning box will appear. At this point, you can point to the location of the disk image it's attempting to find, or you can click *No*; you can still analyze the data from the Autopsy case.

<img width="1074" height="410" alt="Image" src="https://github.com/user-attachments/assets/152584da-13cb-4b24-a1e1-982a9939b8dd" />

Once the case you wish to analyze is open, you are ready to start exploring the data. You can also identify the name of the case at the top left corner of the Autopsy window when the case is open.

<img width="678" height="970" alt="Image" src="https://github.com/user-attachments/assets/a4f7d8c5-8f2c-40a1-9a46-232e82f9ccfd" />

---

## Data Sources

Autopsy can analyze multiple disk image formats. Before diving into the data analysis step, let's briefly cover the different data sources Autopsy can analyze. You can add data sources using the **Add Data Source** button. Available options are shown below.

![Image](https://github.com/user-attachments/assets/dbc27ecc-a4e7-4c12-a587-f5dc2aabf6c1)

We will focus primarily on the **Disk Image or VM File** option in this room.

**Supported Disk Image Formats:**

* Raw Single (e.g., `*.img`, `*.dd`, `*.raw`, `*.bin`)
* Raw Split (e.g., `*.001`, `*.002`, `*.aa`, `*.ab`)
* EnCase (e.g., `*.e01`, `*.e02`)
* Virtual Machines (e.g., `*.vmdk`, `*.vhd`)

If there are multiple image files (e.g., E01, E02, E03, etc.), Autopsy only needs you to point to the first image file; it will handle the rest.

**Note:** Refer to the Autopsy documentation to understand the other data sources that can be added to a case.

---

## Ingest Modules

Essentially, Ingest Modules are Autopsy plug-ins. Each Ingest Module is designed to analyze and retrieve specific data from the drive. You can configure Autopsy to run specific modules during the source-adding stage or later by choosing the target data source available on the dashboard.

By default, the Ingest Modules are configured to run on *All Files, Directories, and Unallocated Space*. You can change this setting during the module selecting step. You can track the process by clicking the bar in the lower right corner.

We have demonstrated two different approaches to using the ingest modules in this task.

### While Adding Data Sources

One of the ways to configure the ingest modules is to enable/disable them while adding a data source in Autopsy. This is a straightforward process, as you get this option while adding a data source.

<img width="852" height="534" alt="Image" src="https://github.com/user-attachments/assets/846883a3-1edf-4496-a980-ff953b1816a1" />

### After Adding Data Sources

Another method of configuring the ingest modules is configuring them on a pre-loaded disk. This can be done by following the steps given below:

* Open the "Run Ingest Modules" menu by right-clicking on the data source.
* Choose the modules to implement and click on the **Finish** button.
* Track the progress of implementation.

![Image](https://github.com/user-attachments/assets/c7e904e9-5128-4fe8-8746-9d3c77f4d2d5)

**Note:** Using ingest modules requires time to implement. Therefore, we will not cover ingest modules in this room.

The results of any Ingest Module you select to run against a data source will populate the **Results node** in the *Tree View*, which is the left pane of the Autopsy user interface.

Below is an example of using the *Interesting Files Identifier* ingest module. Note that the results depend on the dataset. If you choose a module to retrieve specific data that is unavailable in the drive, there will be no results.

<img width="600" height="160" alt="Image" src="https://github.com/user-attachments/assets/36a389f7-a0d6-47a4-a713-320add15ca8e" />

## Drawing Attention Back to Configure Ingest Modules

Notice that some Ingest Modules have per-run settings and some do not. For example, the *Keyword Search* Ingest Module does not have per-run settings, while the *Interesting Files Finder* Ingest Module does. The yellow triangle represents the per-run settings option.

As Ingest Modules run, alerts may appear in the **Ingest Inbox**. Below is an example of the Ingest Inbox after a few Ingest Modules have completed running.

<img width="817" height="551" alt="Image" src="https://github.com/user-attachments/assets/daac192f-3d56-4566-aba1-b3e090c45a19" />

To learn more about Ingest Modules, you can read Autopsy documentation here.

---

## The User Interface I

Let’s look at the Autopsy user interface, which is comprised of **five primary areas**:

### Tree Viewer

The Tree Viewer has five top-level nodes:

<img width="434" height="220" alt="Image" src="https://github.com/user-attachments/assets/2c0c7493-1592-4c44-8a2b-da898c8d20a5" />

* **Data Sources** – All the data will be organized as typically seen in a normal Windows File Explorer.
* **Views** – Files will be organized based on file types, MIME types, file size, etc.
* **Results** – This is where the results from Ingest Modules will appear.
* **Tags** – Displays files and/or results that have been tagged.
* **Reports** – Shows reports either generated by modules or by the analyst.

Refer to the Autopsy documentation on the Tree Viewer for more information here.

---

### Result Viewer

**Note:** Don’t confuse the *Results node* (from the Tree Viewer) with the *Result Viewer*.

When a volume, file, folder, etc., is selected from the Tree Viewer, additional information about the selected item is displayed in the Result Viewer.

For example, the sample case’s data source is selected, and additional information is now visible in the Results Viewer.

<img width="1470" height="189" alt="Image" src="https://github.com/user-attachments/assets/f54d6491-8db8-47e1-b323-4227e990555a" />

If a volume is selected, the Result Viewer’s information will change to reflect what is stored in the local database. From within the Result Viewer, you can also extract any file if you right-click and select **Extract File(s)**.

<img width="1388" height="286" alt="Image" src="https://github.com/user-attachments/assets/313ebce7-fb40-45aa-80b8-9742d8baad2a" />

The Result Viewer pane has **three tabs**: *Table, Thumbnail, and Summary.*

* **Table Tab** – Default view for displaying structured info.
* **Thumbnail Tab** – Works best with image or video files.
* **Summary Tab** – Provides overview information.

<img width="1206" height="218" alt="Image" src="https://github.com/user-attachments/assets/cd48a0e6-c21c-468a-aa1b-6662a335ac06" />

Volume nodes can be expanded, and an analyst can navigate through contents like in a Windows file system.

<img width="329" height="307" alt="Image" src="https://github.com/user-attachments/assets/c450e5ad-3505-4f7e-937a-4e2804960e8e" />

In the Views tree node, files are categorized by File Types:

* By Extension
* By MIME Type
* By Deleted Files
* By File Size

<img width="203" height="203" alt="Image" src="https://github.com/user-attachments/assets/4813e408-d10d-4255-b71e-2f7fe5b925ee" />

**Tip:** Be careful when relying on *By Extension*. An adversary can rename a file with a misleading extension. Always compare with *By MIME Type* for accuracy.

<img width="197" height="324" alt="Image" src="https://github.com/user-attachments/assets/0eacc04a-2abe-4add-8be7-63e0278f7be2" />

Refer to the Autopsy documentation on the Result Viewer for more information here.

---

### Contents Viewer

If you click any folder or file from the Table tab in the Result Viewer, additional information is displayed in the **Contents Viewer** pane.

<img width="1444" height="1234" alt="Image" src="https://github.com/user-attachments/assets/8caa9b8d-bd30-4869-b28d-1c0708f9fcd1" />

Columns explained:

* **S = Score**

  * Red exclamation = Notable
  * Yellow triangle = Suspicious
* **C = Comment**

  * Yellow page icon = Comment attached
* **O = Occurrence**

  * Shows how many times the file/folder has been seen in past cases (requires Central Repository)

Refer to the Autopsy documentation on the Contents Viewer for more information here.

---

### Keyword Search

At the top right, you will find **Keyword Lists** and **Keyword Search**. With Keyword Search, analysts can perform **ad-hoc keyword searches**.

<img width="345" height="280" alt="Image" src="https://github.com/user-attachments/assets/91e2bb13-e714-49be-ba9f-c888ca935df7" />

Example: Searching for the word *“secret”* produces results like the screenshot below.

<img width="1164" height="250" alt="Image" src="https://github.com/user-attachments/assets/b63e6893-1a16-42e5-bd5e-60e41627fed3" />

Refer to the Autopsy documentation for more information on keyword searches.

---

### Status Area

The Status Area is at the bottom right.

* When Ingest Modules run, a **progress bar with % completed** is shown.
* Clicking the bar gives more detailed information.
* Clicking the **X** next to the bar will prompt you to confirm if you want to cancel.

<img width="635" height="70" alt="Image" src="https://github.com/user-attachments/assets/a63104e7-3e1a-48fd-8ad1-b88101935995" />

Refer to the Autopsy documentation on the UI overview here.

---

## The User Interface II

Autopsy also provides summarized information to help analysts prioritize. Reviewing the **Data Sources Summary** before diving deep can save time.

### Data Sources Summary

This summary provides aggregated info in **nine categories**.
If you need to go deeper, analyze individual modules through the Result Viewer.

![Image](https://github.com/user-attachments/assets/a3329a17-6c48-4e56-97dd-750c9b9b4a11)

---

## Generate Report

Autopsy can generate reports in multiple formats. Reports include all Result Viewer findings, which makes them useful for documentation and offline review.

**Tip:** Autopsy is resource-heavy. On low-end systems, generating reports might be faster than navigating the GUI.

Steps to generate a report:

![Image](https://github.com/user-attachments/assets/d3ac88d4-6fac-41b4-9097-2ede7072fe02)

Once generated, you can view the HTML Report in your browser.

<img width="2192" height="1542" alt="Image" src="https://github.com/user-attachments/assets/f3bf72f9-e7ea-4aff-b229-1a9dd598b7f8" />

---

## Data Analysis – Mini Scenario

**Scenario:** An employee is suspected of leaking company data. A disk image from their machine is obtained. You must perform an initial analysis.

**Note:** Since no actual disk image is included in the VM, some sections will display only metadata. Click **No** when prompted about missing images. You also don’t need to run ingest modules for this exercise.

<img width="1074" height="410" alt="Image" src="https://github.com/user-attachments/assets/a82dae95-4680-4da5-b04b-2744287481ec" />

---

## Visualization Tools

Autopsy also includes visualization features for exploring data differently.

<img width="803" height="40" alt="Image" src="https://github.com/user-attachments/assets/00c514c3-fdaa-4903-9293-01ad9f14139e" />

### References:

* **Images/Videos:** [http://sleuthkit.org/autopsy/docs/user-docs/4.12.0/image\_gallery\_page.html](http://sleuthkit.org/autopsy/docs/user-docs/4.12.0/image_gallery_page.html)
* **Communications:** [http://sleuthkit.org/autopsy/docs/user-docs/4.12.0/communications\_page.html](http://sleuthkit.org/autopsy/docs/user-docs/4.12.0/communications_page.html)
* **Timeline:** [http://sleuthkit.org/autopsy/docs/user-docs/4.12.0/timeline\_page.html](http://sleuthkit.org/autopsy/docs/user-docs/4.12.0/timeline_page.html)

---

## Timeline Tool

The Timeline is one visualization feature available in the VM.

<img width="2542" height="1378" alt="Image" src="https://github.com/user-attachments/assets/46cc7c0e-3296-45cd-8c66-df227c6fe718" />

The Timeline is split into three sections:

* **Filters** – Narrow displayed events
* **Events** – Visual representation based on mode
* **Files/Contents** – Extra details about selected events

**View Modes:**

* **Counts** – Bar chart representation
* **Details** – Collapsed clustering for readability
* **List** – Table view

<img width="678" height="326" alt="Image" src="https://github.com/user-attachments/assets/35d02e9c-b125-4853-8d30-4103c4389ebe" />

Clusters show event counts over time. For example, 130,257 events were logged for */Windows* between 2009-06-10 and 2010-03-18.

<img width="440" height="127" alt="Image" src="https://github.com/user-attachments/assets/978a027d-cfcd-42f7-9b24-c73dc465c0a6" />

Expand clusters with the green **+** icon.

<img width="198" height="88" alt="Image" src="https://github.com/user-attachments/assets/3fbfe807-6c1f-41ca-a94c-7ac5f60df8b6" />

Collapse events with the red **–** icon. Pin groups using the map marker with a **+**.

<img width="343" height="119" alt="Image" src="https://github.com/user-attachments/assets/996d6edd-81b5-4272-83a5-e03d421b8363" />

Unpin events with the map marker **–** icon. Hide events using the **eye –** icon.

<img width="318" height="174" alt="Image" src="https://github.com/user-attachments/assets/cfe34eff-a963-4fd4-8b9b-2de028d3c40b" />

To unhide, right-click and select **Unhide**.

<img width="293" height="78" alt="Image" src="https://github.com/user-attachments/assets/12fb018b-d865-4a2c-9bef-f349892ba07a" />

Finally, the **List View Mode** provides a table representation.

<img width="1094" height="245" alt="Image" src="https://github.com/user-attachments/assets/e56f1e5d-e854-4776-887b-99b80d08de7d" />

---

## Conclusion
To conclude, we learned about the capabilities of a powerful disk image analysis tool, Autopsy. There is more to the Autopsy tool that wasn't covered in detail in this room. Below are some topics that you should explore on your own to configure Autopsy to do more out of the box:

   **Global Hash Lookup Settings**
    **Global File Extension Mismatch Identification Settings**
    **Global Keyword Search Settings**
    **Global Interesting Items Settings**
    **Yara Analyser**

3rd Party [modules](http://sleuthkit.org/autopsy/docs/user-docs/4.12.0/module_install_page.html) are available for Autopsy. Visit the official SleuthKit GitHub repo for a list of 3rd party modules [here](https://github.com/sleuthkit/autopsy_addon_modules). The disk image used with this room's development was created and released by the NIST under the Computer Forensic Reference Data Sets CFReDS Project. You are encouraged to download the disk image, go through the full exercise ([here](https://www.cfreds.nist.gov/data_leakage_case/data-leakage-case.html) to practice using Autopsy), and level up your investigation techniques.


