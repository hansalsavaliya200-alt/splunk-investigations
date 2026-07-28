<div align="center">

# 🛡️ Splunk SOC Investigation Portfolio

### Enterprise Blue Team Investigations · Threat Hunting · Incident Response

![Splunk](https://img.shields.io/badge/SIEM-Splunk-00A651?style=for-the-badge&logo=splunk&logoColor=white)
![MITRE](https://img.shields.io/badge/MITRE-ATT%26CK-D93636?style=for-the-badge&logo=mitre&logoColor=white)
![Blue Team](https://img.shields.io/badge/Team-Blue%20Team-1E63C8?style=for-the-badge&logo=windowsdefender&logoColor=white)
![Status](https://img.shields.io/badge/Investigations-33%2F40-success?style=for-the-badge)

![Progress](https://img.shields.io/badge/Portfolio%20Completion-80%25-yellow?style=flat-square)
![Windows](https://img.shields.io/badge/Focus-Windows%20Security-0078D6?style=flat-square&logo=windows&logoColor=white)
![Active Directory](https://img.shields.io/badge/Focus-Active%20Directory-00A4EF?style=flat-square&logo=microsoft&logoColor=white)
![Azure](https://img.shields.io/badge/Focus-Azure%20Cloud-0089D6?style=flat-square&logo=microsoftazure&logoColor=white)
![License](https://img.shields.io/badge/Docs-Educational%20Use-lightgrey?style=flat-square)

*A hands-on, evidence-based SOC portfolio — 32 realistic enterprise attack investigations, fully documented from log to lesson learned.*

</div>

---

## 📌 Overview

Welcome to my **Splunk SOC Investigation Portfolio** — a growing collection of enterprise-grade Security Operations Center (SOC) investigations built using **Splunk Enterprise**. This repository demonstrates practical Blue Team skills through realistic attack simulations, threat hunting, Active Directory security investigations, malware analysis, cloud security incidents, email security investigations, and enterprise incident response.

Every investigation follows the same rigor an interviewer — or a real SOC — expects:

<table>
<tr>
<td align="center">🔎<br><b>Splunk Search<br>Queries</b></td>
<td align="center">🪵<br><b>Windows Event<br>Log Analysis</b></td>
<td align="center">🚩<br><b>IOC<br>Identification</b></td>
<td align="center">🗺️<br><b>MITRE ATT&CK<br>Mapping</b></td>
<td align="center">⏱️<br><b>Attack Timeline<br>Reconstruction</b></td>
<td align="center">📝<br><b>Incident<br>Response</b></td>
<td align="center">📄<br><b>Professional<br>Reporting</b></td>
</tr>
</table>

---

## 📖 Table of Contents

- [🎯 Skills Demonstrated](#-skills-demonstrated)
- [🌟 Featured Investigation](#-featured-investigation)
- [📂 Investigation Portfolio](#-investigation-portfolio)
- [📊 Portfolio Progress](#-portfolio-progress)
- [🛠️ Technologies & Tools](#️-technologies--tools)
- [📈 Investigation Workflow](#-investigation-workflow)
- [🚨 Attack Scenarios Covered](#-attack-scenarios-covered)
- [🎯 MITRE ATT&CK Techniques Practiced](#-mitre-attck-techniques-practiced)
- [📊 Portfolio Statistics](#-portfolio-statistics)
- [🎯 Current Learning](#-current-learning)
- [👨‍💻 About Me](#-about-me)
- [⭐ Repository Goal & Roadmap](#-repository-goal)

---

## 🎯 Skills Demonstrated

<table>
<tr><th width="25%">Category</th><th>Skills</th></tr>
<tr>
<td><b>🔍 SIEM & Threat Hunting</b></td>
<td>

`Splunk Enterprise` `Threat Hunting` `Detection Engineering` `IOC Identification` `Incident Investigation` `EDR Investigation`

</td>
</tr>
<tr>
<td><b>🪟 Windows Security</b></td>
<td>

`Windows Event Logs` `PowerShell Investigation` `Registry Persistence` `Scheduled Tasks` `Windows Services` `Anti-Forensics` `Event Log Analysis` `LOLBins Analysis` `Windows Defender Tampering Detection`

</td>
</tr>
<tr>
<td><b>🏢 Active Directory</b></td>
<td>

`Domain Enumeration` `DC Discovery` `Domain Admin Enumeration` `LSASS Dumping` `DCSync Detection` `Kerberoasting Detection` `SPN Enumeration` `PsExec Detection` `SMB Investigation` `Trust Relationship Abuse` `Pass-the-Ticket Detection` `AD CS (Certificate Services) Abuse Detection`

</td>
</tr>
<tr>
<td><b>☁️ Cloud & Email Security</b></td>
<td>

`Azure CLI Investigation` `Azure Storage Investigation` `Azure Active Directory` `Cloud Persistence` `Cloud Data Exfiltration` `Rclone Investigation` `Microsoft 365 / Exchange Online Investigation` `Business Email Compromise (BEC) Detection` `OAuth Abuse Detection`

</td>
</tr>
<tr>
<td><b>🧠 Threat Analysis</b></td>
<td>

`Malware Investigation` `Phishing Investigation` `Enterprise Ransomware` `Credential Theft` `Kerberos Ticket Theft` `C2 Detection` `Lateral Movement` `Data Exfiltration` `Evidence-Based Analyst Reporting`

</td>
</tr>
</table>

<div align="right"><a href="#-splunk-soc-investigation-portfolio">↑ back to top</a></div>

---

## 🌟 Featured Investigation

> ### 🕵️ Investigation 29 — Active Directory Trust Abuse
> **Trust Enumeration → Kerberos Ticket Theft (Rubeus/Mimikatz) → Pass-the-Ticket → Lateral Move to Trusted DC → Collection → Exfil → Log Wipe**
>
> | | |
> |---|---|
> | **Events Triaged** | 99 (20 High/Critical — the densest kill chain in the portfolio so far) |
> | **Primary Actor** | Administrator (via forged/passed Kerberos ticket) |
> | **Key Techniques** | T1482 · T1558 · T1003 · T1550.003 · T1021.002 · T1560 · T1567 · T1070.001 |
> | **Analyst Highlight** | Resisted treating "administrator has the most events" as proof the native account was compromised from the start — the evidence shows Administrator-level access was *obtained* via a passed ticket mid-chain, not phished at the outset. |
>
> A full cross-domain trust abuse scenario — trust key theft, forged inter-realm tickets, and lateral movement onto a trusted Domain Controller — reconstructed entirely from process-creation telemetry. The most Kerberos-heavy investigation in the portfolio to date.

**🆕 Recently added:** Investigations 31–33 expand the portfolio into **Active Directory Certificate Services abuse**, **Windows Defender tampering / defense evasion**, and **Business Email Compromise** — pushing the portfolio beyond endpoint and AD telemetry into identity, email, and defense-evasion tradecraft.

<div align="right"><a href="#-splunk-soc-investigation-portfolio">↑ back to top</a></div>

---

## 📂 Investigation Portfolio

<details open>
<summary><b>🟢 Foundational (⭐–⭐⭐) — Logon, Brute Force & Execution Basics</b></summary>
<br>

| # | Scenario | Difficulty | Status |
|:-:|---|:-:|:-:|
| 1 | Failed Login Investigation | ⭐ | ✅ |
| 2 | Brute Force Attack Detection | ⭐ | ✅ |
| 3 | Privilege Escalation Investigation | ⭐⭐ | ✅ |
| 4 | PowerShell Abuse Investigation | ⭐⭐ | ✅ |
| 5 | Malware Execution Investigation | ⭐⭐ | ✅ |

</details>

<details open>
<summary><b>🟡 Intermediate (⭐⭐⭐–⭐⭐⭐⭐) — Insider Threats & Lateral Movement</b></summary>
<br>

| # | Scenario | Difficulty | Status |
|:-:|---|:-:|:-:|
| 6 | Windows Event Log Investigation | ⭐⭐⭐ | ✅ |
| 7 | Insider Threat Investigation | ⭐⭐⭐ | ✅ |
| 8 | Credential Access Investigation | ⭐⭐⭐ | ✅ |
| 9 | Lateral Movement Investigation | ⭐⭐⭐⭐ | ✅ |
| 10 | Ransomware Attack Investigation | ⭐⭐⭐⭐ | ✅ |

</details>

<details open>
<summary><b>🟠 Advanced (⭐⭐⭐⭐⭐) — Domain Compromise, C2 & Cloud/Phishing Exfil</b></summary>
<br>

| # | Scenario | Difficulty | Status |
|:-:|---|:-:|:-:|
| 11 | Domain Controller Compromise | ⭐⭐⭐⭐⭐ | ✅ |
| 12 | Command & Control (C2) & Data Exfiltration | ⭐⭐⭐⭐⭐ | ✅ |
| 13 | Cloud Account Compromise & Cloud Data Exfiltration | ⭐⭐⭐⭐⭐ | ✅ |
| 14 | Phishing Email Malware Delivery & HR Data Exfiltration | ⭐⭐⭐⭐⭐ | ✅ |

</details>

<details open>
<summary><b>🔴 Expert (⭐⭐⭐⭐⭐⭐–⭐⭐⭐⭐⭐⭐⭐⭐) — Enterprise Ransomware, AD Attacks & Ticket Abuse</b></summary>
<br>

| # | Scenario | Difficulty | Status |
|:-:|---|:-:|:-:|
| 15 | Credential Dumping, PsExec Lateral Movement & Finance Data Exfiltration | ⭐⭐⭐⭐⭐⭐ | ✅ |
| 16 | Enterprise Ransomware Outbreak, SMB Propagation & Multi-Host Encryption | ⭐⭐⭐⭐⭐⭐ | ✅ |
| 17 | Active Directory DCSync Attack & Password Hash Exfiltration | ⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| 18 | Active Directory Kerberoasting Attack & Service Account Credential Theft | ⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| 19 | Golden Ticket Attack Investigation | ⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| 20 | Silver Ticket Attack Investigation | ⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |

</details>

<details open>
<summary><b>⚫ Master Tier (⭐⭐⭐⭐⭐⭐⭐⭐⭐) — Ticket Attacks, Threat Hunting, Cloud, EDR & Trust Abuse</b></summary>
<br>

| # | Scenario | Difficulty | Status |
|:-:|---|:-:|:-:|
| 21 | Pass-the-Hash Attack Investigation | ⭐⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| 22 | Pass-the-Ticket Attack Investigation | ⭐⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| 23 | Enterprise Threat Hunting Challenge | ⭐⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| 24 | Active Directory Persistence Investigation | ⭐⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| 25 | Ransomware Recovery Investigation | ⭐⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| 26 | EDR Lateral Movement Investigation | ⭐⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| 27 | Enterprise Cloud Exfiltration Threat Hunt | ⭐⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| 28 | EDR Multi-Stage Intrusion Detection | ⭐⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| 29 | 🌟 Active Directory Trust Abuse (Trust Enum → Rubeus/Mimikatz → Pass-the-Ticket → Trusted DC Compromise) | ⭐⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |

</details>

<details open>
<summary><b>🟣 Cloud, Identity & Email Security (30–33) — Certificate Abuse, Defense Evasion & BEC</b></summary>
<br>

| # | Scenario | Difficulty | Status |
|:-:|---|:-:|:-:|
| 30 | Domain Controller Persistence | ⭐⭐⭐⭐⭐⭐⭐⭐⭐ | 🔹 Planned |
| **31** | **Active Directory Certificate Services (AD CS) Abuse** — Enumeration → Vulnerable Template Discovery → Certificate Request/Issuance → Kerberos Auth (Rubeus) → Privilege Escalation → Exfil → Log Wipe | ⭐⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| **32** | **Windows Defender Tampering & Defense Evasion** — Protection Disabled → Exclusions Added → Mimikatz Credential Dumping → Lateral Movement (PsExec) → HR Data Exfil → Log Clearing | ⭐⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |
| **33** | **Business Email Compromise (BEC)** — Mailbox Snooping → Inbox Rule Manipulation → OAuth Consent Abuse → Spoofed CEO Impersonation → Finance Mailbox Targeting | ⭐⭐⭐⭐⭐⭐⭐⭐ | ✅ |

</details>

<div align="right"><a href="#-splunk-soc-investigation-portfolio">↑ back to top</a></div>

---

## 📊 Portfolio Progress

```
████████████████████████████████░░░░░░  32 / 40  ·  80%
```

<div align="center">

| Milestone | Target | Status |
|:-:|:-:|:-:|
| Foundational Investigations | 1–10 | ✅ Complete |
| Advanced / Domain Compromise | 11–20 | ✅ Complete |
| Master Tier | 21–29 | ✅ Complete |
| Cloud, Identity & Email Expansion | 30–33 | 🔄 In Progress (3/4) |
| Capstone & Purple Team | 34–40 | ⬜ Planned |

</div>

🎯 **Goal:** Build a professional portfolio of 40 enterprise-grade SOC investigations covering Windows Security, Active Directory, Cloud Security, Email Security, Threat Hunting, Detection Engineering, Malware Analysis, Digital Forensics, and Incident Response.

<div align="right"><a href="#-splunk-soc-investigation-portfolio">↑ back to top</a></div>

---

## 🛠️ Technologies & Tools

<div align="center">

![Splunk](https://img.shields.io/badge/-Splunk%20Enterprise-000000?style=flat-square&logo=splunk)
![Windows](https://img.shields.io/badge/-Windows-0078D6?style=flat-square&logo=windows&logoColor=white)
![Active Directory](https://img.shields.io/badge/-Active%20Directory-00A4EF?style=flat-square&logo=microsoft&logoColor=white)
![Azure](https://img.shields.io/badge/-Microsoft%20Azure-0089D6?style=flat-square&logo=microsoftazure&logoColor=white)
![Microsoft 365](https://img.shields.io/badge/-Microsoft%20365-D83B01?style=flat-square&logo=microsoftoffice&logoColor=white)
![PowerShell](https://img.shields.io/badge/-PowerShell-5391FE?style=flat-square&logo=powershell&logoColor=white)
![MITRE ATT&CK](https://img.shields.io/badge/-MITRE%20ATT%26CK-D93636?style=flat-square)
![NIST](https://img.shields.io/badge/-NIST%20SP%20800--61-002855?style=flat-square)

</div>

| Category | Tools / Frameworks |
|---|---|
| **SIEM** | Splunk Enterprise |
| **Operating Systems** | Windows, Active Directory, Windows Event Logs |
| **Cloud & Email** | Microsoft Azure, Azure AD, Azure CLI, Azure Storage, Rclone (exfil tooling), Microsoft 365, Exchange Online, Defender for Office 365 |
| **Frameworks** | MITRE ATT&CK, Cyber Kill Chain, Incident Response Lifecycle, NIST SP 800-61 r2 |

<div align="right"><a href="#-splunk-soc-investigation-portfolio">↑ back to top</a></div>

---

## 📈 Investigation Workflow

```text
Log Collection → Event Analysis → Threat Detection → IOC Identification
      → MITRE ATT&CK Mapping → Attack Timeline Reconstruction
      → Incident Response → Lessons Learned
```

<div align="center">

| Step | Focus |
|:-:|---|
| 1️⃣ | **Log Collection** — pull raw events from Splunk indexes |
| 2️⃣ | **Event Analysis** — baseline normal vs. anomalous activity |
| 3️⃣ | **Threat Detection** — isolate high-severity, high-signal events |
| 4️⃣ | **IOC Identification** — extract hosts, users, processes, hashes |
| 5️⃣ | **MITRE ATT&CK Mapping** — tag each action to a technique |
| 6️⃣ | **Timeline Reconstruction** — sequence the full kill chain |
| 7️⃣ | **Incident Response** — recommend containment & remediation |
| 8️⃣ | **Lessons Learned** — document detection gaps & analyst takeaways |

</div>

<div align="right"><a href="#-splunk-soc-investigation-portfolio">↑ back to top</a></div>

---

## 🚨 Attack Scenarios Covered

<table>
<tr><th>🪟 Windows Security</th><th>🏢 Active Directory</th></tr>
<tr>
<td valign="top">

- Failed Logons
- Brute Force Attacks
- Privilege Escalation
- PowerShell Abuse (incl. encoded/obfuscated)
- Malware Execution
- Registry Persistence
- Scheduled Tasks
- Windows Service Creation
- Credential Dumping
- LSASS Memory Analysis
- Event Log Clearing
- Anti-Forensics
- LOLBins Abuse (MSHTA, Rundll32, WMIC, Curl, Wevtutil)
- Windows Defender Tampering (Protection Disabled, Exclusions Added, Service Stopped)

</td>
<td valign="top">

- Domain Enumeration
- Domain Controller Discovery
- Domain Admin Enumeration
- DCSync Attack Detection
- Kerberoasting Detection
- Golden / Silver Ticket Detection
- Kerberos Ticket Analysis
- SPN Enumeration
- Password Hash Theft
- KRBTGT Investigation
- SMB ADMIN$ Abuse
- PsExec Lateral Movement
- Domain Trust Enumeration & Abuse
- Pass-the-Ticket Attacks (Rubeus)
- AD CS Abuse — Vulnerable Template Discovery, Certificate Request/Issuance, Certificate-Based Kerberos Auth

</td>
</tr>
<tr><th>☁️ Cloud & Email Security</th><th>🦠 Ransomware & Malware</th></tr>
<tr>
<td valign="top">

- Azure Account Compromise
- Azure CLI Abuse
- Azure Storage Enumeration
- Cloud Persistence
- Cloud Data Exfiltration
- Rclone Investigation
- HTTPS Egress / Cloud Upload Detection
- Business Email Compromise (BEC)
- Mailbox Rule & Forwarding Abuse
- OAuth Application Consent Abuse
- CEO / Executive Spoofing Detection
- Impossible Travel Alert Triage

</td>
<td valign="top">

- Enterprise Reconnaissance
- Multi-Host Propagation
- Shadow Copy Deletion
- Backup Service Termination
- Enterprise File Encryption
- Ransom Note Deployment
- Spear Phishing / Macro Docs
- MSHTA Abuse & C2 Beaconing

</td>
</tr>
</table>

<div align="right"><a href="#-splunk-soc-investigation-portfolio">↑ back to top</a></div>

---

## 🎯 MITRE ATT&CK Techniques Practiced

<details>
<summary><b>Click to expand full technique list (50+ techniques)</b></summary>

| Technique | ATT&CK ID |
|---|---|
| Spearphishing Attachment | T1566.001 |
| Phishing | T1566 |
| Spearphishing via Service | T1566.003 |
| User Execution | T1204.002 |
| PowerShell | T1059.001 |
| MSHTA | T1218.005 |
| Rundll32 | T1218.011 |
| Ingress Tool Transfer | T1105 |
| Account Discovery | T1087 |
| Domain Account Discovery | T1087.002 |
| Domain Trust Discovery | T1482 |
| Permission Groups Discovery | T1069 |
| Network Configuration Discovery | T1016 |
| Remote System Discovery | T1018 |
| File & Directory Discovery | T1083 |
| Certificate Services Discovery | T1580 |
| OS Credential Dumping | T1003 |
| LSASS Credential Dumping | T1003.001 |
| DCSync | T1003.006 |
| Steal or Forge Kerberos Tickets | T1558 |
| Kerberoasting | T1558.003 |
| Golden Ticket | T1558.001 |
| Silver Ticket | T1558.002 |
| Steal or Forge Authentication Certificates | T1649 |
| Pass the Hash | T1550.002 |
| Pass the Ticket | T1550.003 |
| Steal Application Access Token (OAuth Abuse) | T1528 |
| Local Account Persistence | T1136.001 |
| Boot or Logon Autostart Execution | T1547 |
| Windows Service Persistence | T1543.003 |
| Scheduled Task | T1053.005 |
| Registry Modification | T1112 |
| Impair Defenses (Disable/Modify Security Tools) | T1562.001 |
| SMB / Windows Admin Shares | T1021.002 |
| Remote Services | T1021 |
| Remote Service Session | T1563.002 |
| Service Execution | T1569.002 |
| Windows Management Instrumentation | T1047 |
| Valid Accounts | T1078 |
| Account Manipulation | T1098 |
| Email Collection | T1114 |
| Data from Local System | T1005 |
| Local Data Staging | T1074.001 |
| Archive Collected Data | T1560 |
| Archive Collected Data (via Utility) | T1560.001 |
| Application Layer Protocol | T1071 |
| Exfiltration Over Alternative Protocol | T1048 |
| Exfiltration Over Web | T1041 |
| Exfiltration Over Web Services | T1567 |
| Exfiltration to Cloud Storage | T1567.002 |
| File Deletion | T1070.004 |
| Clear Windows Event Logs | T1070.001 |
| Data Encrypted for Impact | T1486 |
| Inhibit System Recovery | T1490 |
| Service Stop | T1489 |
| Defacement / Ransom Note | T1491 |

</details>

<div align="right"><a href="#-splunk-soc-investigation-portfolio">↑ back to top</a></div>

---

## 📊 Portfolio Statistics

<div align="center">

| Category | Count |
|---|:-:|
| Total Investigations | **32** |
| Windows Investigations | **22** |
| Active Directory Investigations | **10** |
| Cloud & Email Investigations | **3** |
| Phishing / BEC Investigations | **2** |
| Credential Access Investigations | **8** |
| Enterprise Ransomware Investigations | **2** |
| Threat Hunting Investigations | **2** |
| Data Exfiltration Investigations | **9** |
| MITRE ATT&CK Techniques Practiced | **50+** |
| IOC Reports | **32** |
| Incident Reports | **32** |

</div>

<div align="right"><a href="#-splunk-soc-investigation-portfolio">↑ back to top</a></div>

---

## 🎯 Current Learning

<div align="center">

![Splunk](https://img.shields.io/badge/-Splunk%20Enterprise-000000?style=flat-square)
![Wazuh](https://img.shields.io/badge/-Wazuh%20SIEM-3AA5DC?style=flat-square)
![Sentinel](https://img.shields.io/badge/-Microsoft%20Sentinel-0078D4?style=flat-square)
![EDR](https://img.shields.io/badge/-EDR-8A2BE2?style=flat-square)
![Digital Forensics](https://img.shields.io/badge/-Digital%20Forensics-C71585?style=flat-square)

</div>

I am continuously improving my practical cybersecurity skills through enterprise attack simulations and hands-on Blue Team investigations involving Windows Security, Active Directory Security, Azure Security, Email/Cloud Identity Security, Threat Hunting, Detection Engineering, Malware Analysis, Digital Forensics, Incident Response, Endpoint Detection & Response (EDR), and the MITRE ATT&CK Framework.

<div align="right"><a href="#-splunk-soc-investigation-portfolio">↑ back to top</a></div>

---

## 👨‍💻 About Me

<div align="center">

### Hansal Savaliya
**Aspiring SOC Analyst · Threat Hunter · Blue Team · Active Directory Security Enthusiast**

</div>

I am building a practical cybersecurity portfolio by investigating realistic enterprise attack scenarios using Splunk Enterprise. My goal is to become a professional SOC Analyst capable of detecting, investigating, and responding to advanced threats across Windows, Active Directory, cloud environments, email/identity platforms, and enterprise networks.

<div align="right"><a href="#-splunk-soc-investigation-portfolio">↑ back to top</a></div>

---

## ⭐ Repository Goal

This repository documents my journey toward becoming a professional SOC Analyst through practical investigations rather than theory. Every investigation includes:

✅ Splunk Search Queries · ✅ Event Analysis · ✅ IOC Identification · ✅ MITRE ATT&CK Mapping · ✅ Attack Timeline · ✅ Incident Response Summary · ✅ Professional Documentation

<div align="center">

### ✅ 33 / 40 Enterprise SOC Investigations Completed — 80%

</div>

### 🗺️ Upcoming Roadmap

| # | Investigation | Status |
|:-:|---|:-:|
| 34 | Malware Reverse Engineering | 🔹 Planned |
| 35 | Threat Intelligence Investigation | 🔹 Planned |
| 36 | DNS Tunneling Detection | 🔹 Planned |
| 37 | Living Off The Land (LOLBins) | 🔹 Planned |
| 38 | Detection Engineering Challenge | 🔹 Planned |
| 39 | Purple Team Simulation | 🔹 Planned |
| 40 | Enterprise SOC Capstone Investigation | 🔹 Planned |

---

### 🧰 Technology Stack & Competencies

- **SIEM / Data Analytics:** Splunk Enterprise, Splunk Search Processing Language (SPL)
- **Log Source Specializations:** Windows Event Logs (Security, System, Application, Sysmon), Windows Filtering Platform (WFP), Microsoft 365 / Exchange Online Audit Logs, AWS CloudTrail, Web Server Access Daemons
- **Framework Mappings:** MITRE ATT&CK, Cyber Kill Chain, NIST SP 800-61 r2
- **Core Security Focus:** Threat Hunting, Detection Engineering, Incident Triage, Log Forensic Architecture, Evidence-Based Analyst Reporting

<div align="center">

⭐ **This repository represents my continuous journey toward becoming a SOC Analyst through practical investigations, enterprise attack simulations, and professional incident response documentation.**

<sub>If this portfolio was useful or interesting, consider leaving a ⭐ on the repo.</sub>

</div>
