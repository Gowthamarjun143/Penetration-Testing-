
# Step 1 – Port Scanning

  Port scanning is a core part of the scanning phase and helps identify all live services, communication endpoints, and potential entry points on the target system.

---

## 1.1 Full TCP Port Scan – Using `Nmap`

### **Why This Tool Is Used**
The nmap -p- -sS command is used to perform a full TCP port sweep across all 65,535 ports.
This scan helps identify:
 - All open TCP ports on the target  
 - Hidden or non-standard services running on high ports
 - Services that may not appear in a basic scan
 - The full attack surface before enumeration  

This ensures that no TCP port is missed, and the analyst has complete visibility of reachable services.

---

### **Command**

    sudo nmap -p- -sS 192.168.56.102

## Output
    Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-10 15:29 IST
    Nmap scan report for 192.168.56.102
    Host is up (0.00067s latency). 
    Not shown: 65505 closed tcp ports (reset)
    PORT      STATE SERVICE
    21/tcp    open  ftp
    22/tcp    open  ssh
    23/tcp    open  telnet
    25/tcp    open  smtp
    53/tcp    open  domain
    80/tcp    open  http
    111/tcp   open  rpcbind
    139/tcp   open  netbios-ssn
    445/tcp   open  microsoft-ds
    512/tcp   open  exec
    513/tcp   open  login
    514/tcp   open  shell
    1099/tcp  open  rmiregistry
    1524/tcp  open  ingreslock 
    2049/tcp  open  nfs
    2121/tcp  open  ccproxy-ftp
    3306/tcp  open  mysql
    3632/tcp  open  distccd
    5432/tcp  open  postgresql
    5900/tcp  open  vnc
    6000/tcp  open  X11
    6667/tcp  open  irc
    6697/tcp  open  ircs-u
    8009/tcp  open  ajp13
    8180/tcp  open  unknown
    8787/tcp  open  msgsrvr
    36939/tcp open  unknown
    38792/tcp open  unknown
    47180/tcp open  unknown
    54496/tcp open  unknown
    MAC Address: 08:00:27:F0:BB:3D (PCS Systemtechnik/Oracle VirtualBox virtual NIC)


## Findings
- A large number of ports are open, confirming a highly exposed attack surface.
- Both default services and high-port custom/unknown services are running.
- Key services identified:
  - Remote access: ssh, telnet, exec, login, shell, vnc
  - Web services: http, ajp13, port 8180
  - Databases: mysql (3306), postgresql (5432)
  - File/Network protocols: ftp, smb (139,445), nfs, rpcbind
  - Developer/legacy services: rmiregistry, distccd
  - IRC services: 6667, 6697
- Multiple high ports (>30000) are open, suggesting backend processes or misconfigured applications.

This confirms Metasploitable 2 is intentionally vulnerable.

## Interpretation
- The target is hosting many insecure services, increasing exploitation options.
- The presence of:
  - Telnet (23),
  - VNC (5900),
  - RMI Registry (1099),
  - DistCCD (3632),
  - NFS (2049),
  - Shell services (512, 513, 514),
    shows severe misconfigurations and historically vulnerable services.
-  Large exposure across the port range indicates no firewall or filtering, meaning the system is directly accessible.

---

## 1.2 TCP Port SYN Scan – Using `Nmap`

### **Why This Tool Is Used**
The nmap -sS command is used to run a stealth SYN scan on the most common 1,000 ports.
This scan helps identify:
 - Key open TCP services
 - Commonly exposed applications (HTTP, FTP, SSH, SMB, etc.)
 - Quick confirmation that the host is alive and responding
 - Initial visibility of services before deeper enumeration

This scan provides a fast and reliable overview of the target’s primary services while generating minimal network noise.

---

### **Command**

    sudo nmap -sS 192.168.56.102


## Output
    Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-10 15:30 IST
    Nmap scan report for 192.168.56.102
    Host is up (0.00054s latency).
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
    6667/tcp open  irc
    8009/tcp open  ajp13
    8180/tcp open  unknown

## Findings
- Confirms the same major services as the full port scan.
- No additional ports (only the top 1000 were checked).
- Confirms stability of detected services.
- These services represent the most exploitable entry points.

## Interpretation
- The host remains consistently reachable with low latency, confirming a stable lab.
- The dangerous combination of:
  - Anonymous FTP
  - Telnet
  - RMI Registry 
  - DistCCD (From full scan),
  - VNC
  - SMB
  - Outdated Web Services
    indicates multiple vulnerabilities across network, web, and system-level services.
-  This scan validates the initial results and is ready for service version detection and vulnerability scanning.

---

