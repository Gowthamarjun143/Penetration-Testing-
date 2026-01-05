# Step 2– Service & OS Detection

Service detection and OS fingerprinting help identify what software, which versions, and which operating system are running on the target.

This directly supports vulnerability assessment by pointing to version-specific security issues.

---

## 2.1  OS Detection Scan (-O)– Using `Nmap`

### **Why This Tool Is Used**
The -O (OS detection) scan attempts to determine the operating system, kernel version, and device type running on the target machine.

This scan helps identify:
 - Validate that the target is Metasploitable 2 (Linux 2.6.x) 
 - Determine kernel-level vulnerabilities
 - Confirm network distance and host type
 - Identify whether the system behaves like a virtual machine

It is essential for planning OS-based exploitation.

---

### **Command**

    sudo nmap -O 192.168.56.102


## Output
    Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-10 15:39 IST
    Nmap scan report for 192.168.56.102
    Host is up (0.00069s latency).
    Not shown: 977 closed tcp ports (reset)
    PORT     STATE SERVICE
    21/tcp   open  ftp
    22/tcp   open  ssh
    23/tcp   open  telnet
    25/tcp   open  smtp
    53/tcp   open  domain
    80/tcp   open  http
    111/tcp  open  rpcbind
    139/tcp  open  netbios-ssn
    445/tcp  open  microsoft-ds
    512/tcp  open  exec
    513/tcp  open  login
    514/tcp  open  shell
    1099/tcp open  rmiregistry
    1524/tcp open  ingreslock
    2049/tcp open  nfs
    2121/tcp open  ccproxy-ftp
    3306/tcp open  mysql
    5432/tcp open  postgresql
    5900/tcp open  vnc
    6000/tcp open  X11
    6667/tMAC vendor indicates a VirtualBox virtual NIC.cp open  irc
    8009/tcp open  ajp13
    8180/tcp open  unknown
    MAC Address: 08:00:27:F0:BB:3D (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
    Device type: general purpose
    Running: Linux 2.6.X
    OS CPE: cpe:/o:linux:linux_kernel:2.6
    OS details: Linux 2.6.9 - 2.6.33
    Network Distance: 1 hop


## Findings
- The target is running Linux Kernel 2.6, consistent with Metasploitable 2.
- Kernel range detected: 2.6.9 – 2.6.33, which is extremely outdated and vulnerable.
- Device type identified as general purpose Linux machine.
- MAC vendor indicates a VirtualBox virtual NIC.
- Network distance: 1 hop, confirming the VM is directly connected on the same virtual network.

## Interpretation
- The OS fingerprint matches Metasploitable 2, confirming correct target identification.
- Linux 2.6 is vulnerable to numerous exploit modules (dirty cow, rds, udev, etc.).
- The presence of many legacy services + outdated kernel indicates a high attack surface.
- Being only one hop away ensures low latency and reliable exploitation conditions.

---

## 2.2 Service Version Detection Scan (-sV) – Using `Nmap`

### **Why This Tool Is Used**
The -sV option identifies service versions running on each open port.
This is critical because exploitation requires version-specific vulnerabilities..

This scan helps identify:
 - Identify vulnerable services (vsftpd 2.3.4, UnrealIRCd, ProFTPD, etc.)
 - Confirm software stacks (Apache 2.2.8, MySQL 5.0.51, Tomcat/Coyote)
 - Prioritize what to exploit first
 - Map services to known CVEs

---

### **Command**

    nmap -sV 192.168.56.102

## Output
    Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-10 15:39 IST
    Nmap scan report for 192.168.56.102
    Host is up (0.00060s latency).
    Not shown: 977 closed tcp ports (reset)
    PORT     STATE SERVICE     VERSION
    21/tcp   open  ftp         vsftpd 2.3.4
    22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
    23/tcp   open  telnet      Linux telnetd
    25/tcp   open  smtp        Postfix smtpd
    53/tcp   open  domain      ISC BIND 9.4.2
    80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
    111/tcp  open  rpcbind     2 (RPC #100000)
    139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
    445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
    512/tcp  open  exec        netkit-rsh rexecd
    513/tcp  open  login       OpenBSD or Solaris rlogind
    514/tcp  open  shell       Netkit rshd
    1099/tcp open  java-rmi    GNU Classpath grmiregistry
    1524/tcp open  bindshell   Metasploitable root shell
    2049/tcp open  nfs         2-4 (RPC #100003)
    2121/tcp open  ftp         ProFTPD 1.3.1
    3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
    5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
    5900/tcp open  vnc         VNC (protocol 3.3)
    6000/tcp open  X11         (access denied)
    6667/tcp open  irc         UnrealIRCd
    8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
    8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
    MAC Address: 08:00:27:F0:BB:3D (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
    Service Info: Hosts: metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

## Findings
Critical vulnerable services detected:

High-risk services
- vsftpd 2.3.4 — contains a backdoor vulnerability (CVE-2011-2523).
- UnrealIRCd — contains a remote command execution backdoor.
- ProFTPD 1.3.1 — vulnerable to mod_copy remote file copy exploit.
- Apache 2.2.8 — outdated, multiple vulnerabilities.
- PostgreSQL 8.3 — privilege escalation vulnerabilities.
- MySQL 5.0.51 — authentication bypass vulnerabilities.
- Tomcat/Coyote JSP engine 1.1 — exploitable manager interface.
- 1524/tcp bindshell — indicates the machine already has a known root shell service.

Other key notes
 - Samba (139/445) identifies outdated configurations vulnerable to multiple exploits.
 - RMI registry running GNU Classpath is historically insecure.
 - Multiple legacy r-services (rsh, rlogin, rexec) present—considered highly insecure.
 - X11 open without authentication indicates remote desktop exposure.

## Interpretation
- The service versions confirm Metasploitable 2 is intentionally misconfigured and full of exploitable services.
- Several services map directly to Metasploit modules, ensuring a straightforward exploitation phase.
- The presence of the bindshell (1524/tcp) already gives direct root access—this is an intentional training vulnerability.
- All detected services contribute to a wide attack surface, making the next phase (Vulnerability Assessment) rich in findings.

---

