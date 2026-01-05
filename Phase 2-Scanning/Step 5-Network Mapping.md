# Step 5 – Network Mapping

Network mapping identifies all domain names, subdomains, or related hosts associated with a target IP address.
While this is primarily used in external reconnaissance, running it in a local lab demonstrates methodology and verifies whether the target is part of a broader network footprint.

---

## 5.1 Network Mapping (Nmap Hostmap Scripts) – Using `Nmap`

### **Why This Tool Is Used**
hostmap-* Nmap scripts are used to:
 - Enumerate domain names associated with the IP (DNS pivoting)
 - Identify potential attack surfaces (multiple domains on one host)
 - Detect shared hosting environments
 - Perform passive reconnaissance using external APIs (Robtex, etc.)
This helps attackers or analysts understand the target’s network exposure and associated assets.

---

### **Command**

    sudo nmap --script=hostmap-* 192.168.56.102

## Output
    Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-10 15:59 IST
    Pre-scan script results:
    |_hostmap-robtex: *TEMPORARILY DISABLED* due to changes in Robtex's API. See https://www.robtex.com/api/
    Nmap scan report for 192.168.56.102
    Host is up (0.00071s latency).
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
    MAC Address: 08:00:27:F0:BB:3D (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

    Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds

    
## Findings
- Hostmap scripts did not detect any associated domains or subdomains.
- hostmap-robtex script failed due to external API deprecation (normal for offline labs).
- The scan enumerated open TCP ports as part of default Nmap behavior, but no domain associations were identified.
- Confirms that the target is purely an internal/local IP with no external DNS mappings.

## Interpretation
- The host does not belong to any public network footprint, confirming that Metasploitable 2 is isolated within the internal lab environment.
- No subdomains, hostnames, or external associations were discovered, which is expected for a private RFC1918 address.
- The absence of hostmap results indicates:
  - No domain‑based attack vectors
  - No shared hosting information
  - No DNS‑based reconnaissance surface
This validates the network’s controlled environment and reinforces that all further scanning and exploitation remain within the safe, local lab.

---

