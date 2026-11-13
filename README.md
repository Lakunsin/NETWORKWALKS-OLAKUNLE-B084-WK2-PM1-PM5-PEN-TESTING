# PENETRATION TESTING & RECONNAISSANCE REPORT
## FOOTPRINTING & NETWORK SCANNING
**W2-PM-FINAL | Networkwalks Cybersecurity Internship**

| Field | Details |
| :--- | :--- |
| **Pentester Name** | Olakunle Odesanya |
| **Program Batch** | B084 - Networkwalks |
| **Date** | 12 November 2026 |
| **Modules Completed** | W2-PM1 (Footprinting with multiple Kali Tools)<br>W2-PM5 (Zenmap Scanning) |
| **Target/Client** | 1. `networkwalks.com` (Written permission secured)<br>2. My own local LAN Network |
| **Letter of Authorization** | Explicit Permission Confirmed |
| **Assessment Phases** | Phase 1: Reconnaissance and Footprinting (Passive & Active)<br>Phase 2: Scanning & Network Discovery<br>Phase 3–5: In Progress |


## 1. Liability Disclaimer & Authorization
All technical activities documented in this report were executed strictly within the authorized permission from `networkwalks.com` and my system was also included in the test. This document is prepared solely for educational and professional development purposes as part of my practical cybersecurity training only. Unauthorized scanning, exploitation, or data access without explicit written consent is illegal and can lead to criminal charges.


## 2. Executive Summary
This technical report details my hands-on execution for Week 2 of the Networkwalks Cybersecurity program. The engagement is divided into two distinct operational phases: external web footprinting using built-in Kali Linux tools and internal network discovery using Zenmap on my own local network.


## 3. Objective
My primary objective was to experience firsthand how an ethical hacker transitions from open-source intelligence (OSINT) gathering against a web property to active subnet mapping and host enumeration within a local network segment. Every command was executed from my Kali Linux terminal and Windows host workstations, capturing specific outputs, identifying system attributes, and analyzing the security posture from an attacker's vantage point.


## 4. Tools Used
The table below outlines the tools utilized and their respective operational functions:

| Tools | Operational Function |
| :--- | :--- |
| **Kali Linux & Windows 11** | Operating systems utilized for reconnaissance. |
| **Whois** | Queries public domain registries to extract ownership, registrar details, and name servers. |
| **Whatweb** | Fingerprints web server software, content management systems, versions, and scripts. |
| **Nslookup** | Resolves domain names to specific IPv4 addresses via public DNS resolvers. |
| **curl -I** | Extracts HTTP response headers, server banners, cookies, and hidden routing paths. |
| **wafw00f** | Probes target web applications to detect active Web Application Firewall (WAF) solutions. |
| **dnsrecon** | Performs deep DNS enumeration across A, MX, NS, TXT, and SRV records. |
| **Zenmap (Nmap GUI)** | Executes network discovery scans across local subnets to discover live hosts and MAC addresses. |
| **Windows CMD** | Identifies local network adapter IP assignments, subnet masks, and physical hardware addresses. |



## 5. Hands-On Execution & Technical Findings

### 5.1 Phase 1: External Footprinting & Reconnaissance (`networkwalks.com`)
Using my Kali Linux terminal, I systematically ran six reconnaissance utilities against `networkwalks.com` to profile its digital footprint without triggering active alarms:

* **WHOIS Query:** Uncovered that the target domain is registered through GoDaddy (Registrar IANA ID: 146), created on November 6, 2019, and utilizing HostGator name servers (`NS6135` and `NS6136.HOSTGATOR.COM`) alongside Domains By Proxy privacy protection.
* **WhatWeb Fingerprinting:** Successfully identified that the website runs on an Apache web server backend powering WordPress (specifically version `7.1.4`) and WordPress Download Manager (version `3.3.76`), with Bootstrap `7.1.4` and jQuery `3.7.1` frontend frameworks. It also leaked the primary server IP (`192.232.216.135`) and administrative email (`info@networkwalks.com`).
* **Nslookup Resolution:** Confirmed the target domain's translation to the direct host IPv4 address `192.232.216.135` using Google Public DNS (`8.8.8.8`).
* **Curl Header Inspection:** Inspected HTTP response headers which revealed a `301` permanent redirect handled by Really Simple Security, exposed the active `__wpdm_client` cookie with `HttpOnly` protection, and leaked the WordPress REST API endpoint (`/wp-json/`).
* **Wafw00f Detection:** Probed the target and confirmed that `networkwalks.com` is actively protected behind a ModSecurity (SpiderLabs) Web Application Firewall, indicating that direct naive exploit payloads will face automated inspection and blocking.
* **DNSRecon Enumeration:** Mapped out comprehensive DNS records, revealing Bind DNS version `9.16.23-RH` running on name server nodes, Google site verification tokens, SPF mail policies, and 8 distinct cPanel mail autodiscover SRV records.

### 5.2 Phase 2: Internal Network Discovery & Scanning (Zenmap Lab)
Transitioning to my Windows 11 environment, I performed local network discovery on my local network:

* **Subnet Identification:** Ran `ipconfig` in the Windows command prompt to confirm local gateway and subnet configuration parameters.
* **Ping Scan Execution:** Configured Zenmap with a Ping Scan profile (executing command `nmap -sn 10.0.0.0/24`) across the 256 address space.
* **Live Host Discovery:** The scan completed successfully, discovering exactly **3 live hosts** up in my environment:
  * `10.0.0.1` (MAC: `52:55:0A:00:00:01`)
  * `10.0.0.2` (MAC: `08:00:27:77:E2:E1`)
  * `10.0.0.10` (MAC: `08:00:27:7B:9C:3A`)
* **Topology Export:** Generated and exported the live network node topology map in PDF format.

## 6. Risk Assessment & Attack Surface Analysis
Analyzing the intelligence gathered across both reconnaissance phases highlights several informational exposures and defensive considerations:

| # | Risk Finding | Observation / Evidence | Security Impact | Risk Level |
| :--- | :--- | :--- | :--- | :--- |
| **1** | **Exact CMS & Plugin Version Leak** | WhatWeb identified WordPress `7.1.4` and WP Download Manager `3.3.76` | Attackers cross-reference specific version numbers with public vulnerability databases (CVEs) for known exploits. | 🟠 **Medium** |
| **2** | **Direct Origin Server IP Exposed** | Nslookup mapped `networkwalks.com` to `192.232.216.135` | Bypasses edge proxies or cloud layers if misconfigured, allowing direct traffic to the underlying server. | 🟡 **Low** |
| **3** | **Administrative REST API Path Disclosure** | Curl headers exposed `/wp-json/` endpoint | Facilitates user enumeration, author scraping, and targeted brute-force attacks against API endpoints. | 🟡 **Low** |
| **4** | **ModSecurity WAF Fingerprinted** | Wafw00f confirmed ModSecurity (SpiderLabs) protection | Informs attackers to utilize payload encoding, obfuscation, or evasion techniques to bypass rule sets. | 🟡 **Low** |
| **5** | **DNS Software & SRV Records Exposed** | DNSRecon mapped Bind `9.16.23-RH` and cPanel mail nodes | Provides deep structural insight into mail routing, hosting infrastructure, and potential DNS daemon flaws. | 🟠 **Medium** |
| **6** | **Active Local Subnet Host Presence** | Zenmap ping scan mapped 3 live hosts with Oracle MACs | Exposes active virtual machines on the local network segment, aiding internal lateral movement planning. | 🟠 **Medium** |

> **Risk Level Key:** 🔴 Critical | 🟠 Medium | 🟡 Low  

**Note:** The items cataloged above represent intelligence gathering and reconnaissance observations rather than exploited vulnerabilities. No exploitation or intrusive vulnerability testing was performed during these modules. This information does not by itself mean the system is vulnerable; further authorized security testing would be required to confirm if there are any vulnerabilities.


## 7. Strategic Hardening Recommendations
To mitigate the information leakage and visibility risks identified during this assessment, I recommend the following defensive hardening measures:

* **Minimize Software Version Disclosure:**  
Configure web servers and security plugins to suppress explicit version banners (such as WordPress and Apache version headers) to frustrate automated vulnerability fingerprinting.
* **Keep Software Updated:**  
CMS platforms, plugins, and other web technologies should be regularly updated and reviewed against current security advisories.
* **Restrict REST API Exposure:**  
Limit public access to sensitive WordPress REST API endpoints (`/wp-json/`) unless required for public site functionality.
* **Maintain and Tune WAF Rules:**  
Keep ModSecurity actively updated with robust rule sets (such as the OWASP Core Rule Set) to catch advanced injection and traversal attempts.
* **Audit DNS Zone Records:**  
Periodically review external DNS entries to ensure only necessary production services, mail servers, and verification records are publicly accessible.
* **Conduct Regular Internal Audits:**  
Perform routine internal network discovery scans across local subnets to ensure all active hosts, VMs, and connected devices are accounted for and authorized.
* **Strictly Adhere to Scope:**  
Ensure all reconnaissance and scanning activities remain confined to authorized testing scopes with proper documentation and management approval.

