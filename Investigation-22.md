# 🛡️ Investigation 22 – Pass the Ticket Attack

## Scenario

An attacker compromised a Windows Active Directory environment by capturing a valid Kerberos Ticket Granting Service (TGS) ticket (`ticket.kirbi`). Using Mimikatz, the attacker executed a Pass-the-Ticket (PtH) attack via the `kerberos::ptt` module, injecting the forged ticket directly into the current session memory of a workstation (`WIN-SEC01`). This allowed the adversary to authenticate directly to the network file shares on `FILESERVER01` without interacting with the Domain Controller. The attacker successfully harvested confidential engineering data, compressed it using 7-Zip, exfiltrated it over an outbound connection via BITS, deleted local files, and cleared the Windows Security Logs to cover their tracks.

---

## Splunk Queries Used

### Total Events

```spl
index=* source="investigation22_pass_the_ticket.csv"
| stats count
Event ID DistributionSplunk SPLindex=* source="investigation22_pass_the_ticket.csv"
| stats count by event_id
| sort event_id
Severity DistributionSplunk SPLindex=* source="investigation22_pass_the_ticket.csv"
| stats count by severity
Process Creation EventsSplunk SPLindex=* source="investigation22_pass_the_ticket.csv"
event_id=4688
| table timestamp user hostname process_name command_line severity
Kerberos Service Ticket RequestsSplunk SPLindex=* source="investigation22_pass_the_ticket.csv"
event_id=4769
| table timestamp user hostname process_name command_line
| sort timestamp
Outbound Network ConnectionsSplunk SPLindex=* source="investigation22_pass_the_ticket.csv"
event_id=5156 user=administrator
| table timestamp process_name hostname source_ip destination_ip
Investigation FindingsTotal Events60 EventsEvent ID DistributionEvent ID 1102 – Audit Log ClearedEvent ID 4624 – Successful LogonEvent ID 4634 – LogoffEvent ID 4672 – Special Privileges AssignedEvent ID 4688 – New Process CreatedEvent ID 4769 – Kerberos Service Ticket RequestedEvent ID 5156 – Windows Filtering Platform Connection AllowedSeverity DistributionMedium – 26Low – 25Critical – 5High – 4Suspicious Processes Observedmimikatz.exerobocopy.exe7z.exebitsadmin.exewevtutil.exeAttack TimelineStep 1 – Kerberos Ticket Injection (Pass-the-Ticket)At 09:34:00, the attacker leveraged a previously harvested Kerberos ticket to masquerade as the administrator on host WIN-SEC01 using Mimikatz:kerberos::ptt ticket.kirbi
This injected the Kerberos authentication token directly into the memory space of the active session, bypassing traditional interactive authentication prompts.Step 2 – Forged Ticket Resource AccessAt 09:35:00, the injected ticket was leveraged against FILESERVER01. Splunk identified an Event ID 4769 indicating an anomalous Kerberos TGS request with the system flag noting:Service ticket reused
Step 3 – Data HarvestingWith unauthenticated access established directly to the target storage platform, the attacker harvested industrial designs using Windows native utilities:Process: robocopy.exeCommand Line: Copy Engineering shareStep 4 – Data Compression & PackagingThe adversary grouped and compressed the exfiltration payload to minimize data stream visibility across the egress perimeter:Process: 7z.exeCommand Line: Archive Engineering dataStep 5 – Data Exfiltration via BITSAt 09:38:00, the attacker created a background transport agent using native administrative resources to transfer the archive:Process: bitsadmin.exeCommand Line: Upload engineering.7zNetwork Alert (Event ID 5156): Outbound HTTP traffic initiated from internal host 192.168.10.60 to external malicious infrastructure IP 198.51.100.220.Step 6 – Local Evidence Cleanup & Log ClearanceTo disrupt forensic timelines and evade SIEM alerting structures, the attacker deleted the staging file and cleared the target event log structure:File Deletion: del engineering.7z (at 09:39:00)Log Evacuation: wevtutil cl Security (at 09:39:40)Critical Alert: Generated Event ID 1102 indicating the Security log file was wiped clean.Indicators of Compromise (IOCs)Suspicious File & Command Indicatorskerberos::ptt ticket.kirbiCopy Engineering shareArchive Engineering dataUpload engineering.7zdel engineering.7zwevtutil cl SecurityNetwork Infrastructure IOCsRogue Staging IP Address: 198.51.100.220MITRE ATT&CK TechniquesTacticTechniqueLateral MovementT1550.001 – Use of Alternate Authentication Material: Pass the TicketPrivilege Escalation / AccessT1558 – Steal or Forge Kerberos TicketsCollectionT1005 – Data from Local SystemDefense EvasionT1560.001 – Archive via UtilityExfiltrationT1197 – Background Intelligent Transfer Service (BITS)Defense EvasionT1070.001 – Indicator Removal: Clear Windows Event LogsConclusion & RemediationThe analysis confirms a critical Pass-the-Ticket (PtT) breach originating from WIN-SEC01 and targeting engineering repositories on FILESERVER01. Because Kerberos tickets reside in memory, the adversary successfully avoided generating typical failed authentication challenges.Recommended Tiered Incident Actions:Session Termination & Ticket Purge: Terminate all active sessions on compromised systems and force a ticket cache purge via klist purge across affected infrastructure.Credential Cycling: Perform a rapid dual-reset of the Active Directory Domain Controller krbtgt account password to invalidate all existing Kerberos tickets across the domain.Network Boundary Controls: Enforce host firewall blocks to restrict internal hosts from initializing direct bitsadmin connections to unverified public IP locations. Enact endpoint detection rules to alert on non-standard executions of wevtutil.exe.
