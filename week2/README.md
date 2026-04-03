# SOC Analyst Internship - Week 2 Project Report  
**Alert Prioritization, Incident Classification & Basic Incident Response**


*Report by Sudeep | Submitted to CYART SOC Training Program*

## Project Overview

This project showcases comprehensive theoretical knowledge and hands-on practical skills in Security Operations Center (SOC) operations. It covers **alert prioritization**, **incident classification**, **basic incident response**, **alert management**, **triage**, and **escalation procedures** using industry-standard frameworks (MITRE ATT&CK, VERIS, NIST SP 800-61) and real tools (Wazuh SIEM, TheHive, VirusTotal).

The report demonstrates the full SOC workflow: from detecting and prioritizing alerts to classifying incidents, responding effectively, and escalating critical cases.

## Objectives

- Master alert priority levels using CVSS scoring and SOC factors.
- Learn incident classification using standardized taxonomies and metadata enrichment.
- Understand and apply the full incident response lifecycle (NIST/SANS).
- Gain practical experience with SIEM monitoring, case management, threat intelligence validation, and escalation.
- Build real-world SOC analyst skills for efficient threat detection and response.

## Lab Environment

- **Hypervisor**: Oracle VM VirtualBox
- **Operating Systems**:
  - Kali Linux 2023.3 (primary analysis VM)
  - Ubuntu-based Wazuh manager/agent setup
- **Network**: Isolated lab environment (localhost IPs used for simulation)
- **Date of Activities**: March 2026

## Tools Used

| Category              | Tools                                      |
|-----------------------|--------------------------------------------|
| SIEM & Monitoring     | Wazuh SIEM                                 |
| Case Management       | TheHive                                    |
| Alert Classification  | Google Sheets                              |
| Threat Intelligence   | VirusTotal, GuardPot                       |
| Log Analysis          | Linux `auth.log` (`tail -f`)               |
| Frameworks            | MITRE ATT&CK, CVSS, VERIS, NIST SP 800-61, SANS |
| Others                | SOAR concepts, EDR principles              |

## Tasks

### Task 1: Alert Priority Levels

#### Theory
Alert priority levels classify security events by severity, impact, and urgency:
- **Critical**: Immediate action (e.g., ransomware, data breach)
- **High**: Serious threat (e.g., unauthorized admin access)
- **Medium**: Needs investigation (e.g., brute-force attempts)
- **Low**: Informational/minor (e.g., port scans, failed logins)

**Prioritization Factors**:
1. Asset Criticality (production server vs. test system)
2. Exploit Likelihood (public exploit available → higher risk)
3. Business Impact (financial loss → high)

**CVSS Scoring** (0–10):
- 9.8 → Critical (e.g., Log4Shell CVE-2021-44228)
- Tools: SIEM (Wazuh/Splunk), Threat Intelligence, CVSS calculators

#### Practical Application
- Created Google Sheets alert classification table with CVSS scores and priorities.
- Simulated prioritization (e.g., Log4Shell = Critical, Port Scan = Low).
- Monitored alerts in **Wazuh Dashboard** (pie charts for severity distribution, timeline graphs).

**Example Alert Classification Table** (excerpt):

| Alert ID | Alert Name              | Type             | CVSS | Priority | MITRE Technique |
|----------|-------------------------|------------------|------|----------|-----------------|
| 1        | Phishing Email          | Phishing         | 8.0  | High     | T1566           |
| 2        | Brute Force Login       | Authentication   | 5.0  | Medium   | T1110           |
| 3        | Port Scan               | Reconnaissance   | 3.5  | Low      | T1046           |

### Task 2: Incident Classification

#### Theory
Incident classification identifies, categorizes, and labels security events using standardized frameworks.

**Common Incident Categories**:
- Malware (ransomware, trojans)
- Phishing
- DDoS Attack
- Insider Threat (e.g., unauthorized data copy to USB)
- Data Exfiltration

**Taxonomy Frameworks**:
- **MITRE ATT&CK** (tactics & techniques)
- **VERIS** (Actors, Actions, Assets, Attributes, Impact)
- **ENISA Incident Taxonomy**
- **CIRCL / CERT** taxonomies

**Key Enrichment**: Contextual metadata (timestamp, source/destination IP, user details, IOCs).

**Types of IOCs**:
- Network-based (malicious IPs, domains)
- Host-based (file hashes, registry changes)
- Email-based (suspicious attachments, phishing links)

#### Practical Application
- Mapped alerts to MITRE ATT&CK in Google Sheets.
- Enriched incidents with IOCs (hashes, IPs, timestamps).
- Used Wazuh for real-time log monitoring and classification.

### Task 3: Basic Incident Response

#### Theory
Structured approach to handle cybersecurity incidents using the **NIST SP 800-61** lifecycle:

**Incident Response Lifecycle**:
1. **Preparation** – Playbooks, tools (SIEM/EDR), team training
2. **Identification** – Alert monitoring, triage, false-positive filtering
3. **Containment** – Isolate systems, block IPs, disable accounts
4. **Eradication** – Remove malware, patch vulnerabilities
5. **Recovery** – Restore from backup, monitor
6. **Lessons Learned** – Post-incident review

**Key Procedures**:
- System isolation (disconnect network, use EDR)
- Evidence preservation (memory dumps, SHA-256 hashing)
- Communication protocols
- SOAR automation (e.g., Splunk Phantom)

#### Practical Application
- Documented full response procedures.
- Practiced isolation steps and evidence collection concepts.
- Understood SOAR workflow: Alert → Auto Analysis → Action → Report.

### Task 4: Alert Management Practice

#### Theory
Efficient alert management involves classification, prioritization (CVSS), MITRE mapping, and ticket creation.

#### Practical Application
- Built **Google Sheets Alert Classification System**.
- Created custom **Wazuh Dashboard** with:
  - Pie chart for alert severity
  - Timeline for event trends
  - Events tab showing port changes, login sessions, AppArmor denials.
- Created **TheHive incident ticket** for simulated ransomware:
  - Title: `[Critical] Ransomware Detected on Server-X`
  - Severity: Critical
  - Indicators: `crypto_locker.exe`, IP `192.168.1.50`
  - Added observables, tasks, and description.

**Wazuh Dashboard Screenshots** (in original report) showed 555 total alerts with severity visualization.

### Task 5: Alert Triage Practice

#### Theory
Triage involves analyzing logs, determining priority, validating with threat intelligence, and closing false positives.

#### Practical Application
- Monitored Linux authentication logs:
  ```bash
  sudo tail -f /var/log/auth.log
