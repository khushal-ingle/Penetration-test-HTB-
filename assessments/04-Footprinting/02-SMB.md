# SMB Enumration

## Spawn the target, gain a foothold and submit the contents of the user.txt flag.

## 🔐 VPN Connection

>Before interacting with the target, I established a secure connection to the lab using **OpenVPN**.

```
openvp academy-regular.ovpn
```
## Question.1
>What version of the SMB server is running on the target system? Submit the entire banner as the answer.

# SMB Service Detection Using Nmap

### Command Used:

```bash
nmap -p 139,445 -sV 10.129.9.49
```
<img width="348" height="214" alt="image" src="https://github.com/user-attachments/assets/9798c9fc-5125-4fd5-bca2-287ffa461c7d" />

## Answer
> Samba smbd 4


### What is happening here?

This scan is checking whether SMB is running on the machine.

- Port **139** = NetBIOS SMB
    
- Port **445** = Modern SMB over TCP
    

The key discovery is:

The target is running:

```
Samba smbd 4
```

Meaning:

- SMB service is active
    
- File shares may exist
    
- Enumeration is possible
    

---

### Why this matters?

Once you confirm Samba is running, the next step is to find:

- What shares are available
    
- Whether guest login works
    
- If files can be accessed
    

---
## Question.2
> What is the name of the accessible share on the target?

# Listing SMB Shares

### Command Used:

```bash
smbclient -N -L //10.129.18.14
```
<img width="648" height="258" alt="image" src="https://github.com/user-attachments/assets/50540df1-7b6d-4b8d-9195-c056b9411a09" />

## Answer 
> sambashare

### What is happening here?

This command is asking the SMB server:

“Show me all available shared folders”

The server responds with shares:

|Share Name|Type|Purpose|
|---|---|---|
|print$|Disk|Printer files|
|sambashare|Disk|Main shared folder|
|IPC$|IPC|Internal service|

---

### Key Discovery:

 Share found:

```
sambashare
```

Comment reveals:

```
InFreight SMB v3.1
```

This is important because it confirms:

- The share is intentional
    
- It may contain user files
    

---

### Bottom Message:

```
Unable to connect with SMB1
```

This warning happens because:

- SMB1 is disabled (old/insecure)
    
- Server only supports SMB2/SMB3
    

But it is NOT a problem.

---

## Question.3
> Connect to the discovered share and find the flag.txt file. Submit the contents as the answer.

## Step 1: Connecting to the Share

### Command:

```bash
smbclient //10.129.18.14/sambashare
```

You successfully enter the SMB environment:

```
smb: \>
```
## Step 2: Listing Files

### Command:

```bash
ls
```

You see directories and files:

```
.profile
.contents
.bashrc
```

## Step 3: Entering the Directory

### Command:

```bash
cd contents
```

## Step 4: Finding the Flag File

Inside folder:

```bash
ls
```

### Output:

```
flag.txt
```

## Step 5: Downloading the Flag

### Command:

```bash
get flag.txt
```

This copies the file from the target to your Kali system.

### Command:

```bash
cat flag.txt
```
<img width="613" height="887" alt="image" src="https://github.com/user-attachments/assets/8f7872d9-5a70-45ad-8c44-e1e144367d2e" />


## Answer
> HTB{0873nz4xd0873n4z0873zn4fksuhlds}


### What happened overall?

✅ Anonymous SMB share access  
✅ File browsing  
✅ Sensitive file downloaded  
✅ Flag captured

This is a real-world misconfigured share vulnerability.

---

## Question.4
> Find out which domain the server belongs to.

# Domain Enumeration Using rpcclient

### Command Used:

```bash
rpcclient -U "" 10.129.18.32
```

Then:

```bash
querydominfo
```
<img width="464" height="274" alt="image" src="https://github.com/user-attachments/assets/6711c49a-0479-4b47-98d6-3541f24ac492" />


### Answer
> DEVOPS


---

### What is happening here?

Now instead of file browsing, you're using RPC enumeration.

This asks:

👉 “What domain or workgroup does this SMB server belong to?”

The server reveals:

- Domain name: **DEVOPS**
    
- Server name: DEVSMB
    
- Role: Domain Controller-like behavior
    

---

### Why is this useful?

Domain info helps in:

- Username enumeration
    
- Kerberos attacks (if AD exists)
    
- Spray attacks
    
- Further SMB exploitation
    

---
## Question.5
> Find additional information about the specific share we found previously and submit the customized version of that specific share as the answer.

# Extracting Share Information

### Command Used:

```bash
netsharegetinfo sambashare
```
<img width="365" height="162" alt="image" src="https://github.com/user-attachments/assets/b14d6422-9e29-4c18-9fed-0556f1d56731" />

---

## Answer
> InFreight SMB v3.1


### What is happening here?

This command is asking:

👉 “Give me full internal details of the share sambashare”

The server responds with metadata:

- Share remark/description
    
- File path
    
- Permissions
    
- Max users
    

The remark confirms the share identity again.

---

---
## Question.6
>  What is the full system path of that specific share? (format: "/directory/names")
# Revealing the Real Filesystem Path

### Command Again:

```bash
netsharegetinfo sambashare
```
<img width="499" height="411" alt="image" src="https://github.com/user-attachments/assets/d8e5ff13-7266-44e0-bfac-a7a02bc4564c" />


## Answer
> \home\sambauser

### What is happening here?

This is extremely important.

The SMB server reveals:

The share maps directly to:

```
C:\home\sambauser\
```

Meaning:

- sambashare is literally the home folder of a user
    
- Sensitive user data may be exposed
    
- Misconfigured access permissions exist
    

---

### Why this matters?

If you had write access, you could potentially:

 Upload malicious scripts  
 Drop SSH keys  
 Gain shell access

---

---

# Final Summary

|Question|Flags|Meaning|
|---|---|---|
|1|Samba smbd 4|SMB service confirmed running|
|2|sambashare Disk|Share discovered for exploitation|
|3|flag.txt + HTB flag|Share accessed, flag downloaded|
|4|Domain DEVOPS|Domain/workgroup info revealed|
|5|Remark InFreight|Share metadata confirmed|
|6|Path C:\home\sambauser\ |Real directory exposure discovered|

---

# ✅ What You Achieved

✅ Identified SMB service  
✅ Enumerated available shares  
✅ Accessed share anonymously  
✅ Found sensitive file  
✅ Extracted the flag  
✅ Enumerated domain + share path  
✅ Confirmed misconfiguration vulnerability

---
