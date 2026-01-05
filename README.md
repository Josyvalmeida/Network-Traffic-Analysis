# Network Traffic Analysis: DNS Connectivity Incident

## 1. Scenario Overview
As a cybersecurity analyst for an IT services company, I investigated a connectivity issue reported by several customers. Users were unable to access the website `www.yummyrecipesforme.com`, encountering a "destination port unreachable" error. 

The objective of this analysis was to identify which network protocol was affected and determine why the server was failing to resolve the domain name.

---

## 2. Investigation Methodology
To diagnose the root cause, I simulated a visit to the website and used the network analyzer tool **tcpdump** to capture traffic in real-time. 

The following command was used to filter traffic related to the DNS server (`203.0.113.2`) and identify errors at the network layer:

\`\`\`bash
# Capturing DNS (UDP 53) and ICMP error messages
tcpdump -ni any host 203.0.113.2 and (udp port 53 or icmp)
\`\`\`

---

## 3. Incident Report

### Part 1: Traffic Log Summary
* **UDP Protocol Analysis**: The client machine (`192.51.100.15`) initiated a DNS query via UDP to the server (`203.0.113.2`) requesting the IP address for the domain.
* **ICMP Error Detection**: The network analysis shows that the server returned an ICMP error message: "udp port 53 unreachable".
* **Service Impact**: Port 53 is the standard port for **DNS (Domain Name System)**. The "unreachable" status indicates that the request was rejected by the destination host.
* **Key Finding**: The browser could not retrieve an IP address, which prevented the subsequent HTTPS connection to the web server.



### Part 2: Root Cause Analysis
* **Timestamp**: The incident was first captured at **13:24:32.192571** (1:24 p.m.).
* **Detection**: Awareness began following multiple customer reports of the website failing to load after an extended wait time.
* **Technical Evidence**: 
    * The logs confirm three separate attempts to resolve the domain, all resulting in the same ICMP error.
    * The server at `203.0.113.2` is online (as it generates ICMP responses), but it is not accepting traffic on the DNS port.
* **Suspected Root Cause**: A **DNS service outage** on the server side. The DNS daemon (service) was likely down or misconfigured, causing the host to reject incoming queries on port 53.

---

## 4. Resolution & Next Steps
This incident has been escalated to the security engineering team. Recommended actions include:
1. Verifying if the DNS service is running on host `203.0.113.2`.
2. Checking firewall rules to ensure UDP port 53 is not being blocked.
3. Restarting the DNS daemon if it has crashed.
