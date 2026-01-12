So I attempted my first CTF this week, I attempted it last week but kept getting the "Failed to connect to server" error, so I gave it the weekend and attempted again today. 

Target Info
Machine: Billing (MagnusBilling application)
Target IP: 10.64.159.54
AttackBox IP (LHOST): 10.64.70.22 (tun0 interface)
Vulnerable service: MagnusBilling web app on port 80 (CVE-2023-30258 – unauthenticated RCE)
Other open ports: 22 (SSH), 3306 (MariaDB unauthorized), 5038 (Asterisk AMI)

I started with a couple of NMAP scans.
First I conducted a full port scan with version detection using nmap -sC -sV -p- --open 10.64.159.54

Then I conducted a targeted quick scan on the open ports
nmap -sC -sV -p22,80,3306,5038 10.64.159.54


Next I had to ID the exploit so I ran msfconsole and used search magnus
It then gave me the relevant module  exploit/linux/http/magnusbilling_unauth_rce_cve_2023_30258

I loaded the module to inspect details and confirmed that it targets the exact vulnerability in MagnusBilling.

This is about as far as I got on Thursday before running into server issues, so I repeated the steps Monday when I was back at my terminal.

When I retried I tried again and boom instant success.

I stabilized the shell with shell, python3 -c 'import pty; pty.spawn("bin/bash")'

Privelege escalation
then sudo -l (ALL) NOPASSWD: /usr/bin/fail2ban-client
sudo fail2ban-client status 

confirmed
asterisk-iptables jail

Flags were found at 

cd /root && cat root.txt
cat /home/asterisk/user.txt

key takeaways
Metasploits search command is a useful tool for finding relevant exploits by app name
