# Network-Traffic-Analysis-Lab

## Network Traffic Analysis Report – Live Capture & Malware PCAP Analysis

## Executive Summary 

This repository contains two practical network traffic analysis investigations performed using Wireshark in an Ubuntu 22.04 VirtualBox lab environment.

The first investigation analysed a 60-second live packet capture generated from normal Firefox web browsing activity on an Ubuntu virtual machine. The purpose was to establish a clean traffic baseline and understand legitimate network behaviour. Analysis confirmed that all observed traffic was benign, consisting primarily of HTTP/HTTPS connections to Cloudflare-hosted content, DNS queries, and OCSP certificate validation traffic.

The second investigation focused on a real-world malware PCAP involving an Emotet and Trickbot co-infection obtained from malware-traffic-analysis.net. The analysis revealed multiple indicators of compromise (IOCs), including C2 beaconing, randomized HTTP POST requests, suspicious DNS activity, Active Directory reconnaissance, SMB lateral movement, persistent outbound connections on non-standard ports, and high NXDOMAIN rates associated with DGA behaviour.

Both analyses demonstrate practical SOC analyst skills in packet inspection, traffic filtering, IOC extraction, malware traffic analysis, and network threat investigation.


# Part 1 – Live Network Capture Analysis (Clean Traffic Baseline)

## Network Environment Analysed

| Parameter                                        | Value
|-----------------------------------------------|----------------------------|
| Operating System	                            | Ubuntu 22.04 LTS
| Hypervisor	                                  | VirtualBox
| Capture Interface	                            | enp0s3
| Capture Duration	                            | 60 seconds
| Total Packets	                                | 11,305
| Analyst Machine IP	                          | 10.0.2.15
| DNS/Gateway	                                  | 192.168.43.1
| Primary Destinations	                        | Cloudflare and Google infrastructure
| Browser Used	                                | Firefox


## Suspicious Findings
### 1. HTTP Traffic Analysis

Wireshark Filter Used: http

### Findings
- HTTP GET requests were made to bored.com hosted on Cloudflare infrastructure.
- HTTP 301 redirects upgraded traffic from HTTP to HTTPS.
- OCSP certificate validation traffic to Google infrastructure was identified.
- No malicious payloads or suspicious HTTP requests were observed.

### Assessment
The traffic was legitimate and consistent with normal browser activity. However, the initial HTTP request exposed browser metadata and headers in cleartext before encryption was established. 

<img width="700" height="400" alt="livecap_http" src="https://github.com/user-attachments/assets/f2eecb22-27a0-4acc-8162-57325ec15afc" />
Figure 1 – HTTP filter analysis showing GET requests and HTTPS redirection.

### 2. DNS Traffic Analysis

Wireshark Filter Used: dns

### Findings
- DNS queries resolved successfully for bored.com and www.bored.com.
- Firefox telemetry traffic to incoming.telemetry.mozilla was identified.
- No DGA activity or NXDOMAIN anomalies were detected.
- DNS queries were transmitted over UDP port 53 in cleartext.

### Assessment
The DNS activity reflected legitimate browsing behaviour with no evidence of malicious domain generation or suspicious DNS tunnelling.

<img width="700" height="400" alt="livecap_dns" src="https://github.com/user-attachments/assets/46e784b2-3cd6-41b2-bf42-925fb3e7b306" />
Figure 2 – DNS filter analysis displaying successful domain resolutions.

### 3. TCP SYN Analysis

Wireshark Filter Used: tcp.flags.syn==1

### Findings
- SYN packets were observed only for ports 80 and 443.
- All TCP handshakes completed successfully with valid SYN-ACK responses.
- No port scanning behaviour or unusual destination ports were identified.

### Assessment

Traffic patterns were normal and aligned with legitimate web browsing behaviour.

<img width="700" height="500" alt="livecap_tcpflagsyn" src="https://github.com/user-attachments/assets/cfa89beb-a670-4b6a-999a-ae835e300be6" />
Figure 3 – SYN packet analysis showing standard TCP connection establishment.

### 4. ARP Traffic Analysis

Wireshark Filter Used: arp

### Findings
- Zero ARP packets were captured.

### Assessment

VirtualBox handled ARP resolution internally, preventing ARP broadcasts from reaching the capture interface.

<img width="700" height="500" alt="livecap_arp" src="https://github.com/user-attachments/assets/79f57c6f-cda3-4c68-a547-d329fa573e58" />
Figure 4 – ARP filter showing zero packets.

### IOCs Found
Result: No Confirmed Indicators of Compromise

All observed IP addresses belonged to legitimate infrastructure providers such as Cloudflare and Google.

### Observed Legitimate Endpoints




## Tools Used
- Wireshark — For packet capture and traffic analysis.
- VirusTotal — Used to verify malicious IP addresses, domains, and other indicators of compromise (IOCs).
- AbuseIPDB — Used for threat intelligence and reputation checks on suspicious external IP addresses.
- Ubuntu — Operating system analysis environment.
- Firefox browser - Generated controlled browsing traffic





Used to analyse DNS queries and responses.

### Findings
- DNS queries resolved successfully for www.bored.com.
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

<img width="600" height="400" alt="Screenshot from 2026-05-06 18-49-52" src="https://github.com/user-attachments/assets/97536198-c80a-4e0e-ab6c-93109bcc8355" />


### Findings
- Multiple HTTP POST requests to 90.160.138.175.
- Randomized URL paths consistent with Emotet C2 communication.
- POST requests to 167.99.105.11:8080 linked to Trickbot activity.

### Observation

The repeated POST requests, randomized paths, and persistent communication strongly indicate active malware beaconing and data exfiltration. 

### 2. dns.qry.name

Used to analyse DNS queries and identify Domain Generation Algorithms (DGA) activity.

<img width="600" height="400" alt="Screenshot from 2026-05-06 18-51-50" src="https://github.com/user-attachments/assets/3e54f9d2-a369-4425-843a-bbf54c51983d" />

### Findings
- 30 NXDOMAIN responses out of 162 DNS queries.
- Queries to api.ipify.org identified.
- WPAD abuse attempts detected through wpad.stonypebble.com.
- DNSBL lookups against Spamhaus, SORBS, and Barracuda observed.

### Observation

The high NXDOMAIN rate and automated DNSBL lookups are strong indicators of malware-driven DGA activity and C2 validation behaviour.


### tcp.port==4444

Used to display all TCP traffic.

<img width="600" height="400" alt="Screenshot from 2026-05-06 18-53-10" src="https://github.com/user-attachments/assets/c98fb248-7773-4de5-81b1-74121a02f575" />


### findings
- No Metasploit reverse shell activity detected.

### icmp

Used to display all ICMP (Internet Control Message Protocol) traffic.

<img width="600" height="400" alt="Screenshot from 2026-05-06 18-53-36" src="https://github.com/user-attachments/assets/4a5a4bb1-bc05-48cd-ab06-e4821d386fff" />


### findings 
- 
No ICMP scanning or flood activity observed.

### 3. Statistics > Conversations and Endpoints

Used to identify high-volume external communications and unusual ports.

### findings 
- 47 unique external IP addresses contacted.
- Persistent sessions on non-standard ports 447, 449, and 8080.
- Large data transfers to suspected C2 infrastructure.

### Observation

The large number of external endpoints and persistent communications are abnormal for a workstation and strongly indicate malware activity.


### Findings Summary
| Category	                                       | Findings
|-----------------------------------------------|----------------------------|
| Total Packets	                                    | 19,835
| Malware Families                               	  | Emotet + Trickbot
| External IPs	                                    | 47
| Suspicious Ports	                                | 447, 449, 8080
| SMB Activity	                                    | Detected
| Data Exfiltration	                                | Confirmed
| Overall Severity	                                | CRITICAL



