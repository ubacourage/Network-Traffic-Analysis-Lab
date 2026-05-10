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

<img width="1000" height="600" alt="livecap_http" src="https://github.com/user-attachments/assets/f2eecb22-27a0-4acc-8162-57325ec15afc" />


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
Zero ARP packets were observed.

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
| Overall Severity	| Low

### Indicators of Suspicious Activity
No confirmed indicators of compromise (IOCs) were identified during the live capture.

### Recommendations
- Enable DNS-over-HTTPS (DoH) to encrypt DNS queries.
- Enable HTTPS-Only Mode in Firefox

# Part 2 – Malware PCAP Analysis (Emotet + Trickbot Co-infection)

## Objectives
- Analyse a real-world malware PCAP using Wireshark.
- Identify indicators of compromise (IOCs).
- Investigate suspicious DNS and HTTP activity.
- Detect command-and-control (C2) communication.
- Demonstrate SOC analyst investigative methodology.

## Tools Used
- Wireshark — For packet capture and traffic analysis.
- VirusTotal — Used to verify malicious IP addresses, domains, and other indicators of compromise (IOCs).
- AbuseIPDB — Used for threat intelligence and reputation checks on suspicious external IP addresses.
- Ubuntu — Operating system analysis environment.

## Wireshark Display Filters Used (Analysis)
### 1. http.request

Used to isolate outbound HTTP requests and identify C2 communication.

<img width="1280" height="768" alt="Screenshot from 2026-05-06 18-49-52" src="https://github.com/user-attachments/assets/97536198-c80a-4e0e-ab6c-93109bcc8355" />


### Findings
- Multiple HTTP POST requests to 90.160.138.175.
- Randomized URL paths consistent with Emotet C2 communication.
- POST requests to 167.99.105.11:8080 linked to Trickbot activity.

### Observation

The repeated POST requests, randomized paths, and persistent communication strongly indicate active malware beaconing and data exfiltration. 

### 2. dns.qry.name

Used to analyse DNS queries and identify Domain Generation Algorithms (DGA) activity.

<img width="1280" height="768" alt="Screenshot from 2026-05-06 18-51-50" src="https://github.com/user-attachments/assets/3e54f9d2-a369-4425-843a-bbf54c51983d" />

### Findings
- 30 NXDOMAIN responses out of 162 DNS queries.
- Queries to api.ipify.org identified.
- WPAD abuse attempts detected through wpad.stonypebble.com.
- DNSBL lookups against Spamhaus, SORBS, and Barracuda observed.

### Observation

The high NXDOMAIN rate and automated DNSBL lookups are strong indicators of malware-driven DGA activity and C2 validation behaviour.


### tcp.port==4444

Used to display all TCP traffic.

<img width="1280" height="768" alt="Screenshot from 2026-05-06 18-53-10" src="https://github.com/user-attachments/assets/c98fb248-7773-4de5-81b1-74121a02f575" />


### findings
- No Metasploit reverse shell activity detected.

### icmp

Used to display all ICMP (Internet Control Message Protocol) traffic.

<img width="1280" height="768" alt="Screenshot from 2026-05-06 18-53-36" src="https://github.com/user-attachments/assets/4a5a4bb1-bc05-48cd-ab06-e4821d386fff" />


### findings 
- 
No ICMP scanning or flood activity observed.






