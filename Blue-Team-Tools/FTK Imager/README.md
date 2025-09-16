# FTK-Imager

To begin her investigation, Anna must acquire the Windows registry data from the target system. Registry artifacts contain rich configuration and execution information crucial to forensic timelines and attribution. This write-up explains the difference between **live** and **cold** collections, describes why and when to use each approach, and gives a polished, step-by-step FTK Imager procedure Anna can follow — preserving the same screenshots placeholders included in the original task.

---

## Live vs Cold Acquisition — Key differences

### Live Acquisition

A **live acquisition** is performed while the system is running. Because the Windows OS is loaded in memory, the system’s active configuration is available (for example, `CurrentControlSet` is already in use). However:

* Registry hive files on a running system are typically locked and cannot be copied directly without special techniques or tools.
* Any tool executed on the system (for example FTK Imager) will leave traces — it may create registry entries, files, timestamps or other artefacts that modify the system state.
* Live collection is commonly chosen for **speed**. When rapid results are required (e.g., during an outbreak or active incident), live collection can be far more practical than creating a full disk image first.

Trade-off: speed and immediacy vs. risk of altering evidence and incomplete capture of locked artifacts.

### Cold Acquisition

A **cold acquisition** is performed when the system is not running. Typical cold acquisition workflow:

1. Remove the target drive and attach it to a forensic workstation using a **hardware write-blocker**, or create a trusted disk image with a hardware/firmware imager.
2. Hash the image (MD5/SHA1/SHA256) and work only on the copy; analysis is performed against that verified image.
3. Mount the image read-only (or browse it with a forensic tool) and extract registry hives and related files.

Advantages:

* Minimal impact to the original system.
* Stronger integrity guarantees for court (hashes and preserved original evidence).
* Can extract all files without concerns about locks or live tool artifacts.

Drawback: more time and extra steps compared with a quick live collection.

---

## Using FTK Imager to Acquire Registry Data

FTK Imager can be used to read disk images and to export registry files and associated artifacts. Below is a precise, repeatable procedure Anna can apply in the VM from Task 1.

### High-level overview

* FTK Imager supports both **live** (logical drive) and **cold** (image file) evidence items.
* It can obtain protected files (for locked hives) or allow manual navigation of the drive/image to export exact files you need.
* For complete registry analysis, export each hive plus adjacent transaction logs/backup files and additional artifacts such as `Amcache.hve` and user `NTUSER.DAT`.

---

## Step-by-step: Load evidence in FTK Imager

1. **Start FTK Imager** (in the VM it’s available via a Desktop shortcut).

2. Navigate to **File → Add Evidence Item**.

<img width="1146" height="716" alt="Image" src="https://github.com/user-attachments/assets/973f1b86-b127-4723-8f4a-90efaf03b9de" />

3. Choose the evidence type:

   * **Logical Drive** — use for a live collection (select the OS drive, typically `C:`).

<img width="1148" height="717" alt="Image" src="https://github.com/user-attachments/assets/6c0b3ac9-da4a-418d-a324-b7e4bb44bd3e" />


     Select the OS drive from the available list.
     

<img width="1145" height="714" alt="Image" src="https://github.com/user-attachments/assets/57a8bf1d-54c6-43a0-b3a7-8fcdd092a892" />

   * **Image File** — use when working from a cold image; browse to and add the saved disk image.

<img width="1144" height="717" alt="Image" src="https://github.com/user-attachments/assets/7ec27c92-8699-4adc-aa08-dbd1a8d7e885" />

4. Once added, the evidence will appear in the FTK Imager tree. The remainder of the export process is the same for live or cold evidence.

<img width="1146" height="715" alt="Image" src="https://github.com/user-attachments/assets/87d1bb63-42ab-47c7-8781-466ad6be9a93" />

---

## Exporting registry files

### Option A — Obtain Protected Files (live only)

* In a live system FTK Imager’s **Obtain Protected Files** can attempt to export locked system files (registry hives).

<img width="1146" height="714" alt="Image" src="https://github.com/user-attachments/assets/f5782404-eb21-47b2-a26e-ec0a45f68a1c" />

* **Caveat:** this function may not capture every required hive (for example, `Amcache.hve` may be missed). It may also leave traces on the system (tool execution artifacts). The bottom-left of the referenced screenshot indicates that this option includes exports that may facilitate SAM extraction; use with awareness of operational risk.

### Option B — Manual navigation & export (recommended for completeness)

* Expand the image/drive tree in FTK Imager and navigate to the file locations you need.
* Select desired files and choose **Export Files** (right-click or use export toolbar).

<img width="1147" height="717" alt="Image" src="https://github.com/user-attachments/assets/8a888833-9a7a-4062-b6e7-b169be62eac1" />

**Files to export from** `C:\Windows\System32\config\`:

* `SAM`
* `SYSTEM`
* `SOFTWARE`
* `SECURITY` (if required)
* `DEFAULT`
* Transaction / log files in the same folder (e.g., `.LOG`, `.LOG1`, `.LOG2`, `.sav`) — these capture pending/uncommitted changes and are crucial to a full forensic picture.

**Additional important artifacts to export:**

* `C:\Windows\AppCompat\Programs\Amcache.hve` (application execution metadata)
* Per-user registry hives: `C:\Users\<username>\NTUSER.DAT`
* `C:\Users\<username>\AppData\Local\Microsoft\Windows\UsrClass.dat`
* Prefetch: `C:\Windows\Prefetch\*.pf` (execution timestamps)
* Event logs: `C:\Windows\System32\winevt\Logs\*` (for full timeline correlation)

---

## Export destination, hashing, and documentation

1. **Export destination:** always export to a case directory on your analysis host, not back to the evidence disk. Preserve directory structure and filenames if possible.
2. **Hashing:** compute and record cryptographic hashes (MD5, SHA1, SHA256) for each exported file immediately after export to preserve integrity and support chain of custody.
3. **Documentation:** document operator name, tool and version, date/time (absolute timestamps), source evidence identifier (serial number or image file name), export path, and any notable tool options used. If the collection was live, explicitly note that the collector may have modified system state.

---

## Minimum file set for Anna’s Task 1 goals

To achieve the outputs described in Task 1, Anna should minimally collect:

* `C:\Windows\System32\config\SAM` (and associated logs)
* `C:\Windows\System32\config\SYSTEM` (and associated logs)
* `C:\Windows\System32\config\SOFTWARE`
* Transaction / log files located alongside the above hives
* `C:\Windows\AppCompat\Programs\Amcache.hve`
* `C:\Users\<username>\NTUSER.DAT` for relevant accounts

Collecting these will provide the registry data needed to extract user/computer configuration, installed programs and execution traces required by the scenario.

---

## Post-collection analysis tips

* Use dedicated hive parsers and forensic tools (e.g., Registry Explorer, RegRipper, commercial suites, or the appropriate Volatility plugins) to parse exported hives.
* Correlate registry findings with event logs, prefetch entries, and file system metadata to build reliable timelines.
* Where possible, load hive files into a tool that supports `HKLM\SYSTEM\CurrentControlSet` mappings so you can reliably determine active configurations.
* For live collections, cross-verify entries against logs and prefetch to detect collector-induced changes and reduce false positives.

---

## Conclusion

FTK Imager gives precise control over registry exports and works with both live logical drives and cold disk images. For forensic integrity and court readiness, prefer **cold acquisition** (image, hash, mount, export). For urgent incident response, a **live acquisition** may be justified but must be carefully documented and limited to minimize evidence modification. By exporting the core hives plus transaction logs and supplementary artifacts such as `Amcache.hve` and `NTUSER.DAT`, Anna will have the registry granularity required to answer the investigative questions from Task 1.



