
# Laudanum – One Web Shell to Rule Them All

## What Is Laudanum?

**Laudanum** is a curated repository of **prebuilt web shells and injectable payloads** designed for penetration testing. It provides ready-to-use files for many common **server-side web technologies**, allowing attackers to:

* Execute OS commands from a browser
* Obtain web-based shells
* Trigger reverse shells
* Maintain lightweight access through web apps

Laudanum is considered a **pentesting staple** and is commonly available by default on **Kali Linux** and **Parrot OS**.

Supported languages include:

* PHP
* ASP
* ASPX
* JSP
* CFML
* Perl
* Python

---

## Why Laudanum Is Useful

Laudanum solves a common problem in web exploitation:

> “I have a file upload vulnerability, but I need a working shell fast.”

Advantages:

* No need to write custom shells during an engagement
* Language-specific payloads for different web stacks
* Clean, readable, and modifiable code
* Designed specifically for **post-exploitation via web apps**

In real engagements, time matters. Laudanum saves time.

---

## Where Laudanum Is Located

On Kali or Parrot OS:

```bash
/usr/share/laudanum/
```

Example structure:

```text
laudanum/
├── asp/
├── aspx/
├── jsp/
├── php/
├── cfml/
└── python/
```

Each directory contains **web shells and helper scripts** for that language.

---

## Important Usage Rule

**Do not blindly upload Laudanum shells.**

Most shells require **modification before use**, especially:

* IP address restrictions
* Callback IPs for reverse shells
* Obvious ASCII art and comments

Failing to modify them can:

* Break functionality
* Trigger antivirus signatures
* Alert defenders during a real assessment

---

## Preparing a Laudanum Web Shell

### Step 1: Copy the Shell for Editing

Never edit files directly inside `/usr/share/laudanum`.

Example:

```bash
cp /usr/share/laudanum/aspx/shell.aspx ~/demo.aspx
```

---

### Step 2: Edit the Shell

Open the file and locate the **allowed IPs** or callback section.

Example (ASPX):

```csharp
string[] allowedIps = { "127.0.0.1", "10.10.14.12" };
```

Actions to take:

* Add your attack box IP
* Remove ASCII art banners
* Remove excessive comments
* Keep functionality minimal

This reduces detection risk.

---

## Uploading the Shell

Once modified, upload the shell using the vulnerable web functionality, such as:

* Configuration import
* File upload
* Profile image upload
* Backup restore feature

If the upload succeeds, the application often reveals:

* The upload path
* The saved filename

Always note this.

---

## Accessing the Web Shell

After upload, navigate directly to the file.

Example:

```text
http://status.inlanefreight.local/files/demo.aspx
```

Some applications:

* Normalize slashes (`\\` → `/`)
* Randomize filenames
* Hide upload directories

If filename randomization occurs, you must enumerate or intercept traffic.

---

## Using the Laudanum Shell

Once accessed, Laudanum shells typically provide:

* Command execution input
* STDOUT / STDERR output
* Environment info

Example command:

```text
systeminfo
```

Typical execution context:

* IIS: `IIS APPPOOL\DefaultAppPool`
* Apache: `www-data`
* Nginx: `nginx`

This confirms **remote code execution**.

---

## Limitations of Laudanum Web Shells

Web shells are **not full shells**.

Common limitations:

* No job control
* No interactive programs
* Limited environment variables
* Stateless execution
* Often removed by cleanup jobs

Because of this:

> **Web shells are footholds, not endpoints**

---

## Correct Post-Web-Shell Workflow

Once command execution is confirmed:

1. Enumerate the system
2. Identify writable directories
3. Upload additional tooling
4. Launch a reverse shell
5. Upgrade to a TTY shell
6. Escalate privileges if in scope

Never stay in a browser shell longer than necessary.

---

## Key Takeaways

* Laudanum is a **ready-made web shell toolkit**
* Supports multiple web technologies
* Requires **manual modification before use**
* Ideal for **file upload exploitation**
* Best used to **pivot into a reverse shell**

---
