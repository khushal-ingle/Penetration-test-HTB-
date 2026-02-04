

---

#  NFS (Network File System) 

---

## 1. What is NFS?

**NFS (Network File System)** is a protocol that allows a Linux/Unix machine to share folders over a network.

Think of it like this:

> A remote folder becomes accessible on your computer just like a USB drive.

So instead of copying files, you can directly mount and browse them.

 Similar to SMB, but:

- SMB → Mostly Windows
- NFS → Mostly Linux/Unix

---
## 2. Why is NFS Important in Pentesting?

Because:

- Misconfigured NFS shares can leak sensitive files
- Sometimes you can write files inside the share
- Bad settings can lead to **privilege escalation**
---

#  NFS Versions Explained

|Version|Key Point|
|---|---|
|NFSv2|Old, works over UDP only|
|NFSv3|Better features, variable file sizes|
|NFSv4|Secure, firewall friendly, supports Kerberos|
|NFSv4.1|Cluster support + multipathing|

 Modern NFS mainly uses:

- TCP/UDP port **2049**
---

#  NFS Uses RPC (Very Important)

NFS works using **ONC-RPC** (Remote Procedure Calls)

So you will always see these ports:

|Port|Service|
|---|---|
|111|rpcbind|
|2049|nfs|

### Why?

Because rpcbind tells the client:

> Which RPC services are running and on what ports.
---

#  Default Configuration File: `/etc/exports`

This file decides:

 What folder is shared  
 Who can access it  
 What permissions they have

Example:

```bash
/srv/homes hostname1(rw)
/srv/homes hostname2(ro)
```

---

## Example `/etc/exports`

```bash
/mnt/nfs 10.129.14.0/24(sync,no_subtree_check)
```

### Meaning:

|Part|Explanation|
|---|---|
|/mnt/nfs|Folder being shared|
|10.129.14.0/24|Only this subnet can access|
|sync|Safe writing method|
|no_subtree_check|Faster, but risky|

---

---

#  Export Options (Most Important)

|Option|Meaning|
|---|---|
|rw|Read + Write access|
|ro|Read only|
|sync|Writes happen safely (slower)|
|async|Faster but unsafe|
|root_squash|Root becomes unprivileged user|
|no_root_squash|Root stays root (VERY dangerous)|
|insecure|Allows access from ports >1024|

---
#  Dangerous Settings (Pentester Gold)

These are misconfigurations:

|Option|Risk|
|---|---|
|rw|Attacker can upload files|
|insecure|Any user can connect|
|no_root_squash|Root attacker becomes root on server|
|nohide|Hidden mounted dirs become visible|

 **no_root_squash = Privilege Escalation**

---
#  Creating an NFS Share (Server Side)

Command:

```bash
echo '/mnt/nfs 10.129.14.0/24(sync,no_subtree_check)' >> /etc/exports
```

### Meaning:

- Adds share rule into exports file
---

Restart service:

```bash
systemctl restart nfs-kernel-server
```

 Applies changes

---

Check exported shares:

```bash
exportfs
```

Output:

```
/mnt/nfs 10.129.14.0/24
```

 Now the folder is shared.

---

---

#  Footprinting NFS (Pentesting Phase)

We first scan ports **111 and 2049**

---

## Step 1: Nmap Scan

```bash
sudo nmap 10.129.14.128 -p111,2049 -sV -sC
```

### Breakdown:

|Flag|Meaning|
|---|---|
|-p111,2049|Scan NFS related ports|
|-sV|Detect service versions|
|-sC|Run default NSE scripts|

---

### Output Explanation

```
111/tcp open rpcbind
2049/tcp open nfs
```

 Confirms NFS is running.

---

---

## Step 2: rpcinfo Script Output

This part:

```bash
| rpcinfo:
```

Shows all RPC services.

Example:

```
100003 → NFS service
100005 → mountd
100021 → lock manager
```

 This confirms:

- NFS is active
    
- Mounting is supported
    

---

---

#  NFS Enumeration Scripts

```bash
sudo nmap --script nfs* 10.129.14.128 -p111,2049
```

### What this does:

Runs all NFS NSE scripts like:

|Script|Purpose|
|---|---|
|nfs-showmount|Lists shares|
|nfs-ls|Lists directory contents|
|nfs-statfs|Shows disk usage|

---

### Output Example:

```
nfs-showmount:
/mnt/nfs 10.129.14.0/24
```

 Share found!

---

---

#  Step 3: Listing Shares Manually

```bash
showmount -e 10.129.14.128
```

### Meaning:

- `-e` → show exported directories

Output:

```
/mnt/nfs 10.129.14.0/24
```

 Remote share available.
 
---

#  Step 4: Mounting the Share

Create mount folder:

```bash
mkdir target-NFS
```

Mount:

```bash
sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock
```

### Breakdown:

|Part|Meaning|
|---|---|
|-t nfs|NFS mount type|
|10.129.14.128:/|Remote root export|
|./target-NFS|Local mount folder|
|nolock|Avoid lock errors|

 Now the share is accessible locally.
 
---

#  Step 5: Viewing Files

```bash
tree .
```

Output:

```
mnt/nfs/id_rsa
mnt/nfs/id_rsa.pub
nfs.share
```

 This is huge because:

- `id_rsa` = private SSH key leak
---

#  Step 6: Checking Permissions

### List with usernames:

```bash
ls -l mnt/nfs/
```

Output:

```
-rw-r--r-- root root id_rsa
```

 File owned by root

---

### List with numeric UID/GID:

```bash
ls -n mnt/nfs/
```

Output:

```
-rw-r--r-- 0 0 id_rsa
```

UID 0 = root  
GID 0 = root

---

#  Root Squash Impact

If `root_squash` enabled:

- Even if attacker is root locally
- They become user **nobody (65534)**

So root cannot edit files.

But if:

```bash
no_root_squash
```

 Attacker becomes root on server.

---

---

#  Privilege Escalation via NFS

If share is writable + no_root_squash:

- Attacker uploads a SUID shell
    
- Executes it on target
    
- Gets root
    

Classic escalation technique.

---

---

#  Unmounting After Use

```bash
sudo umount ./target-NFS
```

 Safely disconnect share.

---

---

#  Pentesting Checklist for NFS

|Step|Command|
|---|---|
|Scan ports|`nmap -p111,2049 -sV`|
|List RPC services|`rpcinfo -p <ip>`|
|Find exports|`showmount -e <ip>`|
|Run NSE scripts|`nmap --script nfs*`|
|Mount share|`mount -t nfs <ip>:/ <dir>`|
|Enumerate files|`ls -la`|
|Look for keys/configs|`cat id_rsa`|
|Check root squash|permissions + exports|

---

---

#  Key Takeaway

NFS is dangerous when:

 Shares are public  
 Writable  
 no_root_squash enabled  
 Sensitive files exposed
