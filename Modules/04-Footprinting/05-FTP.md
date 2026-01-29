##  What is FTP?

**FTP (File Transfer Protocol)** is a network protocol used to **transfer files** between a client and a server over the internet or a local network.

With FTP, we can:

- Upload files to a server
- Download files from a server
- View directories and manage files remotely

FTP works on the **Application Layer**, same as HTTP or SMTP.

---

##  Default FTP Port

| Service             | Port       |
| ------------------- | ---------- |
| FTP Control Channel | **21/TCP** |
| FTP Data Channel    | **20/TCP** |

---

##  FTP Working (2 Channels)

FTP uses **two connections**:
### 1. Control Channel (Port 21)

Used for sending commands like:
- login
- ls
- get
- put
### 2. Data Channel (Port 20 or random port)

Used for transferring files (upload/download).

---

##  Active vs Passive Mode

###  Active Mode
- Server connects back to client
- Can fail if client firewall blocks incoming traffic
###  Passive Mode (Most Common)

- Server provides a port
- Client connects to server
- Works better with firewalls

---

# Authentication in FTP

### 1. Authenticated Login

Requires username + password

### 2. Anonymous Login

Some servers allow public access:

```bash
Username: anonymous
Password: anything
```

⚠️ Anonymous FTP is dangerous if misconfigured.

---

#  FTP vs TFTP

|Feature|FTP|TFTP|
|---|---|---|
|Protocol|TCP|UDP|
|Port|21|69|
|Authentication|Yes|No|
|Security|Low|Very Low|
|Directory listing|Yes|No|

TFTP is used only in **small internal networks**.

---
# Connecting to FTP Server

### Using ftp client

```bash
ftp <target-ip>
```

### Using lftp (better)

```bash
lftp <target-ip>
```

### Using Web Browser

```text
ftp://username:password@<ip>
```

---
# Reconnaissance (Finding FTP)

### Nmap Scan FTP Port

```bash
nmap -p 21 <target-ip>
```

### Banner Grabbing

```bash
nc -nv <target-ip> 21
```

Banner shows:

- FTP software name  
- Version info

---

# Enumeration (Important for Pentesting)

### Check Anonymous Login

```bash
nmap -p21 --script ftp-anon <target-ip>
```

### FTP Features Enumeration

```bash
nmap -p21 --script ftp-features <target-ip>
```

### Brute Force Attack Script

```bash
nmap -p21 --script ftp-brute <target-ip>
```

---

# Common FTP Commands

|Command|Use|
|---|---|
|`ls`|List files|
|`cd folder`|Change remote directory|
|`lcd folder`|Change local directory|
|`get file.txt`|Download file|
|`mget *.txt`|Download multiple files|
|`put file.txt`|Upload file|
|`mput *.php`|Upload multiple files|
|`binary`|File transfer mode|
|`quit`|Exit FTP|

---

# Downloading Files

### Download Single File

```bash
ftp> get flag.txt
```

### Download All Files

```bash
wget -m ftp://anonymous:anonymous@<target-ip>
```

Downloaded files are stored inside a folder named after the target IP.

---

---

# Uploading Files

If upload is allowed:

```bash
ftp> put test.txt
```

Upload permission can lead to attacks, especially if FTP is linked to a website.

---

---

# vsFTPd Configuration (Linux FTP Server)

Main config file:

```bash
/etc/vsftpd.conf
```

Important options:

|Setting|Meaning|
|---|---|
|`anonymous_enable=YES`|Allow anonymous login|
|`local_enable=YES`|Allow local user login|
|`write_enable=YES`|Allow uploads/deletes|
|`anon_upload_enable=YES`|Anonymous upload allowed|
|`anon_root=/home/ftp`|Root folder for anonymous users|

---

### Block Users File

```bash
/etc/ftpusers
```

Users listed here cannot login via FTP.

---

---

# Dangerous FTP Misconfigurations

|Misconfiguration|Risk|
|---|---|
|Anonymous Login Enabled|Data leakage|
|Upload Allowed|Web shell/reverse shell|
|Writable Directories|Malware upload|
|Plain-text Credentials|Sniffing possible|

---

---

# FTP Attack Vectors

## 1. Anonymous Access

```bash
ftp <ip>
Username: anonymous
```

Can expose files without credentials.

---

## 2. Common Credentials

Try weak passwords:

- admin/admin
- ftpuser/ftpuser
- test/test

---

## 3. Hydra Bruteforce

```bash
hydra -L users.txt -P pass.txt ftp://<target-ip>
```

---

## 4. FTP Bounce Attack (Rare Today)

Allows scanning through FTP server:

```bash
nmap -b <ftp-server>:21 <target-network>
```

Most modern servers block this.

---

# Reverse Shell via FTP Upload (CTF Scenario)

Only possible if:

- FTP folder is web-accessible
- PHP execution is enabled

Upload shell:

```bash
ftp> put shell.php
```

Listener:

```bash
nc -lvnp 1234
```

Trigger in browser:

```text
http://target.com/shell.php
```

---
# Pentesting Checklist for FTP

 Scan port 21  
 Grab banner/version  
 Test anonymous login  
 Enumerate files/folders  
 Check upload permissions  
 Look for sensitive documents  
 Try weak credentials  
 Use Hydra if allowed  
 Check vsFTPd vulnerabilities

---

# Summary (Easy Line)

FTP is a file transfer service running on port 21. During pentesting, we check for anonymous login, file access, upload permission, and misconfigurations that may lead to data leaks or remote code execution.
