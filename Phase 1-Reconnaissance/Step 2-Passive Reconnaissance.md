# Step 2 – Passive Reconnaissance  
## WHOIS & DNS Enumeration

Passive reconnaissance helps gather information **without directly interacting with the target system**.  
Even though Metasploitable 2 is a **local machine**, WHOIS & DNS checks are still documented to show reconnaissance methodology.

---

## 2.1  WHOIS Lookup – Using `Whois`

### **Why This Tool Is Used**
The `Whois` command is used to check:
- Identify IP ownership  
- Check whether the target is public or private  
- Understand allocated IP ranges  

Since Metasploitable 2 uses a **private IP (192.168.x.x)**, WHOIS reveals IANA private address space information.

---

### **Command**
    whois 192.168.56.102

## Output

    NetRange:       192.168.0.0 - 192.168.255.255
    CIDR:           192.168.0.0/16
    NetName:        PRIVATE-ADDRESS-CBLK-RFC1918-IANA-RESERVED
    NetType:        IANA Special Use
    Organization:   Internet Assigned Numbers Authority (IANA)
    Comment: These addresses are used in private networks by millions of devices✔ Safe to continue reconnaissance without internet exposure.
             and cannot be routed on the public Internet.

## Findings
- The IP belongs to RFC 1918 Private Address Space.
- It is not a public server but an isolated lab machine.
- Traffic does NOT leave the internal network.

## Interpretation
- ✔ Confirms our target (Metasploitable 2) is inside a private virtual network.
- ✔ Safe to continue reconnaissance without internet exposure.

---

## 2.2 DNS Lookup – Using `nslookup`

### **Why This Tool Is Used**
The `nslookup` command is used to check: 
- Query DNS records
- Check whether the target has any hostname associated

---

### **Command**
    nslookup 192.168.56.102

## Output

    ** server can't find 102.56.168.192.in-addr.arpa: NXDOMAIN

## Findings
- No PTR (reverse DNS) record exists.
- Expected in local/isolated lab networks.

## Interpretation
- ✔ No DNS hostname assigned — normal for Metasploitable.

---

## 2.3 Reverse DNS Lookup – Using `dig -x`

### **Why This Tool Is Used**
The `dig -x` command is used to check: 
- Verify DNS reverse lookup
- Check if any DNS server inside the network has mappings

---

### **Command**
    dig -x 192.168.56.102

## Output

    ;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN
    ;; ANSWER: 0, AUTHORITY: 0

## Findings
- Reverse DNS does not exist.
- Confirms this is an unmanaged private VM, not an enterprise environment.

## Interpretation
- ✔ No DNS information leaked.
- ✔ Typical for private lab targets.

---

## 2.4 Public OSINT Research

## 📌 Metasploitable 2 – Default Services Overview

Metasploitable 2 is an intentionally vulnerable Linux VM designed for penetration testing practice.
It automatically runs several outdated, misconfigured, and vulnerable services.

---

## Key Default Services

-  FTP – vsftpd 2.3.4
     - Contains a backdoor vulnerability
     - Allows remote code execution
-  SSH – OpenSSH 4.7p1
     - Outdated and vulnerable
     - Useful for brute-force or exploit practice
-  Telnet
     - Enabled by default
     - Sends credentials in plaintext
-  Samba (SMB) – Samba 3.0.20
     - Misconfigured anonymous shares
     - Vulnerable to usermap_script exploit
-  HTTP – Apache 2.2.8
  Hosts several intentionally vulnerable web apps:
     - Damn Vulnerable Web Application (DVWA)
     - Mutillidae
     - phpMyAdmin
     - Tiki Wiki CMS
-  Databases
     - PostgreSQL (default creds: postgres:postgres)
     - MySQL (root user with empty password)
-  VNC
     - Weak password: password
-  IRC – UnrealIRCd 3.2.8.1
     - Contains a known backdoor
     - Contains a known backdoor
-  DistCC Service
      - Remote command execution vulnerability
---

## 🏗 Metasploitable 2 Architecture (Summary)

Metasploitable 2 is a 32-bit Linux virtual machine loaded with insecure services.
It is NOT a framework but a purposely weak operating system.

---
## 🔨 Architecture Components

1. Vulnerable Network Services

These provide remote entry points:
   - FTP()
   - SSH
   - Telnet
   - SMB/Samba
   - Databases
   - IRC
   - DistCC
   - HTTP Web pages

2. Vulnerable Web Applications

Includes:
   - DVWA
   - Mutillidae
   - phpMyAdmin
   - Tiki Wiki
   - Other misc vulnerable sites

2. 3. Weak System Configuration
   - Default passwords (e.g., msfadmin:msfadmin)
   - Misconfigured permissions
   - Outdated kernels & packages

## 🖧 Deployment Architecture

Metasploitable 2 is typically used in an isolated penetration testing lab:
  - Running on VirtualBox / VMware
  - Kali Linux as attacker
  - Metasploitable 2 as the target
  - Connected through Host-Only or NAT Network

This ensures:
  - Safety
  - Isolation
  - No Internet Connection
---

## 📌 Summary – Passive Reconnaissance Findings

| Step | Tool  | Result | Status |
|---|------|------|--------|
| WHOIS | WHOIS | Confirmed RFC1918 Private IP | ✓ Expected |
| Reverse DNS | Nslookup | No DNS name found | ✓ Normal |
| Reverse DNS (dig) | dig -x | No PTR record | ✓ Normal |
| Target Info | Google | Identified default services & architecture | 📌 Informational |

---

## ✅ Interpretation (Overall)

- Target is a private, isolated system
- No DNS records exist → good OPSEC
- Services are designed to be exploited later
