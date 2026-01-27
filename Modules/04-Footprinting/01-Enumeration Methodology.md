# Enumeration Methodology (6-Layer Approach)

Enumeration is one of the most important phases of penetration testing. Since real-world environments are complex and unpredictable, penetration testers need a **standardized methodology** to avoid missing key targets, services, or vulnerabilities.

This methodology divides enumeration into **6 logical layers**, like boundaries or walls that we try to cross step-by-step until we reach deeper access.

---

# Why This Methodology is Needed

Penetration testing is a **dynamic process**,

meaning:

- Every target behaves differently
- Attack paths change based on discoveries
- New entry points appear during testing

So instead of random scanning, this layered approach provides:

 - Structure  
 - Coverage  
 - Efficiency  
 - Adaptability

---

# Enumeration Levels

Enumeration is divided into 3 broad levels:

| Level                                | Focus                                       |
| ------------------------------------ | ------------------------------------------- |
| **Infrastructure-based Enumeration** | External footprint (domains, IPs, gateways) |
| **Host-based Enumeration**           | Services running on hosts                   |
| **OS-based Enumeration**             | Internal OS and privilege-level information |
---
>Note: The items listed in each enumeration layer are only the main things to look for, not everything. Also, the first two layers (Internet Presence and Gateway) mostly apply to external testing and do not fully apply to internal networks like Active Directory. The enumeration of internal infrastructure is explained separately in other modules.
---
## Layer 1: Internet Presence

### Goal:
Identify all externally visible assets of the company.
### What we enumerate:

> Domains, Subdomains, Virtual Hosts (vHosts), ASN (Autonomous System Number), Netblocks, Public IP addresses, Cloud instances, External security measures
### Why important:
>This layer defines the **scope of targets** we can test.

---
## Layer 2: Gateway
### Goal:
Understand how the organization protects its infrastructure.
### What we identify:

>Firewalls,  DMZ zones, IDS/IPS systems, EDR solutions, Proxies, NAC controls, Network segmentation, VPN access, Cloudflare or WAF protection
### Why important:
Before exploitation, we must know what defenses exist.

---
## Layer 3: Accessible Services

### Goal:
Discover all reachable services and interfaces.
### Information collected:

>Service type (HTTP, SMB, FTP, SSH),  Port number, Software version, Configuration, Functionality, Interfaces exposed
### Why important:
Most vulnerabilities come from misconfigured or outdated services.

---
## Layer 4: Processes

### Goal:
Understand what happens internally when services run.
### What we enumerate:

>Running processes (PID),  Data being processed, Scheduled tasks, Source and destination of traffic, Service dependencies
### Why important:
Services often interact with databases, APIs, or internal networks.

---
## Layer 5: Privileges

### Goal:
Identify permissions and privilege boundaries.
### What we check:

 >Users and groups,  Permissions and restrictions, Role assignments, Environment limitations
### Why important:
Even small privileges can lead to escalation.

---
## Layer 6: OS Setup

### Goal:
Gain full understanding of the target operating system setup.
### What is collected:

>OS type and version,  Patch level, Network configuration, Configuration files, Sensitive internal files, OS environment details
### Why important:
This reflects the organization’s internal security posture.

---
# Key Concept: Layers as Walls

Each layer is like a **wall** in front of the attacker.
We don’t blindly break through

### we search for:

- Entrances
- Weak points
- Misconfigurations
- Vulnerability gaps

Not every vulnerability leads deeper, but one correct gap often does.

---
# Summary Table

| Layer | Name                | Main Goal                      |
| ----- | ------------------- | ------------------------------ |
| 1     | Internet Presence   | Find everything about the company that is visible on the internet    |
| 2     | Gateway             | Understand how the company protects its network from outside access  |
| 3     | Accessible Services | Find which services are running and can be accessed     |
| 4     | Processes           | Analyze service-related internal processes, tasks, and data flow  |
| 5     | Privileges          | Identification of the internal permissions and privileges to the accessible services |
| 6     | OS Setup            | Collect information about the operating system and its configuration    |

---
# Summary

> Enumeration was performed using a 6-layer methodology, beginning with identifying internet-facing assets and gateway protections, followed by service discovery, process analysis, privilege review, and OS-level configuration assessment to uncover vulnerabilities and possible attack paths.
