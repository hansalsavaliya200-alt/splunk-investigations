# 🛡️ Investigation 11 – Domain Controller Compromise & Active Directory Enumeration

## Scenario

A Windows Domain Controller (DC01) generated multiple security events indicating an Active Directory compromise. The attacker successfully authenticated using the Administrator account, performed reconnaissance, dumped credentials, created a backdoor administrator account, established persistence, disabled Microsoft Defender, and attempted to erase forensic evidence.

---

# Investigation Objectives

- Identify attacker activity
- Analyze Windows Security Events
- Detect Active Directory enumeration
- Detect credential dumping
- Detect persistence mechanisms
- Detect defense evasion
- Build attack timeline
- Map techniques to MITRE ATT&CK

---

# Dataset Statistics

| Item | Count |
|------|------:|
| Total Events | 25 |

## Event ID Distribution

| Event ID | Description | Count |
|----------|-------------|------:|
| 4624 | Successful Logon | 1 |
| 4625 | Failed Logon | 1 |
| 4663 | Object Access | 1 |
| 4672 | Special Privileges Assigned | 1 |
| 4688 | Process Creation | 18 |
| 4698 | Scheduled Task Created | 1 |
| 4720 | User Account Created | 1 |
| 4732 | User Added to Administrators | 1 |

---

# Severity Distribution

| Severity | Count |
|----------|------:|
| Critical | 9 |
| High | 6 |
| Medium | 3 |
| Low | 7 |

---

# Primary User

**Administrator**

All 25 events were generated using the Administrator account, indicating the attacker had administrative privileges throughout the attack.

---

# Investigation Findings

## 1. Initial Access

Event IDs:

- 4624
- 4672

The attacker successfully authenticated using the Administrator account and received administrative privileges.

---

## 2. Active Directory Reconnaissance

Commands executed:

```cmd
whoami /all
hostname
systeminfo
ipconfig /all
net user
net group "Domain Admins" /domain
nltest /dclist:corp.local
dsquery user
```

Purpose:

- User discovery
- System discovery
- Network discovery
- Local account enumeration
- Domain account enumeration
- Domain Admin discovery
- Domain Controller discovery

---

## 3. Credential Dumping

### Mimikatz

```cmd
sekurlsa::logonpasswords
```

Purpose:

Dump credentials stored in LSASS memory.

---

### ProcDump

```cmd
procdump.exe -ma lsass.exe lsass.dmp
```

Purpose:

Create an LSASS memory dump for offline credential extraction.

---

## 4. Microsoft Defender Disabled

```powershell
Set-MpPreference -DisableRealtimeMonitoring $true
```

Purpose:

Disable Microsoft Defender Real-Time Monitoring to evade detection.

---

## 5. Backdoor Account Creation

```cmd
net user backupsvc P@ssw0rd123! /add
```

```cmd
net localgroup administrators backupsvc /add
```

Purpose:

Create a persistent administrator account.

---

## 6. Scheduled Task Persistence

```cmd
schtasks /create /sc onlogon /tn updater /tr powershell.exe
```

Purpose:

Execute PowerShell automatically whenever a user logs on.

---

## 7. Anti-Forensics

```cmd
wevtutil cl Security
```

Purpose:

Clear Windows Security Event Logs.

---

```cmd
del C:\Temp\lsass.dmp
```

Purpose:

Delete the LSASS memory dump.

---

# Attack Timeline

1. Administrator logged into DC01.
2. Administrative privileges assigned.
3. System and Active Directory reconnaissance performed.
4. Mimikatz executed.
5. LSASS memory dumped using ProcDump.
6. Microsoft Defender disabled.
7. Backdoor account (backupsvc) created.
8. backupsvc added to Administrators group.
9. Scheduled task created for persistence.
10. Windows Security Event Logs cleared.
11. LSASS dump deleted.

---

# MITRE ATT&CK Mapping

| Activity | Technique | MITRE ID |
|-----------|-----------|-----------|
| User Discovery | System Owner/User Discovery | T1033 |
| System Discovery | System Information Discovery | T1082 |
| Network Discovery | System Network Configuration Discovery | T1016 |
| Local Account Discovery | Local Account Discovery | T1087.001 |
| Domain Account Discovery | Domain Account Discovery | T1087.002 |
| Domain Groups Discovery | Domain Groups Discovery | T1069.002 |
| Domain Controller Discovery | Remote System Discovery | T1018 |
| Credential Dumping | OS Credential Dumping | T1003.001 |
| Defender Disabled | Impair Defenses | T1562.001 |
| Account Creation | Create Account | T1136.001 |
| Account Manipulation | Account Manipulation | T1098 |
| Scheduled Task | Scheduled Task | T1053.005 |
| Clear Windows Event Logs | Clear Windows Event Logs | T1070.001 |
| Delete LSASS Dump | File Deletion | T1070.004 |

---

# Indicators of Compromise (IOCs)

## Accounts

- Administrator
- backupsvc

## Processes

- mimikatz.exe
- procdump.exe
- powershell.exe
- net.exe
- schtasks.exe
- wevtutil.exe
- nltest.exe
- dsquery.exe

## Files

- lsass.dmp

---

# Incident Severity

## 🔴 Critical

### Reasons

- Domain Controller compromise
- Administrative privileges
- Credential dumping
- Microsoft Defender disabled
- Backdoor administrator account
- Scheduled task persistence
- Event log clearing
- Anti-forensics

---

# Conclusion

This investigation demonstrates a complete Active Directory compromise involving reconnaissance, credential access, persistence, defense evasion, and anti-forensics. The attack highlights how a compromised Domain Controller can be used to establish long-term persistence and maintain full administrative control over an enterprise environment.

---

**Investigator:** Hansal Savaliya

**Platform:** Splunk Enterprise

**Category:** Windows Security Logs

**Difficulty:** ⭐⭐⭐⭐☆
