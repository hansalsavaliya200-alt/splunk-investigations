Here is a clean, well-formatted **`README.md`** tailored for your GitHub SOC Analyst portfolio repository. It follows the standard structure used by SOC/DFIR teams to document writeups, making it easy for recruiters and hiring managers to review your Splunk SPL skills, threat detection knowledge, and MITRE ATT&CK mapping.

---

```markdown
# Investigation 30: Domain Controller Persistence & Anti-Forensics

## 📌 Executive Summary
During routine security monitoring, a SOC analyst received an alert indicating suspicious persistence activity on the primary Domain Controller (**DC01**). This investigation analyzes a complete post-compromise persistence chain where an adversary, having already obtained privileged credentials (`administrator`), established multiple persistence vectors using native Windows utilities (LOLBins) and attempted anti-forensics by clearing the Security Event Log.

This report documents the Splunk SPL queries, host statistics, timeline of compromise, MITRE ATT&CK mapping, and actionable Indicators of Compromise (IOCs).

---

## 🎯 Investigation Objectives
- Identify the primary host(s) and account(s) involved in the compromise.
- Uncover all active persistence mechanisms deployed across the domain controller.
- Map observed adversary tactics, techniques, and procedures (TTPs) to the MITRE ATT&CK framework.
- Establish an accurate timeline of attacker activity and log tampering.

---

## 🔍 Log Analysis & Triage Steps

### Step 1 — Event ID Distribution
To understand the nature of the logged events, an initial breakdown by `event_id` was performed.

**SPL:**
```spl
index=* source="investigation30_domain_controller_persistence.csv"
| stats count by event_id
| sort -count

```

**Results:**

| Event ID | Count | Description |
| --- | --- | --- |
| **4688** | 981 | Process Creation |
| **1102** | 1 | Security Event Log Cleared |
| **5156** | 1561 | Windows Filtering Platform Connection |

> **Analysis:** Overwhelming reliance on **Event ID 4688** indicates the adversary leveraged native Windows CLI utilities to perform post-exploitation actions rather than dropping external binaries.

---

### Step 2 — Severity Distribution

Filtering events by severity helps isolate background noisy traffic from critical security alerts.

**SPL:**

```spl
index=* source="investigation30_domain_controller_persistence.csv"
| stats count by severity
| sort -count

```

**Results:**

| Severity | Count | Actionability |
| --- | --- | --- |
| **Low** | 92 | Baseline noise / Normal background tasks |
| **Critical** | 5 | **Immediate triage required** (Persistence & Log Tampering) |
| **High** | 2 | **Immediate triage required** (Account Manipulation & Registry Modifications) |
| **Medium** | 1 | Suspicious background activity |

---

### Step 3 — User Account Statistics

Identifying which user accounts generated events to isolate compromise boundaries.

**SPL:**

```spl
index=* source="investigation30_domain_controller_persistence.csv"
| stats count by user
| sort -count

```

**Results:**

| User Account | Count | Status |
| --- | --- | --- |
| `alex.morgan` | 23 | Standard Baseline |
| `emma.smith` | 23 | Standard Baseline |
| `it.admin` | 23 | Standard Baseline |
| `john.doe` | 23 | Standard Baseline |
| `administrator` | 5 | **Compromised Account (Adversary Activity)** |
| `SYSTEM` | 3 | System Level (Log Clearing Execution) |

---

### Step 4 — Host Statistics

Determining the target systems affected by the malicious activity.

**SPL:**

```spl
index=* source="investigation30_domain_controller_persistence.csv"
| stats count by hostname
| sort -count

```

**Results:**

| Hostname | Role | Count | Status |
| --- | --- | --- | --- |
| `FIN-01` | Workstation | 23 | Clean Baseline |
| `HR-PC01` | Workstation | 23 | Clean Baseline |
| `WS-101` | Workstation | 23 | Clean Baseline |
| `WS-102` | Workstation | 23 | Clean Baseline |
| **`DC01`** | **Domain Controller** | **8** | **TARGET / COMPROMISED** |

> **Analysis:** While workstations generated uniform baseline traffic, **DC01** was the sole recipient of critical administrative actions.

---

### Step 5 — Suspicious Process Identification

Analyzing command execution on **DC01** revealed heavy abuse of **Living-off-the-Land Binaries (LOLBins)**:

* `powershell.exe` — PowerShell Execution
* `schtasks.exe` — Scheduled Task Management
* `sc.exe` — Service Controller
* `reg.exe` — Registry Console Tool
* `net.exe` — Network & User Account Administration
* `wevtutil.exe` — Windows Event Log Utility

---

### Step 6 — Deep Dive into High & Critical Security Events

**SPL:**

```spl
index=* source="investigation30_domain_controller_persistence.csv" 
(severity="High" OR severity="Critical")
| table timestamp user hostname process_name command_line severity description

```

#### Detailed Findings & Persistence Vectors:

1. **Registry Run Key Persistence**
* **Process:** `powershell.exe`
* **Command:** `powershell Set-ItemProperty HKLM\...\Run`
* **Mechanism:** Writes an autorun entry into the Registry to ensure payload execution upon system boot.


2. **Scheduled Task Creation**
* **Process:** `schtasks.exe`
* **Command:** `schtasks /create /tn WinUpdate`
* **Mechanism:** Masquerades as a routine Windows Update task to execute attacker code on a schedule.


3. **Malicious Windows Service**
* **Process:** `sc.exe`
* **Command:** `sc create WinHostSvc`
* **Mechanism:** Registers a persistent system service (`WinHostSvc`) set to launch with elevated privileges.


4. **Direct Registry Modification**
* **Process:** `reg.exe`
* **Command:** `reg add HKLM\...\Run`
* **Mechanism:** Redundant execution modifying HKLM Run keys directly via native CLI commands.


5. **Rogue Local Account Creation**
* **Process:** `net.exe`
* **Command:** `net user backupsvc /add`
* **Mechanism:** Provisions a backdoor local user account (`backupsvc`) disguised as a legitimate service account for persistent access.


6. **Anti-Forensics / Security Log Clearing**
* **Process:** `wevtutil.exe`
* **Commands:** `wevtutil cl Security` & `Security log cleared`
* **Mechanism:** Wipes the Windows Security Event Log to obscure attacker commands and prevent post-incident analysis.



---

## ⏱️ Attack Timeline

```
09:00 AM ─── Registry Run Key Persistence (PowerShell)
09:01 AM ─── Scheduled Task Created (schtasks /create /tn WinUpdate)
09:02 AM ─── Malicious Service Created (sc create WinHostSvc)
09:03 AM ─── Autorun Registry Key Modified (reg add HKLM\...\Run)
09:04 AM ─── Rogue Account Created (net user backupsvc /add)
09:05 AM ─── Security Log Wiped (wevtutil cl Security)
09:06 AM ─── Security Log Clearing Confirmed (Event ID 1102 Generated)

```

---

## 🛡️ MITRE ATT&CK Mapping

| Tactic | Technique Name | ID | Details |
| --- | --- | --- | --- |
| **Persistence** | Boot or Logon Autostart Execution: Registry Run Keys | [T1547.001](https://attack.mitre.org/techniques/T1547/001/) | Execution via `HKLM\...\Run` keys |
| **Persistence** | Scheduled Task/Job: Scheduled Task | [T1053.005](https://attack.mitre.org/techniques/T1053/005/) | Task creation named `WinUpdate` |
| **Persistence** | Create or Modify System Process: Windows Service | [T1543.003](https://attack.mitre.org/techniques/T1543/003/) | Service creation named `WinHostSvc` |
| **Persistence** | Account Creation: Local Account | [T1136.001](https://attack.mitre.org/techniques/T1136/001/) | Creation of user `backupsvc` |
| **Defense Evasion** | Modify Registry | [T1112](https://attack.mitre.org/techniques/T1112/) | Direct autorun modifications via `reg.exe` |
| **Defense Evasion** | Indicator Removal: Clear Windows Event Logs | [T1070.001](https://attack.mitre.org/techniques/T1070/001/) | Clearing Security log via `wevtutil` |

---

## 🚨 Indicators of Compromise (IOCs)

### Compromised / Suspicious Accounts

* `administrator` (Compromised domain admin account used for persistence tasks)
* `backupsvc` (Rogue persistence account created by attacker)
* `SYSTEM` (Leveraged during automated log wiping)

### Target Host

* `DC01` (Primary Domain Controller)

### Monitored Binaries & Processes

* `powershell.exe`
* `schtasks.exe`
* `sc.exe`
* `reg.exe`
* `net.exe`
* `wevtutil.exe`

### Explicit Malicious Commands

```cmd
powershell Set-ItemProperty HKLM\...\Run
schtasks /create /tn WinUpdate
sc create WinHostSvc
reg add HKLM\...\Run
net user backupsvc /add
wevtutil cl Security

```

---

## 🎯 Remediation & Strategic Recommendations

1. **Immediate Containment:**
* Force a global password reset for the `administrator` account and invalidate active kerberos ticket-granting tickets (`krbtgt` reset).
* Disable and delete the rogue account `backupsvc`.
* Terminate and delete the malicious service `WinHostSvc` and scheduled task `WinUpdate`.
* Remove newly created keys under `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`.


2. **Detection & Hardening:**
* Implement alert rules for `Event ID 1102` (Event log clearing) and flag command-line arguments using `wevtutil cl`.
* Enforce **Restricted Admin Mode** or **LAPS** (Local Administrator Password Solution) across the domain to minimize privilege escalation exposure.
* Forward Event ID 4688 logs in real time to an off-host SIEM repository so log-wiping on DC01 does not destroy forensic data.



```

```
