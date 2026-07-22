# 🕵️ Investigation 27 — Enterprise Cloud Exfiltration Threat Hunt

A SOC-style threat hunting investigation simulating a real-world **cloud data exfiltration attack** on an enterprise Windows environment. This project walks through log triage, event correlation, MITRE ATT&CK mapping, and evidence-based incident reporting — the way an analyst would present findings in a live SOC or during a technical interview.

---

## 📌 Overview

| | |
|---|---|
| **Scenario** | Insider/compromised-account cloud exfiltration |
| **Log Source** | Windows Security Event Logs (Sysmon-style) |
| **Total Events Analyzed** | 85 |
| **Confirmed Malicious Events** | 8 |
| **Primary Actor** | Administrator account |
| **Attack Outcome** | Successful exfiltration of HR data to cloud storage + log tampering |

The goal of this investigation was not just to *find* malicious activity, but to demonstrate **analyst-level discipline**: separating what the logs prove from what is merely suspicious, and avoiding conclusions the evidence doesn't support.

---

## 🔎 Methodology

1. **Triage** — Reviewed 85 events across Event IDs 4688, 4624, 5156, and 1102
2. **Severity Filtering** — Isolated the 8 High/Critical events out of 85 total (~9%), consistent with real-world SOC signal-to-noise ratios
3. **Process Analysis** — Flagged anomalous binaries (`adfind.exe`, `nltest.exe`, `rclone.exe`, `wevtutil.exe`) against a baseline of normal enterprise processes (`outlook.exe`, `chrome.exe`, `teams.exe`, etc.)
4. **Timeline Reconstruction** — Sequenced attacker actions by timestamp to build a coherent kill chain
5. **ATT&CK Mapping** — Mapped every confirmed malicious action to a MITRE technique
6. **Evidence-Based Reporting** — Explicitly separated *confirmed facts* from *unsupported assumptions*

---

## 📊 Key Findings

### Event Distribution

| Event ID | Count | Description |
|---|---:|---|
| 4688 | 82 | Process Creation |
| 4624 | 1 | Successful Logon |
| 5156 | 1 | Network Connection Allowed |
| 1102 | 1 | Security Log Cleared |

### Severity Distribution

| Severity | Count |
|---|---:|
| Low | 75 |
| High | 7 |
| Critical | 1 |
| Medium | 1 |

Only **8 of 85 events** required immediate escalation — a realistic reminder that malicious activity is almost always buried in legitimate noise.

---

## 🚨 Suspicious Process Activity

| Process | Purpose | MITRE ATT&CK |
|---|---|---|
| `adfind.exe` | Active Directory enumeration | T1087 – Account Discovery |
| `nltest.exe` | Domain Controller discovery | T1018 – Remote System Discovery |
| `net.exe` | Domain Admin group enumeration | T1069 – Permission Group Discovery |
| `powershell.exe` (Compress-Archive) | Archive sensitive files | T1560.001 – Archive Collected Data |
| `robocopy.exe` | Copy HR files | T1005 – Data from Local System |
| `rclone.exe` | Cloud exfiltration | T1567.002 – Exfiltration to Cloud Storage |
| `wevtutil.exe` | Clear Security logs | T1070.001 – Clear Windows Event Logs |

---

## ⏱️ Attack Timeline

| Time | Activity |
|---|---|
| 09:18 | Active Directory enumeration via ADFind |
| 09:19 | Domain Controller discovery via NLTest |
| 09:20 | Domain Admin group enumeration |
| 09:21 | HR files copied via Robocopy |
| 09:22 | Files archived via PowerShell Compress-Archive |
| 09:23 | Archive uploaded to cloud via Rclone |
| 09:24 | Outbound HTTPS connection to cloud provider established |
| 09:26 | Security log cleared via Wevtutil |
| 09:27 | Windows logs Event ID 1102 confirming log clearance |

**Confirmed attack stages:** Reconnaissance → Collection → Archive → Exfiltration → Defense Evasion

---

## 🗺️ MITRE ATT&CK Mapping

| Technique | ATT&CK ID |
|---|---|
| Account Discovery | T1087 |
| Permission Group Discovery | T1069 |
| Remote System Discovery | T1018 |
| Archive Collected Data | T1560.001 |
| Data from Local System | T1005 |
| Exfiltration to Cloud Storage | T1567.002 |
| Application Layer Protocol | T1071 |
| Clear Windows Event Logs | T1070.001 |

---

## 🧠 Analyst Notes: Evidence vs. Assumption

A core focus of this investigation was **not overstating conclusions**. For example, several user accounts (`alex.morgan`, `it.admin`, `john.doe`, `SYSTEM`) appeared in the logs alongside the Administrator account — but the logs do **not** prove credential theft.

> **Unsupported claim (avoided):** *"Attacker gained passwords of other users."*
>
> **Evidence-based conclusion (used instead):** Multiple accounts generated activity during the investigation window, but the Administrator account is the only one tied to every confirmed malicious action. There is insufficient evidence in this dataset to conclude credential theft or account compromise.

This distinction — separating *what happened* from *what looks suspicious* — is the difference between junior log-reading and analyst-level reasoning, and was a deliberate focus of this write-up.

---

## 📝 Incident Summary

An attacker operating under the Administrator account conducted Active Directory reconnaissance (`ADFind`, `NLTest`, `net.exe`) to map domain resources and privileged groups. Sensitive HR files were then collected via `Robocopy`, compressed with PowerShell, and exfiltrated to cloud storage using `Rclone` over HTTPS. The attacker concluded the operation by clearing the Windows Security log via `Wevtutil` — a classic anti-forensics technique (T1070.001) — in an attempt to hide evidence of the intrusion.

---

## 🎯 Skills Demonstrated

- Windows Security Event Log analysis (Event IDs 4688, 4624, 5156, 1102)
- Threat hunting & anomaly detection in high-noise environments
- MITRE ATT&CK technique mapping
- Attack timeline / kill-chain reconstruction
- Evidence-based incident reporting and analyst-level write-up discipline
- Cloud exfiltration detection (Rclone / HTTPS egress)
- Anti-forensics / log-tampering detection

---

## ⚠️ Disclaimer

This is a **simulated investigation** created for educational and portfolio purposes. All hostnames, usernames, and IOCs are fictional and do not represent any real organization or individual.

---

## 📂 Repo Contents

```
├── README.md              # This write-up
├── logs/                  # (Optional) raw/sample log data used in the investigation
├── timeline.md            # (Optional) detailed timeline breakdown
└── attack-mapping.md      # (Optional) full MITRE ATT&CK reference
```

---

*If you found this useful or have feedback, feel free to open an issue or connect with me.*
