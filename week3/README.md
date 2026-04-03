
** SOC Analyst Project – Week 3**

Cybersecurity Operations & Threat Detection Lab

** Project Overview

This repository contains a comprehensive Security Operations Center (SOC) Week 3 project, focusing on real-world cybersecurity operations, including:
**
Advanced Log Analysis

Threat Intelligence Integration

Incident Escalation Workflows

Alert Triage with Threat Intelligence

Evidence Preservation & Analysis

Full SOC Workflow Simulation (Capstone)
**

The project simulates real attack scenarios in a controlled lab environment, demonstrating how SOC analysts detect, investigate, respond, and report cyber threats.

 Objectives


The primary objectives of this project are:

Develop log analysis and correlation skills

Detect anomalies and brute-force attacks

Integrate threat intelligence feeds (OTX, VirusTotal)

Understand SOC escalation workflows (Tier 1 → Tier 3)

Perform incident response and containment

Preserve digital forensic evidence with integrity

Execute a complete SOC workflow simulation

 Lab Environment
 
Component	Description	IP Address

Attacker Machine	Kali Linux	192.168.56.3

Victim Machine	Metasploitable / Ubuntu	192.168.56.5 / .6

SOC Monitoring System	Ubuntu	192.168.56.4

 Tools Used
 
SIEM: Elastic Stack, Wazuh

Threat Intelligence: AlienVault OTX, VirusTotal

Attack Tools: Hydra, Metasploit, Nmap

Incident Response: TheHive

Forensics: Velociraptor

Networking: tcpdump, syslog

 Tasks Breakdown

 TASK 1: Advanced Log Analysis
 
 Description

This task focuses on analyzing system logs to detect suspicious behavior such as brute-force attacks.

Methodology

Logs collected from: /var/log/auth.log
Commands used:

grep "Failed password" /var/log/auth.log

awk '{print $11}'

sort | uniq -c

 Key Findings
Multiple failed login attempts detected

Source IP: 127.0.0.1

Repeated attempts within short time → Brute-force pattern


 As shown in page 1–3 of the report, repeated login failures were correlated using timestamps and IP analysis
 

 Concepts Covered
 
Log Correlation

Anomaly Detection

Log Enrichment

 TASK 2: Threat Intelligence Integration
 
 Description

Integration of external threat intelligence to validate suspicious indicators.


 Process
Attack simulated using Hydra

Logs monitored using:

sudo tail -f /var/log/auth.log

IP analyzed using:

VirusTotal

AlienVault OTX

 Findings
 
Suspicious IP flagged by security vendors

Threat intelligence enriched investigation context

 Refer page 6–8: IP reputation and OTX enrichment details

 MITRE Mapping
 
Technique: T1110 – Brute Force

Tactic: Credential Access

 TASK 3: Incident Escalation Workflow
 
 Description
 

Simulates SOC workflow from detection to response.

 Workflow
 
Alert → Tier 1 → Tier 2 → Tier 3 → Response
 Tier-wise Actions
 
 Tier 1 (Triage)

Identified abnormal login attempts

Marked as suspicious



 Tier 2 (Investigation)

Log analysis confirmed attack


Verified attacker IP

 Tier 3 (Response)

sudo ufw deny from 192.168.56.3

 SITREP Summary

Incident: SSH Brute-force Attack

Action: IP blocked

Status: Contained


 Refer page 12: Full escalation workflow and SITREP

 TASK 4: Alert Triage with Threat Intelligence

Description

Analyzing alerts and validating IOCs.


 Example Alert

Alert ID	Description	Source IP	Priority	Status

004	Privilege Escalation	192.168.56.9	High	Open

Findings

Privilege escalation detected via sudo

External TI showed no threat (internal IP)

Internal logs revealed suspicious behavior

 Refer page 42–43: IOC validation results

TASK 5: Evidence Preservation & Analysis

 Description


Digital forensics process ensuring integrity of collected evidence.

 Evidence Collection

Network Data

SELECT * FROM netstat()

Memory Dump

File: winlogon.exe.dmp

 Hash Verification

certutil -hashfile file.dmp SHA256

 
 Evidence Table

Item	Description	Hash

Memory Dump	Process dump	SHA256 verified

 Refer page 57: Hash verification proof

 TASK 6: Capstone – Full SOC Workflow

 Description

Complete attack → detection → response lifecycle.


Attack Simulation

Exploit: Samba vulnerability

Tool: Metasploit

use exploit/multi/samba/usermap_script

 Detection

Logs forwarded via syslog

Alerts detected:

Unauthorized access

Privilege escalation


Response

sudo iptables -A INPUT -s 192.168.56.3 -j DROP

Timeline

Time	Event

15:25	Scan

15:28	Exploit

15:55	Detection

16:00	Containment

 Refer page 76: Full SOC workflow timeline

 SOC Workflow Summary

Attack → Log Generation → Detection → Analysis → Threat Intel → Escalation → Response → Reporting

 Challenges Faced

Handling Hydra connection errors

Identifying relevant logs

Distinguishing normal vs malicious behavior

Limited threat intel for internal IPs

Key Learnings

Log correlation helps detect hidden attacks

Threat intelligence improves validation accuracy

SOC escalation ensures structured response

SIEM tools enable centralized monitoring

Forensics ensures evidence integrity

 Recommendations


Implement account lockout policies

Disable password-based SSH login

Deploy IDS/IPS systems

Enable centralized logging

Regular vulnerability scanning

Use SOAR for automation

 Conclusion

This project successfully demonstrates a real-world SOC environment, covering:

Detection of cyber attacks

Threat intelligence integration

Incident escalation workflows

Digital forensics and evidence handling

Full attack lifecycle simulation


It highlights the importance of proactive monitoring, structured response, and automation in modern cybersecurity operations.

 Author
SUDEEP R J | SOC ANALYST TRAINEE
Sudeep R J 
SOC Analyst Trainee
