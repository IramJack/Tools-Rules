# Introduction to KAPE

**Kroll Artifact Parser and Extractor (KAPE)** parses and extracts Windows forensic artifacts. It is a tool that can significantly reduce the time needed to respond to an incident by providing forensic artifacts from a live system or a storage device much earlier than the imaging process completes.

By TryHackMe: [https://tryhackme.com/room/kape](https://tryhackme.com/room/kape)

<img width="256" height="256" alt="Image" src="https://github.com/user-attachments/assets/3d206316-b939-43d8-be24-37e445e59398" />

KAPE serves two primary purposes:

1. **Collect files**
2. **Process the collected files** as per the provided options.

To achieve these purposes, KAPE uses the concepts of **targets** and **modules**:

* **Targets** = the forensic artifacts that need to be collected.
* **Modules** = programs that process the collected artifacts and extract information from them.

We will learn more about them in the upcoming tasks.

---

## How It Works

KAPE is extensible and highly configurable. In essence, the KAPE binary collects files and processes them according to the provided configuration.

* **File collection (targets):**
  KAPE adds files to a queue and copies them in two passes:

  * **Pass 1:** Copies files the OS has not locked.
  * **Pass 2:** Uses raw disk reads to bypass OS locks and copy the remaining files.

  Files are saved with original timestamps and metadata, stored in a similar directory structure.

* **Processing with modules:**
  Modules are independent binaries that process collected data. For example, KAPE may collect a Prefetch file, then a module like **PECmd** can parse it and export results into CSV format.

<img width="1140" height="230" alt="Image" src="https://github.com/user-attachments/assets/a420e5bd-7183-432f-8abf-124ba33f7a25" />

As shown above, KAPE can extract targets from a **live system, a mounted image, or F-response utility**. It is portable, does not need installation, and can run from a USB or network location.

<img width="801" height="398" alt="Image" src="https://github.com/user-attachments/assets/25132ae5-81bb-4f1f-be37-b2b6aaa38c77" />

Inside the main directory you’ll find two binaries:

* `kape.exe` → CLI version.
* `gkape.exe` → GUI version (the “g” stands for graphical).

Other files include:

* **gkape.settings** – stores default GUI settings.
* **Get-KAPEUpdate.ps1** – PowerShell script to check and download updates.

---

## Target Options

In KAPE’s lexicon, **Targets** = forensic artifacts to be collected from a system or image and copied to a destination.

Example:

* A **Prefetch Target** to capture Windows Prefetch files.
* A **Registry Target** to capture registry hives.

<img width="782" height="547" alt="Image" src="https://github.com/user-attachments/assets/c93d0e05-6f78-4aa0-afe0-be750c7cc392" />

At the bottom of the directory, you’ll find guides and templates for creating new Targets or Compound Targets.

<img width="787" height="830" alt="Image" src="https://github.com/user-attachments/assets/bc90d56e-16ea-43c6-b9b7-188f64220b9e" />

Targets are defined in `.tkape` files. These specify the artifact’s path, category, and file masks.

<img width="801" height="565" alt="Image" src="https://github.com/user-attachments/assets/1d71b059-58d0-48d3-8619-aca7b05a3922" />

Example: Prefetch Target collects `*.pf` files from:

* `C:\Windows\prefetch`
* `C:\Windows.old\prefetch`

The inclusion of `Windows.old` allows investigators to access artifacts retained from OS upgrades.

---

### Compound Targets

KAPE supports **Compound Targets** — collections of multiple targets, useful for quick triage.

Examples:

* `!BasicCollection`
* `!SANS_triage`
* `KapeTriage`

<img width="517" height="513" alt="Image" src="https://github.com/user-attachments/assets/6a5bb94a-8331-4abe-b41c-d494d4783d54" />

This example Compound Target collects execution evidence from **Prefetch, RecentFileCache, AmCache, and Syscache**.

Other directories include:

* **!Disabled** – inactive Targets you still want to keep.
* **!Local** – custom Targets not synced with KAPE’s GitHub repository.

---

## Module Options

Modules are different from Targets. Instead of copying files, **Modules run tools** on collected files and save results (CSV/TXT).

<img width="777" height="592" alt="Image" src="https://github.com/user-attachments/assets/0d9d4a52-6c83-4b7a-83c0-1c5464fb7e6e" />

Like Targets, you’ll see guides, templates, and directories grouped by category. The **bin directory** is worth noting.

<img width="806" height="747" alt="Image" src="https://github.com/user-attachments/assets/ee81950e-1ef2-4476-a690-802369893630" />

`.mkape` files define Modules. For example, the **Windows\_IPConfig** module specifies:

* Executable to run
* Command-line parameters
* Export format and filename

<img width="803" height="376" alt="Image" src="https://github.com/user-attachments/assets/c3047550-f29b-4d4c-9f34-d237a7b72530" />

If the required executable is not natively available, it must be placed in the **bin directory**.

<img width="781" height="712" alt="Image" src="https://github.com/user-attachments/assets/d9b39337-3516-4d93-924d-856e3089a6d0" />

Most binaries here are Eric Zimmerman’s forensic tools.

---

## KAPE GUI

With the basics understood, let’s explore the **GUI (gkape.exe)**.

<img width="1438" height="707" alt="Image" src="https://github.com/user-attachments/assets/1d080839-8ae7-4e5b-9d59-2d95bf8649f8" />

💡 Tip: If the split-screen view is cramped, expand to a full browser tab or resize the VM window.

<img width="917" height="204" alt="Image" src="https://github.com/user-attachments/assets/554ff587-ddaf-4e60-8778-1bf8ec0d25d1" />

Initially, many options are disabled. Enable **Use Target Options** to unlock the left-hand settings.

![Image](https://github.com/user-attachments/assets/e05e3341-a9f8-463c-a4d2-9062fd25b6a1)

Example:

* Target source = `C:\`
* Destination = your chosen folder.

<img width="722" height="325" alt="Image" src="https://github.com/user-attachments/assets/f4da2d91-de84-4392-8f54-f4aab86ccd66" />

Options:

* **Flush** – wipes the destination folder before writing (use carefully).
* **Add %d / %m** – append date or machine info to destination folder names.
* **Search bar** – quickly find Targets.

<img width="717" height="233" alt="Image" src="https://github.com/user-attachments/assets/64cb89cd-f70e-472f-bda0-b45166fc9481" />

Additional options include:

* Process VSCs (Volume Shadow Copies).
* Transfer via SFTP/S3 (requires containerization: ZIP/VHD/VHDX).
* Exclusions based on SHA-1.

<img width="717" height="113" alt="Image" src="https://github.com/user-attachments/assets/5057303a-40dd-45ca-a206-f3081e73ea31" />

The **Current command line** tab updates dynamically as you change GUI options.

<img width="865" height="166" alt="Image" src="https://github.com/user-attachments/assets/14177da8-47e0-4414-8a5b-fc7a187930b4" />

Enabling **Use Module Options** unlocks the right-hand settings.

<img width="1920" height="1018" alt="Image" src="https://github.com/user-attachments/assets/bf956f82-c078-49e1-a836-fe1dc8933657" />

When Targets + Modules are both selected, the Module Source defaults to the Target destination.

<img width="1918" height="252" alt="Image" src="https://github.com/user-attachments/assets/fe13478f-be3b-4b11-8e6d-69b0d9d7d417" />

Here’s a full GUI setup using:

* **KapeTriage** (Compound Target)
* **!EZParser** (Compound Module)

<img width="1920" height="1017" alt="Image" src="https://github.com/user-attachments/assets/2f4be83f-3a85-497c-9aed-787ec952038a" />

Clicking **Execute!** launches the process in CLI mode under the hood.

<img width="981" height="530" alt="Image" src="https://github.com/user-attachments/assets/75d3b697-4c62-4e85-ae13-6c65792fa5d1" />

Processed files are categorized neatly in the Module destination.

<img width="687" height="448" alt="Image" src="https://github.com/user-attachments/assets/2423ff66-e57f-48ba-8f25-36a37ed6a098" />

---

## KAPE CLI

KAPE is fundamentally a command-line tool. To see its switches, run:

```powershell
D:\KAPE> kape.exe
```

<img width="870" height="123" alt="Image" src="https://github.com/user-attachments/assets/fce7c236-25b8-437d-adce-cd0969d31a9a" />

Required flags:

* Targets → `--tsource`, `--target`, `--tdest`
* Modules → `--module`, `--mdest`

Example command:

```powershell
kape.exe --tsource C: --target KapeTriage --tdest C:\Users\thm-4n6\Desktop\target --mdest C:\Users\thm-4n6\Desktop\module --module !EZParser
```

Run this in an **elevated shell** for full functionality.

---

### Batch Mode

KAPE supports **batch mode** with a `_kape.cli` file stored alongside the binary.

Example `_kape.cli` file:

```
--tsource C: --target KapeTriage --tdest C:\Users\thm-4n6\Desktop\Target --mdest C:\Users\thm-4n6\Desktop\module --module !EZParser
```

Running `kape.exe` will automatically execute the commands inside.

---

## Hands-on Challenge

Organization X has a strict **Acceptable Use Policy (AUP)** forbidding:

* Connecting removable or network drives
* Installing unauthorized software
* Connecting to unknown networks

One user may have violated this policy.

Your task:

* Use KAPE on the provided VM.
* Run your chosen Target + Module options.
* Answer investigation questions.

Tip: Use **EZviewer** (in the EZtools folder on Desktop) to open CSV results.

---

## Conclusion

Phew — Windows forensics keeps getting more exciting with KAPE. 

You can:

* Explore the VM for more interesting artifacts.
* Try running KAPE on your own machine and see what evidence you can uncover.

