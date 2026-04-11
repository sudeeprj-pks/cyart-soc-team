# CyART Week 1 SOC Intern Lab Project - Complete Documentation

**Student:** Sudeep R J  
**Role:** Cyber Security Intern (SOC Domain)  
**Organization:** CyArt  
**Date:** March 2026  
**Report PDF:** `Task report(1st week).pdf` (137 pages)

## 📋 Table of Contents
- [Project Overview](#project-overview)
- [Theoretical Knowledge](#theoretical-knowledge)
- [Practical Application & Lab Work](#practical-application--lab-work)
- [Tools & Lab Setup](#tools--lab-setup)
- [Challenges & Solutions](#challenges--solutions)
- [Conclusion & Lessons Learned](#conclusion--lessons-learned)

## Project Overview
This Week 1 project builds the foundational knowledge and hands-on skills required for a SOC Analyst role. It covers SOC operations, security monitoring, and log management using real tools in a lab environment.

## Theoretical Knowledge

### TASK 1. SOC Fundamentals and Operations
1. **Explanation**  
   A Security Operations Center (SOC) is a centralized team responsible for 24/7 monitoring, detecting, analyzing, and responding to cybersecurity incidents.

2. **Purpose of a SOC**  
   - Continuous Monitoring (24/7)  
   - Threat Detection  
   - Incident Response  
   - Threat Intelligence Integration  
   - Log Analysis  

3. **SOC Team Structure**  
   - **Tier 1 Analyst**: Alert triage, initial investigation, escalation.  
   - **Tier 2 Analyst**: Deep analysis, malware investigation, network forensics.  
   - **Tier 3 Analyst (Threat Hunter)**: Proactive hunting, detection rule development.  
   - **SOC Manager**: Strategy, coordination, compliance reporting.

4. **Key SOC Functions**  
   - Log Monitoring (Windows events, firewalls, DNS, authentication).  
   - Alert Triage (validate → check severity → investigate → escalate).  
   - Threat Intelligence (AlienVault OTX, VirusTotal, AbuseIPDB).  
   - SIEM platforms (Splunk, ELK Stack, IBM QRadar, Microsoft Sentinel).

5. **SOC Frameworks**  
   - NIST Cybersecurity Framework (Identify, Protect, Detect, Respond, Recover).  
   - MITRE ATT&CK Framework (Tactics & Techniques with full matrix shown in report).

6. **SOC Tools Used in Real Environments**  
   - SIEM, EDR (CrowdStrike, Microsoft Defender), Network tools (Wireshark, Zeek, Suricata).

7. **SOC Workflow**  
   Log Collection → SIEM Analysis → Alert Triage → Investigation → Incident Response → Documentation.

### TASK 2. Security Monitoring Basics
1. **Explanation**  
   Continuous observation of system, network, and application activity to detect threats.

2. **Key Monitoring Sources**  
   - Log Monitoring (SIEM)  
   - Network Traffic Monitoring (Wireshark)

3. **Key Security Monitoring Objectives**  
   - Detect Anomalies  
   - Detect Unauthorized Access  
   - Detect Policy Violations

4. **Key Metrics Used in SOC**  
   - Mean Time to Detect (MTTD)  
   - False Positives  
   - False Negatives

5. **Tools Required for Lab**  
   - Elastic SIEM, Wireshark, Kali/Ubuntu VM.

### TASK 3. Log Management Fundamentals
1. **Introduction & Objective**  
   Understand log lifecycle and build a real SOC log ingestion pipeline.

2. **Log Lifecycle**  
   Collection → Normalization → Storage → Retention → Analysis.

3. **Tools Used**  
   Fluentd (Log Collector), Syslog, Ubuntu Linux.

## Practical Application & Lab Work

### TASK 2 – Wireshark Network Monitoring Lab
- Installed Wireshark (`sudo apt install wireshark`).  
- Launched and selected `eth0` interface.  
- Started live packet capture.  
- Generated traffic (`ping google.com` + web browsing).  
- Applied filters: `dns`, `http`, `icmp`.  
- Performed detailed packet inspection (Ethernet, IP, TCP/UDP, Application layers).  
- **Figures 1–8** in the report show complete lab screenshots.

### TASK 3 – Log Management Lab (Fluentd + Log Querying)
**Lab 1: Log Collection Pipeline**  
- Installed Fluentd using official script.  
- Configured syslog listener on port 5140.  
- Started Fluentd service.  
- Generated test log using netcat:  
  ```bash
  echo "<13>Mar 17 testhost SOC MANUAL TEST" | nc -u localhost 5140

  Lab 2: Log Querying & Detection

Created security_logs.json with Event ID 4625 (failed logins).
Filtered failed logins:Bashgrep 4625 security_logs.json
Grouped by Source IP (brute-force detection):Bashgrep 4625 security_logs.json | awk -F'"' '{print $6}' | sort | uniq -c
Results: 3 attempts from 192.168.1.10 → clear brute-force pattern.

Tools & Lab Setup

OS: Ubuntu / Kali Linux VM (VirtualBox)
Core Tools: Wireshark, Fluentd, netcat, Linux CLI (grep, awk)
Network: Host-only or bridged adapter

Challenges & Solutions

Fluentd service not using correct config → Ran manually with -c /etc/fluent/fluent.conf -vv.
Default syslog port conflict → Used custom port 5140.
Too many packets in Wireshark → Applied protocol filters.

Conclusion & Lessons Learned
Week 1 successfully established core SOC skills:

Understood SOC structure, frameworks, and real-world workflow.
Performed live network monitoring and packet analysis with Wireshark.
Built and tested a complete log collection pipeline with Fluentd.
Detected brute-force attacks using Linux log querying.

This foundation prepares me for advanced SOC tasks in the coming weeks.
Full Report: Task report(1st week).pdf

