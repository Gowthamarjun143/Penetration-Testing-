
# Step 3–Basic Enumeration

Enumeration is performed to extract detailed information from discovered services, such as versions, users, shares, and configurations. 

It helps identify potential attack vectors by revealing how each service operates and where weaknesses may exist.

---

## 3.1 HTTP Service Fingerprinting -using(WhatWeb + cURL)

### **Why This Tool Is Used**
To identify web technologies, frameworks, and versions running on the target web server and detect potentially vulnerable components.
 - WhatWeb: Detects CMS, plugins, server software, and web frameworks.
 - cURL: Fetches raw HTTP headers to examine server info like Apache version, PHP version, and security headers.

---

### **Command**

    whatweb 192.168.56.102
    curl -I http://192.168.56.102


## Output
    WhatWeb:
    192.168.56.102 [ Unassigned ]

    cURL:
    HTTP/1.1 200 OK
    Server: Apache/2.2.8 (Ubuntu) DAV/2
    X-Powered-By: PHP/5.2.4-2ubuntu5.10
    Content-Type: text/html


## Findings
- Server runs Apache 2.2.8 and PHP 5.2.4.
- Minimal signatures detected by WhatWeb.
- Exposed headers reveal outdated web technologies.

## Interpretation
- The web server is vulnerable due to outdated Apache and PHP versions.
- Can serve as a starting point for exploiting known vulnerabilities (RCE, XSS, SQLi in web apps).

---

## 3.2 Nmap Default Script + Version Scan (-sC -sV) – Using `Nmap`

### **Why This Tool Is Used**
To enumerate running services, detect versions, and run default NSE scripts for vulnerability hints.
This scan helps identify:
 - Identifies open ports and service versions.
 - NSE scripts detect misconfigurations and potential vulnerabilities automatically.
 - Provides detailed service-level enumeration useful for later exploitation.

---

### **Command**
    sudo nmap -sC -sV 192.168.56.102


## Output
    Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-10 15:46 IST
    Nmap scan report for 192.168.56.102
    Host is up (0.00071s latency).
    Not shown: 977 closed tcp ports (reset)
    PORT     STATE SERVICE     VERSION
    21/tcp   open  ftp         vsftpd 2.3.4
    |_ftp-anon: Anonymous FTP login allowed (FTP code 230)
    | ftp-syst:
    |   STAT:
    | FTP server status:
    |      Connected to 192.168.56.1
    |      Logged in as ftp
    |      TYPE: ASCII
    |      No session bandwidth limit
    |      Session timeout in seconds is 300
    |      Control connection is plain text
    |      Data connections will be plain text
    |      vsFTPd 2.3.4 - secure, fast, stable
    |_End of status
    22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
    | ssh-hostkey:
    |   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
    |_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
    23/tcp   open  telnet      Linux telnetd
    25/tcp   open  smtp        Postfix smtpd
    |_ssl-date: 2025-12-10T10:16:56+00:00; 0s from scanner time.
    |_smtp-commands: metasploitable.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
    | sslv2:
    |   SSLv2 supported
    |   ciphers:
    |     SSL2_DES_64_CBC_WITH_MD5
    |     SSL2_RC4_128_WITH_MD5
    |     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
    |     SSL2_DES_192_EDE3_CBC_WITH_MD5
    |     SSL2_RC4_128_EXPORT40_WITH_MD5
    |_    SSL2_RC2_128_CBC_WITH_MD5
    | ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
    | Not valid before: 2010-03-17T14:07:45
    |_Not valid after:  2010-04-16T14:07:45
    53/tcp   open  domain      ISC BIND 9.4.2
    | dns-nsid:
    |_  bind.version: 9.4.2
    80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
    |_http-server-header: Apache/2.2.8 (Ubuntu) DAV/2
    |_http-title: Metasploitable2 - Linux
    111/tcp  open  rpcbind     2 (RPC #100000)
    | rpcinfo:
    |   program version    port/proto  service
    |   100000  2            111/tcp   rpcbind
    |   100000  2            111/udp   rpcbind
    |   100003  2,3,4       2049/tcp   nfs
    |   100003  2,3,4       2049/udp   nfs
    |   100005  1,2,3      36939/tcp   mountd
    |   100005  1,2,3      50950/udp   mountd
    |   100021  1,3,4      46427/udp   nlockmgr
    |   100021  1,3,4      47180/tcp   nlockmgr
    |   100024  1          54496/tcp   status
    |_  100024  1          57826/udp   status
    139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
    445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
    512/tcp  open  exec        netkit-rsh rexecd
    513/tcp  open  login
    514/tcp  open  shell       Netkit rshd
    1099/tcp open  java-rmi    GNU Classpath grmiregistry
    1524/tcp open  bindshell   Metasploitable root shell
    2049/tcp open  nfs         2-4 (RPC #100003)
    2121/tcp open  ftp         ProFTPD 1.3.1
    3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
    | mysql-info:
    |   Protocol: 10
    |   Version: 5.0.51a-3ubuntu5
    |   Thread ID: 19
    |   Capabilities flags: 43564
    |   Some Capabilities: LongColumnFlag, Support41Auth, SupportsTransactions, SwitchToSSLAfterHandshake, Speaks41ProtocolNew, ConnectWithDatabase, SupportsCompression
    |   Status: Autocommit
    |_  Salt: vPjHfK$o$dK*0O!Tbr6T
    5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
    | ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
    | Not valid before: 2010-03-17T14:07:45
    |_Not valid after:  2010-04-16T14:07:45
    |_ssl-date: 2025-12-10T10:16:56+00:00; 0s from scanner time.
    5900/tcp open  vnc         VNC (protocol 3.3)
    | vnc-info:
    |   Protocol version: 3.3
    |   Security types:
    |_    VNC Authentication (2)
    6000/tcp open  X11         (access denied)
    6667/tcp open  irc         UnrealIRCd
    8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
    |_ajp-methods: Failed to get a valid response for the OPTION request
    8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
    |_http-favicon: Apache Tomcat
    |_http-server-header: Apache-Coyote/1.1
    |_http-title: Apache Tomcat/5.5
    MAC Address: 08:00:27:F0:BB:3D (Oracle VirtualBox)

    Host script results:
    |_smb2-time: Protocol negotiation failed (SMB2)
    | smb-security-mode:
    |   account_used: guest
    |   authentication_level: user
    |   challenge_response: supported
    |_  message_signing: disabled (dangerous, but default)
    |_clock-skew: mean: 1h15m00s, deviation: 2h30m00s, median: 0s
    |_nbstat: NetBIOS name: METASPLOITABLE
    | smb-os-discovery:
    |   OS: Unix (Samba 3.0.20-Debian)
    |   Computer name: metasploitable
    |   Domain name: localdomain
    |_  System time: 2025-12-10T05:16:48-05:00

    Service detection performed.
    Nmap done: 1 IP address (1 host up) scanned in 21.12 seconds

    
## Findings
- FTP allows anonymous login, exposing file contents.
- SSH, SMTP, BIND DNS, Apache, Samba, MySQL, and PostgreSQL are all outdated.
- Multiple insecure services running:
  - Telnet (cleartext)
  - VNC with weak authentication
  - Shell services (rlogin/rsh)
  - Metasploitable bind shell (port 1524)
- Apache Tomcat and RPC/NFS services accessible externally.
- Samba message signing disabled → susceptible to MiTM attacks.


## Interpretation
- The target host is running numerous vulnerable and misconfigured services, many of which are intentionally left insecure for exploitation.
- Service version detection reveals:
  - Weak or deprecated protocols
  - Outdated server versions with public exploits
  - Misconfigurations such as anonymous access and disabled security features
- The scan provides a clear attack surface for the next phases:
  - Vulnerability Assessment
  - Exploitation

---
