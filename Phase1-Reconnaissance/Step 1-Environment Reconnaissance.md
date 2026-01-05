
# Step 1 – Lab Verification

Before starting reconnaissance or vulnerability assessment, it is essential to verify that the **Kali Linux attacker machine** and **Metasploitable 2 target machine** are correctly configured and reachable in the same network.  
This phase ensures correct IP configuration, routing, and ARP table entries.

---

## 1.1 IP Address Verification – Using `ip a`

### **Why This Tool Is Used**
The `ip a` command is used to check:
- Active network interfaces  
- Assigned IP addresses  
- Host-Only network configuration  
- Whether Kali and Metasploitable can communicate  

This confirms the network setup before running any scans.

---

### **Command**

    ip a

## Output
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
    2: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
        link/ether f4:d1:08:05:09:38 brd ff:ff:ff:ff:ff:ff
    inet 10.66.221.19/24 brd 10.66.221.255 scope global dynamic noprefixroute wlan0
       valid_lft 3339sec preferred_lft 3339sec
    inet6 2409:40f4:a1:e1ba:bed8:6adb:c555:c494/64 scope global temporary dynamic 
       valid_lft 6942sec preferred_lft 6942sec
    inet6 2409:40f4:a1:e1ba:f6d1:8ff:fe05:938/64 scope global dynamic mngtmpaddr noprefixroute 
       valid_lft 6942sec preferred_lft 6942sec
    inet6 fe80::f6d1:8ff:fe05:938/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
    3: vboxnet0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
       link/ether 0a:00:27:00:00:00 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.1/24 brd 192.168.56.255 scope global vboxnet0
       valid_lft forever preferred_lft forever
    inet6 fe80::800:27ff:fe00:0/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever

## Findings
- Kali’s Host-Only network interface (vboxnet0) is assigned 192.168.56.1/24
- This confirms the lab network: 192.168.56.0/24
- Metasploitable will also receive an IP in the same network
- Wireless interface (wlan0) is not part of the lab setup

## Interpretation
- ✔ Kali is properly connected to the Host-Only network.
- ✔ Ready to detect and communicate with Metasploitable.

---

## 1.2 Route Table Verification – Using ip route

### **Why This Tool Is Used**
ip route verifies:
- How network traffic is routed
- Whether the Host-Only network is recognized
- If the correct adapter is used for Metasploitable communication

---

### **Command**
    ip route

## Output
    default via 10.66.221.98 dev wlan0 proto dhcp src 10.66.221.19 metric 600 
    10.66.221.0/24 dev wlan0 proto kernel scope link src 10.66.221.19 metric 600 
    192.168.56.0/24 dev vboxnet0 proto kernel scope link src 192.168.56.1 

## Findings
- The 192.168.56.0/24 network is routed through vboxnet0
- Internet traffic goes through wlan0
- No routing conflicts are detected

## Interpretation
- ✔ All packets intended for Metasploitable will travel through the correct interface.
- ✔ Lab network routing is configured correctly.

---

## 1.3 ARP Table Verification – Using arp -a

### **Why This Tool Is Used**
arp -a is used to:
- Check Layer 2 network visibility
- See if any hosts in the local network responded
- Confirm MAC-to-IP resolution

This helps verify whether the target device is detectable on the local subnet.

---

### **Command**
    arp -a

## Output
    _gateway (10.66.221.98) at 42:8f:fd:84:ed:c9 [ether] on wlan0

## Findings
- Only gateway entry exists
- No Host-Only network entries yet (expected)
- Metasploitable will appear once we ping or scan it

## Interpretation
- ✔ ARP table is fresh and clean
- ✔ Kali has not yet interacted with Metasploitable
- ✔ ARP entries will populate automatically during scanning

---

##  Summary – Phase 1 Lab Verification

| Check | Tool Used | Result | Status |
|---|------|------|--------|
| IP Address Verification | ip a | Host-Only IP 192.168.56.1 is active | ✓ Correct |
| Route Table Validation | ip route | 192.168.56.0/24 routed via vboxnet0 | ✓ Correct |
| ARP Table Check | arp -a | Only gateway visible; target will appear later | ⚠ Expected |

---

## Final Conclusion
- ✔ Kali Linux is correctly configured for the penetration testing environment.
- ✔ Host-Only network is functioning correctly


