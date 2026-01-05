# Step 4 – Active Reconnaissance  
## Host Discovery Using Nmap (Ping Scan)

Host discovery ensures that the target system is **alive and reachable** before performing deeper scans.  
A ping scan (`-sn`) checks host availability without scanning ports.  
This is safer and faster than a full scan.

---

## 4.1  Nmap Ping Scan(-sn) – Using `Nmap`

### **Why This Tool Is Used**
The `nmap -sn` command is used to check:
- Verify whether the target host is **up and responding**
- Perform a **non-intrusive** host check  
- Avoid scanning ports at this stage  
- Confirm network stability before port scanning

---

## **Command**
    sudo nmap -sn 192.168.56.102

## Output 

    Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-09 11:23 IST
    Nmap scan report for 192.168.56.102
    Host is up (0.00055s latency).
    MAC Address: 08:00:27:F0:BB:3D (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
    Nmap done: 1 IP address (1 host up) scanned in 0.30 seconds

## Findings

- Target 192.168.56.102 is alive and reachable.
- MAC address shows vendor:
  - PCS Systemtechnik / Oracle VirtualBox NIC
  - → Confirms this is a VirtualBox VM, consistent with Metasploitable 2.
- Latency is extremely low → indicates a local host-only lab network.

## Interpretation 

- ✔ The target system (Metasploitable 2) is active.
- ✔ The VirtualBox MAC vendor confirms the machine is correctly configured in the host-only virtual environment.
- ✔ No firewall or network filtering is blocking ICMP discovery for this host.
- ✔ We can now safely proceed to Phase 4.2: Port Scanning.

---

## 📌 Step 4 Summary – Host Discovery

| Check | Result | 
|---|------|
| Host Alive? | yes |
| IP Confirmed | 192.168.56.102 | 
| MAC vendor | VirtualboxNIC | 
| Network Latency | very low(vmlocal)

---

## ✅ Overall Interpretation

The ping scan confirms the Metasploitable target is up, active, and ready for deeper scanning.

