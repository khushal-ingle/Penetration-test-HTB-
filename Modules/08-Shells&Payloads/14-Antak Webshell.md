
# Antak Webshell (ASPX + PowerShell)

## Why ASPX Webshells Matter

When targeting **Windows web servers**, especially those running:

* IIS
* ASP.NET
* SharePoint
* Exchange
* Custom .NET applications

ASPX web shells are often **the most reliable foothold**.

They execute server-side using **ASP.NET**, which means:

* Commands run as the IIS worker process
* PowerShell is usually available
* Windows Defender may trust IIS more than standalone binaries

This makes ASPX web shells extremely valuable during **external pentests**.

---

## Learning Tip: IPPSEC as a Visual Companion

Some concepts are easier to learn visually.
**ippsec.rocks** is a powerful indexed search engine over IPPSEC’s YouTube walkthroughs.

Why it’s useful:

* Keyword-based search (e.g., `aspx`, `iis`, `webshell`)
* Jumps directly to timestamps
* Shows real exploitation in action

For ASPX shells, his **Cereal** walkthrough is a strong example:

* File upload
* ASPX execution
* Command execution via browser

This reinforces exactly what you are doing with Antak.

---

## What Is ASPX?

**ASPX (Active Server Pages Extended)** is part of the **ASP.NET Framework**.

How it works:

1. User sends HTTP request to `.aspx` file
2. IIS hands it to the ASP.NET runtime
3. Server-side code executes
4. Output is rendered as HTML

If we upload an ASPX file containing malicious logic:

* We get **remote code execution**
* The browser becomes our shell interface
* Commands execute on the Windows OS

---

## What Is Antak?

**Antak** is a **PowerShell-based ASPX web shell** from the **Nishang** framework.

Key characteristics:

* Written in ASP.NET
* Uses **PowerShell** under the hood
* Browser-based interface
* Authentication-protected
* Can execute encoded PowerShell in memory

Antak is not just a command runner.
It is closer to a **lightweight command-and-control interface**.

---

## Where Antak Is Located

On Kali / Parrot / Pwnbox:

```bash
/usr/share/nishang/Antak-WebShell/
```

Contents:

```text
antak.aspx
Readme.md
```

---

## Why Antak Is Powerful

Antak can:

* Execute PowerShell commands
* Run PowerShell scripts in memory
* Encode commands to evade detection
* Upload and download files
* Parse `web.config`
* Execute SQL queries (if credentials exist)

Each command runs as a **new PowerShell process**, which:

* Avoids some logging artifacts
* Makes it harder to correlate execution
* Improves stealth compared to cmd.exe

---

## Preparing Antak for Use

### Step 1: Copy the Shell

Never modify system copies.

```bash
cp /usr/share/nishang/Antak-WebShell/antak.aspx ~/Upload.aspx
```

---

### Step 2: Set Authentication Credentials

Inside `Upload.aspx`, locate the authentication logic (around line 14).

Example:

```csharp
if (username == "Disclaimer" && password == "ForLegitUseOnly")
```

Change this to:

* Custom username
* Strong password

Why this matters:

* Prevents accidental discovery
* Stops other testers or users from hijacking your shell
* Reduces noise in real engagements

---

### Step 3: Remove Identifiable Artifacts

Before upload:

* Remove ASCII art
* Remove verbose comments
* Keep only required functionality

These artifacts are often **signatured by AV/EDR**.

---

## Uploading Antak

Upload Antak via:

* File upload form
* Configuration import
* Backup restore
* Profile image upload (after bypass)
* Any unrestricted or weakly restricted upload feature

In the lab example:

* Files land in `\\files\\`
* ASPX execution is allowed

---

## Accessing Antak

Navigate to:

```text
http://status.inlanefreight.local/files/upload.aspx
```

You should see:

* Username prompt
* Password prompt

Once authenticated, the Antak interface loads.

---

## Using Antak

The interface behaves like a **PowerShell console**.

You can:

* Run standard PowerShell commands
* Navigate the filesystem
* Enumerate users
* Upload payloads
* Download sensitive files
* Encode and execute PowerShell one-liners

Example:

```powershell
Get-ChildItem C:\Users
```

If unsure where to start:

```powershell
help
```

---

## Typical Execution Context

Commands usually execute as:

* `IIS APPPOOL\DefaultAppPool`
* or another application pool identity

This is **not SYSTEM**, but it is a valid foothold.

---

## Correct Next Step After Antak

Antak is **not the end goal**.

Correct workflow:

1. Confirm command execution
2. Enumerate environment
3. Upload a reverse shell payload
4. Execute via PowerShell
5. Catch callback
6. Upgrade to interactive shell
7. Privilege escalation

Web shells are temporary.
Reverse shells are control.

---

## Antak vs Laudanum (Quick Comparison)

| Feature              | Antak             | Laudanum |
| -------------------- | ----------------- | -------- |
| Language             | ASPX + PowerShell | Multiple |
| Authentication       | Yes               | Optional |
| PowerShell support   | Native            | Limited  |
| File upload/download | Yes               | Minimal  |
| Stealth              | Higher            | Lower    |
| Complexity           | Higher            | Simpler  |

Use **Antak** when:

* Target is Windows
* PowerShell is available
* You want post-exploitation capability

---

## Key Takeaways

* Antak is a **PowerShell-driven ASPX web shell**
* Ideal for **Windows IIS environments**
* Authentication protects access
* Excellent for staging reverse shells
* Should be used as a **pivot**, not a terminal

---

