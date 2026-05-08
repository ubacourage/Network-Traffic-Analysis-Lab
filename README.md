# Network-Traffic-Analysis-Lab
## Overview

This report analyzes a malicious network traffic capture involving an Emotet and Trickbot co-infection using Wireshark, with additional threat intelligence from [VirusTotal](https://www.virustotal.com) and [AbuseIPDB](https://www.abuseipdb.com). The investigation identified multiple indicators of compromise (IOCs), including command-and-control (C2) communication, HTTP data exfiltration, SMB lateral movement, Active Directory enumeration, SSDP reconnaissance, and Domain Generation Algorithm (DGA) activity. Analysis of nearly 20,000 packets and numerous external connections revealed significant malicious behavior associated with advanced malware operations and potential ransomware deployment.


## Objective
- Analyze the PCAP file to identify malicious network activity
- Identify common network protocols
- Apply Wireshark display filters
- Detect indicators of compromise (IOCs) associated with Emotet and Trickbot malware
- Investigate command-and-control (C2) communication and data exfiltration attempts
- Recommend appropriate containment and remediation measures based on the findings

## Tools Used
- Wireshark — Used for packet capture analysis, protocol inspection, and identifying suspicious network activity within the PCAP file.
- VirusTotal
 — Used to verify malicious IP addresses, domains, and other indicators of compromise (IOCs).
AbuseIPDB
 — Used for threat intelligence and reputation checks on suspicious external IP addresses.
Ubuntu — Operating system environment used during the network traffic analysis process.
