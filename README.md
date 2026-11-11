# PENETRATION TESTING REPORT
## Footprinting & Network Scanning Phases

**W2-PM-FINAL | Cybersecurity | Networkwalks**

---

## Project Information

| Field | Details |
|---|---|
| Pentester Name | Tabia Babar |
| Role | Cybersecurity Professional / Intern |
| Program / Batch | B084C – Networkwalks |
| Date | 11 November 2026 |
| Modules Completed | W2-PM1 (Multiple Kali Tools), W2-PM5 (Zenmap Scanning) |
| Client / Target | 1. `networkwalks.com` (written permission secured)<br>2. My own local network (Home LAN) |
| Permission Secured from Client? | Yes |
| Phases Covered | Phase 1: Reconnaissance & Footprinting<br>Phase 2: Scanning & Network Discovery<br>Phase 3–5: In Progress |

---

## 1. Liability Disclaimer

All activities described in this report were performed only on systems that I own or had written permission to test.This report is prepared for educational and research purposes only. The information must not be used for any illegal activity or unauthorized access.The instructor, authors, and Networkwalks are not responsible for any misuse of the information provided in this report. Every individual is responsible for their own actions.
Unauthorized access to computer systems may result in legal consequences, financial penalties, loss of employment, or a criminal record. In many countries, accessing a system without permission is considered a crime, even if no damage is caused.

---

## 2. Introduction

This report presents my practical work completed during Week 2 of the Cybersecurity & Ethical Hacking Internship at Networkwalks.
The project consists of two main activities:

- **W2-PM1 – Footprinting & Reconnaissance:** Collecting publicly available information about `networkwalks.com` using six Kali Linux tools.
- **W2-PM5 – Network Scanning:** Using Zenmap to scan my own home network and identify active devices.

These activities demonstrate two important stages of security assessment: collecting information about a target and identifying active devices within a network.The footprinting activities were performed using Kali Linux, while network scanning was conducted on my Windows 10 Pro computer using Command Prompt and Zenmap.
For each activity, this report explains the tool or command used, the results obtained, and their security importance. Supporting screenshots are included in the **Evidences Collected** section.

---

## 3. Tools Used

The following tools and operating systems were used during the practical activities.
| Tool / Platform | Purpose |
|---|---|
| Kali Linux | Operating system used for footprinting and reconnaissance. |
| Windows 10 Pro | Operating system used for local network scanning. |
| WHOIS | Retrieves public domain registration information, including registrar, registration dates, and name servers. |
| WhatWeb | Identifies website technologies such as the web server, CMS, plugins, and IP address. |
| Nslookup | Resolves a domain name into its corresponding IP address through DNS. |
| Curl (`-I`) | Retrieves HTTP response headers from a web server. |
| Wafw00f | Checks whether a website is protected by a Web Application Firewall (WAF). |
| DNSRecon | Enumerates DNS records, including SOA, NS, MX, A, TXT/SPF, and SRV records. |
| Zenmap (Nmap GUI) | Performs network discovery, identifies live hosts, and generates a network topology map. |
| Windows Command Prompt | Runs `ipconfig` to identify the local IP address and subnet mask. |

---

## 4. Activities Performed

### 4.1 Footprinting & Reconnaissance
I performed passive and light active reconnaissance on `networkwalks.com` using six Kali Linux tools.Each tool provided different information about the target domain. Together, the results helped create a basic profile of the website and its publicly visible infrastructure.

#### Task 1 – WHOIS

**Command Used:**

```bash
whois networkwalks.com
```
**Findings:**
The WHOIS lookup showed that the domain is registered through **GoDaddy.com, LLC**.

| Information | Result |
|---|---|
| Registrar | GoDaddy.com, LLC |
| Domain Creation Date | 6 November 2019 |
| Domain Expiry Date | 6 November 2027 |
| Name Servers | NS6135 and NS6136 (HostGator), NS29 and NS30 (DomainControl) |
| Registrant Privacy Service | Domains By Proxy, LLC |
| DNSSEC Status | Unsigned |

The registrant's personal details are protected by a domain privacy service.

**Security Significance:**
WHOIS information can reveal domain registration details, hosting-related information, and name servers. This information may help an analyst understand the publicly visible domain infrastructure.

---

#### Task 2 – WhatWeb

**Command Used:**
```bash
whatweb networkwalks.com
```
**Findings:**
WhatWeb first identified an HTTP 301 redirect to HTTPS. It then detected the following technologies:

| Information | Detected Result |
|---|---|
| Web Server | Apache |
| CMS | WordPress 7.1.4 |
| Plugin | WP Download Manager 3.3.76 |
| JavaScript Library | jQuery 3.7.1 |
| Tracking Tool | Google Tag Manager |
| Website Title | Networkwalks Academy |
| IP Address | 192.232.216.135 |
| Public Email | info@networkwalks.com |
| HTTP Redirect | HTTP 301 to HTTPS |

**Security Significance:**
Identifying website technologies and their versions helps security professionals understand the target's technology stack. Older or outdated software versions may have publicly documented vulnerabilities and should be reviewed against current security advisories.

---

#### Task 3 – Nslookup
**Command Used:**
```bash
nslookup networkwalks.com
```
The lookup used Google's public DNS server (`8.8.8.8`).

**Findings:**

| Information | Result |
|---|---|
| DNS Server | 8.8.8.8 |
| Domain | networkwalks.com |
| Resolved IP Address | 192.232.216.135 |
| Response Type | Non-authoritative answer |

The domain successfully resolved to **192.232.216.135**, matching the IP address identified by WhatWeb.

**Security Significance:**
DNS resolution helps identify the IP address associated with a domain. This provides basic information about the network location of the website.The response was marked as non-authoritative, meaning the answer was provided by a DNS resolver rather than directly by the domain's authoritative name server.

---

#### Task 4 – Curl
**Command Used:**
```bash
curl -I https://networkwalks.com
```

**Findings:**
The server returned the following response status:

```text
HTTP/2 200 OK
```
The response headers revealed:

- Server: Apache
- `x-nginx-cache: WordPress`
- Referrer-Policy header
- Permissions-Policy header
- Secure and HttpOnly cookie: `__wpdm_client`
- WordPress REST API link: `/wp-json/`

**Security Significance:**
HTTP response headers provide information about server configuration and website technologies. The `/wp-json/` endpoint also identifies a publicly accessible WordPress REST API path that may be reviewed during an authorized security assessment.

---

#### Task 5 – Wafw00f
**Command Used:**
```bash
wafw00f networkwalks.com
```

**Findings:**
After sending two requests, Wafw00f identified the website as being protected by:

**ModSecurity (SpiderLabs) Web Application Firewall**

**Security Significance:**
A Web Application Firewall helps protect websites by filtering and monitoring HTTP traffic.Identifying the WAF provides information about the security controls used by the website. However, detecting a WAF does not confirm that every possible attack is blocked.

---

#### Task 6 – DNSRecon
**Command Used:**
```bash
dnsrecon -d networkwalks.com
```

**Findings:**
DNSRecon identified eight DNS records and provided information about the domain's DNS and email infrastructure.

| Record / Information | Result |
|---|---|
| SOA Record | ns6135.hostgator.com |
| Name Servers | ns6135 and ns6136 |
| DNS Software | BIND 9.16.23-RH |
| Mail Server | mail.networkwalks.com |
| A Record | 192.232.216.135 |
| SPF Record | Detected |
| TXT Record | Google site-verification |
| SRV Records | cPanel email autodiscovery |
| DNSSEC | No DNSSEC answer returned |

**Security Significance:**
DNS records provide useful information about domain hosting, name servers, and email services.The DNSRecon result also showed no DNSSEC answer, which was consistent with the WHOIS result.

---

### 4.2 Network Scanning with Zenmap
In the second activity, I used **Zenmap**, the graphical interface of Nmap, to scan my own home network.
The main objectives were:

- Identify local network details.
- Discover active devices.
- Record available IP and MAC addresses.
- Identify the router manufacturer.
- Generate a network topology map.

#### Step 1 – Identify Network Configuration
I opened Windows 10 Pro Command Prompt and executed:

**Command:**
```cmd
ipconfig
```

**Findings:**
The command displayed multiple network adapters. The adapter connected to my home network showed the following information:

| Network Information | Result |
|---|---|
| Local IP Address | 192.168.1.103 |
| Subnet Mask | 255.255.255.0 |
| Network Subnet | 192.168.1.0/24 |
| Total Address Range | 256 addresses |

The subnet `192.168.1.0/24` was used as the target for the Zenmap scan.

#### Step 2 – Configure Zenmap
I opened Zenmap and entered the following target:
```text
192.168.1.0/24
```

**Selected Profile:** Ping Scan
**Nmap Command:**
```bash
nmap -sn 192.168.1.0/24
```
This scan was used to discover active hosts on my local network.

#### Step 3 – Scan Results
The scan completed in approximately **2.50 seconds** and identified two active hosts out of the 256 addresses in the subnet.

| IP Address | Device | MAC Address | Additional Information |
|---|---|---|---|
| 192.168.1.1 | TP-Link Router | 10:FE:ED:2F:CE:09 | Manufacturer: TP-Link Technologies; latency: 0.021 seconds |
| 192.168.1.103 | DESKTOP-201IGP0 | Not displayed | My own Windows PC |

Nmap identified the router's MAC address as `10:FE:ED:2F:CE:09` and identified its manufacturer as TP-Link Technologies.The MAC address of my own PC was not displayed in the scan output because Nmap does not show the MAC address of the machine running the scan in this result.

#### Step 4 – Generate Network Topology
After completing the scan, I opened the **Topology** tab in Zenmap and enabled the legend.The topology map showed my computer and the router as connected devices.The map was saved on the Desktop with the following filename:

```text
network_topology.pdf
```

**Result:**
The network topology displayed:
- Localhost (my Windows PC) at the centre.
- TP-Link router at `192.168.1.1`.
- DESKTOP-201IGP0 at `192.168.1.103`.
This helped visualize the devices identified during the network discovery process.

---

## 5. Risk Analysis / Impact
Based on the information collected during footprinting and network scanning, I identified the following potential risks and security observations.

| # | Risk / Finding | Evidence / Observation | Potential Impact | Risk Level |
|---|---|---|---|---|
| 1 | CMS and plugin versions visible | WhatWeb detected WordPress 7.1.4 and WP Download Manager 3.3.76. | Attackers may compare exact versions with known vulnerability lists. Outdated software could become an easier target. | Medium |
| 2 | Server IP address identifiable | Nslookup and WhatWeb both showed 192.232.216.135. | Reveals the website's hosting IP and provides a starting point for further reconnaissance. | Low |
| 3 | Public email address exposed | WhatWeb found info@networkwalks.com on the website. | The address may be collected for spam or phishing attempts. | Low |
| 4 | HTTP headers reveal technology | Curl showed Apache, x-nginx-cache: WordPress, and the /wp-json/ API link. | Helps identify the website platform and discover additional endpoints. | Low |
| 5 | WAF technology identifiable | Wafw00f detected ModSecurity (SpiderLabs). | Reveals the type of web protection in use. The WAF itself is a positive security control. | Low |
| 6 | DNS and server details exposed | DNSRecon listed NS, MX, SPF/TXT, and SRV records, along with BIND version 9.16.23-RH. | Provides infrastructure details. The DNS software version can be checked against known security issues. | Medium |
| 7 | DNSSEC not enabled | WHOIS showed DNSSEC as unsigned, and DNSRecon returned no DNSSEC answer. | DNS responses are not digitally signed, which may increase the risk of DNS spoofing under certain conditions. | Low |
| 8 | Registration details publicly visible | WHOIS showed registrar, registration dates, and name servers. Owner details were hidden by a privacy service. | Provides limited domain registration information, while the privacy service protects personal details. | Low |
| 9 | Live hosts and router vendor visible on LAN | Ping scan found two hosts and identified router MAC 10:FE:ED:2F:CE:09 (TP-Link). | Device and manufacturer details help map a network. Unknown devices should always be investigated. | Low |

### Risk Level Key

| Risk Level | Description |
|---|---|
| Critical | A severe security issue that may cause major impact. |
| Medium | A potential security concern that requires review and appropriate action. |
| Low | A limited security observation that should be monitored or reviewed. |

### Risk Assessment Notes
The risks listed above are observations from the footprinting and network scanning activities. They are not confirmed vulnerabilities.The practical exercises mainly involved information gathering and host discovery. No exploitation or vulnerability validation was performed during these modules.The presence of a software version, IP address, or DNS record does not automatically mean that a system is vulnerable. Further authorized security testing would be required to confirm any actual vulnerability.

---

## 6. Recommendations
Based on the results of the activities, the following security improvements are recommended.

### 1. Review Publicly Exposed Technology Information
Keep WordPress core and all installed plugins, including WP Download Manager, updated. Regularly review software versions against current security advisories.

### 2. Limit Exposed Technology Information
Remove unnecessary version details from publicly visible responses. Review whether the `/wp-json/` REST API and server headers need to expose additional information.

### 3. Add HTTP Security Headers
The captured response did not show HSTS, X-Frame-Options, or X-Content-Type-Options.Adding these headers, along with a suitable Content-Security-Policy, can improve browser-side security.

### 4. Enable DNSSEC
Both WHOIS and DNSRecon indicated that DNSSEC was not enabled or that no DNSSEC answer was returned.DNSSEC should be reviewed and enabled through the domain registrar and DNS provider where supported. It helps protect DNS responses against certain types of manipulation.

### 5. Harden DNS and Email Records
Hide unnecessary BIND version information on name servers and review the SPF record, which currently ends in `~all`.Implementing DKIM and DMARC can further improve email authentication and security.

### 6. Protect the Public Email Address
Consider using a contact form or an obfuscated email address instead of displaying `info@networkwalks.com` in plain text. This may help reduce unwanted spam and phishing attempts.

### 7. Keep the WAF Enabled and Properly Configured
ModSecurity was identified during the WAF detection activity.Keep its rules updated, review its configuration, and regularly monitor security logs to identify suspicious activity.

### 8. Monitor and Secure the Local Network
Perform regular internal network scans and investigate unknown devices.Keep router firmware updated, use a strong router password, and maintain an updated network topology document.

### 9. Perform Testing Only with Authorization
Footprinting, reconnaissance, and network scanning must only be performed on systems that are owned by the tester or where written permission has been provided.

---

## 7. Conclusion
During Week 2 of my Cybersecurity & Ethical Hacking Internship at Networkwalks, I completed practical activities related to footprinting, reconnaissance, and network scanning.
For the footprinting activity, I used six Kali Linux tools to gather publicly available information about `networkwalks.com`.

- **WHOIS** provided domain registration information.
- **WhatWeb** identified WordPress 7.1.4, its plugin, and other website technologies.
- **Nslookup** resolved the domain to `192.232.216.135`.
- **Curl** displayed HTTP response headers and the `/wp-json/` endpoint.
- **Wafw00f** detected the ModSecurity Web Application Firewall.
- **DNSRecon** identified DNS, mail, and SRV records.

For the network scanning activity, I used `ipconfig` and Zenmap on my own home network. I scanned the subnet `192.168.1.0/24` and identified two active hosts: the TP-Link router and my Windows PC.I also recorded the router's MAC address and generated a network topology map, which was saved as `network_topology.pdf`.
These activities helped me understand that information gathering is an important first stage of cybersecurity assessment. Public information and network responses can reveal useful details about a system even before any exploitation is attempted.I also learned the importance of proper technical documentation. A good security report should clearly explain the activities performed, the findings, their possible impact, and the recommended improvements.Most importantly, all reconnaissance and scanning activities must remain within an authorized scope. The activities documented in this report were performed as part of an educational assignment, using a target with written permission and my own local network.

---

## 9. Author / Mentor Information

| Field | Details |
|---|---|
| Author / Pentester | Tabia Babar |
| Mentor | Waqas Karim CCIE |
| Designation | Cybersecurity Professional B084C |
| LinkedIn | https://www.linkedin.com/in/waqaskarim/ |
| Organization | Networkwalks |
| Internship Week | Week 02 |
| Batch | B084C |

---
Linkedln: https://lnkd.in/p/dMKD7TzS

**Thank You**

*Prepared as part of the Networkwalks Cybersecurity Internship – Week 02.*

Disclaimer: All network scanning and reconnaissance activities were performed for educational purposes in an authorized testing environment.
