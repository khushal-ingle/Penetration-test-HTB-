

## Detection & Prevention – Simplified Explanation

This section explains **how defenders detect shells and payloads**, **where attackers leave evidence**, and **how organizations reduce the risk and impact of these attacks**. Every shell or payload needs execution and communication, and that activity can be detected.

---

## Why Detection Matters

Shells and payloads do not exist silently. They:

* Execute commands on a host
* Create processes
* Communicate over the network

Detection focuses on **host behavior**, **network behavior**, and **user behavior**.

---

## MITRE ATT&CK Framework (Big Picture)

The **MITRE ATT&CK Framework** is a globally used model that describes **real attacker behavior**. Defenders use it to map attacks to detection and response.

For **shells and payloads**, three tactics are most important:

---

## 1. Initial Access

**What it is:**
How attackers gain their first foothold.

**Common methods:**

* Web application vulnerabilities
* File upload flaws
* Misconfigured services (SMB, FTP, RDP)
* Weak authentication

**Why it matters:**

* Usually targets an internet-facing system
* Provides access, not full control

**What defenders monitor:**

* Web server logs
* Authentication attempts
* File uploads
* WAF alerts

---

## 2. Execution

**What it is:**
When attacker code runs on the victim system.

**Examples:**

* Web shell command execution
* PowerShell one-liners
* Metasploit payload execution
* Uploaded scripts or binaries

**Why it matters:**

* Shells and payloads must execute to work
* This stage produces strong log evidence

**What defenders monitor:**

* Process creation
* Command-line activity
* PowerShell logging
* Script execution events

---

## 3. Command & Control (C2)

**What it is:**
How attackers maintain access and send commands.

**Examples:**

* Reverse shells
* Meterpreter sessions
* HTTP/DNS callbacks
* Encrypted or clear-text channels

**Why it matters:**

* Payloads must communicate externally
* Network behavior often looks abnormal

**What defenders monitor:**

* Outbound connections
* Repeated beaconing
* Non-standard ports (e.g., 4444)
* Unknown external IP addresses

---

## Key Events Defenders Watch For

### 1. File Uploads

* Especially on web applications
* Script files uploaded where images are expected
* Upload followed by immediate access or execution

**Why important:**
File upload is one of the most common ways to deploy web shells.

---

### 2. Suspicious Non-Admin Activity

Examples:

* Normal users running `whoami`, `id`, `net user`
* Web service accounts spawning shells
* User machines connecting to other user machines via SMB

**Why important:**
Normal users rarely use shells. This behavior stands out in logs.

---

### 3. Anomalous Network Sessions

Defenders rely on **normal traffic patterns (baselines)**.

Suspicious signs:

* Repeated traffic to one IP
* Regular heartbeat connections
* Use of uncommon ports
* Sudden spikes in HTTP requests

**Tools used:**

* NetFlow
* Firewall logs
* IDS/IPS
* SIEM platforms

---

## Why Clear-Text Shells Are Easy to Detect

Basic shells (like Netcat):

* Are not encrypted
* Expose commands and responses
* Can be reconstructed in packet captures

Defenders can see:

* Commands typed
* User creation
* Persistence actions

This is why simple shells are **easy to detect** and **poor for stealth**.

---

## Importance of Network Visibility

Strong detection requires:

* Accurate network documentation
* Understanding traffic flow
* Knowing what “normal” looks like

Modern network devices provide:

* Application-layer visibility
* Central dashboards
* Behavioral analysis

If a payload communicates, visibility makes it noticeable.

---

## Protecting End Devices

**End devices include:**

* Workstations
* Servers
* Storage devices
* IoT devices

**Why they matter:**

* They run operating systems
* They execute commands
* They are frequent attack targets

**Core protections:**

* Anti-virus / EDR
* Host firewalls
* Patch management
* Command logging

### On Windows:

* Defender should stay enabled
* Firewall profiles should remain active
* PowerShell logging is critical

---

## Key Mitigations

### Application Sandboxing

* Limits what compromised apps can access
* Reduces impact of web shells

### Least Privilege

* Users and services get only necessary permissions
* Reduces privilege escalation opportunities

### Host Segmentation & Hardening

* Internet-facing systems isolated (DMZ)
* Prevents lateral movement

### Firewalls (Inbound and Outbound)

* Block reverse shell callbacks
* Restrict unauthorized traffic
* NAT can break poorly designed payloads

---

## Final Summary

No single control stops attacks.

Effective defense requires:

* Endpoint protection
* Network monitoring
* Logging and alerting
* Least privilege
* Segmentation

This layered approach is called **defense in depth**.

---

### Key takeaway:

If a shell exists, it must execute and communicate. Detection focuses on finding that activity.

