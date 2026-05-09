# Network-Traffic-Analysis-Lab
## Overview

This repository contains two practical Network Traffic Analysis projects completed using Wireshark in a controlled Ubuntu 22.04 VirtualBox environment. The projects focus on identifying normal network behaviour through a clean live capture and detecting malicious activity in a real-world malware PCAP involving Emotet and Trickbot malware.

The first analysis establishes a clean baseline of legitimate network activity generated from a personal Ubuntu virtual machine using Firefox browser traffic. The second analysis investigates a publicly available malware PCAP from malware-traffic-analysis.net to identify indicators of compromise (IOCs), command-and-control (C2) communication, suspicious DNS activity, lateral movement, and malware behaviour patterns.


## Objective
- Analyze the PCAP file to identify malicious network activity
- Identify common network protocols
- Apply Wireshark display filters
- Detect indicators of compromise (IOCs) associated with Emotet and Trickbot malware
- Investigate command-and-control (C2) communication and data exfiltration attempts
- Recommend appropriate containment and remediation measures based on the findings

## Tools Used
- Wireshark — Used for packet capture analysis, protocol inspection, and identifying suspicious network activity within the PCAP file.
- VirusTotal — Used to verify malicious IP addresses, domains, and other indicators of compromise (IOCs).
- AbuseIPDB — Used for threat intelligence and reputation checks on suspicious external IP addresses.
- Ubuntu — Operating system environment used during the network traffic analysis process.
