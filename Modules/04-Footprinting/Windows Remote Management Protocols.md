
# Windows Remote Management Protocols 

Windows servers can be administered locally or remotely using built-in management technologies. Since **Windows Server 2016**, remote management is **enabled by default**.

Remote management relies on:

* **WS-Management (WS-Man)**
* **SOAP-based communication**
* **COM APIs and scripting interfaces**

These mechanisms allow administrators (and attackers) to manage systems even without physical access.

---

## Core Windows Remote Management Technologies

### 1. Remote Desktop Protocol (RDP)

### 2. Windows Remote Management (WinRM)

### 3. Windows Management Instrumentation (WMI)

---

## 1. Remote Desktop Protocol (RDP)

### Overview

RDP is a Microsoft protocol that provides **graphical remote access** to Windows systems.

* Operates at **Application Layer**
* Provides **full GUI access**
* Installed by default on Windows Servers

### Default Ports

| Protocol | Port | Description                        |
| -------- | ---- | ---------------------------------- |
| TCP      | 3389 | Main RDP communication             |
| UDP      | 3389 | Optional, performance optimization |

---

### Security Features

* Uses **TLS/SSL** since Windows Vista
* Supports **Network Level Authentication (NLA)** via **CredSSP**
* Uses **self-signed certificates** by default

 **Security Weakness**

* Self-signed certificates allow **MITM attacks**
* Systems may allow **legacy RDP Security** instead of TLS
* Certificate name mismatch warnings are common

---

### Enabling RDP

* Enabled via **Server Manager**
* Default: **Only allow NLA-enabled clients**

---

### RDP Footprinting (Enumeration)

RDP reveals valuable information **without authentication**.

#### Information Disclosure:

* Hostname
* Domain name
* Windows version
* NLA enabled or not
* System time

---

### Nmap RDP Enumeration

```bash
nmap -sV -sC -p3389 --script rdp* <IP>
```

#### Useful NSE Scripts

* `rdp-enum-encryption`
* `rdp-ntlm-info`

---

### Packet Tracing Risk

```bash
nmap --packet-trace -p3389 <IP>
```

* Uses identifiable cookies like:

  ```
  mstshash=nmap
  ```
* Can be detected by **EDR / SOC tools**
* Risky in **hardened environments**

---

### RDP Security Analysis Tool

**rdp-sec-check.pl** (Cisco CX Security Labs)

Purpose:

* Detect supported RDP security protocols
* Identify weak encryption configurations
* No authentication required

#### Result Interpretation

* `PROTOCOL_HYBRID` → NLA (CredSSP)
* `PROTOCOL_SSL` → TLS
* `PROTOCOL_RDP` → Legacy (weak)

---

### RDP Connection (Linux)

```bash
xfreerdp /u:<user> /p:<pass> /v:<IP>
```

Certificate warnings are expected due to self-signed certs.

---

## 2. Windows Remote Management (WinRM)

### Overview

WinRM is a **command-line based remote management protocol**.

* Built on **SOAP**
* Implements **WS-Management**
* Used heavily by **PowerShell Remoting**

---

### Default Ports

| Port | Protocol | Usage   |
| ---- | -------- | ------- |
| 5985 | HTTP     | Default |
| 5986 | HTTPS    | Secure  |

 In real environments, **5985 (HTTP)** is very common.

---

### Enabled By Default

| OS Version           | WinRM                  |
| -------------------- | ---------------------- |
| Windows Server 2012+ | Enabled                |
| Windows 10+          | Requires configuration |

---

### WinRM Capabilities

* Remote PowerShell sessions
* Remote command execution
* Event log forwarding
* Administrative automation

---

### WinRM Enumeration

```bash
nmap -sV -sC -p5985,5986 <IP>
```

Typical output:

* `Microsoft-HTTPAPI/2.0`
* No visible webpage

---

### WinRM Access (Linux)

**evil-winrm**

```bash
evil-winrm -i <IP> -u <user> -p <password>
```

Results in an **interactive PowerShell shell**.

---

### Pentest Relevance

* Often used for **post-exploitation**
* Requires **valid credentials**
* Cleaner than SMB-based execution
* Widely used in **Active Directory attacks**

---

## 3. Windows Management Instrumentation (WMI)

### Overview

WMI is the **most powerful Windows administration interface**.

* Based on **CIM / WBEM**
* Read/write access to system internals
* Used by:

  * PowerShell
  * WMIC
  * VBScript
  * Impacket tools

---

### Ports Used

| Stage              | Port             |
| ------------------ | ---------------- |
| Initial connection | TCP 135          |
| Data communication | Random high port |

---

### WMI Remote Execution

Using **Impacket wmiexec.py**

```bash
wmiexec.py user:pass@<IP> "command"
```

Example:

```bash
wmiexec.py Cry0l1t3:P455w0rD!@10.129.201.248 hostname
```

---

### Pentest Characteristics

* Requires credentials
* Executes commands **without writing binaries**
* Uses **DCOM**
* Very stealthy in legacy environments

---

## Comparison Summary

| Feature         | RDP            | WinRM             | WMI               |
| --------------- | -------------- | ----------------- | ----------------- |
| Access Type     | GUI            | CLI               | CLI               |
| Default Enabled | Yes            | Server 2012+      | Yes               |
| Auth Required   | Yes            | Yes               | Yes               |
| Common Ports    | 3389           | 5985/5986         | 135               |
| Pentest Use     | Initial access | Post-exploitation | Stealth execution |

---

## Key Takeaways for Exams & Pentesting

* **RDP** → GUI access, heavy logging, certificate issues
* **WinRM** → Best for PowerShell-based post-exploitation
* **WMI** → Stealthy remote execution using DCOM
* Enumeration often works **without authentication**
* Credential reuse across these services is common

---
