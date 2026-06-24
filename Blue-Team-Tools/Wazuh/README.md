
# Wazuh

<img width="399" height="399" alt="dab38a8c03e9e6ba3232ded19228f037" src="https://github.com/user-attachments/assets/329fe8d5-d745-4db9-b04e-323f604862d1" />


## Introduction

In today's digital landscape, protecting your systems and data has become more critical than ever. With cyber threats evolving daily, organizations need robust security solutions that can monitor, detect, and respond to potential breaches effectively. This is where Wazuh comes in – a powerful, open-source security platform that helps you keep your infrastructure safe.

Whether you're a small business owner, an IT administrator, or a security enthusiast, this guide will walk you through everything you need to know about Wazuh. We'll explore what makes it special, how to set it up, and how to use its various features to protect your systems. By the end, you'll have a solid understanding of how to leverage Wazuh for your security needs.

---

## What is Wazuh?

Let's start with the basics. Wazuh started as an endpoint detection and response (EDR) tool, but it has grown into something much more comprehensive. Today, it's a unified security platform that brings together several essential security functions:

- **Endpoint Detection and Response (EDR)** – Monitors and responds to threats on individual devices
- **Security Information and Event Management (SIEM)** – Collects and analyzes security events from across your network
- **Vulnerability Management** – Scans systems for known security weaknesses
- **Cloud Security Monitoring** – Protects cloud-based infrastructure

Founded in 2015, Wazuh has become trusted by organizations of all sizes – from small startups to large enterprises and government agencies. One of its greatest strengths is that it's open-source, meaning you can use it for free while having full access to its source code.

### How Wazuh Works

Wazuh operates on a simple but effective model: the manager and agent architecture. Think of it like a security command center:

- **Wazuh Server (Manager)** – This is the brain of the operation. It stores all the security data, processes it, and provides you with a dashboard to view everything.
- **Wazuh Agents** – These are the eyes and ears. They're installed on the devices you want to monitor (servers, workstations, etc.) and send security data back to the manager.

<img width="1620" height="687" alt="image" src="https://github.com/user-attachments/assets/a73c7836-40f3-4a07-a681-d0a2443fbe49" />

*Diagram showing four agents sending logs to the Wazuh server*

---

## Setting Up Your Wazuh Server

Getting started with Wazuh begins with deploying the management server. This is the central hub where all your security data will be collected and analyzed.

<img width="1480" height="656" alt="image" src="https://github.com/user-attachments/assets/9a41011a-037b-4d7f-b30f-8f1f2d269584" />

*Wazuh server installation interface*

The installation process is straightforward, and Wazuh provides comprehensive documentation to guide you through it. Once your server is up and running, you'll have access to the web-based dashboard where you can monitor all your agents and security events.

---

## Installing Wazuh Agents

Agents are the workhorses of the Wazuh ecosystem. These are software components installed on the devices you want to monitor – they record system events, user activities, and security-related processes.

### What Agents Track

Agents monitor various activities on your devices, including:
- User authentication attempts (both successful and failed)
- System processes and applications
- File system changes
- Network connections
- System logs

### Deploying Agents Made Easy

Wazuh provides an intuitive wizard to help you deploy agents. You'll need to provide:

1. **Operating System** – Choose the OS of the device you're monitoring
2. **Server Address** – The IP address or domain name of your Wazuh server
3. **Agent Group** – Optional: organize agents into groups for better management

To access the agent deployment wizard, navigate to: **Wazuh → Agents → Deploy New Agent**

<img width="1120" height="821" alt="image" src="https://github.com/user-attachments/assets/d09527d0-b79a-44a6-979b-801458ce4fc7" />

*Agent deployment wizard interface showing the setup steps*

The wizard will generate a custom installation command for your specific system. Simply copy and paste this command into the terminal of the device you want to monitor – it's that simple!

### Installing on Different Systems

#### Windows Installation
<img width="965" height="754" alt="image" src="https://github.com/user-attachments/assets/671241ea-bf38-4178-a12b-fb72b0cebe77" />
*Windows agent installation process*

#### Debian/Ubuntu Installation
<img width="969" height="831" alt="image" src="https://github.com/user-attachments/assets/45f2f4dc-892a-4a73-b4e9-7fcbd6351a65" />
*Linux agent installation process*

---

## Vulnerability Assessment

One of Wazuh's most powerful features is its vulnerability assessment module. Think of this as a health check for your systems – it regularly scans your agents to identify software that has known security vulnerabilities.

### How It Works

1. The agent scans its operating system for installed applications
2. It collects version information for each application
3. This data is sent to the Wazuh server
4. The server compares the information against a database of Common Vulnerabilities and Exposures (CVEs)

For example, if your system has an older version of Vim that has a known security issue (like CVE-2019-12735), Wazuh will flag this for you.

<img width="2022" height="1114" alt="image" src="https://github.com/user-attachments/assets/de4ed375-5f68-48fa-a8db-9afa0cfe07db" />

### Configuration

The vulnerability scanner runs its first full scan immediately after agent installation. After that, you can configure it to run at regular intervals – by default, it's set to check every 5 minutes.

<img width="356" height="107" alt="image" src="https://github.com/user-attachments/assets/f1d7dfe4-f961-45e2-851b-09bd6dd14907" />
*Configuration file showing vulnerability scanner settings*

You can adjust these settings in the Wazuh configuration file:
```
/var/ossec/etc/ossec.conf
```

### Understanding Security Events

By default, Wazuh is quite sensitive when it comes to detecting security events. While this thoroughness helps catch potential threats, it can also generate many alerts. For instance, a Linux system might report hundreds of events during normal daily operations – like file deletions or system maintenance tasks.

<img width="652" height="275" alt="image" src="https://github.com/user-attachments/assets/6e960167-9667-49d5-bf1c-1e3eb39570cc" />
*Example showing 769 events from routine system maintenance*

These events are categorized using Wazuh's rule sets, which determine their severity. You can click on individual events to examine them in detail, sorting them by timestamp, type, or description.

<img width="1480" height="686" alt="image" src="https://github.com/user-attachments/assets/e8e00434-8b2d-453d-a5ad-de8fa624f14b" />
*Event analysis interface showing detailed view of security events*

---

## Policy Auditing and Compliance

For organizations that need to meet regulatory requirements, Wazuh's policy auditing capabilities are invaluable. The platform can check your systems against various security frameworks and standards.

### Supported Frameworks

Wazuh provides compliance reporting for:
- **PCI DSS** – Payment Card Industry Data Security Standard
- **HIPAA** – Health Insurance Portability and Accountability Act
- **NIST** – National Institute of Standards and Technology
- **MITRE ATT&CK** – A comprehensive knowledge base of adversary tactics
- **GDPR** – General Data Protection Regulation

### Viewing Compliance Scores

When you install the Wazuh agent, it performs an initial audit and generates compliance metrics. These scores help you understand how well your systems align with security standards.

<img width="1920" height="899" alt="image" src="https://github.com/user-attachments/assets/d1894280-8d54-4475-a153-34c75a3dd40f" />
*Compliance scores showing how a domain controller performs against various frameworks*

The dashboard provides visual representations of your compliance status, making it easy to identify areas that need attention.

<img width="1920" height="937" alt="image" src="https://github.com/user-attachments/assets/baf1e1a8-6dd3-4648-a264-4661f05ea1f9" />
*Detailed compliance benchmark view*


To access the policy management module:
1. Click on **Wazuh → Modules**
2. Select **Policy Management**

<img width="1043" height="455" alt="image" src="https://github.com/user-attachments/assets/5299b261-3643-4229-a4f8-01f3aa376709" />
*Policy Management module interface*

---

## Monitoring User Logins

Authentication monitoring is crucial for detecting unauthorized access attempts. Wazuh actively records both successful and unsuccessful login attempts across your systems.

### Detecting Failed Logins

Wazuh uses rule ID 5710 to detect failed SSH authentication attempts. Here's a practical example:

<img width="1850" height="512" alt="image" src="https://github.com/user-attachments/assets/8759b2e2-9c94-4566-957a-710dcac84f17" />

*Animated GIF showing failed SSH login attempt being detected*

In this scenario, someone tried to log in to the agent "ip-10-10-73-118" using the username "cmnatic" – a user that doesn't exist. Wazuh generated an alert for this suspicious activity.

| Alert Component | Details |
|---|---|
| Agent | ip-10-10-73-118 |
| Username | cmnatic |
| Status | FAILED |
| Action | Authentication Attempt |

<img width="983" height="448" alt="image" src="https://github.com/user-attachments/assets/f5457689-a033-44c1-ab8e-c31233eb5758" />

*Alert summary table showing failed login attempt details*

### Where Alerts Are Stored

All alerts are stored in a specific file on the Wazuh server:
```
/var/ossec/logs/alerts/alerts.log
```

You can manually search through this file using commands like:
```bash
sudo less /var/ossec/logs/alerts/alerts.log
```

<img width="985" height="160" alt="image" src="https://github.com/user-attachments/assets/7a65f9f7-1b2a-415f-8022-3de09b3ef51a" />

*Command showing how to view alert logs*

### Managing Alert Severity

Wazuh assigns different severity levels to alerts based on their nature:

- **Successful logins** – Lower severity (normal activity)
- **Failed logins** – Higher severity (potential brute force attempts)

You can customize these settings to match your environment. For example, if a rarely-used account suddenly shows activity, you might configure Wazuh to assign a higher severity to that event.

<img width="1850" height="512" alt="image" src="https://github.com/user-attachments/assets/e4eb29ea-b700-4eca-93bf-e00bb5cfa814" />
*Animated GIF showing successful Windows login detection*

### Tracking Login Patterns

Wazuh can help you analyze login patterns. For instance, you might discover that out of 285 total login events, only 79 are related to a specific user account.

<img width="1659" height="619" alt="image" src="https://github.com/user-attachments/assets/8827a429-f02b-4bd4-8a63-a4f81d309caf" />
*Statistical view showing filtered login events*

To manage rules:
1. Navigate to **Wazuh → Management**
2. Select **Rules**

<img width="496" height="303" alt="image" src="https://github.com/user-attachments/assets/6b73838f-190f-432f-914a-14fb7a068935" />
*Rules management interface*

---

## Collecting Windows Logs

Windows systems generate extensive logs covering everything from authentication attempts to application behavior. Wazuh leverages Microsoft's Sysmon tool to collect this rich telemetry.

### Understanding Sysmon

Sysmon (System Monitor) is a Windows service that provides detailed information about system activities. It records:
- Authentication attempts
- Network connections
- File access events
- Application and service behavior
- Process creation

### Configuring Sysmon

Sysmon uses XML-based rules to determine what events to capture. Here's an example rule that monitors for PowerShell execution:

<img width="831" height="571" alt="image" src="https://github.com/user-attachments/assets/a8dcf54d-6199-4457-9ff2-e7a9612e9cf6" />
*XML configuration snippet for monitoring PowerShell*

To apply these rules:
1. Install Sysmon on your Windows system
2. Provide the configuration file:
```bash
Sysmon64.exe -accepteula -i detect_powershell.xml
```
<img width="725" height="240" alt="image" src="https://github.com/user-attachments/assets/06356edc-0a77-40c0-be8a-76ebb6d41947" />

<img width="974" height="508" alt="image" src="https://github.com/user-attachments/assets/243e7789-15f0-430d-93ae-d0bf106878ce" />

*Sysmon installation process*

<img width="732" height="352" alt="image" src="https://github.com/user-attachments/assets/321234c8-c4a3-4175-97dd-24f323a492e0" />

### Verifying Configuration

After installation, you can verify that Sysmon is working by checking the Windows Event Viewer:

<img width="374" height="333" alt="image" src="https://github.com/user-attachments/assets/f6604a41-c0f4-4ecd-a86f-4bc4d5a657e9" />

<img width="1530" height="1032" alt="image" src="https://github.com/user-attachments/assets/b12a08e8-33ec-4565-82a6-5394e9eb7cdb" />

*Event Viewer showing Sysmon module*

When you launch PowerShell on the Windows server, you'll see events being recorded:

<img width="1534" height="1036" alt="image" src="https://github.com/user-attachments/assets/d1668477-ab0f-4b5b-9285-0138a66e7725" />
*Event Viewer showing PowerShell activity records*

### Configuring the Wazuh Agent for Windows

To send Sysmon events to your Wazuh server, you need to modify the agent's configuration file:
```
C:\Program Files (x86)\ossec-agent\ossec.conf
```

Add the following configuration:

<img width="682" height="717" alt="image" src="https://github.com/user-attachments/assets/5319285e-9aec-43ad-80f6-e287af56e27a" />

*Configuration snippet for Windows event collection*

Your configuration file should look like this:

<img width="541" height="748" alt="image" src="https://github.com/user-attachments/assets/432832a2-ced1-443d-b88b-a38595bd3fbd" />
*Complete ossec.conf showing Windows event configuration*

After making these changes, restart the Wazuh agent. For safety, you might want to restart the entire operating system to ensure all changes take effect.

<img width="1836" height="1051" alt="image" src="https://github.com/user-attachments/assets/5f235872-b435-47bf-be5a-8fc02568aabb" />
*Restart confirmation screen*

### Adding Rules on the Management Server

Finally, tell the Wazuh server how to process these new Windows events by adding a custom rule:

<img width="831" height="151" alt="image" src="https://github.com/user-attachments/assets/551b78ab-03b8-425a-a6e6-3a3f01af0044" />

*Local rules configuration for Windows events*

Add your rule to:
```
/var/ossec/etc/rules/local_rules.xml
```

Then restart the Wazuh management server. You should now see Windows event data appearing in your dashboard.

---

## Collecting Linux Logs

Monitoring Linux systems with Wazuh is straightforward, similar to the Windows process. We'll use Wazuh's log collector service to gather logs from various applications.

### Monitoring Apache Web Server

Let's walk through monitoring an Apache2 web server as an example. Wazuh comes with approximately 900 pre-configured rules for common applications, including:

- Docker
- FTP servers
- WordPress
- SQL Server
- MongoDB
- Firewalld
- Apache
- And many more!

### Configuring Log Collection

First, locate the rules for your application. Apache2 rules are in:
```
/var/ossec/ruleset/rules/0250-apache_rules.xml
```

This ruleset can analyze Apache logs for warnings, errors, and security events.

To enable monitoring, modify the agent's configuration:
```
/var/ossec/etc/ossec.conf
```

<img width="831" height="151" alt="image" src="https://github.com/user-attachments/assets/08c85315-4bbf-48bf-8a03-8febf2a15955" />

*Configuration showing Apache log collection setup*

After adding the configuration, restart the Wazuh agent on your Linux system. The logs will now flow to your management server.

---

## Auditing Linux Commands

One of the most valuable security features is monitoring what commands users execute on your Linux systems. Wazuh uses `auditd` – the Linux Auditing System – to track command execution.

### Installing auditd

First, install the auditing tools:
```bash
sudo apt-get install auditd audispd-plugins
```

Enable the service to start automatically and run it now:
```bash
sudo systemctl enable auditd.service
sudo systemctl start auditd.service
```

### Creating Audit Rules

You define what to monitor by adding rules to auditd. For example, to monitor all commands executed as root:

```bash
sudo nano /etc/audit/rules.d/audit.rules
```

Add this rule:
```
-a exit,always -F arch=64 -F euid=0 -S execve -k audit-wazuh-c
```

<img width="831" height="248" alt="image" src="https://github.com/user-attachments/assets/be622619-4405-46de-ba2d-b136db588500" />

*Rule configuration showing auditd settings*

This rule tracks all commands (execve) run by the root user (euid=0) and tags them with "audit-wazuh-c".

### Applying Rules

After editing the rules file, apply the changes:
```bash
sudo auditctl -R /etc/audit/rules.d/audit.rules
```

### Configuring Wazuh to Read auditd Logs

Now configure the Wazuh agent to send auditd logs to the management server:

```bash
sudo nano /var/ossec/etc/ossec.conf
```
<img width="817" height="88" alt="image" src="https://github.com/user-attachments/assets/6c8e20f2-7908-4cf5-830b-626d9e55b66a" />

<img width="1920" height="920" alt="image" src="https://github.com/user-attachments/assets/e7243669-6a14-4cec-ab2e-5ed6793dea4c" />

*Wazuh configuration for auditd log collection*

Add the auditd log location to the configuration file.

---

## Using the Wazuh API

Wazuh provides a powerful RESTful API that allows you to interact with your security platform programmatically. This is useful for automation, integration with other tools, and custom scripting.

### API Authentication

The API requires authentication. First, obtain an authentication token:

```bash
TOKEN=$(curl -u : -k -X GET "https://WAZUH_SERVER_IP:55000/security/user/authenticate?raw=true")
```

### Making API Calls

Once authenticated, include the token in your requests:

```bash
curl -k -X GET "https://SERVER_IP:55000/" -H "Authorization: Bearer $TOKEN"
```

<img width="831" height="215" alt="image" src="https://github.com/user-attachments/assets/445fbf01-c194-417d-ad81-1fd56e272c85" />

*API authentication response*

### Common API Operations

**Check server status:**
```bash
curl -k -X GET "https://SERVER_IP:55000/manager/status?pretty=true" -H "Authorization: Bearer $TOKEN"
```

**View server configuration:**
```bash
curl -k -X GET "https://SERVER_IP:55000/manager/configuration?pretty=true&section=global" -H "Authorization: Bearer $TOKEN"
```

<img width="831" height="506" alt="image" src="https://github.com/user-attachments/assets/f654e114-ff98-46f3-bf62-b947735e9285" />

*Server configuration API response*

**Query agent information:**
```bash
curl -k -X GET "https://SERVER_IP:55000/agents?pretty=true&offset=1&limit=2&select=status%2Cid%2Cmanager%2Cname%2Cnode_name%2Cversion&status=active" -H "Authorization: Bearer $TOKEN"
```

<img width="831" height="328" alt="image" src="https://github.com/user-attachments/assets/17b10b99-e12b-42e6-9b83-4d525fa09b12" />

*Agent information API response*

### Using the Built-in API Console

Wazuh also provides a web-based API console for convenience:

1. Click on **Wazuh → Tools**
2. Select the **API Console** option

<img width="492" height="461" alt="image" src="https://github.com/user-attachments/assets/56b9e983-ef57-49a8-8a8d-77550ca42eb3" />
*API Console location in the interface*

The console comes with sample queries you can run immediately. Just select a query and click the green run button.

<img width="1278" height="335" alt="image" src="https://github.com/user-attachments/assets/5447519a-b5de-435a-8d83-7293713f4db7" />
*API Console showing sample queries*

For complete API documentation, visit:
[Wazuh API Reference](https://documentation.wazuh.com/current/user-manual/api/reference.html)

---

## Generating Reports

Wazuh makes it easy to create professional security reports that you can share with stakeholders, auditors, or your security team.

### Creating a Report

1. Navigate to **Modules → Security Events**
2. Apply your desired filters (e.g., last 24 hours)

<img width="999" height="487" alt="image" src="https://github.com/user-attachments/assets/4d865f8e-0baa-4bdd-ba67-47072aae6c6b" />
*Security Events view for report generation*

3. If data is available, you'll see a report generation button

<img width="1916" height="256" alt="image" src="https://github.com/user-attachments/assets/6e2b7390-d7ea-4d4b-891d-7efedda30852" />
*Report generation interface*

> **Note:** If the button appears greyed out, it means no data matches your current query. Try adjusting the date range or filters.

### Accessing Generated Reports

After generating a report, access it through the report dashboard:

1. Click **Wazuh → Management**
2. Select **Reporting** under "Status and Reports"

<img width="692" height="463" alt="image" src="https://github.com/user-attachments/assets/1f59894e-2436-43f7-b016-b1ac6204531b" />
*Report dashboard showing all generated reports*

### Downloading Reports

Your reports are saved as PDF files. Simply click the save icon under the "Actions" column to download a report.

<img width="746" height="812" alt="image" src="https://github.com/user-attachments/assets/b6f4ef59-82b4-4837-b912-ba9f6b16089f" />
*Sample security events report in PDF format*

---

## Working with Sample Data

To help you explore Wazuh's capabilities, the platform comes with sample data that you can load at any time. This is especially useful when you're first learning the system.

### Loading Sample Data

1. Click **Wazuh → Settings**
2. Select **Sample Data**
3. Click **Add Data** on the cards to import sample datasets

<img width="543" height="462" alt="image" src="https://github.com/user-attachments/assets/89f12cb9-0782-47a1-a055-a5b910ef914e" />
*Sample Data import interface*

The import process may take up to a minute per dataset. You'll know the import is complete when the button changes to "Remove Data."

<img width="1915" height="768" alt="image" src="https://github.com/user-attachments/assets/b6735af0-3075-4a4d-833d-3e2716e5ba10" />
*Successful data import confirmation*

### Viewing Imported Data

After loading sample data, return to the Wazuh dashboard. You'll notice significantly more data in various modules – the Security Events module will be especially rich with information.

**Important:** You may need to adjust the date range to see the sample data. Set the range to "Last 7 days" or longer and refresh the dashboard.

<img width="654" height="565" alt="image" src="https://github.com/user-attachments/assets/dfb3f11c-2f3e-4353-bb09-ff3efdbfce81" />

*Dashboard showing imported sample data*

---

## Conclusion

Wazuh is more than just a security tool – it's a comprehensive platform that brings together multiple essential security functions under one roof. From vulnerability assessment and compliance monitoring to real-time event analysis and automated reporting, Wazuh provides the capabilities you need to protect your digital infrastructure.

### Key Takeaways

- **Unified Security Platform** – Wazuh combines EDR, SIEM, vulnerability management, and cloud security in one solution
- **Open Source** – Free to use with full access to the source code
- **Flexible Architecture** – Works across Windows, Linux, and macOS systems
- **Comprehensive Monitoring** – Tracks everything from user logins to command execution
- **Compliance Ready** – Built-in support for major security frameworks
- **API Access** – Programmatic control for automation and integration
- **Professional Reporting** – Generate shareable PDF reports for stakeholders

### Getting Started

Ready to deploy Wazuh in your environment? Here's your action plan:

1. **Deploy the Wazuh Server** – Start with the management server installation
2. **Install Agents** – Deploy agents to the systems you want to monitor
3. **Configure Log Collection** – Set up specific log sources (Windows events, Apache logs, etc.)
4. **Fine-tune Rules** – Adjust sensitivity and create custom rules for your needs
5. **Explore the Dashboard** – Familiarize yourself with the interface and available data
6. **Set Up Alerts** – Configure notifications for critical security events
7. **Generate Reports** – Create regular security reports for your team

### Additional Resources

- [Official Wazuh Documentation](https://documentation.wazuh.com/)
- [Wazuh GitHub Repository](https://github.com/wazuh/wazuh)
- [Community Forums and Support](https://wazuh.com/community/)

Remember, security is an ongoing process, not a one-time setup. Regularly review your Wazuh configuration, update your rules, and stay informed about new threats and security best practices. With Wazuh as your security platform, you're taking a significant step toward protecting your digital assets.

---

*This guide was created to help you understand and implement Wazuh for your security monitoring needs. For the most current information, always refer to the official Wazuh documentation.*
