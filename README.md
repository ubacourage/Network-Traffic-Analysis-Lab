# Network-Traffic-Analysis-Lab
## Overview

This repository contains two hands-on Network Traffic Analysis projects completed using Wireshark in an Ubuntu virtual environment. The projects focus on analyzing normal network behavior through live traffic capture and investigating malicious activity within an Emotet and Trickbot malware PCAP to identify indicators of compromise (IOCs), suspicious communications, and potential attack behavior.

# Part 1 – Live Network Capture Analysis (Clean Traffic Baseline)

## Objective
- Capture and analyse live network traffic from an Ubuntu 22.04 VM.
- Understand normal network behaviour during standard web browsing.
- Apply Wireshark display filters to isolate and inspect traffic.
- Validate observed IP addresses using threat intelligence platforms.
- Establish a clean traffic baseline for comparison with malicious captures.

## Tools Used
- Wireshark — For packet capture and traffic analysis.
- VirusTotal — Used to verify malicious IP addresses, domains, and other indicators of compromise (IOCs).
- AbuseIPDB — Used for threat intelligence and reputation checks on suspicious external IP addresses.
- Ubuntu — Operating system analysis environment.
- Firefox browser - Generated controlled browsing traffic

## Wireshark Display Filters Used (Analysis)

### 1. http

Used to isolate unencrypted HTTP traffic.

### Findings
- Detected HTTP GET requests to bored.com hosted on Cloudflare infrastructure.
- OCSP certificate checks to Google infrastructure were identified.
- No malicious HTTP traffic or suspicious payloads were detected.

### Observation

The traffic was legitimate.

### 2. dns

Used to analyse DNS queries and responses.

### Findings
- DNS queries resolved successfully for bored.com and www.bored.com.
- Firefox telemetry traffic to incoming.telemetry.mozilla was identified.
- No NXDOMAIN anomalies or DGA behaviour were detected.
- All DNS traffic was transmitted over UDP port 53 in cleartext.

### Observation

The DNS activity was normal and consistent with legitimate browser behaviour. However, unencrypted DNS queries present privacy concerns in real environments.

### 3. tcp.flags.syn==1

Used to identify TCP connection attempts and possible port scanning activity.

### Findings
- Observed SYN packets to ports 80 and 443 only.
- Connections were made to Cloudflare and Google IP ranges.
- No port scanning or suspicious connection patterns were observed.

### Observation

Traffic reflected normal browser communication patterns with no evidence of reconnaissance or malicious scanning.

### 4. arp

Used to detect ARP broadcasts and possible ARP spoofing activity.

### Findings
-Zero ARP packets were observed.

### Observation

In NAT mode, VirtualBox handles ARP resolution internally, preventing ARP broadcasts from reaching the VM capture interface.

### Findings Summary

| Category	    | Findings
|-----------------------------------------------|----------------------------|
| Total Packets |	11,305
| Traffic Type  |	Legitimate browsing traffic
| External IPs	| Cloudflare and Google infrastructure
| Suspicious Activity	|None detected
| DNS Anomalies	| None
| Port Scanning	| None
| Malware Indicators	| None
| Overall Severity	|LOW / INFORMATION

