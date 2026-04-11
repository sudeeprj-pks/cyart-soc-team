# CyART Week 4 SOC Analyst Lab Project - Complete Documentation

**Project Title:** Threat Hunting, SOAR Automation, Post-Incident Analysis, Adversary Emulation & Security Metrics  
**Student:** Sudeep  
**Date:** April 2026  
**Report PDF:** `1 Week 4 Report.pdf` (108 pages)  

## 📋 Table of Contents
- [Project Overview](#project-overview)
- [Theoretical Knowledge](#theoretical-knowledge)
- [Practical Application & Lab Environment](#practical-application--lab-environment)
- [Tools & Lab Setup](#tools--lab-setup)
- [Key Findings & Evidence](#key-findings--evidence)
- [Capstone Project: Full SOC Incident Response](#capstone-project-full-soc-incident-response)
- [Conclusion & Lessons Learned](#conclusion--lessons-learned)
- [References](#references)

## Project Overview
This project demonstrates end-to-end SOC Analyst skills: proactive **threat hunting**, **SOAR playbook automation**, **post-incident RCA**, **adversary emulation**, and **executive-level metrics reporting**.  
All theoretical concepts were studied from official frameworks (SqRR, TaHiTI, MITRE ATT&CK, CISA, SANS).  
**Lab activities** were performed in a Windows + Kali Linux virtual environment using real tools (Elastic Stack, Winlogbeat, Velociraptor, Wazuh, TheHive, MITRE Caldera, etc.).

## Theoretical Knowledge

### TASK 1. Threat Hunting Methodologies
- **Core Concepts**: Proactive (hypothesis-driven) vs Reactive hunting; SqRR (Search-Query-Retrieve-Respond) and TaHiTI frameworks; data sources (EDR/XDR, SIEM, network logs, threat intel).
- **Key Objectives**: Reduce dwell time, hunt TTPs (e.g., T1078 – Valid Accounts misuse), generate new detection content.
- **How I Learned**: Sqrrl whitepaper, TaHiTI whitepaper, MITRE ATT&CK APT29 case study, Elastic Threat Hunting Guide.

### TASK 2. Advanced SOAR Automation
- **Core Concepts**: Orchestration, Automation, Response; Playbook development (triggers, steps, decision points, integrations); SOAR vs SIEM/EDR.
- **Playbooks Created**: Phishing Response, Malware/C2 Traffic Response.
- **Tools**: Splunk SOAR, TheHive + Cortex, Shuffle SOAR.
- **Key Benefits**: Reduced MTTR, lower analyst fatigue, 50-90% automation rate.
- **How I Learned**: CISA Phishing Automation guide, Splunk Phantom docs, TheHive examples.

### TASK 3. Post-Incident Analysis and Continuous Improvement
- **Core Concepts**: Root Cause Analysis (5 Whys, Fishbone Diagram), Lessons Learned (blame-free post-mortems), SOC KPIs (MTTD, MTTR, Dwell Time, False Positive Rate).
- **How I Learned**: SANS Reading Room, NIST SP 800-61, CISA Cybersecurity Metrics.

### TASK 4. Adversary Emulation Techniques
- **Core Concepts**: Simulate real TTPs (T1566 Phishing, T1210 Exploitation of Remote Services); MITRE Caldera, Atomic Red Team; Purple Teaming.
- **How I Learned**: Caldera documentation, APT28 case study, Red Canary guide.

### TASK 5. Security Metrics and Executive Reporting
- **Core Concepts**: MTTD, MTTR, Dwell Time, False Positive Rate, Incident Resolution Rate; Executive dashboards and non-technical summaries.
- **How I Learned**: SANS “Measuring SOC Success”, CISA Metrics framework, SANS IR report templates.

## Practical Application & Lab Environment

### TASK 1. Threat Hunting Practice (Fully Executed in Lab)
1. **Setup & Installation**  
   - Downloaded & extracted Winlogbeat 9.3.2 on Windows 10 VM.  
   - Started Elasticsearch on Kali Linux (`sudo systemctl start elasticsearch`).  
   - Verified service: `curl -X GET "localhost:9200"`.

2. **Network & Firewall Configuration**  
   - Confirmed connectivity: `ipconfig` (Windows) + `ip addr` (Kali) + ping tests.  
   - Added firewall rules on Windows (File and Printer Sharing, Winlogbeat ports).

3. **Winlogbeat Configuration**  
   - Edited `winlogbeat.yml` to point to Kali IP: `192.168.56.3:9200`.  
   - Installed & started service: `.\Install-Service-winlogbeat.ps1` & `Start-Service winlogbeat`.

4. **Log Generation**  
   - Ran privilege commands (`whoami /priv`, `net user`, etc.) to trigger Security Event Logs.

5. **Log Monitoring in Elastic Security**  
   - Detected Event ID **4672** (Privileged logon / Special privileges assigned).  
   - Created LOG ANALYSIS TABLE:

     | Timestamp          | User              | Event ID | Notes                              |
     |--------------------|-------------------|----------|------------------------------------|
     | 2026-04-07 14:14:59| DESKTOP-V2S553G  | 4672     | Privileged logon detected          |
     | 2026-04-07 14:14:50| DESKTOP-V2S553G  | 4672     | Multiple admin privilege assignment|
     | 2026-04-07 14:14:47| DESKTOP-V2S553G  | 4672     | Elevated privileges observed       |

6. **Velociraptor Investigation**  
   - Deployed Velociraptor client on Windows.  
   - Collected client info, processes, PowerShell execution logs.  
   - Analyzed artifacts for suspicious activity.

7. **Threat Intelligence Analysis**  
   - Queried AlienVault OTX for suspicious IPs/hashes.  
   - Validated IOCs and enriched findings.

**Screenshots**: See Figures 01–15 in the report PDF (Winlogbeat download → Elastic dashboard → Velociraptor results).

### TASK 2. SOAR Playbook Development (Lab)
- Created Phishing Response Playbook in TheHive + simulated Wazuh alerts.  
- Steps: Enrich IP/URL → VirusTotal check → Auto-block via CrowdSec → Create ticket.  
- Tested & documented success rate.

### TASK 3. Post-Incident Analysis (Lab)
- Performed 5 Whys & Fishbone Diagram for mock phishing incident (using Draw.io).  
- Calculated MTTD = 2 hrs, MTTR = 4 hrs.  
- Documented lessons learned in Google Sheets.

### TASK 4. Adversary Emulation Practice (Lab)
- Used MITRE Caldera to simulate T1566 (spearphishing) and T1210 (remote service exploitation).  
- Configured Wazuh to detect the attack.  
- Documented detection status and gaps.

### TASK 5. Security Metrics & Executive Reporting (Lab)
- Built Elastic Security dashboard for MTTD, MTTR, False Positive Rate.  
- Drafted 150-word executive summary with recommendations.

### Capstone Project: Comprehensive SOC Incident Response (Lab)
- Simulated Samba exploit (Metasploit) against Metasploitable2.  
- Detected via Wazuh, triaged in TheHive, contained with CrowdSec.  
- Ran Caldera emulation, performed RCA (5 Whys + Fishbone), calculated metrics, and wrote full 300-word SANS-style report.

## Tools & Lab Setup
- **OS**: Windows 10 VM + Kali Linux VM (VirtualBox)  
- **Core Tools**: Elastic Stack (Elasticsearch + Kibana), Winlogbeat, Velociraptor, Wazuh, TheHive, MITRE Caldera, Metasploit, CrowdSec, AlienVault OTX, VirusTotal  
- **Network**: Host-only adapter (192.168.56.0/24)

## Key Findings & Evidence
- Successfully hunted privilege escalation (Event ID 4672) using hypothesis-driven approach.  
- Reduced manual response time via SOAR playbooks.  
- Identified gaps in detection rules during emulation → recommended new Wazuh rules.  
- All evidence (screenshots, logs, tables) stored in the report PDF.

## Conclusion & Lessons Learned
This lab successfully bridged theory and practice. I can now:
- Proactively hunt threats using SqRR/TaHiTI.  
- Automate SOC workflows with SOAR playbooks.  
- Conduct proper RCA and produce executive-ready metrics reports.  
- Safely emulate adversaries to strengthen defenses.

**Next Steps**: Deploy these playbooks in a production-like environment and automate more use cases with AI-enhanced SOAR.

## References
- Full report: `1 Week 4 Report.pdf`  
- MITRE ATT&CK, Sqrrl, TaHiTI, CISA, SANS, Elastic, Red Canary documentation.

---
