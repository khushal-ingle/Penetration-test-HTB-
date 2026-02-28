
# Infiltrating Unix / Linux

According to **W3Techs**, over **70% of web servers** run on **Unix-based operating systems** (Linux, BSD, etc.).
For penetration testers, this means Linux exploitation is **not optional**—it is a **core skill**.

In real environments, Linux systems often host:

* Web servers
* Web applications
* Databases
* Network management tools
* Internal services

Gaining a shell on one Linux host can allow:

* Internal **pivoting**
* Credential harvesting
* Lateral movement
* Infrastructure takeover

---

## Key Questions Before Exploitation

Before attempting to get a shell on a Linux system, always ask:

1. **Which Linux distribution is running?**

   * Ubuntu, CentOS, Debian, Alpine, etc.
2. **Which shells and languages exist?**

   * Bash, sh, Python, Perl, PHP
3. **What is the system’s role?**

   * Web server, DB server, management server
4. **What application is exposed?**

   * CMS, admin panel, network tool
5. **Are there known vulnerabilities?**

   * CVEs, Exploit-DB, Metasploit modules

These questions guide **payload choice**, **exploit selection**, and **post-exploitation strategy**.

---

## Step 1: Enumerate the Linux Host

```bash
nmap -sC -sV 10.129.201.101
```

### Key Findings from the Scan

| Port     | Service      | Insight                                  |
| -------- | ------------ | ---------------------------------------- |
| 21       | FTP          | Possible anonymous access or file upload |
| 22       | SSH          | Potential brute-force or key reuse       |
| 80 / 443 | Apache + PHP | Web application attack surface           |
| 3306     | MySQL        | Backend database                         |
| OS       | CentOS       | Older enterprise Linux                   |

From this alone, we can infer:

* The host is a **web server**
* Running **Apache + PHP**
* Likely hosting a **web application**
* Good candidate for **web-based RCE**

---

## Step 2: Identify the Web Application

Browsing to the target reveals **rConfig**, a **network configuration management tool**.

Why this is important:

* rConfig typically has **privileged access** to routers & switches
* Compromising it can lead to **full network takeover**
* High-value pivot point

At the bottom of the login page:

```
rConfig Version: 3.9.6
```

---

## Step 3: Research Vulnerabilities

Search keywords:

```
rConfig 3.9.6 vulnerability
```

Results show:

* Arbitrary file upload
* Authenticated RCE
* Chained exploits leading to shell access

This confirms:

* The application version is **known vulnerable**
* Exploitation is feasible

---

## Step 4: Search Metasploit Modules

```text
msf6 > search rconfig
```

Relevant modules:

* `exploit/linux/http/rconfig_ajaxarchivefiles_rce`
* `exploit/unix/webapp/rconfig_install_cmd_exec`

If a module **does not appear**:

* Check Rapid7 GitHub repos
* Manually install `.rb` exploit files into:

```bash
/usr/share/metasploit-framework/modules/exploits/
```

Then reload Metasploit.

---

## Step 5: Exploit rConfig and Gain a Shell

```text
use exploit/linux/http/rconfig_vendors_auth_file_upload_rce
```

Run the exploit after setting required options:

### What the Exploit Does

1. Detects rConfig 3.9.6
2. Authenticates to the web app
3. Uploads a PHP reverse shell
4. Triggers payload execution
5. Deletes uploaded file
6. Returns a **Meterpreter shell**

Output confirms success:

```
Meterpreter session opened
```

---

## Step 6: Drop to a System Shell

```text
meterpreter > shell
```

You now have OS-level command execution, but notice:

* No prompt
* Limited interaction
* Commands like `sudo` may fail

This is a **non-TTY shell**.

Why?

* The payload executed as the **apache user**
* Apache does not spawn interactive shells
* No terminal allocated

---

## Step 7: Upgrade to a TTY Shell

Check for Python:

```bash
which python
```

If present, spawn a proper TTY:

```bash
python -c 'import pty; pty.spawn("/bin/sh")'
```

Result:

```bash
sh-4.2$ whoami
apache
```

### Why This Matters

A TTY shell enables:

* Proper command execution
* `su` and `sudo`
* Job control
* Shell history
* Reliable privilege escalation

This step is **mandatory** in real engagements and exams.

---

## What We Learned from This Attack

✔ Enumeration revealed Linux + web stack
✔ Version disclosure exposed vulnerable app
✔ Public exploits led to remote code execution
✔ Web RCE → reverse shell
✔ Apache user → non-TTY shell
✔ Python → full interactive shell

This is a **classic Linux web exploitation chain**.

---

## Key Takeaways 

* Linux servers dominate web hosting
* Web apps are the **primary entry point**
* Version numbers matter
* Metasploit is powerful but not complete
* Manual exploit installation is a real skill
* Always upgrade to a **TTY shell**
* Apache user shells are common
* Python is your best friend on Linux

---
