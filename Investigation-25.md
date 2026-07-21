# Investigation 25 – Enterprise Ransomware Recovery Investigation

## Scenario

The SOC received multiple alerts indicating that a critical file server had been compromised by ransomware. Users reported that files within the Finance department became inaccessible and a ransom note appeared on the server. The investigation aims to reconstruct the attack timeline, identify the attacker’s actions, determine how recovery mechanisms were disabled, and document the impact on the environment.

---

# Objectives

- Identify suspicious processes executed during the attack.
- Detect ransomware execution.
- Identify attempts to disable recovery mechanisms.
- Detect backup deletion activities.
- Detect Windows Event Log tampering.
- Reconstruct the complete ransomware attack timeline.
- Map attacker activity to the MITRE ATT&CK Framework.

---

# Splunk Queries Used

## Total Events

```spl
index=* source="investigation25_ransomware_recovery.csv"
| stats count
```

---

## Event ID Distribution

```spl
index=* source="investigation25_ransomware_recovery.csv"
| stats count by event_id
| sort event_id
```

---

## Severity Distribution

```spl
index=* source="investigation25_ransomware_recovery.csv"
| stats count by severity
```

---

## Most Active Users

```spl
index=* source="investigation25_ransomware_recovery.csv"
| stats count by user
| sort -count
```

---

## Most Executed Processes

```spl
index=* source="investigation25_ransomware_recovery.csv"
| stats count by process_name
| sort -count
```

---

## Critical Events

```spl
index=* source="investigation25_ransomware_recovery.csv"
severity="Critical"
| table timestamp user hostname process_name command_line description
```

---

## High Severity Events

```spl
index=* source="investigation25_ransomware_recovery.csv"
severity="High"
| table timestamp user hostname process_name command_line description
```

---

## Suspicious Processes

```spl
index=* source="investigation25_ransomware_recovery.csv"
process_name IN ("vssadmin.exe","wbadmin.exe","bcdedit.exe","net.exe","encryptor.exe","cmd.exe","wevtutil.exe")
| table timestamp user hostname process_name command_line severity
```

---

# Investigation Summary

## Total Events

- **80 Events**

---

## Event ID Distribution

| Event ID | Description |
|----------|-------------|
| 4688 | Process Creation |
| 4624 | Successful Logon |
| 4634 | Logoff |
| 4672 | Special Privileges Assigned |
| 5156 | Network Connection |
| 1102 | Security Log Cleared |

---

## Severity Distribution

- Critical: **5**
- High: **3**
- Medium: **17**
- Low: **70**

---

## Most Active Users

- john.doe
- administrator
- SYSTEM
- it.admin
- backup.service

---

## Suspicious Processes Identified

- vssadmin.exe
- wbadmin.exe
- bcdedit.exe
- net.exe
- encryptor.exe
- cmd.exe
- wevtutil.exe

---

# Attack Timeline

### 08:18

**vssadmin.exe**

```
vssadmin delete shadows /all /quiet
```

Purpose:

- Deletes all Volume Shadow Copies.
- Prevents restoring previous versions of encrypted files.

MITRE:

- **T1490 – Inhibit System Recovery**

---

### 08:19

**wbadmin.exe**

```
wbadmin delete catalog -quiet
```

Purpose:

- Deletes Windows Backup Catalog.
- Prevents recovery from backups.

MITRE:

- **T1490 – Inhibit System Recovery**

---

### 08:20

**bcdedit.exe**

```
bcdedit /set recoveryenabled no
```

Purpose:

- Disables Windows Recovery Environment.

MITRE:

- **T1490 – Inhibit System Recovery**

---

### 08:21

**net.exe**

```
net stop BackupSvc
```

Purpose:

- Stops Backup Service.
- Prevents automatic backups during encryption.

MITRE:

- **T1489 – Service Stop**

---

### 08:22

**encryptor.exe**

```
encryptor.exe D:\Finance
```

Purpose:

- Encrypts Finance department files.
- Main ransomware payload.

MITRE:

- **T1486 – Data Encrypted for Impact**

---

### 08:23

**cmd.exe**

```
echo Your files are encrypted > README.txt
```

Purpose:

- Creates ransom note.

MITRE:

- **T1491 – Internal Defacement / Ransom Note**

---

### 08:25

**wevtutil.exe**

```
wevtutil cl Security
```

Purpose:

- Clears Windows Security Event Logs.
- Attempts to hide evidence.

MITRE:

- **T1070.001 – Clear Windows Event Logs**

---

# Complete Attack Chain

```text
Administrator Access
        │
        ▼
Delete Shadow Copies
(vssadmin.exe)
        │
        ▼
Delete Backup Catalog
(wbadmin.exe)
        │
        ▼
Disable Recovery
(bcdedit.exe)
        │
        ▼
Stop Backup Service
(net.exe)
        │
        ▼
Encrypt Finance Files
(encryptor.exe)
        │
        ▼
Drop Ransom Note
(cmd.exe)
        │
        ▼
Clear Security Logs
(wevtutil.exe)
```

---

# MITRE ATT&CK Mapping

| Technique ID | Technique |
|--------------|-----------|
| T1490 | Inhibit System Recovery |
| T1489 | Service Stop |
| T1486 | Data Encrypted for Impact |
| T1491 | Internal Defacement |
| T1070.001 | Clear Windows Event Logs |

---

# Indicators of Compromise (IOCs)

### Suspicious Processes

- vssadmin.exe
- wbadmin.exe
- bcdedit.exe
- net.exe
- encryptor.exe
- cmd.exe
- wevtutil.exe

### Affected Host

- FILESERVER01

### Privileged Account Used

- administrator

---

# Incident Response Recommendations

- Immediately isolate the affected server.
- Disable the compromised administrator account.
- Restore encrypted files from offline backups.
- Reset privileged account credentials.
- Enable tamper protection for backups.
- Monitor for suspicious use of vssadmin, wbadmin, bcdedit, and wevtutil.
- Implement ransomware detection and response rules within the SIEM.

---

# Conclusion

This investigation identified a complete ransomware attack against **FILESERVER01**. The attacker used the **administrator** account to disable recovery mechanisms by deleting shadow copies, removing backup catalogs, disabling Windows Recovery, and stopping backup services before executing the ransomware payload. After encrypting Finance department files, a ransom note was created and Windows Security Logs were cleared to hide evidence.

The investigation successfully reconstructed the full attack lifecycle, identified all malicious processes, and mapped the activity to the MITRE ATT&CK Framework, demonstrating practical ransomware detection and incident response skills using Splunk SIEM.
