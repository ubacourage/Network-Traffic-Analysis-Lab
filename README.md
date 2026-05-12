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

## IOCs Found
Result: No Confirmed Indicators of Compromise

All observed IP addresses belonged to legitimate infrastructure providers such as Cloudflare and Google.

### Observed Legitimate Endpoints

|IOC Type	          | Value	                      | Assessment
|-------------------|----------------------------|----------------------------|
| IP Address        |	104.18.3.152	             | Cloudflare CDN
| IP Address	      | 185.26.117.92	             | Cloudflare CDN
| IP Address	      | 142.251.216.35	           | Google OCSP
| Domain	          | bored.com	                 | Legitimate
| Domain	          | incoming.telemetry.mozilla |Firefox telemetry


### Not Observed
- Port 4444 activity
- Port 6667 activity
- Port 8080 activity
- DGA behaviour
- NXDOMAIN anomalies
- SMB traffic
- ARP spoofing


## Recommended Actions
- Enable DNS-over-HTTPS (DoH) in Firefox.
- Enable HTTPS-Only Mode in Firefox.
- Disable unnecessary Firefox telemetry services.
- Use Bridged Adapter mode when practising ARP analysis.
- Use NAT Network mode for multi-VM SOC labs.


## Tools Used
- Wireshark — For packet capture and traffic analysis.
- VirusTotal — Used to verify malicious IP addresses, domains, and other indicators of compromise (IOCs).
- AbuseIPDB — Used for threat intelligence and reputation checks on suspicious external IP addresses.
- Ubuntu — Operating system analysis environment.
- Firefox browser - Generated controlled browsing traffic


# Part 2 – Malware PCAP Analysis (Emotet + Trickbot)

## Network Environment Analysed
| Parameter	                                           | Value
|-----------------------------------------------|----------------------------|
| PCAP Source	                                  | malware-traffic-analysis.net
| Malware Families	                            | Emotet + Trickbot
| Total Packets	                                | 19,835
| Infected Host	                                | 172.16.1.101
| Internal DNS Server	                          | 172.16.1.10
| External IPs Contacted	                      | 47
| TCP Conversations	                            | 198
| Dominant Protocol	                            | TCP (98.1%)
| Suspicious Ports	                            | 447, 449, 8080
| Analysis Platform	                            | Ubuntu - Wireshark
| Threat Intel Tools                            | VirusTotal, AbuseIPDB


## Suspicious Findings
### 1. Emotet C2 Beaconing

Wireshark Filter Used: http.request

### Findings
- Multiple HTTP POST requests to 90.160.138.175.
- Randomized URL paths characteristic of Emotet C2 communication.
- Additional POST requests to 167.99.105.11:8080 linked to Trickbot activity.
- SSDP M-SEARCH broadcasts identified.

### Assessment
The randomized POST requests and persistent outbound communication strongly indicate active malware beaconing and data exfiltration.

<img width="600" height="400" alt="pcap_http.request" src="https://github.com/user-attachments/assets/8a8e860a-d310-4805-b4f5-513a23690f02" />
Figure 5 – HTTP POST requests to Emotet and Trickbot C2 servers.


### 2. Suspicious DNS Activity and DGA Behaviour

Wireshark Filter Used: dns.qry.name

### Findings
- 30 NXDOMAIN responses out of 162 DNS queries (18.52%).
- WPAD abuse attempts detected through wpad.stonypebble.com.
- DNSBL lookups against Spamhaus, SORBS, and Barracuda observed.
- LDAP SRV lookups indicated Active Directory reconnaissance.

### Assessment

The high NXDOMAIN rate and automated DNSBL queries are strong indicators of malware-driven DGA activity and C2 validation behaviour.

<img width="500" height="300" alt="pcap_dns.qry.name" src="https://github.com/user-attachments/assets/34cc3ead-5373-4f48-bb94-dc8b879c093d" />
Figure 6 – DNS analysis showing DGA activity and DNSBL lookups.

### 3. Negative Findings
Wireshark Filter Used: tcp.port==4444
No Metasploit reverse shell activity detected.

Wireshark Filter Used: icmp
No ICMP scanning or flood traffic identified.

### Assessment

The malware relied on TCP-based HTTP/HTTPS communication rather than ICMP or default Metasploit ports.
<img width="500" height="300" alt="pcap_tcp.port" src="https://github.com/user-attachments/assets/b0d1a62a-4fcd-4c76-8ac7-1c2624fd448a" /> |
<img width="500" height="300" alt="Screenshot from 2026-05-06 18-53-36" src="https://github.com/user-attachments/assets/80ef556d-6370-487f-ba85-37eeea047c4e" />
Figure 7–8: Negative findings for port 4444 and ICMP.


### 4. Persistent C2 Communication on Non-Standard Ports

Wireshark Tools Used: Statistics > Conversations and Endpoints

### Findings
- 47 external IP addresses contacted by a single infected host.
- Persistent outbound sessions on ports 447, 449, and 8080.
- Long-duration sessions exceeding three hours.
- Large data transfers to suspected malware infrastructure.

### Assessment
The communication patterns strongly indicate persistent command-and-control activity and malware module delivery.

<img width="500" height="300" alt="pcap_stat_conversations" src="https://github.com/user-attachments/assets/06f93bfe-857f-415d-b8fc-904e99b1559f" /> |
<img width="500" height="300" alt="pcap_stat_endpoint" src="https://github.com/user-attachments/assets/9a61329b-2ec2-4ab6-a339-3d86eedc5a9f" />
Figure 7-8: IPv4 Conversations and Endpoint analysis


## 5. Protocol Hierarchy 

Wireshark Tool Used: Statistics > Protocol Hierarchy

### Findings

* 98.1% of all captured traffic consisted of TCP packets.
* TLS accounted for 1,413 packets (7.1%) indicating encrypted outbound communication.
* HTTP traffic accounted for 83 packets (0.4%) and was associated with malicious HTTP POST activity.
* SMB and SMB2 traffic were identified within the capture.
* NetBIOS traffic was present, indicating possible internal network discovery.
* LDAP protocol activity was detected, suggesting Active Directory reconnaissance.

### Assessment

The dominance of TCP traffic confirms that the malware primarily relied on TCP-based communication for command-and-control (C2) activity. The presence of SMB, SMB2, NetBIOS, and LDAP traffic is highly suspicious for a standard workstation and strongly suggests internal reconnaissance and lateral movement attempts across the network. Combined with the DNS and HTTP findings, the observed behaviour is consistent with known Emotet and Trickbot infection patterns.


<img width="500" height="300" alt="Screenshot from 2026-05-06 18-59-02" src="https://github.com/user-attachments/assets/5355a38e-37df-4537-8592-be02e246f80f" />

Figure 9 — Protocol Hierarchy Statistics 



## IOCs Found
### Malicious IP Addresses
| IP Address	       | Port	               | Description
|-----------------|------------------|----------------------------|
| 90.160.138.175	| 80	             | Emotet primary C2 server
| 167.99.105.11	  | 8080	           | Trickbot C2 server
| 110.39.160.66   | 447	             | Suspected module delivery server
| 102.164.208.44	| 449	             | Persistent C2 communication
| 103.220.47.220	| 447	             | Secondary malware server
| 97.46.66.173	  | N/A	             |Referenced in DNSBL lookups


## Suspicious Domains and URLs
| Domain / URL	|                                   Description
|-----------------------------|----------------------------------------------|
| api.ipify.org	              | External IP lookup service abused by malware
| wpad.stonypebble.com	      | WPAD abuse activity
| Randomized POST URL paths	  | Emotet C2 communication
| DNSBL lookups	              | Automated malware validation activity


## Recommended Actions
- Isolate the infected host from the network.
- Block all identified malicious IP addresses.
- Restrict outbound communication on ports 447, 449, and 8080.
- Reset all credentials associated with the infected system.
- Scan internal systems for Trickbot indicators.
- Detect outbound POST requests with randomized URL paths.
- Disable WPAD network-wide.
- Perform forensic imaging before remediation.
- Rebuild infected systems from known-clean images.



## Tools Used
- Wireshark — For packet and protocol analysis
- malware-traffic-analysis.net - Malware PCAP source
- VirusTotal — Used to verify malicious IP addresses, domains, and other indicators of compromise (IOCs).
- AbuseIPDB — Used for threat intelligence and reputation checks on suspicious external IP addresses.
- Ubuntu — Operating system analysis environment.
- VirtualBox - Isolated lab setup


## Conclusion

This project demonstrates practical network traffic analysis skills across both legitimate and malicious environments. The live capture established a clean traffic baseline, while the malware PCAP investigation revealed real-world indicators of compromise associated with Emotet and Trickbot infections.

Using Wireshark and threat intelligence platforms, suspicious behaviour including C2 beaconing, DNS abuse, SMB lateral movement, Active Directory reconnaissance, and persistent malware communication was successfully identified and analysed.

The project highlights essential SOC analyst skills in packet analysis, threat hunting, IOC identification, malware traffic investigation, and network-based incident response within a controlled lab environment.







