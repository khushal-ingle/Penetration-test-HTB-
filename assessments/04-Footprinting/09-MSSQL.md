
# MSSQL Enumeration Walkthrough

---

## Task Goal

The objective of this task is to enumerate the MSSQL service running on the target host in order to:

1. Identify the **hostname of the MSSQL server**
2. Connect to the MSSQL instance using provided credentials
3. Enumerate and identify any **non-default databases**

---

## Question.1

> Enumerate the target and list the hostname of the MSSQL server.

---

### Command executed

```bash
sudo nmap -sV -p 1433 --script "ms-sql-*" 10.129.36.21
```
<img width="566" height="759" alt="image" src="https://github.com/user-attachments/assets/44da80a9-f5c8-4003-ad37-22632e113b01" />

---

### What is happening in this screenshot

An Nmap scan is performed against port **1433**, the default port for Microsoft SQL Server.

The scan uses:

* `-sV` for version detection
* `--script "ms-sql-*"` to run MSSQL-specific NSE scripts

This retrieves detailed information about:

* MSSQL version
* Instance name
* Domain details
* Hostname

---

### Highlighted result

```text
Target_Name: ILF-SQL-01
```

---

### Explanation

The `ms-sql-ntlm-info` script reveals the NetBIOS and DNS name of the target.

The MSSQL server hostname is:

```
ILF-SQL-01
```

---

### Answer

```
ILF-SQL-01
```

---

## Question.2

> Connect to the MSSQL instance running on the target using the account (backdoor:Password1), then list the non-default database present on the server.

---

## Step 1 — Install Impacket MSSQL Client

Before connecting, ensure the Impacket tools are installed:

```bash
apt install impacket-scripts
```

---

## Step 2 — Connect to MSSQL Using Impacket

### Command executed

```bash
impacket-mssqlclient backdoor@10.129.36.21 -windows-auth
```

When prompted, enter the password:

```
Password1
```
<img width="571" height="482" alt="image" src="https://github.com/user-attachments/assets/5cbf8fe5-ecc0-44c8-9553-90b68594d6ab" />


---

### What is happening

The tool establishes an authenticated MSSQL session using:

* Username: `backdoor`
* Password: `Password1`
* Windows authentication
* Automatic TLS negotiation

Successful login confirms valid credentials.

---

## Step 3 — Enumerate Databases

### Command executed inside MSSQL

```sql
select name from sys.databases;
```

---

### Highlighted result

```text
master
tempdb
model
msdb
Employees
```

---

### Explanation

The default MSSQL system databases are:

* master
* tempdb
* model
* msdb

The only **non-default** database present is:

```
Employees
```

---

### Answer

```
Employees
```

---

## Final Answers Summary

| Question             | Answer     |
| -------------------- | ---------- |
| MSSQL Hostname       | ILF-SQL-01 |
| Non-default Database | Employees  |

---
