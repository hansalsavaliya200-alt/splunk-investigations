# 🛡️ Splunk SOC Investigation Lab

![Splunk](https://img.shields.io/badge/SIEM-Splunk-green)
![SOC](https://img.shields.io/badge/Role-SOC%20Analyst-blue)
![Windows](https://img.shields.io/badge/Platform-Windows%20Security-orange)
![MITRE ATT\&CK](https://img.shields.io/badge/MITRE-ATT%26CK-red)
![Status](https://img.shields.io/badge/Investigations-9%20Completed-success)

---

# 📖 Overview

Welcome to my **Splunk SOC Investigation Lab**, a hands-on cybersecurity portfolio documenting realistic Security Operations Center (SOC) investigations using **Splunk Enterprise**.

Each investigation simulates real-world attack scenarios, including authentication attacks, privilege escalation, malware execution, credential dumping, persistence, lateral movement, data exfiltration, and anti-forensics. Every investigation includes Windows Security Event Log analysis, Splunk SPL queries, attack reconstruction, MITRE ATT&CK mapping, and a professional SOC incident report.

The goal of this repository is to demonstrate practical SIEM, threat hunting, and incident response skills expected from an entry-level SOC Analyst.

---

# 🎯 Skills Demonstrated

## SIEM & Log Analysis

* Splunk Enterprise
* Splunk SPL Queries
* Windows Security Event Analysis
* Security Log Investigation
* IOC Identification
* Attack Timeline Reconstruction
* Threat Hunting
* SOC Incident Reporting

---

## Windows Security Monitoring

* Authentication Monitoring
* Failed Logon Detection
* Privileged Logon Analysis
* Process Creation Monitoring
* User Activity Analysis
* Severity Classification
* Event Correlation

---

## Threat Detection

* Brute Force Detection
* Privilege Escalation
* Credential Dumping
* Malware Execution
* Registry Persistence
* Scheduled Task Persistence
* Lateral Movement
* Data Collection
* Data Compression
* Data Exfiltration
* Event Log Clearing
* Anti-Forensics
* Living-off-the-Land Binary (LOLBin) Abuse

---

# 📂 Investigation Portfolio

| Investigation   | Scenario                                                 | Status      |
| --------------- | -------------------------------------------------------- | ----------- |
| Investigation 1 | Windows Authentication Investigation                     | ✅ Completed |
| Investigation 2 | Windows Security Event Investigation                     | ✅ Completed |
| Investigation 3 | SOC Alert Investigation                                  | ✅ Completed |
| Investigation 4 | Windows Event Log Investigation                          | ✅ Completed |
| Investigation 5 | Brute Force Attack Detection                             | ✅ Completed |
| Investigation 6 | Privilege Escalation Investigation                       | ✅ Completed |
| Investigation 7 | Lateral Movement & Persistence Investigation             | ✅ Completed |
| Investigation 8 | Malware Execution & Registry Persistence                 | ✅ Completed |
| Investigation 9 | Credential Dumping, Lateral Movement & Data Exfiltration | ✅ Completed |

---

# 🔍 Investigation Workflow

Each investigation follows a structured SOC investigation methodology:

1. Import Windows Security Logs into Splunk
2. Execute Splunk SPL Queries
3. Analyze Windows Event IDs
4. Investigate Users and Processes
5. Detect Indicators of Compromise (IOCs)
6. Reconstruct the Attack Timeline
7. Map Techniques to MITRE ATT&CK
8. Produce a SOC Incident Report

---

# 📑 Windows Event IDs Covered

| Event ID | Description                 |
| -------- | --------------------------- |
| 4624     | Successful Logon            |
| 4625     | Failed Logon                |
| 4672     | Special Privileges Assigned |
| 4688     | Process Creation            |

---

# ⚠️ Attack Techniques Investigated

* Windows Authentication Attacks
* Brute Force Attacks
* Privilege Escalation
* PowerShell Abuse
* Encoded PowerShell Commands
* Credential Dumping (Mimikatz)
* Registry Run Key Persistence
* Scheduled Task Persistence
* Unauthorized Account Creation
* Remote Desktop (RDP)
* PsExec Lateral Movement
* Data Collection
* Data Compression
* Data Exfiltration
* Event Log Clearing
* Anti-Forensics

---

# 🛠️ Windows Utilities & Tools Investigated

### Legitimate Windows Utilities (LOLBins)

* PowerShell
* Certutil
* PsExec
* WMIC
* Reg.exe
* Schtasks.exe
* BITSAdmin
* Wevtutil
* Cmd.exe
* Robocopy
* Whoami
* Net User
* Net Localgroup
* Netstat
* Tasklist
* NSLookup
* Ping

### Offensive Security Tools

* Mimikatz

---

# 🛡️ MITRE ATT&CK Techniques Covered

| Technique                          | MITRE ID  |
| ---------------------------------- | --------- |
| PowerShell                         | T1059.001 |
| OS Credential Dumping              | T1003     |
| LSASS Memory                       | T1003.001 |
| Create Account                     | T1136     |
| Account Manipulation               | T1098     |
| Registry Run Keys                  | T1547.001 |
| Scheduled Task                     | T1053.005 |
| Windows Management Instrumentation | T1047     |
| Remote Desktop Protocol            | T1021.001 |
| SMB / PsExec                       | T1021.002 |
| Data Staged                        | T1074     |
| Archive Collected Data             | T1560     |
| BITS Jobs                          | T1197     |
| Ingress Tool Transfer              | T1105     |
| Clear Windows Event Logs           | T1070.001 |
| File Deletion                      | T1070.004 |
| Network Service Discovery          | T1046     |

---

# 📁 Repository Structure

```text
Splunk-SOC-Investigations/
│
├── Investigation-1/
│   ├── investigation1.csv
│   └── README.md
│
├── Investigation-2/
├── Investigation-3/
├── Investigation-4/
├── Investigation-5/
├── Investigation-6/
├── Investigation-7/
├── Investigation-8/
├── Investigation-9/
│
└── README.md
```

Each investigation includes:

* Dataset (CSV)
* Splunk SPL Queries
* Investigation Questions
* Detailed Analysis
* Indicators of Compromise (IOCs)
* Attack Timeline
* MITRE ATT&CK Mapping
* SOC Incident Summary
* Investigation Conclusion

---

# 📈 Key Learning Outcomes

Through these investigations, I gained practical experience in:

* Writing efficient Splunk SPL queries
* Investigating Windows Security Events
* Detecting malicious PowerShell activity
* Identifying credential dumping attacks
* Investigating privilege escalation
* Detecting persistence mechanisms
* Analyzing lateral movement
* Investigating data exfiltration techniques
* Detecting anti-forensics behavior
* Mapping attacker techniques to MITRE ATT&CK
* Writing professional SOC investigation reports

---

# 🚀 Future Investigations

Planned investigations include:

* Active Directory Attacks
* Kerberoasting
* Pass-the-Hash
* Pass-the-Ticket
* Golden Ticket Attack
* Silver Ticket Attack
* DCSync Attack
* Ransomware Investigation
* DNS Tunneling
* Command & Control (C2) Detection
* Insider Threat Detection
* Threat Hunting Scenarios
* Advanced Splunk Dashboards

---

# 👨‍💻 About Me

I am an aspiring **SOC Analyst** with hands-on experience in:

* Splunk Enterprise
* Threat Hunting
* Windows Security Monitoring
* Incident Detection & Response
* MITRE ATT&CK Framework
* SIEM Investigations
* Malware Analysis Fundamentals

This repository showcases my continuous learning journey through practical SOC investigations and real-world attack simulations.

---

# 📫 Connect With Me

**GitHub:** https://github.com/hansalsavaliya200-alt

**LinkedIn:** https://www.linkedin.com/in/hansal-savaliya/

*(Replace the links above with your actual GitHub and LinkedIn profiles.)*

---

# ⭐ Support

If you found this repository useful or interesting, consider giving it a ⭐ on GitHub.

Feedback, suggestions, and discussions are always welcome!
