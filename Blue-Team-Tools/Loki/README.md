In the world of threat hunting and incident response, having lightweight, open‑source tools at your disposal can make all the difference. Many commercial security solutions are excellent, but they don't always catch everything – especially when facing novel or highly targeted threats. This is where **LOKI** comes in. Created by Florian Roth, LOKI is a free IOC scanner that helps analysts quickly identify signs of compromise on endpoints using multiple detection methods, including Yara rules, file names, hashes, and C2 back‑connect checks. In this article, we'll walk through what LOKI is, how it works, and how to start using it effectively.

<img width="701" height="364" alt="image" src="https://github.com/user-attachments/assets/b140114d-6ad3-47fe-9730-49ba8d6b5607" />


---

## LOKI – A Free IOC Scanner

**LOKI** is an open‑source **Indicator of Compromise (IOC)** scanner, written by Florian Roth. It is completely free to use. According to its GitHub repository, LOKI detects threats using four primary techniques:

1. File name IOC matching  
2. Yara rule evaluation (the focus of this section)  
3. Hash comparison  
4. C2 back‑connect detection  

LOKI also supports additional checks. For a complete list of features, consult the GitHub readme: https://github.com/Neo23x0/Loki/blob/master/README.md

The tool works on both Windows and Linux platforms. You can download the latest release here: https://github.com/Neo23x0/Loki/releases

```bash
python loki.py -h
```

<img width="732" height="519" alt="1" src="https://github.com/user-attachments/assets/30580542-bb3f-41b0-977b-fb787d189ea9" />


### Putting LOKI to Work

As a security analyst, you will frequently read threat intelligence reports, blog posts, and other sources to stay informed about the latest attacker methods – both current and historic. These readings often share IOCs (hashes, IP addresses, domain names, etc.) and Yara rules, enabling you to build detections for your own environment. On the other hand, you may encounter an unknown threat that your existing security tools missed. In those situations, tools like LOKI allow you to create and add your own rules based on the intelligence you have gathered or findings from incident response work.

As noted earlier, LOKI comes with a pre‑packed set of Yara rules. You can use them right away to scan endpoints for malicious indicators.

If you are running LOKI on your own machine, the first command you should run is `--update`. This downloads the `signature-base` directory, which LOKI relies on to recognize known threats.

```bash
cd signature-base/
ls
```

<img width="1335" height="136" alt="2" src="https://github.com/user-attachments/assets/e0b413dd-95c6-4673-ba3c-a143ac4ba089" />


Now change into the `yara` subdirectory. Take a moment to look through the various Yara files that LOKI uses – this will help you understand what each rule is designed to detect.

To start a scan with LOKI, use the command below:

```bash
python ../../tools/Loki/loki.py -p .
```

<img width="1238" height="554" alt="3" src="https://github.com/user-attachments/assets/02a95c92-f4e0-4d98-9328-c191fb576e34" />

---

## Conclusion

LOKI is a simple yet powerful addition to any security analyst's toolkit. Its ability to detect IOCs through multiple methods – especially Yara rules – makes it invaluable for quick endpoint scans during incident response or proactive threat hunting. Whether you are updating its signature base or writing your own custom rules based on fresh threat intelligence, LOKI gives you flexibility and control without the need for a commercial license. By incorporating LOKI into your regular workflow, you can catch what other tools might miss and respond to compromises faster. As with any open‑source tool, remember to keep it updated and combine it with other detection mechanisms for the best results.

