
# Shells Jack Us In, Payloads Deliver Us Shells

## What Is a Shell?

A **shell** is a program that allows a user to interact with an operating system by entering commands and receiving output. Common examples include:

* Linux: `bash`, `zsh`, `sh`
* Windows: `cmd.exe`, `PowerShell`

In penetration testing and offensive security, *getting a shell* means successfully gaining interactive command-line access to a target system after exploiting a vulnerability or misconfiguration.

Common phrases like:

* “I popped a shell”
* “I caught a shell”
* “I’m in”

all mean the same thing: **the attacker now has command execution on the target OS**.

---

## Why Getting a Shell Is Important

Without a shell, an attacker is extremely limited. A shell enables:

* Running native system commands
* Enumerating users, services, files, and permissions
* Transferring tools and data
* Escalating privileges
* Pivoting to other systems
* Maintaining persistence

A shell turns a *theoretical vulnerability* into *practical control*.

---

## CLI Shells vs Graphical Access

Shell access usually means **command-line access**, not GUI access.

### Advantages of CLI shells:

* Lower bandwidth usage
* Faster interaction
* Easier automation
* Often less noisy than GUI tools like RDP or VNC
* Harder to detect in some environments

This is why professional attackers and penetration testers heavily rely on shells instead of graphical sessions.

---

## Shells from Different Perspectives

### 1. Computing Perspective

A shell is a **text-based user interface** used to:

* Execute commands
* Manage files
* Control processes

Examples: Bash, PowerShell, cmd.

---

### 2. Exploitation & Security Perspective

A shell is the **result of exploitation**.

Example:

* Exploiting EternalBlue on Windows
* Gaining remote `cmd.exe` or PowerShell access

In this context, the shell is proof of compromise.

---

### 3. Web Perspective (Web Shells)

A **web shell** is different from a normal OS shell.

* Usually uploaded via a web vulnerability (file upload, RCE)
* Accessed through a browser
* Executes system commands on the backend server
* Often written in PHP, ASPX, JSP, etc.

Web shells are often **less interactive and less stable**, but still powerful.

---

## What Is a Payload?

The word **payload** has different meanings depending on context.

### General Definitions

* **Networking**: The data portion of a packet
* **Computing**: The actionable part of an instruction
* **Programming**: Data passed to a function or process

---

### Exploitation & Security Definition

In offensive security, a **payload** is:

> Code designed to execute on a target system after exploitation.

Payloads are what *deliver* shells.

---

## Payloads Deliver Shells

In penetration testing:

* The exploit opens the door
* The payload walks through it

Payloads can:

* Spawn a local shell
* Create a reverse shell
* Establish a bind shell
* Execute commands
* Download additional tools

The goal of most payloads in this module is:
**remote shell access**.

---

## Relationship Between Exploits, Payloads, and Shells

Think of it as a chain:

1. **Vulnerability** exists
2. **Exploit** triggers the vulnerability
3. **Payload** executes code
4. **Shell** is established

If any part fails, you don’t get interactive access.

---

## Why This Module Focuses on Shells

Enumeration tells you *what might work*.
Exploitation gives you *a foothold*.
Shells give you **control**.

Most real-world attacks, labs, and assessments revolve around:

* Getting a shell
* Stabilizing it
* Upgrading it
* Using it effectively

That’s why everything that follows in this module builds on shell access.

---

## Key Takeaways

* A shell equals interactive control of a system
* Payloads are the mechanism that delivers shells
* Shells unlock privilege escalation, pivoting, and persistence
* CLI shells are preferred over GUI access in offensive security
* Understanding shells is foundational for penetration testing



Tell me how deep you want to go.
