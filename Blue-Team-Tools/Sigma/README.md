
# Sigma

## Introduction

Imagine you're a security analyst who has just discovered a new attack technique. Bad actors are using PowerShell with encoded commands to hide their malicious activities. You've figured out exactly what to look for and want to share this detection with your team and the broader security community.

But here's the problem: your team uses Splunk, your colleague uses Microsoft Sentinel, and another organization you collaborate with uses Elastic. Each of these platforms speaks a different query language. Writing the same detection three times, with different syntax, field names, and logic, is time-consuming and error-prone.

This is exactly the problem Sigma solves.

**Sigma** is an open-source, vendor-agnostic language for writing detection rules that work across any security information and event management (SIEM) platform. Think of it as the universal translator for security detections. Write your detection logic once in Sigma, and you can convert it to any SIEM query language you need.

<img width="1024" height="373" alt="image" src="https://github.com/user-attachments/assets/57a10a4e-e228-4971-8d6e-7be9302439ab" />

*Sigma logo and concept illustration*

---

## What is Sigma?

Sigma is an open, generic, YAML-based signature format for describing log events in a structured, human-readable way. Created by Florian Roth and Thomas Patzke in 2017, Sigma was built with one simple goal: to give the security community a shared language for detection logic that doesn't depend on any specific SIEM.

The cleanest way to think about Sigma is by comparing it with other detection languages:

| Tool | What It Detects | Where It Looks |
|------|-----------------|----------------|
| **Snort** | Suspicious network traffic | Network packets |
| **YARA** | Suspicious file patterns | Files and memory |
| **Sigma** | Suspicious activity in logs | Log files and events |

Think of it this way:
- **Snort** describes what suspicious traffic looks like on the wire
- **YARA** describes what suspicious byte patterns look like within a file
- **Sigma** describes what suspicious activity looks like inside a log

All three are vendor-neutral, widely adopted, and let you share detection knowledge without forcing the recipient to use the same tools you do.

---

## The Problem Sigma Solves

Let's make this concrete with a real example. You've discovered that attackers are running PowerShell with the `-EncodedCommand` parameter to hide their payload from plain-text inspection. You want to deploy a detection for this across your organization.

Here's how this detection would look in three different SIEM platforms:

**Splunk SPL:**
```
EventCode=1 Image="*\\powershell.exe" CommandLine="*-EncodedCommand*"
```

**Microsoft Sentinel KQL:**
```
SecurityEvent 
| where EventID == 4688 
| where NewProcessName endswith "\\powershell.exe" 
| where CommandLine contains "-EncodedCommand"
```

**Elastic EQL:**
```
process where process.name == "powershell.exe" and process.command_line : "*-EncodedCommand*"
```

**Same idea. Three different query languages. Three different field names.** 

Now imagine your security operations center (SOC) migrates from Splunk to Sentinel next quarter. Every single one of your detections would need to be rewritten by hand. Multiply that across hundreds of rules, and you're looking at months of work.

The pain gets even sharper in a managed security service provider (MSSP) context. If you're working at a managed security provider, you're not running one single SIEM—it's often one per customer. Customer A is on Splunk, Customer B is on Sentinel, and Customer C is on Elastic. Without a portable format, every new detection has to be authored, tested, and tuned three times over.

**Sigma flips the economics:** the analyst writes the detection logic once, and the conversion step produces the right query for each customer's stack.

---

## Where Sigma Fits in the SOC Workflow

In the detection engineering lifecycle, Sigma slots into the pipeline as the portable artifact in the middle, at the **Detection Design** phase. After you've researched a threat and developed an understanding of what to look for, you write the detection logic in Sigma. Then you convert it to your SIEM's native language for deployment.

<img width="1875" height="957" alt="image" src="https://github.com/user-attachments/assets/e209ea07-4519-42d6-befb-15a905464394" />

*Detection lifecycle diagram showing Sigma's position*

---

## The Sigma Ecosystem

As you start working with Sigma, you'll encounter several moving parts:

### Core Components

- **Sigma Rules**: The YAML files themselves. A single rule is one `.yml` file describing one detection.

- **SigmaHQ Repository**: The public, community-driven ruleset on GitHub at [SigmaHQ/sigma](https://github.com/SigmaHQ/sigma). Thousands of rules contributed by detection engineers worldwide, curated and reviewed by maintainers.

- **sigma-cli**: The modern converter that reads a Sigma rule and emits a backend query. Very popular in automated pipelines.

- **Uncoder.io**: A free web-based converter from SOC Prime. Useful for quickly translating a Sigma rule into a SIEM query without installing anything locally.

You don't need to memorize this list—you'll meet each of these tools naturally as you work with detections. For now, just know that Sigma isn't a single tool; it's an ecosystem: a format, a repository, a set of converters, and a community.

---

## Anatomy of a Sigma Rule

Before you can write a Sigma rule, you need to be able to read one. Every Sigma rule follows the same skeleton, and once you know the parts, you can open any rule in the repository and understand what it's doing.

### The YAML Skeleton

A Sigma rule is a single YAML file with four logical sections:

1. **Metadata**: Defines who wrote the rule, what it detects, and what it maps to in MITRE ATT&CK
2. **Log Source**: Defines what kind of data the rule applies to
3. **Detection**: Defines the actual logic that decides whether an event matches
4. **Triage**: Defines how the alert should be handled when it fires

Here's a sample rule shape related to certutil download detection:

<img width="831" height="377" alt="image" src="https://github.com/user-attachments/assets/d63a8a7a-3049-44d8-9478-87a5e7a91d5a" />

*Example Sigma rule YAML structure*

Every Sigma rule, no matter how complex, fits this template. Let's break down each component.

---

### Metadata Fields

The metadata sits at the top of the rule and tells you what the detection is, who wrote it, and how it maps to known threats. It does not affect the detection logic—it's pure documentation, but it's the documentation a SOC analyst lives in.

| Field | Purpose | Example |
|-------|---------|---------|
| **title** | Human-readable name of the rule | "Suspicious PowerShell EncodedCommand" |
| **id** | Unique identifier (UUID) | `d4c1c3f0-6d4a-4f8b-8c3e-6a3f8b9c7d3e` |
| **status** | Rule maturity (experimental, test, stable) | `experimental` |
| **description** | Detailed explanation of what the rule detects | "Detects PowerShell with -EncodedCommand parameter used to hide payload" |
| **references** | Links to threat research, advisories, etc. | "https://attack.mitre.org/techniques/T1059/001/" |
| **author** | Creator's name | "John Doe" |
| **date** | Creation or last update date | "2024-01-15" |
| **tags** | MITRE ATT&CK mapping and other labels | `attack.t1059.001` |

**In short:** The metadata block is where you orient yourself when a rule fires. It's the difference between an alert that reads as "something fired, go investigate" and one that tells you exactly what you're looking at, what motivated the detection, and where to start triaging.

---

### The Log Source Block

The `logsource` block tells the converter what kind of data the rule expects. Without it, the converter wouldn't know whether to query Sysmon process events, Windows Security logs, or Linux auditd records.

<img width="831" height="70" alt="image" src="https://github.com/user-attachments/assets/9d41b92d-3ee7-441e-8c75-aac812c01aad" />

*Log source configuration example*

Three optional keys live here:

- **category**: The type of log event (e.g., `process_creation`, `network_connection`, `file_event`, `registry_event`)
- **product**: The vendor or platform (e.g., `windows`, `linux`, `aws`, `okta`)
- **service**: The specific service within the product (e.g., `sysmon`, `security`, `powershell`)

You don't always need all three. By mapping only the category of a `process_creation` rule for product `windows`, it works whether the underlying logs come from Sysmon Event ID 1 or Windows Security Event ID 4688. The point is: `logsource` describes the data abstractly, and the converter resolves it to the specific index, logs, or table in the target SIEM.

---

### The Detection Block: The Core Logic

This is the heart of every Sigma rule. The detection block has two parts: **selections** and a **condition**.

<img width="831" height="102" alt="image" src="https://github.com/user-attachments/assets/6b6a9ce3-41a1-4720-8c2c-05f10354a206" />

*Detection block structure*

#### Selections

A selection is a named block of field/value pairs that need to match. The name is up to you (e.g., `selection`, `selection_img`, `filter_admin`). The convention is to prefix exclusions with `filter_` and inclusions with `selection_`, but the names themselves carry no special meaning—they're labels you reference later in the condition.

Think of a selection as like parentheses in query languages, but with names. All the conditions inside a selection should match, like an AND.

For example, this detection block:
```yaml
detection:
  selection:
    Image: '*\whoami.exe'
    CommandLine: '*/all*'
  condition: selection
```

Is the same as, in Elastic KQL:
```
(Image:"*\whoami.exe" and CommandLine:"*/all*")
```

#### Modifiers

By default, a Sigma field match is an exact equality. To do anything else, you need to append a modifier with a pipe:

<img width="827" height="163" alt="image" src="https://github.com/user-attachments/assets/f1d7b40c-32c5-4f11-8987-18816276407c" />


*Modifiers syntax example*

**The most common modifiers:**

| Modifier | Description | Example |
|----------|-------------|---------|
| `contains` | Substring match anywhere in the field | `CommandLine|contains: '-EncodedCommand'` |
| `startswith` | Match the beginning of the field | `Image|startswith: 'C:\Windows\'` |
| `endswith` | Match the end of the field | `Image|endswith: '\powershell.exe'` |
| `re` | Regular expression match | `CommandLine|re: '.*-Enc[oO]ded.*'` |
| `all` | Every value in the list must match | `CommandLine|all: ['-EncodedCommand', '-ExecutionPolicy']` |

All the modifiers can be found in the [official Sigma documentation](https://sigmahq.io/docs/basics/modifiers.html).

#### Wildcards and Lists

Another way to define possible field values is to use **lists**. A YAML list under a field is treated as an OR by default.

This example fires if Image ends with either `whoami.exe` or `net.exe`:

<img width="831" height="86" alt="image" src="https://github.com/user-attachments/assets/3bf3add9-d732-4d97-b209-aee534be8046" />

*List syntax example*

```yaml
detection:
  selection:
    Image|endswith: 
      - '\whoami.exe'
      - '\net.exe'
  condition: selection
```

Note that if you use the `all` modifier (e.g., `Image|endswith|all`), the list will work as an AND instead of an OR. A good example of this usage is when a detection relies on matching specific patterns in CommandLine, regardless of the command order:

<img width="831" height="102" alt="image" src="https://github.com/user-attachments/assets/0374756b-bc3f-4d81-943f-36c9a1376ad3" />

*All modifier example*

```yaml
detection:
  selection:
    CommandLine|contains|all:
      - '-EncodedCommand'
      - '-ExecutionPolicy'
  condition: selection
```

Another popular Sigma resource is the **wildcard (`*`)**, which is supported in plain string matches:

<img width="831" height="53" alt="image" src="https://github.com/user-attachments/assets/07c69cfc-926c-4aa0-a2a5-054f31fd5203" />

*Wildcard syntax example*

```yaml
detection:
  selection:
    CommandLine: '*-EncodedCommand*'
  condition: selection
```

#### Condition

The condition is a boolean expression that combines selections. The simplest condition is a single selection name. From there, you can use `and`, `or`, `not`, and a few helpers:


| Expression | Meaning | Example |
|------------|---------|---------|
| `selection` | Must match this selection | `condition: selection` |
| `selection1 and selection2` | Must match both selections | `condition: selection and selection_net` |
| `selection1 or selection2` | Must match at least one | `condition: selection1 or selection2` |
| `not filter` | Must not match this selection | `condition: selection and not filter_admin` |
| `1 of selection_*` | Match at least one selection starting with "selection_" | `condition: 1 of selection_*` |
| `not 1 of filter_*` | Match none of the filter selections | `condition: selection and not 1 of filter_*` |

---

### Triage and Response Fields

Two final fields wrap up the rule:

- **falsepositives**: A list of known legitimate triggers. This is what you read first when triaging an alert: "Are any of these known false positives in play?"

- **level**: The severity: `informational`, `low`, `medium`, `high`, or `critical`. Maps to the alert priority in the SIEM.

<img width="831" height="86" alt="image" src="https://github.com/user-attachments/assets/9d71a097-bcd5-49b8-83bd-faf7f2925440" />

*Triage fields example*

```yaml
falsepositives:
  - Administrative activity
  - Software installation scripts
  - Legitimate helpdesk tools
level: medium
```

These fields don't change what the rule matches—they change how the alert is handled when it fires.

---

## Practice: Analyzing a Real Rule

Now, let's apply this to a real rule from the public SigmaHQ repository.

Open the following rule on GitHub and read it top to bottom:

**[Suspicious Encoded PowerShell Command Line](https://github.com/SigmaHQ/sigma/blob/master/rules/windows/process_creation/proc_creation_win_powershell_base64_encoded_cmd.yml)** — `proc_creation_win_powershell_base64_encoded_cmd.yml`

This rule is more complex than the examples you've worked with so far. It uses multiple selections, a filter, and a richer condition expression. Don't worry if every detail isn't immediately obvious—work through it methodically and use the anatomy you've just learned to understand each block's purpose.

---

## Writing a Sigma Rule

Reading rules is the easy part. Writing them is where the muscle memory builds. In this section, you'll author a Sigma rule from scratch. You'll write a rule that catches multiple reconnaissance commands an attacker typically runs in the first few minutes after landing on a Windows host.

### Detecting Recon Commands in Windows

When an attacker lands on a host, the first thing they do is figure out who they are, what privileges they have, and what's around them on the network.

<img width="831" height="183" alt="image" src="https://github.com/user-attachments/assets/e24e33f5-3bcd-4aa6-bf61-ce6affc4119d" />

*Common Windows reconnaissance commands*

Here are common recon commands:
- `whoami` - Shows current user identity
- `systeminfo` - Displays system configuration
- `ipconfig` - Shows network configuration
- `nltest` - Tests domain trust relationships
- `net user` - Lists user accounts
- `net group` - Lists group memberships
- `net localgroup` - Lists local group memberships

Your job is to write a single Sigma rule that catches as many of these as possible.

### The Design Challenge

Before you start typing YAML, stop and look at the shape of the commands. Not all of these commands are matched the same way:

- **Some are recon just by being run**: `whoami.exe`, `systeminfo.exe`, `ipconfig.exe`, and `nltest.exe` rarely appear in typical user workflows. The binary itself can be considered the signal.

- **Some share their binary with completely benign behavior**: `net.exe` is run by all sorts of legitimate Windows operations. Matching on `net.exe` alone would flood the SOC team. The signal lives in the arguments: `user`, `group`, `localgroup`.

This mismatch is the first design decision you have to make. One selection won't cover both cases cleanly—you'll need at least two.

### Step 1: Filling Metadata

Start with the documentation. Don't skip this—the metadata is what your future self (or your teammate at 2 AM) reads when this rule fires.

<img width="831" height="264" alt="image" src="https://github.com/user-attachments/assets/8d63b354-1071-4382-9422-f9c84161c3dd" />

*Metadata example*

```yaml
title: Windows Reconnaissance Commands
id: 6b7c3d4e-5f8a-9b0c-1d2e-3f4a5b6c7d8e
status: experimental
description: Detects execution of common reconnaissance commands on Windows systems
references:
  - https://attack.mitre.org/tactics/TA0007/
author: Your Name
date: 2024-01-15
tags:
  - attack.discovery
  - attack.t1087
  - attack.t1082
```

**Note:** `status: experimental` is the right starting point for a rule you've just written. It graduates to `test`, then becomes `stable` after it's been validated and tuned in a production environment.

### Step 2: Log Source

Define what kind of data this rule expects. You're trying to detect command execution on Windows—which log category is best for this?

Refer to the [official Sigma log source documentation](https://sigmahq.io/docs/basics/log-sources.html) if you're unsure.

<img width="831" height="70" alt="image" src="https://github.com/user-attachments/assets/1fec924e-d658-4dc8-9aa1-0019d93eceaa" />

*Log source example*

```yaml
logsource:
  category: process_creation
  product: windows
  service: null  # Covers both Sysmon and Security logs
```

Remember that there are two most common event IDs in Windows used to identify command execution (Sysmon Event ID 1 and Windows Security Event ID 4688). By not specifying a service, you cover both.

### Step 3: Selecting Standalone Recon Binaries

Now, tackle the easy half: the binaries that are recon on their own. From the list, there are four such binaries (`whoami`, `systeminfo`, `ipconfig`, `nltest`). You want a single selection that matches if Image ends with any one of them.

<img width="831" height="118" alt="image" src="https://github.com/user-attachments/assets/425debe8-b08e-4ff0-841b-435daf22b06c" />

*Standalone binaries selection*

```yaml
detection:
  selection_recon_binaries:
    Image|endswith:
      - '\whoami.exe'
      - '\systeminfo.exe'
      - '\ipconfig.exe'
      - '\nltest.exe'
```

Notice what you've just used: a YAML list under a single field with a modifier. This is the OR pattern from earlier. The selection matches if Image ends with any value in the list: one selection, four binaries, four possible matches.

**Why not write four separate selections?** Because the logic is the same for all of them ("the binary alone can be considered suspicious"), they belong in the same selection. Splitting them would make the condition longer and less manageable.

### Step 4: Selecting net.exe with Recon Arguments

Now the second selection: `net.exe` is benign by itself, but the arguments that make it recon. The selection needs to match two things at once:

1. The image must end with `\net.exe`
2. The command line must contain a recon-related parameter (e.g., `user`, `group`, `localgroup`)

Both conditions need to be true in the same event. In Sigma, when you put multiple field/value pairs inside the same selection, they're combined with AND by default.

<img width="831" height="118" alt="image" src="https://github.com/user-attachments/assets/f1f6ef6a-3291-4f66-b44d-908afeb3f6bc" />

*Net.exe selection with recon arguments*

```yaml
detection:
  selection_net_recon:
    Image|endswith: '\net.exe'
    CommandLine|contains:
      - ' user'
      - ' group'
      - ' localgroup'
```

**Two things to think about:**

1. **The leading space in `' user'` isn't decorative.** Without it, you'd also match command lines that contain words like `username` or `usergroup` that happen to embed those tokens. The space anchors the match to the start of the argument. (You can also use `|startswith` patterns or regex if you want more control.)

2. **The `CommandLine|contains` value is a list**, so the selection matches if any of those tokens appear. Combined with the AND between fields, the full logic in KQL would be:

<img width="804" height="37" alt="image" src="https://github.com/user-attachments/assets/27b4664c-b3c6-4180-a12d-0462b5a9ef2d" />

*KQL equivalent logic*

```
Image endswith "\net.exe" AND (CommandLine contains " user" OR CommandLine contains " group" OR CommandLine contains " localgroup")
```

### Step 5: The Condition

Now you need to combine the two selections. Think about what should make the rule fire: either selection on its own is enough to be suspicious. You're not requiring both to match in the same event—you're saying, "If I see standalone recon binaries OR I see net.exe with recon arguments, that's a hit."

Which condition expression from earlier fits better? Look at your selection names. If both start with `selection_`, Sigma has a clean shorthand for this exact pattern:

<img width="831" height="37" alt="image" src="https://github.com/user-attachments/assets/30527812-1c62-48c9-8f93-ac744c3d1b94" />

*Condition with wildcard*

```yaml
detection:
  condition: 1 of selection_*
```

This reads as "match if at least one selection whose name starts with `selection_` is true." It's equivalent to `selection_recon_binaries or selection_net_recon`, but it scales. If you add a third selection later (say, `selection_powershell_recon`), the condition doesn't need to change.

### Step 6: Triage Fields

Wrap up with the fields that tell triagers how to handle the alert.

<img width="831" height="102" alt="image" src="https://github.com/user-attachments/assets/c60989bb-e5ac-4f69-9651-27b159a7e113" />

*Triage fields example*

```yaml
falsepositives:
  - IT administrative activity
  - Helpdesk troubleshooting scripts
  - Login banner display commands
  - Asset inventory tools
  - Scheduled maintenance tasks

level: low
```

**Note:** `falsepositives` are genuinely large for this rule: IT admins, helpdesk scripts, login banners, asset inventory tools—all of them run these commands. List the categories you can think of. This gives the next person who triages an alert a head start on the investigation.

### Step 7: Validate the Sigma Structure

Before you ship a Sigma rule, it's nice to validate that you used the Sigma structure objects properly with `sigma-cli`. You can test it inside VSCode by opening the embedded Terminal:

<img width="2940" height="1844" alt="image" src="https://github.com/user-attachments/assets/e5ea8565-26cd-4de1-aff7-e94d9a92d0f9" />

*Terminal open in VSCode*

Then run the following commands:

<img width="831" height="53" alt="image" src="https://github.com/user-attachments/assets/8696251f-68b8-4bd2-8c2e-06159fbb1ed8" />

*Sigma validation commands*

```bash
sigma check first-detection.yml
```

`sigma check` confirms whether:
- The YAML is well-formed
- All required fields are present
- The detection block parses cleanly

It will not tell you whether the detection is good, but it will catch every syntax error, missing field, and broken reference before you push.

### Common Mistakes

There are a few mistakes new Sigma authors hit again and again:

| Mistake | Example | Solution |
|---------|---------|----------|
| **Indentation errors** | Using tabs instead of spaces | Use spaces, not tabs. Two spaces per level is the SigmaHQ convention. |
| **Backslashes in Windows paths** | Inside double quotes: `"\net.exe"` interprets `\n` as newline | Use single quotes: `'\net.exe'` or escape: `"\\net.exe"` |
| **Strings vs Lists** | `Image: '\whoami.exe'` (string) vs `Image: ['\whoami.exe', '\net.exe']` (list) | Single value = string; multiple values = YAML list |
| **Unquoted wildcards** | `Image: *\whoami.exe` | Quote them: `Image: '*\whoami.exe'` |
| **Trailing whitespace and BOMs** | Some editors silently add invisible characters | Use a YAML-aware editor (VS Code with YAML extension) |


---

## From YAML to SIEM Query

You're now familiar with the process of writing a Sigma rule and testing its syntax. But here's a question worth pausing on: **where does that YAML actually run?**

The answer is: nowhere, by itself. Sigma rules are declarations of detection logic, not queries. To turn one into something that actually runs in your SIEM, you need a converter.

### Why Conversion Exists

Sigma was deliberately designed as a specification, not an engine. The whole point is portability, and portability only works if the format itself is agnostic to any particular execution environment. So Sigma describes the detection abstractly, and a separate tool, `sigma-cli` (powered by [SigmaHQ](https://github.com/SigmaHQ)), reads the YAML and emits a query in the target language you specify.

<img width="2095" height="778" alt="image" src="https://github.com/user-attachments/assets/d5e94ab8-f14f-473d-9a4a-5a20b34eef98" />

*Conversion flow diagram*

### Backends and Pipelines

Before walking through a conversion in practice, two terms you'll see in the Sigma conversion process are **backend** and **pipeline**.

Think of it like translating a recipe for a different kitchen:
- The **backend** handles the language (English to Japanese)
- The **pipeline** handles the local ingredients (swap "cilantro" for "coriander")

Both have to be right for the recipe to actually cook in the new kitchen.

#### Backend

A backend is the target query language. Each backend knows how to translate Sigma's generic boolean logic into the syntax of one specific SIEM:

| Backend | Target Language |
|---------|-----------------|
| `splunk` | SPL (Splunk Processing Language) |
| `kusto` | Microsoft Sentinel KQL |
| `elasticsearch` | Elastic EQL |
| `qradar` | IBM QRadar AQL |
| `crowdstrike` | CrowdStrike Query Language |

You pick the backend using the `-t` flag on `sigma-cli`.

#### Pipeline

A pipeline handles the messier problem: **field name mapping**. Sigma's field names are generic (`Image`, `CommandLine`, `ParentImage`), but the actual logs in your SIEM might call those fields something completely different.

| Sigma Field | Sentinel (KQL) | Splunk (SPL) | Elastic (EQL) |
|-------------|----------------|--------------|---------------|
| Image | NewProcessName | process_path | process.executable |
| CommandLine | CommandLine | process | process.command_line |
| ParentImage | ParentProcessName | parent_process | process.parent.executable |

A pipeline is a YAML configuration that tells the converter: "When you see `Image` in a Sigma rule, translate it to `NewProcessName` for Sentinel and `process_path` for Splunk." `sigma-cli` ships with official pipelines for common combinations (Sysmon → Splunk, ECS → Elastic, etc.), and you can write custom ones for in-house log formats.

**Backend + pipeline = a complete conversion.** Skip the pipeline, and the converter will emit a query that references `Image` literally, which won't match anything in a SIEM that calls that field something else.

### When Conversion Goes Wrong

Conversion is mostly painless, but a few failure modes come up often enough that you should recognize them:

| Problem | What Happens | Fix |
|---------|--------------|-----|
| **Missing pipeline** | Query references generic field names that don't exist in your SIEM | Add `-p` with the correct pipeline |
| **Unsupported modifier** | Some modifiers aren't supported by every backend | Check which modifiers your target supports |
| **Unknown log source category** | The pipeline doesn't know how to map your category | Use a different category or write a custom pipeline |
| **Field-name drift** | The SIEM vendor has renamed a field since the pipeline was written | Update the pipeline configuration |

### Practice: Converting Your Sigma Rule

Let's take the `first-detection.yml` rule from the previous section and convert it to three different SIEM query languages.

#### Splunk SPL
```bash
sigma convert -t splunk -p splunk_cim first-detection.yml
```

#### Microsoft Sentinel KQL
```bash
sigma convert -t kusto -p microsoft_xdr first-detection.yml
```

#### Elastic EQL
```bash
sigma convert -t eql -p ecs_windows first-detection.yml
```

Look closely at all three. The logic is identical, but the syntax is wildly different. The field names are different. The table or index is different.

**That gap—between identical logic and three incompatible queries—is exactly what Sigma was built to bridge.**

---

## Sigma Rule Tuning

You've learned why tuning matters: an untuned detection produces alert fatigue, and an over-tuned one stops catching real threats. The mindset is the same in Sigma.

In practice, tuning a Sigma rule means editing the YAML directly: adding a filter selection, tightening a modifier, or refining a condition.

### The Most Common Tuning Pattern

The most common tuning pattern in Sigma uses two pieces together:
1. A `filter_*` selection that describes what you want to exclude
2. A `not` in the condition that actually excludes it

#### Defining the Exclusion: Filter Selections

A filter selection looks identical to a regular selection. The only difference is convention: anything you intend to exclude, use the `filter_` prefix. This makes it easy to identify exclusions in your logic, and whoever reads the rule knows immediately what role the selection plays.

<img width="831" height="102" alt="image" src="https://github.com/user-attachments/assets/655cabd1-3096-4a32-973b-fd5b32acf9ea" />

*Filter selection example*

```yaml
detection:
  selection:
    Image|endswith: '\whoami.exe'
  filter_admin:
    User: 'helpdesk-svc'
    CommandLine|contains: 'asset-inventory'
  condition: selection and not filter_admin
```

In this detection, `filter_admin` describes the legitimate activity: the helpdesk service account running `whoami` as part of its normal duties. But the selection alone changes nothing.

#### Applying the Exclusion: Negation in the Condition

The condition is where the exclusion actually happens. The simplest pattern is `selection and not filter_x`, which reads as "match selection, except when filter_x also matches."

For one filter, that's clean enough. But filter selections can accumulate. Every new false positive pattern you discover adds another `filter_false-positive` to the rule. After three or four, the condition gets unwieldy:

<img width="831" height="37" alt="image" src="https://github.com/user-attachments/assets/4767fe1f-30e6-44ff-82dc-b683ac0ac759" />

*Complex condition with multiple filters*

```yaml
condition: selection and not filter_admin and not filter_antivirus and not filter_installer and not filter_backup
```

Sigma gives you a shorthand for exactly this case:

<img width="831" height="183" alt="image" src="https://github.com/user-attachments/assets/d1492006-6841-4a84-8640-1f4c6bfcbd58" />

*Clean condition with wildcard*

```yaml
condition: selection and not 1 of filter_*
```

`not 1 of filter_*` reads as "and none of the filter selections matched." It's the same logic, but it scales. You can add as many filters as you want without touching the condition. The wildcard pattern matches every selection whose name starts with `filter_`, which is exactly why the naming convention matters.

### Field-Level Refinement

Sometimes a blanket exclusion is the wrong tool, because it excludes too much. The better fix is to tighten the original selection to better match the malicious case.

Suppose your detection currently fires on any PowerShell execution:

<img width="831" height="53" alt="image" src="https://github.com/user-attachments/assets/4d783ef6-966e-4850-8176-eb5bbf75a5df" />

*Overly broad PowerShell detection*

```yaml
detection:
  selection:
    Image|endswith: '\powershell.exe'
  condition: selection
```

That's a flood. PowerShell runs constantly on Windows—in admin scripts, scheduled tasks, and software installers. The noise drowns out the signal.

The malicious pattern you actually care about is more specific: PowerShell being used as a download cradle, pulling a payload from a remote URL into memory and executing it.

With a clear objective, you can tighten the selection:

<img width="831" height="134" alt="image" src="https://github.com/user-attachments/assets/eab2b37a-de84-4ae6-93ce-2478376a1e9c" />

*Tightened PowerShell detection*

```yaml
detection:
  selection:
    Image|endswith: '\powershell.exe'
    CommandLine|contains|all:
      - '-EncodedCommand'
      - 'IEX'
      - 'http'
  condition: selection
```

This new version narrows the rule to the variants attackers actually use, without adding any exclusions. This is almost always the better tuning move when it's available. Can you imagine how many exclusions would be required to remove all benign PowerShell behaviors in your environment?

### Transforming an Atomic Rule into Stateful

In detection engineering, you learned about detection types and when each one is ideal. A common tuning move is to promote an atomic rule to a stateful one. Instead of alerting on a single event, the rule alerts when a pattern of events shows up within a time window.

Let's suppose you have an atomic rule that catches files being written to `C:\Windows\Temp\`. On its own, it's constantly fired by software installers, Windows Update, antivirus scans, and dozens of legitimate processes as scratch space. But **fifty** file-write events from the same process to that folder within two minutes is likely an attacker staging data for exfiltration.

The atomic rule stays as-is, without alerting. But you wrap it in a separate correlation rule that references it:

<img width="831" height="248" alt="image" src="https://github.com/user-attachments/assets/7c0d01ae-4bc5-4aa2-acb2-49f3b618dde3" />

*Correlation rule example*

```yaml
title: Suspicious File Write Activity to Temp Folder
id: 7a8b9c0d-1e2f-3a4b-5c6d-7e8f9a0b1c2d
status: experimental
description: Detects multiple file writes to temp folder from same process within a short time
references:
  - https://attack.mitre.org/techniques/T1020/
author: Your Name
date: 2024-01-15
tags:
  - attack.exfiltration
  - attack.t1020

detection:
  selection:
    Rule: file_write_to_temp  # Reference to atomic rule
    Image: '*'                # Group by process image
  condition: selection_count >= 50

level: medium

# Correlation rule specific fields
type: event_count
rules:
  - file_write_to_temp
group-by: Image
timespan: 2m
```

**Four new keys to notice:**

| Key | Purpose | Example |
|-----|---------|---------|
| `type: event_count` | The simplest correlation type | Counts events within a window |
| `rules:` | References the atomic rule by name or ID | The atomic rule produces the events to count |
| `group-by:` | The field that defines "the same source" | Grouping by Image means "50 writes from the same process" |
| `timespan:` | The time window | `2m` = two minutes (also accepts `s`, `h`, `d`) |
| `condition:` | When to fire | `gte: 50` = greater than or equal to 50 |

Sigma also supports other correlation types:
- `value_count`: N distinct values of a field
- `temporal`: Events from multiple rules within a window
- `temporal_ordered`: Same, but order matters

Once you're comfortable with `event_count`, the [Sigma documentation on correlations](https://sigmahq.io/docs/meta/correlations.html) is your next stop for learning about these other types.

---

## Common Sigma Mistakes

A few patterns that often cause tuned-but-broken rules:

| Mistake | Problem | Better Approach |
|---------|---------|-----------------|
| **Over-filtering** | Excluding too much legitimate activity | Use field-level refinement instead of blanket filters |
| **Overly specific selection** | Too many field/value pairs that must all match | Use multiple selections combined with OR conditions |
| **Under-specified log source** | Not enough information for the converter | Include category and product at minimum |
| **Missing false positives** | No guidance for triagers | Always list known and potential false positives |
| **Wrong severity** | Alert doesn't match the actual risk | Map severity carefully—critical for actual breaches, low for suspicious patterns |

---

## Conclusion

Sigma represents a paradigm shift in how we approach security detection engineering. By providing a vendor-agnostic format for writing detection rules, it enables security teams to:

1. **Share detection logic** across organizations and communities
2. **Maintain consistency** across multiple SIEM platforms
3. **Reduce duplication** of effort in multi-vendor environments
4. **Speed up detection deployment** by writing once and converting anywhere

### Key Takeaways

- **Universal Language**: Sigma is to log files what Snort is to network traffic and YARA is to files
- **Write Once, Convert Anywhere**: The same Sigma rule can generate queries for Splunk, Sentinel, Elastic, and dozens of other platforms
- **YAML-Based**: Simple, human-readable format that's easy to learn and maintain
- **Community-Driven**: Thousands of rules contributed by the global security community
- **Portable Knowledge**: Your detection expertise travels with you, not tied to any specific tool

### Getting Started

Ready to start writing Sigma rules? Here's your action plan:

1. **Install sigma-cli**:
   ```bash
   pip install sigma-cli
   ```

2. **Explore the repository**: Browse the [SigmaHQ ruleset](https://github.com/SigmaHQ/sigma) to see real-world examples

3. **Start with simple rules**: Write detections for known techniques (MITRE ATT&CK mapping is a great starting point)

4. **Test in your environment**: Convert your rules to your SIEM's query language and verify they work

5. **Share back**: Contribute your rules to the community repository

6. **Tune and iterate**: Monitor rule performance, adjust filters, and refine logic based on real alerts

### Additional Resources

- [Official Sigma Documentation](https://sigmahq.io/)
- [SigmaHQ GitHub Repository](https://github.com/SigmaHQ/sigma)
- [Uncoder.io - Online Sigma Converter](https://uncoder.io/)
- [Sigma Rules Specification](https://sigmahq.io/docs/basics/sigma-specification.html)

---

*This guide was created to help you understand and implement Sigma for
