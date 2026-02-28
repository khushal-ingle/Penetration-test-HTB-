
# The Live Engagement – Execution Guide

You are acting as a **junior penetration tester on a live internal engagement**.
Your job is not just to “get shells”, but to **select the right exploit path, payload, and shell type** based on recon.

You must work **only from the Foothold host**.

---

## 0. Environment Setup (Do This First)

### Connect to the Foothold

From your own VM or Pwnbox:

```bash
xfreerdp /v:<FOOTHOLD_IP> /u:htb-student /p:HTB_@cademy_stdnt!
```

You will log in **twice** (once CLI, once GUI).

### Why this matters

* The **targets are only reachable from the foothold**
* All listeners must bind to **172.16.x.x**, not VPN IPs
* Treat the foothold as your **jump box**

---

## 1. Engagement Methodology (How CAT5 Expects You to Think)

For **each host**, you should follow the same mental flow:

1. Identify OS and exposed services
2. Select a **realistic exploit path**
3. Choose the **correct payload**
4. Catch a shell
5. Identify:

   * OS
   * User
   * Shell type
6. Upgrade shell if required

This is exactly what the objectives are testing.

---

## 2. Host-1 (Windows Target – Interactive Shell Required)

### What You’re Being Tested On

* Windows exploitation
* Payload delivery
* Shell identification (CMD vs PowerShell vs Meterpreter)

### Expected Recon Steps

From the foothold:

```bash
nmap -sC -sV -Pn 172.16.1.11
```

Look for:

* SMB (445)
* HTTP (8080)
* RDP
* RPC

### Typical Exploit Logic

You should ask:

* Is this **unauthenticated**?
* Do I have **credentials**?
* Is this **SMB-based** or **web-based**?

Most candidates fail here by:

* Using the wrong payload
* Forgetting LHOST is **internal**
* Not recognizing the shell they landed in

### Shell Validation (Critical)

Once you get a shell:

```cmd
whoami
echo %COMSPEC%
```

or in Meterpreter:

```text
getuid
shell
```

You must correctly identify:

* Meterpreter vs CMD vs PowerShell
* Privilege level

---

## 3. Host-2 (Web Application Target – Web Shell Required)

**This is not a Windows or Linux exploit first.
This is a WEB ATTACK first.**

### What You’re Being Tested On

* Web enumeration
* Upload or RCE logic
* Web shell → OS shell understanding

### Initial Recon

From the foothold:

```bash
nmap -sC -sV blog.inlanefreight.local
```

Then:

* Browse the site
* Identify language:

  * `.php` → PHP
  * `.aspx` → ASP.NET
  * `.jsp` → Java

### Expected Attack Paths

Typical vectors:

* File upload
* LFI → RCE
* CMS exploit
* Admin panel misuse

### Web Shell Rules (Very Important)

Once you upload or trigger a web shell:

* You **must identify**:

  * Language (PHP / ASPX)
  * Execution context (web user)
* You **must demonstrate command execution**
* Bonus: upgrade to reverse shell

Example validation commands:

```bash
whoami
id
uname -a
```

---

## 4. Host-3 (Linux Target – Interactive Shell Required)

### What You’re Being Tested On

* Linux exploitation
* Reverse shell handling
* TTY upgrade
* Shell environment awareness

### Recon

From the foothold:

```bash
nmap -sC -sV 172.16.1.13
```

Look for:

* HTTP
* FTP
* SSH
* MySQL
* Known vulnerable apps

### Shell Handling (Very Important)

If you land a shell and see:

* No prompt
* Broken tab completion
* `su` / `sudo` failing

You **must upgrade it**.

Common expected upgrade:

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

Then:

```bash
whoami
echo $SHELL
tty
```

This proves you understand:

* Non-TTY vs TTY shells
* Linux shell environments

---

## 5. Listener Discipline (Where Most People Fail)

You **must** bind listeners to the foothold’s internal IP.

Correct:

```bash
nc -lvnp 4444
```

Incorrect:

* VPN IP
* 10.10.x.x
* 127.0.0.1

Always confirm:

```bash
ip a
```

---

## 6. Objective Mapping (How Your Actions Are Graded)

| Objective             | What You Must Show          |
| --------------------- | --------------------------- |
| Windows shell         | Exploit + interactive shell |
| Linux shell           | Exploit + upgraded TTY      |
| Web shell             | Upload/RCE via browser      |
| Shell identification  | CMD vs PowerShell vs bash   |
| Professional workflow | Correct order, no guessing  |

---

## 7. Mental Checklist Before Answering Questions

Before submitting answers, confirm:

* You accessed **all 3 hosts**
* You know **what shell you were in**
* You know **which user you were**
* You understand **why that exploit worked**
* You could explain this in an interview

If yes — you passed the skills assessment **properly**, not just mechanically.

---

## Final Advice (Important)

This assessment mirrors:

* HTB Pro Labs
* CRTO / PNPT style thinking
* Real junior pentest interviews

Do **not rush**.
Do **not brute force**.
Think like CAT5 expects a real consultant to think.

---


