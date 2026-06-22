# 🔐 Investigation 2 – Security Alert Analysis Using Splunk

## Overview

This investigation focused on analyzing security alert logs using Splunk to identify suspicious activity, high-risk source IP addresses, and event severity trends. The objective was to determine which countries generated the most events, identify potentially malicious IP addresses, and assess the effectiveness of security controls based on allowed and blocked actions.

---

## Objectives

* Analyze security events from multiple countries.
* Identify the most active source IP addresses.
* Determine event distribution by severity.
* Compare blocked and allowed actions.
* Identify the most suspicious IP address for further investigation.

---

## Environment

**Tool Used:** Splunk Enterprise

**Data Source:** Security Alert Dataset (CSV)

**Investigation Type:** Security Event Analysis

---

## Investigation Questions

### 1. Which country generated the most events?

### 2. Which source IP addresses generated the highest activity?

### 3. Which IP address appeared most suspicious?

### 4. How many events occurred for each severity level?

### 5. How many events were blocked versus allowed?

### 6. Which activity should be prioritized for investigation?

---

## Splunk Queries Used

### Count Events by Country

```spl
index=* | stats count by country
```

### Count Events by Source IP

```spl
index=* | stats count by src_ip
```

### Count Events by Severity

```spl
index=* | stats count by severity
```

### Count Events by Action

```spl
index=* | stats count by action
```

### Review Events from Suspicious IP

```spl
index=* src_ip="45.12.67.89"
```

---

## Findings

### Country Analysis

| Country | Observation                            |
| ------- | -------------------------------------- |
| Russia  | Generated the highest number of events |
| China   | Generated moderate activity            |
| India   | Generated lower-risk activity          |

**Result:** Russia generated the highest number of security events.

---

### Source IP Analysis

| Source IP    | Observation                           |
| ------------ | ------------------------------------- |
| 103.22.45.11 | Multiple security events              |
| 45.12.67.89  | Repeated High severity blocked events |

**Result:** The most suspicious IP address was identified as **45.12.67.89**.

---

### Severity Analysis

| Severity | Event Count |
| -------- | ----------- |
| High     | 5           |
| Low      | 4           |
| Medium   | 3           |

**Result:** High severity alerts represented the largest category of security events.

---

### Action Analysis

| Action  | Event Count |
| ------- | ----------- |
| Blocked | 7           |
| Allowed | 5           |

**Result:** More events were blocked than allowed, indicating that security controls successfully prevented several suspicious activities.

---

## Security Analysis

The source IP address **45.12.67.89** generated multiple High severity alerts and all associated events were blocked. This behavior suggests repeated malicious activity originating from the same external source.

Although another IP address generated a similar number of events, 45.12.67.89 was prioritized because:

* Multiple High severity alerts were observed.
* Security controls blocked every attempt.
* Activity appeared repetitive and targeted.
* The source was external to the environment.

Potential explanations include:

* Port scanning
* Reconnaissance activity
* Automated attack attempts
* Credential guessing attempts
* Malicious probing of exposed services

---

## Risk Assessment

### Most Suspicious IP

**45.12.67.89**

### Associated Country

**Russia**

### Risk Level

**High**

### Justification

* Multiple High severity events.
* Repeated activity from the same source.
* All events required security controls to block access.
* Activity pattern indicates potentially malicious intent.

---

## Recommendations

1. Continue monitoring activity from 45.12.67.89.
2. Review firewall and SIEM logs for related indicators.
3. Search for similar activity across other systems.
4. Add the IP address to a watchlist.
5. Escalate the incident if successful access attempts are later observed.

---

## Conclusion

The investigation identified **45.12.67.89** as the highest-priority source IP address. The IP generated repeated High severity events and all attempts were blocked by security controls. The observed behavior is consistent with suspicious external activity and warrants continued monitoring. The investigation demonstrates the importance of using severity levels, action status, and event patterns to prioritize security incidents effectively.

---

## Skills Practiced

* Splunk Log Analysis
* Security Event Investigation
* Source IP Analysis
* Severity-Based Prioritization
* Blocked vs Allowed Event Analysis
* Incident Documentation
* SOC Analyst Methodology
