# Introduction to Volatility

Volatility is a free memory forensics tool developed and maintained by the Volatility Foundation, commonly used by malware and SOC analysts within a blue team or as part of their detection and monitoring solutions. Volatility is written in Python and is made up of Python plugins and modules designed as a plug-and-play way of analyzing memory dumps.

By TryHackMe: [https://tryhackme.com/room/volatility](https://tryhackme.com/room/volatility)

Volatility is available for Windows, Linux, and Mac OS and is written purely in Python.

<img width="3734" height="1586" alt="Image" src="https://github.com/user-attachments/assets/c84149bb-8eb0-486b-9b39-ff107bd0bc60" />

---

## Volatility Overview

From the Volatility Foundation Wiki:

*"Volatility is the world's most widely used framework for extracting digital artifacts from volatile memory (RAM) samples. The extraction techniques are performed completely independent of the system being investigated but offer visibility into the runtime state of the system. The framework is intended to introduce people to the techniques and complexities associated with extracting digital artifacts from volatile memory samples and provide a platform for further work into this exciting area of research."*

Volatility is built off of multiple plugins working together to obtain information from the memory dump. To begin analyzing a dump, you will first need to identify the image type; there are multiple ways of identifying this information that we will cover further in later tasks. Once you have your image type and other plugins sorted, you can then begin analyzing the dump by using various Volatility plugins against it.

Since Volatility is entirely independent of the system under investigation, this allows complete segmentation but full insight into the runtime state of the system.

At the time of writing, there are two main repositories for Volatility: one built off Python 2 and another built off Python 3. For this room, we recommend using the **Volatility3** version built off Python 3:
👉 [https://github.com/volatilityfoundation/volatility3](https://github.com/volatilityfoundation/volatility3)

> **Note**: When reading blog posts and articles about Volatility, you may see *Volatility2* syntax mentioned. All syntax has changed in Volatility3, and within this room, we will be using the most recent version of the plugin syntax for Volatility.

---

## Installing Volatility

Since Volatility is written purely in Python, installation is straightforward across Windows, Linux, and Mac. If you already attempted to use Python on Windows and Mac, it is suggested to begin on Linux; however, all operating systems will work the same.

If you're using TryHackMe's AttackBox, Volatility is already pre-installed, and you can skip these steps.

![Image](https://github.com/user-attachments/assets/152a50c1-1e01-4644-ae46-4ecbd1804fe9)

When downloading, you can either:

* Use the **pre-packaged executable (.whl file)** (Windows only).
* Run directly from Python (cross-platform).

### Pre-Packaged Executable

Download from the releases page:
👉 [https://github.com/volatilityfoundation/volatility3/releases/tag/v1.0.1](https://github.com/volatilityfoundation/volatility3/releases/tag/v1.0.1)

### From Source

Dependencies required:

* Python 3.5.3 or later
* Pefile 2017.8.1 or later → [https://pypi.org/project/pefile/](https://pypi.org/project/pefile/)

Optional (recommended):

* yara-python 3.8.0 or later → [https://github.com/VirusTotal/yara-python](https://github.com/VirusTotal/yara-python)
* capstone 3.0.0 or later → [https://www.capstone-engine.org/download.html](https://www.capstone-engine.org/download.html)

Clone the repository:

```
git clone https://github.com/volatilityfoundation/volatility3.git
```

Test installation:

```
python3 vol.py -h
```

For Linux or Mac memory files, download **symbol files**:
👉 [https://github.com/volatilityfoundation/volatility3#symbol-tables](https://github.com/volatilityfoundation/volatility3#symbol-tables)

---

## Memory Extraction

Extracting a memory dump can be performed in multiple ways, depending on investigation requirements.

### Bare-Metal Tools

* FTK Imager
* Redline
* DumpIt.exe
* win32dd.exe / win64dd.exe
* Memoryze
* FastDump

Most tools output a `.raw` file (except Redline, which uses its own structure).

<img width="1140" height="309" alt="Image" src="https://github.com/user-attachments/assets/d311cd9e-270f-48ec-8a1f-51f0dc49a3a9" />

### Virtual Machines

Memory can be gathered by collecting virtual memory files from the host machine:

* VMWare → `.vmem`
* Hyper-V → `.bin`
* Parallels → `.mem`
* VirtualBox → `.sav` (partial file)

---

## Plugins Overview

With **Volatility3**, OS profiles are deprecated. The framework now auto-detects OS and build.

### Plugin Syntax

* `windows.plugin`
* `linux.plugin`
* `mac.plugin`

For plugin help:

```
python3 vol.py -h
```

---

## Identifying Image Info and Profiles

In Volatility2, `imageinfo` provided profile suggestions. In Volatility3, profiles are deprecated—use instead:

* `windows.info`
* `linux.info`
* `mac.info`

Example:

```
python3 vol.py -f <file> windows.info
```

---

## Listing Processes and Connections

### Process Plugins

* **pslist** → lists all processes (including terminated).
* **psscan** → scans memory structures for hidden processes.
* **pstree** → shows parent/child process relationships.

### Network Plugin

* **netstat** → lists network connections (unstable on older builds).

Alternative: Extract PCAP using bulk\_extractor:
👉 [https://tools.kali.org/forensics/bulk-extractor](https://tools.kali.org/forensics/bulk-extractor)

### DLL Plugin

* **dlllist** → lists DLLs loaded by processes.

---

## Volatility Hunting and Detection Capabilities

### malfind

Detects code injection and injected processes.

```
python3 vol.py -f <file> windows.malfind
```

### yarascan

Scans memory against YARA rules.

```
python3 vol.py -f <file> windows.yarascan
```

---

## Advanced Memory Forensics

### Hooking

Focus: **SSDT Hooks**

```
python3 vol.py -f <file> windows.ssdt
```

### Drivers

* **modules** → lists loaded kernel modules.
* **driverscan** → scans memory for drivers.

Other plugins: modscan, driverirp, callbacks, idt, apihooks, moddump, handles.

---

## Practical Investigations

### Case 001 – *BOB! THIS ISN'T A HORSE!*

* Banking trojan suspected.
* Suspicious IP: `41.168.5.140`
* Memory file: `/Scenarios/Investigations/Investigation-1.vmem`

### Case 002 – *That Kind of Hurt my Feelings*

* Ransomware incident.
* Decryption key already obtained.
* Memory file: `/Scenarios/Investigations/Investigation-2.raw`

---

## Conclusion

This walkthrough covered only the basics of memory forensics. For a deeper dive, consider:

* *The Art of Memory Forensics*
* [[Volatility Wiki](https://github.com/volatilityfoundation/volatility/wiki)](https://github.com/volatilityfoundation/volatility/wiki)
* [[Volatility Documentation Project](https://github.com/volatilityfoundation/volatility/wiki/Volatility-Documentation-Projec)](https://github.com/volatilityfoundation/volatility/wiki/Volatility-Documentation-Projec)
* [[SANS Memory Forensics Poster](https://digital-forensics.sans.org/media/Poster-2015-Memory-Forensics.pdf)](https://digital-forensics.sans.org/media/Poster-2015-Memory-Forensics.pdf)
* [[eForensics Magazine – Advanced Malware & Volatility](https://eforensicsmag.com/finding-advanced-malware-using-volatility/)](https://eforensicsmag.com/finding-advanced-malware-using-volatility/)
