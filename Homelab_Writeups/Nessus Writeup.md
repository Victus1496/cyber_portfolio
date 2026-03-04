# Isolated Physical Homelab: Nmap Enumeration & Nessus Vulnerability Management Lifecycle

**Objective**  
Demonstrate end-to-end vulnerability management in a safe, isolated environment: network discovery (Nmap), vulnerability scanning (Nessus), risk remediation, and validation via re-scan.  

This project builds directly on my 4+ years of hands-on defensive cybersecurity experience (Nessus/Tenable scanning, Cisco ISE/FMC troubleshooting, Active Directory hardening, and NIST compliance) in cleared DoD environments.

## Environment & Setup
- **Attacker machine**: Pop!_OS laptop (scanner, analysis, and documentation station)  
- **Target machine**: Raspberry Pi 5 running Raspberry Pi OS (configured with intentional weak services)  
- **Network device**: TRENDnet TEG-S80Dg unmanaged Gigabit switch  
- **Network configuration**: Fully isolated LAN using static IPs (192.168.100.10/24 on attacker, 192.168.100.20/24 on target)  
- **Isolation**: No internet uplink — devices connected only to the switch for safety and realism

## Phase 1 – Target Preparation (Vulnerable Services)
Intentionally configured weak services on the Pi to simulate real misconfigurations:

- Apache web server with directory listing enabled and exposed sensitive test files  
- OpenSSH with root login and password authentication permitted  
- Samba with guest/anonymous read/write share  
- vsftpd FTP with anonymous access enabled

## Phase 2 – Discovery & Enumeration (Nmap)
Performed three Nmap scans from the Pop!_OS attacker machine:

1. **Host discovery**  
   `nmap -sn 192.168.100.0/24`  
   → Confirmed Pi target was alive

2. **Service & OS detection**  
   `nmap -sV -O -T4 192.168.100.20`  
   → Identified open ports (21/ftp, 22/ssh, 80/http, 139/445/smb) and service versions

3. **Vulnerability script scan**  
   `nmap -sC -sV --script vuln 192.168.100.20 -oA pi-vuln-scan`  
   → Runtime ~10–15 minutes; revealed weak authentication, guest access, and directory listing

**Key Nmap Findings**  
- Open SSH with root login and password authentication  
- Apache directory listing enabled  
- Samba guest share with write access  
- Anonymous FTP allowed  

## Phase 3 – Initial Vulnerability Assessment (Nessus)
Ran an unauthenticated Basic Network Scan in Nessus Essentials:

- **Target**: 192.168.100.20  
- **Runtime**: ~13–18 minutes  
- **Total vulnerabilities**: ~35 (mostly INFO)  

**Notable Findings**  
- **High (1)**: Microsoft Windows SMB Shares Unprivileged Access (guest/anonymous share)  
- **Medium (1)**: SMB Signing Not Required (no enforced message signing)  
- **Low (1)**: ICMP Timestamp Request Remote Date Disclosure

## Phase 4 – Remediation
Applied targeted fixes directly on the Pi:

- Samba: Disabled guest access (`guest ok = no`), set read-only, and enforced signing (`server signing = mandatory`)  
- ICMP: Blocked timestamp requests/replies via iptables rules  
- (Optional) SSH: Disabled root login (`PermitRootLogin no`)

## Phase 5 – Validation (Post-Remediation Nessus Scan)
Re-ran the identical Nessus scan after remediation:

- **Runtime**: ~13–18 minutes  
- **Results**:  
  - High vulnerabilities: 1 → 0  
  - Medium vulnerabilities: 1 → 0  
  - Low vulnerabilities: Mitigated/reduced (ICMP timestamp still present — low-risk and common in production Linux systems)  
  - Total vulnerabilities reduced from ~35 → 29 (mostly INFO)

**Before/After Comparison**  
 <img width="1920" height="1080" alt="Screenshot_2026-03-04_10-47-00" src="https://github.com/user-attachments/assets/9c444fc2-bc08-4efb-bb0b-74c1b9e41817" />  <img width="1674" height="829" alt="Screenshot 2026-03-04 at 10-50-20 Nessus Essentials _ Folders _ View Scan" src="https://github.com/user-attachments/assets/645ddde7-3694-4f4b-961d-da766f3d2762" />


- High risks eliminated  
- Medium risks eliminated  
- Overall risk posture improved through targeted hardening

## Key Takeaways & Professional Tie-In
This project demonstrates the full vulnerability management lifecycle I performed daily in cleared DoD environments: identify misconfigurations (Nmap + Nessus), prioritize risks (severity/CVSS), apply remediations, and validate improvements.

It directly applies my experience with Nessus/Tenable scanning, Cisco ISE policy enforcement, FMC firewall troubleshooting, Active Directory hardening, and NIST SP 800-53 compliance.


