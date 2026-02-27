
# TryHackMe: Billing Room Write-Up
 
**Difficulty:** Easy / Medium  
**Date Completed:** January 12, 2026  
**Machine IP:** 10.64.159.54  
**AttackBox IP:** 10.64.70.22 (tun0)

## Overview
This room targets a vulnerable **MagnusBilling** VoIP billing application exposing an **unauthenticated Remote Code Execution** vulnerability (CVE-2023-30258).  
The goal is to gain initial access via Metasploit, stabilize the shell, and retrieve both user and root flags.

**Reconnaissance & Enumeration**
Started with standard Nmap scans to identify open ports and services.

**Full port scan with version & default scripts:**
First
nmap -sC -sV -p- --open 10.64.159.54

Then
nmap -sC -sV -p22,80,3306,5038 10.64.159.54

<img width="1905" height="741" alt="Screenshot 2026-01-12 091350" src="https://github.com/user-attachments/assets/28361f8a-c38f-4c39-a290-d0c70b13f4a9" />

**Open ports & services found**

22 → SSH
80 → HTTP (MagnusBilling web application)
3306 → MariaDB (unauthenticated access possible)
5038 → Asterisk AMI

The web application on port 80 was the clear target — quick research confirmed it was vulnerable to CVE-2023-30258 (Unauthenticated RCE).

<img width="1907" height="1390" alt="Screenshot 2026-01-12 091805" src="https://github.com/user-attachments/assets/16b93952-7028-44ec-8e06-d00367591cef" />

**Exploitation**
Tool used: Metasploit Framework

Launched msfconsole
Searched for the vulnerability using
search magnus command in msf

I selected the matching module
exploit/linux/http/magnusbilling_unauth_rce_cve_2023_30258

I configured options (RHOSTS, LHOST, payload, etc.)
Then ran the exploit
<img width="1907" height="1492" alt="Screenshot 2026-01-12 091851" src="https://github.com/user-attachments/assets/dcdfe66e-bd57-4755-ad38-af2ac2dd2db9" />


**Shell Stabilization**
Upgraded the basic reverse shell with

python3 -c 'import pty; pty.spawn("/bin/bash")'

**Privilege Escalation & Flag Collection**
Checked sudo privileges early:
sudo -l
Output showed:
text(ALL) NOPASSWD: /usr/bin/fail2ban-client
While fail2ban was present (asterisk-iptables jail), the path to root was straightforward in this instance.
Flags retrieved:
Bash# User flag
cat /home/asterisk/user.txt

**Root flag**
cd /root && cat root.txt
Key Takeaways & Lessons Learned

Metasploit's search command is incredibly useful, search magnus instantly found the correct module.
Always look up CVEs for unfamiliar web applications, many VoIP/billing platforms have had critical unauthenticated RCEs.
TryHackMe instance connectivity issues are common, if an exploit fails consistently, reset the machine or wait.
Stabilize reverse shells immediately (pty.spawn) , makes everything much smoother.
Run sudo -l right after getting a shell, even seemingly innocent sudo rights can be powerful.
First real CTF success feels amazing.
