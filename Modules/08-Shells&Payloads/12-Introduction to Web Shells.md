
# Introduction to Web Shells

In modern penetration testing, **web applications are the primary attack surface**, especially during **external assessments**. Most organizations have hardened perimeter networks, meaning services like SMB, RPC, or RDP are no longer exposed to the internet. Instead, attackers usually gain their **initial foothold** through:

* File upload vulnerabilities
* Command injection
* SQL injection (leading to RCE)
* LFI / RFI
* Insecure authentication portals (VPN, OWA, Citrix, etc.)
* Misconfigured web services (Tomcat, WebLogic, FTP to webroot)

Because of this, **web shells are one of the most common first access techniques**.

---

## What Is a Web Shell?

A **web shell** is a **script uploaded to a web server** that allows an attacker to execute operating system commands **through a web browser**.

Key idea:

* The shell runs **inside the web server’s execution context**
* Commands are sent via HTTP requests
* Output is returned in the browser

In short:

> A web shell gives us **remote command execution (RCE)** via the browser.

---

## Why Web Shells Matter

Web shells are often:

* The **first RCE** achieved in an external pentest
* A stepping stone to a **reverse shell**
* Used to **enumerate the system**
* Used to **upload additional payloads**

They are not meant to be the final shell.

---

## How Web Shells Are Typically Gained

To use a web shell, we first need **write access** to a directory that the web server executes from (usually the webroot).

Common paths to achieve this:

### 1. File Upload Vulnerabilities

Examples:

* Profile picture upload
* Document upload
* Backup import feature
* Self-registration forms

Attackers bypass:

* Client-side checks (JavaScript)
* File extension filters
* MIME-type validation

---

### 2. Application Deployment Features

Some enterprise software allows code deployment:

* Tomcat / WAR uploads
* Axis2 services
* WebLogic admin consoles

---

### 3. Misconfigured Services

* FTP write access to `/var/www/html`
* SMB share mapped to webroot
* CI/CD pipelines writing to production paths

---

### 4. Code Execution Bugs

* Command injection
* RFI with `allow_url_include`
* Deserialization bugs

---

## Common Web Shell Languages

The web shell language **must match the server-side technology**.

| Web Server Stack  | Web Shell Language |
| ----------------- | ------------------ |
| Apache + PHP      | PHP                |
| IIS               | ASP / ASPX         |
| Tomcat            | JSP                |
| Python frameworks | Python             |
| Node.js           | JavaScript         |

Uploading the wrong language means **no execution**.

---

## Example: PHP Web Shell (Conceptual)

A basic PHP web shell:

```php
<?php system($_GET['cmd']); ?>
```

Usage in browser:

```
http://target/shell.php?cmd=whoami
```

Output:

```
www-data
```

This executes OS commands **as the web server user**.

---

## Characteristics of Web Shells

Web shells are:

* **Stateless** (each request is separate)
* **HTTP-based**
* Often **unstable**
* Usually run as:

  * `www-data`
  * `apache`
  * `nginx`
  * `iis apppool`

Because of this, they:

* Lack job control
* Cannot handle interactive programs well
* Are not suitable for long-term use

---

## Why Web Shells Are Unreliable

Several real-world issues:

* Uploaded files may be:

  * Deleted automatically
  * Cleaned by antivirus
  * Replaced during deployment
* Web servers may:

  * Restart
  * Rotate temp directories
* Limited execution time
* Output buffering issues

This is why **web shells are temporary footholds**, not final shells.

---

## Proper Use of a Web Shell

Correct workflow:

1. Upload web shell
2. Confirm command execution
3. Enumerate:

   * OS
   * Users
   * Writable directories
4. Upload tools or payloads
5. **Upgrade to a reverse shell**
6. Establish persistence (if allowed)

---

## Web Shell vs Reverse Shell

| Web Shell      | Reverse Shell     |
| -------------- | ----------------- |
| Browser-based  | Socket-based      |
| Stateless      | Interactive       |
| Limited        | Full TTY possible |
| Unstable       | More reliable     |
| Easy detection | Harder to detect  |

Best practice:

> **Use web shells only to get a real shell.**

---

## Persistence Consideration

Web shells are **not persistence mechanisms** by default.

Better persistence options:

* Cron jobs
* SSH keys
* Services
* Scheduled tasks
* Backdoored binaries

Only attempt persistence **if scope allows**.

---

## Key Takeaways

* Web shells are extremely common in real pentests
* They are often the **first RCE**
* They rely on **file upload or code execution bugs**
* They are **temporary and unreliable**
* Always upgrade to a **reverse shell**

---


Just tell me what you want next.
