# 🛡️ Investigation 24 – Active Directory Persistence Mechanisms

## Scenario

Following a domain administrative compromise on `DC01`, an adversary initiated a multi-layered persistence campaign to ensure retained access across the Active Directory environment. The attacker executed PowerShell scripts to establish a backdoor local account (`svc_backup`) and added it directly to the local `Administrators` group. To hedge against credential rotation, the adversary simultaneously installed a scheduled task (`schtasks.exe`) set to trigger on user logon (`/sc onlogon`), modified registry startup execution keys under `HKLM\...\Run`, and registered a rogue persistence service (`UpdateSvc`). Finally, the attacker executed `wevtutil cl Security` to clear Windows audit logs and mask their activity footprint.

---

## Splunk Queries Used

### Total Event Volume

```spl
index=* source="investigation24_active_directory_persistence.csv"
| stats count

```

### Event ID Distribution

```spl
index=* source="investigation24_active_directory_persistence.csv"
| stats count by event_id
| sort event_id

```

### Rogue Account & Privilege Escalation Triage

```spl
index=* source="investigation24_active_directory_persistence.csv" (event_id=4720 OR event_id=4732)
| table timestamp, event_id, user, hostname, process_name, command_line, description
| sort timestamp

```

### Task & Registry Persistence Analysis

```spl
index=* source="investigation24_active_directory_persistence.csv" (event_id=4698 OR process_name=reg.exe OR process_name=schtasks.exe)
| table timestamp, event_id, user, hostname, process_name, command_line, description
| sort timestamp

```

### Service Installation & Log Wiping Follow-up

```spl
index=* source="investigation24_active_directory_persistence.csv" (event_id=7045 OR event_id=1102 OR process_name=sc.exe)
| table timestamp, event_id, user, hostname, process_name, command_line, description
| sort timestamp

```

---

## Investigation Findings

### Total Log Volume

* **76 Captured Events**

### Event ID Breakdown

* **Event ID 1102** – Audit Log Cleared (1 Event)
* **Event ID 4688** – New Process Created (71 Events)
* **Event ID 4698** – Scheduled Task Created (1 Event)
* **Event ID 4720** – User Account Created (1 Event)
* **Event ID 4732** – Member Added to Security Group (1 Event)
* **Event ID 7045** – Service Installed in System (1 Event)

### Persistence Vectors Detected

1. **Backdoor Local User Account:** `svc_backup` created and added to `Administrators`.
2. **Logon Scheduled Task:** Created via `schtasks.exe` with `/sc onlogon` trigger.
3. **Registry Startup Key Modification:** Written to `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`.
4. **Rogue Service Installation:** Created `UpdateSvc` via `sc.exe` (Event ID 7045).
5. **Defense Evasion / Log Wiping:** Executed `wevtutil cl Security` (Event ID 1102).

---

# Attack Lifecycle Timeline

### Step 1: Initial Backdoor Account Creation

At **10:21:00**, the attacker created a rogue account via PowerShell on Domain Controller `DC01`:

* **Command:** `New-LocalUser svc_backup`
* **Event ID:** 4720 (User Account Created)

### Step 2: Local Administrator Privilege Escalation

At **10:22:00**, the backdoor account was elevated to administrative privileges:

* **Command:** `net localgroup administrators svc_backup /add`
* **Event ID:** 4732 (Member Added to Local Group)

### Step 3: Scheduled Task Persistence

At **10:23:00** – **10:24:00**, a persistent task was scheduled to execute payloads automatically upon user logon:

* **Command:** `schtasks /create /sc onlogon`
* **Event ID:** 4698 (Scheduled Task Created)

### Step 4: Registry Run Key Persistence

At **10:25:00**, the attacker modified startup registry entries to maintain persistence across reboots:

* **Command:** `reg add HKLM...Run`
* **Target Key:** `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`

### Step 5: System Service Persistence

At **10:26:00** – **10:27:00**, a rogue Windows service was installed on `DC01`:

* **Command:** `sc create UpdateSvc`
* **Event ID:** 7045 (Service Installed)

### Step 6: Log Wiping & Anti-Forensics

At **10:28:00** – **10:29:00**, the attacker executed log clearing commands to disrupt forensic analysis:

* **Command:** `wevtutil cl Security`
* **Event ID:** 1102 (Security Audit Log Cleared)

---

# Indicators of Compromise (IOCs)

### Rogue Accounts & Groups

* Account Name: `svc_backup`
* Target Privilege Group: `Administrators`

### Command Execution Strings

* `New-LocalUser svc_backup`
* `net localgroup administrators svc_backup /add`
* `schtasks /create /sc onlogon`
* `reg add HKLM...Run`
* `sc create UpdateSvc`
* `wevtutil cl Security`

### Installed System Services & Tasks

* Service Name: `UpdateSvc`
* Scheduled Task Trigger: `ONLOGON`

---

# MITRE ATT&CK Framework Alignments

| Phase Tactic | Technique ID | Technique Description |
| --- | --- | --- |
| **Persistence** | T1136.001 | Create Account: Local Account (`svc_backup`) |
| **Privilege Escalation** | T1078.003 | Valid Accounts: Local Accounts |
| **Persistence** | T1053.005 | Scheduled Task/Job: Scheduled Task (`schtasks`) |
| **Persistence** | T1547.001 | Boot or Logon Autostart Execution: Registry Run Keys |
| **Persistence** | T1543.003 | Create or Modify System Process: Windows Service (`sc.exe`) |
| **Defense Evasion** | T1070.001 | Indicator Removal: Clear Windows Event Logs (`wevtutil`) |

---

## Technical Conclusion & Action Items

The analysis confirms that following domain administrative access on `DC01`, the adversary deployed four distinct redundancy persistence mechanisms alongside active log suppression.

### Recommended SOC Remediation Actions:

1. **Account Revocation:** Immediately disable and remove the `svc_backup` user account and audit all local/domain admin memberships.
2. **Persistence Sweeping:**
* Delete scheduled tasks configured with `/sc onlogon` or `/sc boot`.
* Revert unauthorized registry writes in `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`.
* Stop and remove the `UpdateSvc` Windows service.


3. **Hardening & Detection Controls:**
* Deploy alerts for Event ID **4720** and **4732** when administrative groups are modified outside change management.
* Restrict access to `wevtutil.exe` and enforce central log forwarders so events persist even if cleared locally.



```

```
