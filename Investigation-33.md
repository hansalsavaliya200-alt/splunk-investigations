# 📧 Investigation 33 — Business Email Compromise (BEC)

> **SOC Investigation Report** | Microsoft 365 / Exchange Online Log Analysis via Splunk

![Status](https://img.shields.io/badge/Status-Confirmed%20Suspicious%20Activity-red)
![Confidence](https://img.shields.io/badge/Confidence-High-orange)
![Tool](https://img.shields.io/badge/SIEM-Splunk-black)
![MITRE](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-blue)

---

## 📋 Scenario

Investigate Microsoft 365 / Exchange Online logs to determine whether a **Business Email Compromise (BEC)** attack occurred.

---

## 🗂️ Dataset

```
investigation33_bec.csv
```

---

## 📊 Executive Summary

| Metric | Value |
|---|---|
| 🔢 Total Events | **104** |
| 🚨 Critical Events | **15** |
| ⚠️ High Severity Events | **9** |
| ✅ Low Severity Events | **80** |
| 🎯 Most Suspicious Mailbox | `finance.ap@contoso.com` |
| 🕵️ Spoofed Identity Detected | `spoofed-ceo@contoso-exec.com` |
| 🌐 External IPs Involved | `198.51.100.22`, `203.0.113.77` |
| ✅ Verdict | **Confirmed Suspicious Activity (Potential BEC)** |
| 🔒 Confidence | **High** |

---

## 🔎 SPL Queries Used

### 1. Event Distribution
```spl
index=* source="investigation33_bec.csv"
| stats count by event_id
| sort -count
```

| Event | Count |
|---|---:|
| MailItemsAccessed | 36 |
| Send | 21 |
| UserLoggedIn | 18 |
| FileAccessed | 16 |
| Set-Mailbox | 2 |
| AlertTriggered | 1 |
| Consent to application | 1 |
| FileUploaded | 1 |
| MFA Satisfied | 1 |
| New-InboxRule | 1 |
| Remove OAuth Grant | 1 |
| Remove-InboxRule | 1 |
| RevokeSignInSessions | 1 |
| SearchQueryInitiated | 1 |
| SoftDelete | 1 |
| UserAccountDisabled | 1 |

---

### 2. Severity Distribution
```spl
index=* source="investigation33_bec.csv"
| stats count by severity
| sort -count
```

| Severity | Count |
|---|---:|
| 🟢 Low | 80 |
| 🔴 Critical | 15 |
| 🟠 High | 9 |

---

### 3. User Distribution
```spl
index=* source="investigation33_bec.csv"
| stats count by user
| sort -count
```

| User | Count |
|---|---:|
| alex.morgan@contoso.com | 20 |
| emma.smith@contoso.com | 20 |
| john.doe@contoso.com | 20 |
| priya.nair@contoso.com | 20 |
| **finance.ap@contoso.com** | 18 |
| admin.security@contoso.com | 3 |
| SecurityOps | 1 |
| hr.payroll@contoso.com | 1 |
| **spoofed-ceo@contoso-exec.com** | 1 |

---

### 4. Client IP Distribution
```spl
index=* source="investigation33_bec.csv"
| stats count by client_ip
| sort -count
```

| Client IP | Count |
|---|---:|
| 10.10.5.101 | 20 |
| 10.10.5.102 | 20 |
| 10.10.5.103 | 20 |
| 10.10.5.104 | 20 |
| **198.51.100.22** | 18 |
| 10.10.5.10 | 3 |
| 192.168.10.55 | 1 |
| **203.0.113.77** | 1 |

---

### 5. Application Distribution
```spl
index=* source="investigation33_bec.csv"
| stats count by application
| sort -count
```

| Application | Count |
|---|---:|
| Exchange Online | 94 |
| Azure AD | 7 |
| Defender for Office 365 | 1 |
| SMTP Gateway | 1 |
| SharePoint Online | 1 |

---

### 6. Operation Distribution
```spl
index=* source="investigation33_bec.csv"
| stats count by operation
| sort -count
```

Major operations observed:
- 📩 Mail Read (35)
- 📤 Send (17)
- 🔑 Sign-in Success (17)
- 📂 FileAccessed (16)
- ✉️ SendAs
- 🔗 AddOAuth2PermissionGrant
- ❌ RemoveOAuth2PermissionGrant
- 📥 New Inbox Rule
- 🗑️ Remove Inbox Rule
- ↪️ Set-Mailbox Forwarding
- 🔄 Revoke Refresh Tokens
- ✈️ Impossible Travel Alert
- ✅ MFA Prompt Approved
- 🔍 Mailbox Search
- 🗑️ Delete
- 📤 File Uploaded
- 🚫 Disable Account

---

## 🚩 Suspicious Indicators Identified

### 📬 Suspicious Mailbox Activity
- High volume of `MailItemsAccessed` (mailbox snooping)
- Elevated `Send` operations
- `SendAs` permission usage

### 🔀 Mailbox Rule Manipulation
- 📥 New Inbox Rule created
- 🗑️ Inbox Rule removed
- ↪️ Mailbox forwarding configured
- ❌ Mailbox forwarding later removed

> Attackers commonly use inbox rules to hide replies and silently redirect victim emails.

### 🔗 OAuth Abuse
- ✅ OAuth application consent granted
- ➕ OAuth permission grant added
- ➖ OAuth grant later removed

> Possible malicious OAuth application used for persistent access, even surviving password resets.

### 🪪 Identity Events
- Successful sign-ins
- MFA approval
- Refresh token revocation
- Account disabled

### 🔔 Security Alerts
- ✈️ Impossible Travel Alert
- 🛡️ Defender for Office 365 alert generated

### 🎭 Suspicious Accounts
```
finance.ap@contoso.com
admin.security@contoso.com
spoofed-ceo@contoso-exec.com
```
> The **spoofed CEO account** (`spoofed-ceo@contoso-exec.com`) strongly suggests a CEO impersonation attempt — a classic BEC technique used to pressure finance staff into fraudulent payments.

### 🌐 Suspicious External IPs
```
198.51.100.22
203.0.113.77
```
> Should be investigated for geolocation, reputation, and prior activity.

---

## 🗺️ MITRE ATT&CK Mapping

| Technique | MITRE ID |
|---|---|
| Valid Accounts | `T1078` |
| Phishing | `T1566` |
| Spearphishing via Service | `T1566.003` |
| Email Collection | `T1114` |
| Mailbox Manipulation | `T1098` |
| Account Manipulation | `T1098` |
| Cloud Account Discovery | `T1087` |
| OAuth Abuse | `T1528` |
| Exfiltration Over Web Services | `T1567` |

---

## 📝 Investigation Summary

- Total Events: **104**
- **Exchange Online** generated the majority of activity (94 events)
- Mail read activity was significantly higher than normal baseline
- Several **mailbox modifications** were performed
- **OAuth permissions** were granted then revoked
- **Inbox rules and forwarding rules** were modified
- **External IPs** accessed cloud resources
- A **spoofed CEO account** appeared in the logs
- **Finance mailbox** activity appears more suspicious than standard users
- Defender generated alerts including an **Impossible Travel** detection

---

## ✅ Verdict

| | |
|---|---|
| **Status** | 🔴 Confirmed Suspicious Activity (Potential Business Email Compromise) |
| **Confidence** | 🟠 High |

### 🛠️ Recommended Actions

1. 🔍 Investigate `finance.ap@contoso.com` mailbox in depth.
2. 🌐 Review activity from external IPs `198.51.100.22` and `203.0.113.77`.
3. ❌ Remove any unauthorized mailbox forwarding rules.
4. 🔗 Review OAuth application permissions and revoke suspicious grants.
5. 🔑 Reset passwords and invalidate refresh tokens for affected accounts.
6. 📋 Audit all inbox rules across Microsoft 365.
7. 🛡️ Review Defender for Office 365 alerts for related phishing activity.

---

## 🎯 Conclusion

This investigation surfaced strong indicators of a **Business Email Compromise (BEC)** attack targeting the finance function of the organization. The combination of high-volume mailbox access, inbox rule manipulation, OAuth token abuse, external IP activity, and — most critically — a **spoofed executive identity**, points to a coordinated social engineering campaign aimed at fraudulent financial transactions. The SOC's response (session revocation, account disablement, Defender alerting) reflects appropriate containment, but deeper forensic review of the finance mailbox and OAuth grant history is recommended.

---

## 🧰 Tools & Skills Demonstrated

- Splunk log analysis & correlation across Microsoft 365 / Exchange Online
- BEC / phishing pattern recognition
- Mailbox rule & forwarding abuse detection
- OAuth application consent analysis
- Identity & sign-in log triage
- MITRE ATT&CK technique mapping
- SOC analyst incident reporting & recommendations

---

<p align="center">
  <i>📁 Part of my SOC Analyst Portfolio — Detection Lab Investigations Series</i>
</p>
