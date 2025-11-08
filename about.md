# About Me

**Miguel  Torres ** — security enthusiast, pentester and CTF player.

## Short bio
I focus on web application security, Active Directory exploitation, and offensive automation.  
I enjoy solving CTFs, building reproducible write-ups, and creating small tools and scripts to speed up routine tasks during engagement and research.

- Location: Colombia (available for remote collaboration / freelance)  
- Interests: AD/kerberos abuse, web security (XSS, SQLi, XXE, SSTI), DFIR basics, pentest automation, and teaching/mentoring.

## Contact
- GitHub: https://github.com/Abionit  
- Email: mitorres@utb.edu.co   
- LinkedIn: linkedin.com/in/ 
-

> Note: I share retired HTB writeups and PoCs for educational purposes only. Do not reuse published exploits against live/production systems.

## Tools I use (expanded)

Below is a categorized, practical list of tools I use for web pentesting, AD/Windows exploitation, reconnaissance and post-exploitation. This is my typical toolkit — I don’t use every item on every engagement, but these are the go-to tools I rely on.

### Web / Application Testing
- **Burp Suite Professional** (Proxy, Repeater, Intruder, Scanner, Collaborator)  
  - Burp extensions: *Hackvertor, Logger++, Turbo Intruder, HTTP Request Smuggler, Param Miner, DOM Invader, Java Deserialization Scanner, JWT Editor, SAML Raider, Burp Bounty Pro, Autorize, Taborator*
- **OWASP ZAP**  
- **sqlmap**  
- **ffuf**, **gobuster**, **wfuzz**, **dirsearch**, **kiterunner** (directory / API fuzzing)  
- **Nikto**, **acunetix**, **Nuclei** (templated scanning)  
- **curl** / **httpie** (crafting and testing HTTP requests)  
- **Burp Collaborator** (OOB testing, SSRF, blind injection detection)

### Reconnaissance & OSINT
- **nmap**, **masscan** (network discovery)  
- **amass**, **subfinder**, **sublist3r**, **assetfinder** (subdomain enumeration)  
- **crt.sh**, **Censys**, **Shodan**, **VirusTotal** (certificate / internet asset discovery)  
- **theHarvester**, **hunter.io**, **whois**, **Maltego** (email/OSINT)  
- **waybackurls**, **gau**, **wayback-machine tools** (historic content discovery)  
- **dnsrecon**, **dnsenum**, **subjack** (DNS & takeover checks)  
- **Recon-ng** (recon framework)

### Scanning & Vulnerability Discovery
- **Nessus**, **OpenVAS** (vulnerability scanning)  
- **sslyze**, **testssl.sh** (TLS/SSL auditing)  
- **Nuclei** (fast templated vulnerability scanner)  
- **Wappalyzer**, **WhatWeb** (fingerprinting)

### Cloud & Code / Secret Discovery
- **awscli**, **gcloud**, **az cli** (cloud enumeration & testing)  
- **truffleHog**, **Gitleaks**, **git-secrets** (secret discovery in repos)  
- **Cloud recon**: **Pacu** (AWS), **Prowler** (security auditing)

### AD / Windows / Kerberos & Enterprise
- **Impacket** (GetUserSPNs.py, secretsdump.py, wmiexec.py, smbexec)  
- **BloodHound / RustHound-CE** (AD graphing & path analysis)  
- **Kerberoast / Rubeus** (Kerberos tools)  
- **mimikatz**, **PowerSploit**, **winrm / evil-winrm** (post-exploitation / remoting)  
- **SharpHound**, **ADExplorer** (AD enumeration & forensics)

### Cracking & Crypto
- **John the Ripper (Jumbo)**, **Hashcat**, **office2john.py**, **office2hashcat**  
- **hashcat-utils**, **oclHashcat** (GPU cracking workflows)  
- **openssl** (certs & crypto operations)

### Forensics / DPAPI / Secrets
- **dpapick**, **dpapi.py (Impacket)**, **mimikatz** (DPAPI / credential extraction)  
- **volatility**, **strings**, **binwalk** (forensics & binary analysis)

### Post-Exploitation & Lateral Movement
- **Cobalt Strike** (lab/private testing), **Beacon** workflows (when permitted)  
- **Metasploit** (exploit dev / quick PoCs)  
- **PowerShell Empire** (where allowed)  
- **smbclient**, **smbmap**, **crackmapexec** (SMB / lateral movement)

### Utilities & Scripting
- **Python3**, **Go**, **Bash**, **PowerShell** (scripting)  
- **jq**, **sed**, **awk**, **nc / ncat**, **socat** (data parsing & piping)  
- **ssh**, **scp**, **rsync**, **docker** (ops & transport)

### Reporting & Automation
- **mkdocs / mkdocs-material** (docs & static site for writeups)  
- **pandoc**, **asciinema** (recorded sessions), **obs** (video/screenshots)  
- CI: **GitHub Actions** for automated site deploys

---

## Repositories & resources
-  writeups: `https://github.com/Abionit/writeups`  


---

If you want, I can:
- produce a bilingual (EN/ES) About page, or  
- add a compact contact card (badges for email, GitHub, LinkedIn) ready to paste into your README.
Which would you prefer?
