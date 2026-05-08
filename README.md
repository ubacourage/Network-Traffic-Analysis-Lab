# Network-Traffic-Analysis-Lab
Overview

This report analyzes a malicious network traffic capture involving an Emotet and Trickbot co-infection using Wireshark, with additional threat intelligence from [VirusTotal](https://www.virustotal.com) and [AbuseIPDB](https://www.abuseipdb.com). The investigation identified multiple indicators of compromise (IOCs), including command-and-control (C2) communication, HTTP data exfiltration, SMB lateral movement, Active Directory enumeration, SSDP reconnaissance, and Domain Generation Algorithm (DGA) activity. Analysis of nearly 20,000 packets and numerous external connections revealed significant malicious behavior associated with advanced malware operations and potential ransomware deployment.


## Objective
Analyze the PCAP file to identify malicious network activity
- Identify common network protocols
- Apply Wireshark display filters
Detect indicators of compromise (IOCs) associated with Emotet and Trickbot malware
Investigate command-and-control (C2) communication and data exfiltration attempts
Examine lateral movement and reconnaissance activities within the network
Assess the severity and potential impact of the infection on the environment

