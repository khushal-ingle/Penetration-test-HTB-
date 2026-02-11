
# 1. What is IPMI ?

**Intelligent Platform Management Interface (IPMI)** is a hardware-based server management protocol that allows administrators to monitor and control systems independently of the operating system.

It works even when:

* The system is powered off
* The OS is crashed
* The server is unresponsive

IPMI communicates directly with the system hardware through a dedicated controller called the **Baseboard Management Controller (BMC)**.

It does not require:

* OS login access
* SSH access
* A running operating system

---

# 2. Key Features

IPMI allows administrators to:

* Power on / power off systems remotely
* Reboot servers
* Access remote console
* Modify BIOS settings
* Mount remote ISO images
* Reinstall operating systems
* View hardware logs
* Monitor:

  * Temperature
  * Voltage
  * Fan speed
  * Power supply status

Important:
Even if the host is powered down, the IPMI module must remain connected to power and LAN.

---

# 3. Core Components

IPMI requires:

* **Baseboard Management Controller (BMC)** – Main management microcontroller
* **IPMB (Intelligent Platform Management Bus)** – Internal communication
* **ICMB (Intelligent Chassis Management Bus)** – Chassis-to-chassis communication
* **IPMI Memory** – Stores logs and system event records
* **Communication Interfaces** – LAN, Serial, PCI

Most BMCs are embedded Linux systems running on ARM architecture.

---

# 4. Common BMC Implementations (Pentest Relevant)

Frequently seen during internal penetration testing:

* **HP iLO**
* **Dell iDRAC**
* **Supermicro IPMI**

If a BMC is compromised, it is nearly equivalent to physical access to the server.

An attacker can:

* Power off critical systems
* Reinstall OS
* Access console as root
* Perform full system takeover

---

# 5. Network Details

Default Port:

```
UDP 623
```

Service Name:

```
asf-rmcp
```

IPMI Version:

* Commonly IPMI 2.0

---

# 6. Enumeration Notes (Pentesting)

## Nmap Scan

```bash
sudo nmap -sU -p 623 --script ipmi-version <target>
```

Expected Output:

* Confirms port 623 open
* Displays IPMI version
* Shows authentication methods

---

## Metasploit Version Scan

```bash
use auxiliary/scanner/ipmi/ipmi_version
set rhosts <target>
run
```

Identifies:

* IPMI version
* Supported authentication
* Protocol level

---

# 7. Default Credentials (High Priority Check)

| Product         | Username      | Password                     |
| --------------- | ------------- | ---------------------------- |
| Dell iDRAC      | root          | calvin                       |
| HP iLO          | Administrator | Random 8-character (factory) |
| Supermicro IPMI | ADMIN         | ADMIN                        |

Default credentials are very common findings during internal assessments.

---

# 8. Critical Vulnerability – RAKP Hash Disclosure

IPMI 2.0 has a design flaw in the RAKP authentication process.

Issue:

* During authentication, the server sends a salted SHA1/MD5 hash of the password.
* This happens before authentication completes.

Impact:

* Anyone on the network can request the hash of any valid user.
* Hashes can be cracked offline using tools like Hashcat (mode 7300).

---

## Dumping Hashes (Metasploit)

```bash
use auxiliary/scanner/ipmi/ipmi_dumphashes
set rhosts <target>
run
```

If hash is captured:

* Save it
* Crack offline using Hashcat

Example:

```bash
hashcat -m 7300 hash.txt wordlist.txt
```

---

# 9. Why IPMI Is Critical in Internal Pentests

Common real-world findings:

* Default credentials left unchanged
* Weak passwords reused across servers
* Cracked BMC password reused for:

  * SSH
  * Root accounts
  * Monitoring tools
  * Database servers

Impact:

* Full infrastructure compromise
* Lateral movement
* Remote OS reinstall
* Denial of Service

IPMI findings are typically classified as Critical severity.

---

# 10. Mitigation & Hardening Notes

There is no direct fix for the RAKP flaw (design limitation).

Recommended Controls:

* Use long, complex passwords
* Disable unused accounts
* Restrict IPMI access via firewall
* Place BMCs in a separate management VLAN
* Disable IPMI if not required
* Monitor login attempts

---
