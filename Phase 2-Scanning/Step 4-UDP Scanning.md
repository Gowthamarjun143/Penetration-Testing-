# Step 4 – UDP Scanning

UDP scanning is performed to identify services that run over the UDP protocol.
Many critical services such as DNS, DHCP, TFTP, SNMP, and NFS communicate through UDP and may expose vulnerabilities that do not appear during TCP-based scans.

UDP scanning is slower and more resource-intensive because UDP has no handshake or response reliability, but it is essential for discovering services missed by TCP scanning.

---

## 4.1  UDP Scan (-sU)– Using `Nmap`

### **Why This Tool Is Used**
nmap -sU is used to:
  - Detect open or open|filtered UDP ports
  - Identify stateless services that respond only with application data
  - Reveal TFTP, DNS, RPC, or NFS services that may contain misconfigurations
  - Provide a more complete network service profile for vulnerability assessment

This is a key step in discovering attack surfaces not visible through TCP scans.

---

### **Command**

    sudo nmap -sU 192.168.56.102

## Output
    Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-10 15:56 IST
    Nmap scan report for 192.168.56.102
    Host is up (0.00065s latency).
    Not shown: 993 closed udp ports (port-unreach)
    PORT     STATE         SERVICE
    53/udp   open          domain
    68/udp   open|filtered dhcpc
    69/udp   open|filtered tftp
    111/udp  open          rpcbind
    137/udp  open          netbios-ns
    138/udp  open|filtered netbios-dgm
    2049/udp open          nfs
    MAC Address: 08:00:27:F0:BB:3D (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

    Nmap done: 1 IP address (1 host up) scanned in 1029.10 seconds

    
## Findings
- 53/udp open → DNS service running (BIND 9 from previous scans); vulnerable DNS configurations common in Metasploitable.
- 68/udp open|filtered → DHCP client service visible; usually indicates system is listening for DHCP offers.
- 69/udp open|filtered → TFTP service; known for no authentication and frequently exploited for file retrieval/upload.
- 111/udp open → rpcbind service exposed; often associated with NFS and RCE vulnerabilities.
- 137/udp open → NetBIOS Name Service; legacy Windows/Linux Samba interaction, commonly misconfigured.
- 138/udp open|filtered → NetBIOS Datagram Service.
- 2049/udp open → NFS service; high‑risk if exported directories are world-readable.
UDP ports marked open|filtered mean Nmap cannot confirm due to no response, but they are not closed (possible service present).

## Interpretation
- The system exposes multiple high‑risk UDP services, many of which are intentionally vulnerable on Metasploitable 2.
- DNS (53), rpcbind (111), and NFS (2049) are clear indicators of exploitable configurations often used in privilege escalation.
- TFTP (69) and NetBIOS (137/138) indicate weak legacy protocols with poor authentication.
- This UDP footprint is consistent with a deliberately insecure system and confirms multiple avenues for exploitation (TFTP misconfigurations, NFS export leaks, RPC enumeration, etc.).
This scan completes the discovery of the full attack surface beyond TCP, allowing you to transition into vulnerability assessment and exploitation.

