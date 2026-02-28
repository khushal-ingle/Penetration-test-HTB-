
# CAT5 Security – Engagement Preparation Guide

**Shells & Payloads Final Assessment**

You are being evaluated as a **junior penetration tester** before being allowed on a **live client engagement**. CAT5 Security wants to verify that you can **reliably gain shells, build payloads, recognize shells, and adapt based on recon data**.

This is not about memorizing commands — it’s about **decision-making and execution**.

---

## 1. Shell Basics

### Objective

Demonstrate that you understand **how shells work** and can manually establish them.

---

### A. Bind Shell on a Linux Host

**What CAT5 is testing**

* Understanding of bind shells
* Networking basics
* Listener vs client roles
* Linux command execution

**What a bind shell means**

* Target opens a port
* Attacker connects to that port
* Less stealthy, but simpler

**You should be able to**

* Start a shell listener on the Linux host
* Connect to it from your attack machine
* Interact with the shell

**Key concepts to know**

* Port selection
* Firewall implications
* When bind shells fail (NAT, firewalls)

---

### B. Reverse Shell on a Windows Host

**What CAT5 is testing**

* Reverse shell logic
* Windows command execution
* Listener setup on attacker machine

**What a reverse shell means**

* Attacker listens
* Target connects back
* More realistic in enterprise networks

**You should be able to**

* Start a listener (Netcat / Metasploit)
* Execute a payload on Windows
* Receive a shell callback

**Key concepts to know**

* Why reverse shells bypass firewalls more often
* Windows shell types (cmd vs PowerShell)

---

## 2. Payload Basics

This section checks if you understand **how shells are delivered**, not just shells themselves.

---

### A. Launching a Payload from Metasploit (MSF)

**What CAT5 is testing**

* Familiarity with Metasploit
* Payload selection
* Listener coordination

**You should demonstrate**

* Choosing the correct payload (Windows vs Linux)
* Setting LHOST, LPORT correctly
* Executing the payload
* Receiving a shell

**Red flag**
Blindly using Meterpreter without knowing why.

---

### B. Searching & Building a Payload from ExploitDB

**What CAT5 is testing**

* Research skills
* Understanding public exploits
* Manual payload integration

**You should demonstrate**

* Searching ExploitDB properly
* Reading exploit code or PoC
* Modifying payload variables (IP, port)
* Executing the exploit successfully

**Important**
Many ExploitDB PoCs **do not include ready-made shells** — you are expected to adapt them.

---

### C. Payload Creation Knowledge

**What CAT5 is testing**

* Whether you understand payloads conceptually
* Not just “msfvenom copy-paste”

You should understand:

* Staged vs stageless payloads
* OS-specific payloads
* Architecture differences (x86 vs x64)
* File-based vs fileless payloads

---

## 3. Getting a Shell on Windows

### Objective

Use **provided recon results** to compromise a Windows host and gain a shell.

**What CAT5 is testing**

* Recon interpretation
* Payload selection
* Exploit chaining

**Expected workflow**

1. Review recon output
2. Identify a vulnerable service or app
3. Select or build a payload
4. Deliver payload
5. Catch shell

**Important**
This is not about brute force. It’s about **matching exploit → payload → OS**.

---

## 4. Getting a Shell on Linux

### Objective

Repeat the above process on a Linux host.

**What CAT5 is testing**

* Linux-specific exploitation
* Shell handling differences
* Command execution knowledge

**Expected workflow**

* Identify Linux service vulnerability
* Choose compatible payload
* Execute exploit
* Stabilize shell if needed

---

## 5. Landing a Web Shell

### Objective

Demonstrate understanding of **web shells and web technologies**.

---

### A. Identify the Web Application & Language

**What CAT5 is testing**

* Web fingerprinting
* Basic web recon skills

You should identify:

* Application type (WordPress, custom app, etc.)
* Backend language (PHP, ASPX, JSP, etc.)

---

### B. Deploy a Web Shell

**What CAT5 is testing**

* Understanding of file upload/RCE
* Web shell usage
* Browser-based command execution

You should demonstrate:

* Uploading or planting a web shell
* Executing commands via browser
* Using web shell to gain OS-level access

**Key concept**
A web shell is often a **stepping stone**, not the final shell.

---

## 6. Spotting a Shell or Payload

### Objective

Detect evidence of an **existing shell or payload**.

**What CAT5 is testing**

* Defensive awareness
* Blue-team thinking
* Log and artifact analysis

You may be given:

* Process lists
* Network connections
* Logs
* Filesystem artifacts

You should recognize:

* Suspicious listening ports
* Reverse connections
* Encoded payloads
* Known shell patterns

---

## 7. Final Challenge

### Objective

Tie everything together.

**What CAT5 expects**

* Independent thinking
* Tool selection
* Payload crafting
* Shell handling
* Post-exploitation data retrieval

**Expected workflow**

1. Analyze recon
2. Choose exploit path
3. Select or build payload
4. Deliver payload
5. Catch shell
6. Enumerate target
7. Extract required information

This simulates **real client work**, not a CTF trick.

---

## What Passing This Assessment Means

If you complete this successfully, CAT5 Security believes you can:

* Think like a penetration tester
* Adapt when tools fail
* Safely and effectively gain shells
* Handle live engagement conditions

This is the **minimum bar** before touching a real client.

---

