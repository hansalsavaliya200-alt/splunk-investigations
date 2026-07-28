# 🛡️ Investigation 32 — Windows Defender Tampering & Defense Evasion

> **SOC Investigation Report** | Splunk-based Detection & Response Analysis

![Status](https://img.shields.io/badge/Status-Confirmed%20Attack-red)
![Severity](https://img.shields.io/badge/Severity-Critical-critical)
![Tool](https://img.shields.io/badge/SIEM-Splunk-black)
![MITRE](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-blue)

---

## 📋 Objective

Investigate suspicious **Windows Defender tampering** activity to identify defense evasion techniques, detect credential theft, analyze attacker actions, and reconstruct the complete attack timeline using Splunk.

---

## 🗂️ Dataset

```
investigation32_defender_tampering.csv
```

---

## 📊 Executive Summary

| Metric | Value |
|---|---|
| 🔢 Total Events | **111** |
| 🚨 Critical Events | **15** |
| ⚠️ High Severity Events | **5** |
| 🟡 Medium Severity Events | **1** |
| ✅ Low/Benign Events | **90** |
| 🎯 Compromised Account | `administrator` |
| 🖥️ Systems Involved | `WS205`, `FS02` |
| 🌐 External IP | `203.0.113.50` |

The attacker disabled Microsoft Defender protections, dumped credentials with **Mimikatz**, moved laterally, exfiltrated sensitive HR data, and cleared Windows Security logs — a complete defense evasion + credential access + exfiltration attack chain.

---

## 🔎 Splunk Queries Used

### Event ID Distribution
```spl
index=* source="investigation32_defender_tampering.csv"
| stats count by event_id
| sort -count
```

### Severity Distribution
```spl
index=* source="investigation32_defender_tampering.csv"
| stats count by severity
| sort -count
```

### User Statistics
```spl
index=* source="investigation32_defender_tampering.csv"
| stats count by user
| sort -count
```

### Hostname Statistics
```spl
index=* source="investigation32_defender_tampering.csv"
| stats count by hostname
| sort -count
```

### Process Statistics
```spl
index=* source="investigation32_defender_tampering.csv"
| stats count by process_name
| sort -count
```

### High & Critical Events
```spl
index=* source="investigation32_defender_tampering.csv"
(severity="High" OR severity="Critical")
| table timestamp user hostname process_name command_line severity description
```

### Defender Configuration Changes
```spl
index=* source="investigation32_defender_tampering.csv"
description="Defender Configuration Change"
| table timestamp user hostname process_name command_line description
```

### Real-Time Protection Disabled
```spl
index=* source="investigation32_defender_tampering.csv"
description="Real-Time Protection Disabled"
| table timestamp user hostname process_name command_line description
```

### Defender Exclusions Added
```spl
index=* source="investigation32_defender_tampering.csv"
description="Exclusion*"
| table timestamp user hostname process_name command_line description
```

### Mimikatz Detection
```spl
index=* source="investigation32_defender_tampering.csv"
process_name="mimikatz.exe"
| table timestamp user hostname process_name command_line description
```

### Malware Detection Events
```spl
index=* source="investigation32_defender_tampering.csv"
process_name="MsMpEng.exe"
| table timestamp user hostname process_name command_line description
```

### Data Collection Activity
```spl
index=* source="investigation32_defender_tampering.csv"
process_name="robocopy.exe"
```

### Archive Creation
```spl
index=* source="investigation32_defender_tampering.csv"
process_name="rar.exe"
```

### Data Exfiltration
```spl
index=* source="investigation32_defender_tampering.csv"
process_name="curl.exe"
```

### Log Clearing
```spl
index=* source="investigation32_defender_tampering.csv"
(event_id=1102 OR process_name="wevtutil.exe")
```

---

## 🔍 Event ID Distribution

| Event ID | Count | Description |
|---|---:|---|
| `4688` | 106 | 🧩 Process Creation |
| `1102` | 1 | 🧹 Security Log Cleared |
| `1116` | 1 | 🦠 Malware Detection |
| `1117` | 1 | 🛑 Malware Action Taken |
| `5001` | 1 | 🚫 Real-Time Protection Disabled |
| `7040` | 1 | ⚙️ Service Start Type Changed |

---

## 📈 Severity Distribution

| Severity | Count |
|---|---:|
| 🔴 Critical | 15 |
| 🟠 High | 5 |
| 🟡 Medium | 1 |
| 🟢 Low | 90 |

---

## 👥 User Distribution

| User | Events |
|---|---:|
| emma.smith | 23 |
| john.doe | 23 |
| alex.morgan | 22 |
| it.admin | 22 |
| **administrator** | 15 |
| **SYSTEM** | 6 |

### 🚨 Suspicious Account: `administrator`
Central actor behind Defender tampering, credential dumping, lateral movement, and data exfiltration.

### 🧹 `SYSTEM`
Associated with log-clearing activity following the attack.

---

## 🖥️ Host Distribution

| Host | Events | Role |
|---|---:|---|
| WS101 | 23 | Workstation (normal) |
| WS102 | 23 | Workstation (normal) |
| FIN01 | 22 | Workstation (normal) |
| HR01 | 22 | Workstation (normal) |
| **WS205** | 15 | 🎯 Initial compromise — Defender tampering, credential theft |
| **FS02** | 6 | 📁 File server — data collection, exfiltration, log clearing |

---

## ⚙️ Process Analysis

| Process | Purpose | Investigation Finding |
|---|---|---|
| `chrome.exe` | Web browser | 🟢 Normal activity |
| `excel.exe` | Microsoft Excel | 🟢 Routine user activity |
| `explorer.exe` | Windows Explorer | 🟢 Normal Windows shell |
| `svchost.exe` | Windows service host | 🟢 Normal OS process |
| `teams.exe` | Microsoft Teams | 🟢 Routine communication |
| `powershell.exe` | PowerShell | 🔴 Used for Defender tampering |
| `MsMpEng.exe` | Microsoft Defender engine | 🟠 Detected Mimikatz & config changes |
| `wevtutil.exe` | Windows Event Utility | 🔴 Security log clearing |
| `curl.exe` | Command-line HTTP client | 🔴 Data exfiltration |
| `mimikatz.exe` | Credential dumping tool | 🔴 Credential theft |
| `psexec.exe` | Remote administration tool | 🟠 Lateral movement |
| `rar.exe` | Archive utility | 🟠 Data compression |
| `robocopy.exe` | File copy utility | 🟠 HR data collection |
| `sc.exe` | Service controller | 🔴 Stopped Defender service |
| `services.exe` | Windows service manager | 🟠 Modified Defender startup type |

---

## 🎯 High & Critical Events

| Time | Host | Process | Description |
|---|---|---|---|
| 10:01 | WS205 | PowerShell | 🚫 Real-Time Protection Disabled |
| 10:02 | WS205 | MsMpEng | ⚙️ Defender Configuration Changed |
| 10:03 | WS205 | PowerShell | ➕ Exclusion Path Added |
| 10:04 | WS205 | PowerShell | ➕ Exclusion Process Added |
| 10:05 | WS205 | sc.exe | 🛑 Defender Service Stop Attempt |
| 10:06 | WS205 | services.exe | ⚙️ Service Startup Type Changed |
| 10:07 | WS205 | PowerShell | 🚫 IOAV Protection Disabled |
| 10:08 | WS205 | PowerShell | 🚫 Behavior Monitoring Disabled |
| 10:09 | WS205 | MsMpEng | 🦠 Mimikatz Detected |
| 10:10 | WS205 | MsMpEng | 🙈 Detection Suppressed by Exclusions |
| 10:11 | WS205 | mimikatz.exe | 🔑 Credential Dumping |
| 10:12 | WS205 | PowerShell | ➕ Exclusion Extension Added |
| 10:13 | WS205 | PowerShell | 🗑️ Defender Feature Removal Attempt |
| 10:14 | FS02 | PsExec | 🔀 Lateral Movement |
| 10:15 | FS02 | PowerShell | 🚫 Disabled Real-Time Monitoring |
| 10:16 | FS02 | Robocopy | 📂 HR Data Collection |
| 10:17 | FS02 | RAR | 🗜️ Archive Created |
| 10:18 | FS02 | curl | 📤 Data Exfiltration |
| 10:19 | FS02 | wevtutil | 🧹 Security Log Cleared |
| 10:20 | FS02 | wevtutil | 🧹 Audit Log Cleared Confirmation |

---

## 🧭 Indicators of Compromise (IOCs)

**Users**
- `administrator`

**Hosts**
- `WS205`
- `FS02`

**Malicious Processes**
- `powershell.exe`
- `mimikatz.exe`
- `psexec.exe`
- `curl.exe`
- `robocopy.exe`
- `rar.exe`
- `wevtutil.exe`
- `sc.exe`

**External IP**
```
203.0.113.50
```

---

## 🧩 Attack Techniques

- 🛡️ Windows Defender Tampering
- 🚫 Real-Time Protection Disabled
- 🛑 Defender Service Disabled
- 🚫 Behavior Monitoring Disabled
- 🚫 IOAV Protection Disabled
- ➕ Defender Exclusions Added
- 🔑 Credential Dumping
- 🔀 Lateral Movement
- 📂 Data Collection
- 🗜️ Data Compression
- 📤 Data Exfiltration
- 🧹 Security Log Clearing

---

## 🗺️ MITRE ATT&CK Mapping

| Technique | ATT&CK ID |
|---|---|
| PowerShell | `T1059.001` |
| Impair Defenses | `T1562.001` |
| Modify Defender Preferences | `T1562.001` |
| Credential Dumping (LSASS) | `T1003.001` |
| PsExec Lateral Movement | `T1021.002` |
| Data Collection | `T1005` |
| Archive Collected Data | `T1560.001` |
| Exfiltration Over HTTP | `T1041` |
| Clear Windows Event Logs | `T1070.001` |

---

## ⏱️ Attack Timeline

```mermaid
timeline
    title Windows Defender Tampering — Attack Timeline
    10:01 : Real-Time Protection Disabled
    10:02 : Defender Configuration Modified
    10:03 : Exclusion Path Added
    10:04 : Mimikatz Exclusion Added
    10:05 : WinDefend Stop Attempt
    10:06 : Service Startup Modified
    10:07 : IOAV Protection Disabled
    10:08 : Behavior Monitoring Disabled
    10:09 : Mimikatz Detected
    10:10 : Detection Suppressed
    10:11 : Credential Dumping
    10:12 : Extension Exclusion Added
    10:13 : Defender Removal Attempt
    10:14 : Lateral Movement to FS02
    10:15 : Real-Time Protection Disabled on FS02
    10:16 : HR Data Collected
    10:17 : Archive Created
    10:18 : Data Exfiltrated
    10:19 : Security Log Cleared
    10:20 : Audit Log Clear Confirmed
```

| Time | Activity |
|---|---|
| 10:01 | 🚫 Real-Time Protection Disabled |
| 10:02 | ⚙️ Defender Configuration Modified |
| 10:03 | ➕ Exclusion Path Added |
| 10:04 | ➕ Mimikatz Exclusion Added |
| 10:05 | 🛑 WinDefend Stop Attempt |
| 10:06 | ⚙️ Service Startup Modified |
| 10:07 | 🚫 IOAV Protection Disabled |
| 10:08 | 🚫 Behavior Monitoring Disabled |
| 10:09 | 🦠 Mimikatz Detected |
| 10:10 | 🙈 Detection Suppressed |
| 10:11 | 🔑 Credential Dumping |
| 10:12 | ➕ Extension Exclusion Added |
| 10:13 | 🗑️ Defender Removal Attempt |
| 10:14 | 🔀 Lateral Movement to FS02 |
| 10:15 | 🚫 Real-Time Protection Disabled on FS02 |
| 10:16 | 📂 HR Data Collected |
| 10:17 | 🗜️ Archive Created |
| 10:18 | 📤 Data Exfiltrated |
| 10:19 | 🧹 Security Log Cleared |
| 10:20 | 🧹 Audit Log Clear Confirmed |

---

## 💥 Impact Assessment

The attacker successfully tampered with Microsoft Defender by disabling multiple protection mechanisms, adding exclusions, stopping Defender services, and suppressing malware remediation. Credentials were dumped using **Mimikatz**, followed by lateral movement to another system where sensitive HR data was collected, archived, exfiltrated over HTTP, and Windows Security logs were cleared to remove forensic evidence.

---

## ✅ Recommendations

1. 🚨 Immediately isolate `WS205` and `FS02`.
2. 🔑 Reset all privileged account credentials.
3. 🧹 Remove all Microsoft Defender exclusions.
4. 🛡️ Re-enable Defender services and protection settings.
5. 🌐 Block outbound communication to `203.0.113.50`.
6. 🔍 Perform a full malware scan across affected systems.
7. 🔀 Investigate additional lateral movement attempts.
8. 🔒 Enable tamper protection in Microsoft Defender.
9. 👀 Monitor for future Event ID `1102` occurrences.
10. 📝 Review PowerShell logging and enable enhanced auditing.

---

## 🎯 Conclusion

This investigation identified a **complete Windows Defender tampering attack** in which an attacker disabled security controls, executed Mimikatz to steal credentials, moved laterally, collected and exfiltrated sensitive HR data, and cleared Windows Security logs to evade detection. The attack demonstrates multiple MITRE ATT&CK techniques across **Defense Evasion, Credential Access, Lateral Movement, Collection, Exfiltration, and Anti-Forensics**, highlighting the importance of continuous monitoring and endpoint protection.

---

## 🧰 Tools & Skills Demonstrated

- Splunk log analysis & correlation
- Windows Event Log triage (4688, 1102, 1116, 1117, 5001, 7040)
- Defense evasion detection (Defender tampering)
- Credential dumping analysis (Mimikatz)
- Lateral movement tracking
- MITRE ATT&CK technique mapping
- Incident timeline reconstruction
- IOC extraction & documentation

---

<p align="center">
  <i>📁 Part of my SOC Analyst Portfolio — Detection Lab Investigations Series</i>
</p>
