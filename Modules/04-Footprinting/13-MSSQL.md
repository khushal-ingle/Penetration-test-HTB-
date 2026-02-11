
## What is MSSQL?

Microsoft SQL Server (MSSQL) is Microsoft’s relational database management system (RDBMS).

* Closed-source
* Originally built for Windows
* Strong integration with the .NET framework
* Now also available on Linux and macOS
* Common in Active Directory environments

In real-world penetration testing, MSSQL is frequently found in corporate Windows domain networks.

---

# MSSQL Clients

## 1. SQL Server Management Studio (SSMS)

* Official GUI client from Microsoft
* Used for managing databases
* Often installed on database servers and admin workstations
* May contain saved credentials

From a pentesting perspective, a compromised workstation with SSMS may expose saved database credentials.

---

## 2. Other MSSQL Clients

| Client                  | Usage                  |
| ----------------------- | ---------------------- |
| mssql-cli               | Command-line client    |
| SQL Server PowerShell   | Automation             |
| HeidiSQL                | GUI                    |
| SQLPro                  | GUI                    |
| Impacket mssqlclient.py | Common pentesting tool |

---

## Pentester Favorite: Impacket mssqlclient.py

Impacket is preinstalled in many pentesting distributions like Kali Linux.

Locate it with:

```bash
locate mssqlclient
```

Common path:

```
/usr/bin/impacket-mssqlclient
```

Advantages:

* Supports Windows authentication
* Supports pass-the-hash
* Works well in Active Directory environments

---

# Default MSSQL System Databases

These databases exist by default:

| Database | Purpose                              |
| -------- | ------------------------------------ |
| master   | Stores system-level information      |
| model    | Template for new databases           |
| msdb     | SQL Server Agent jobs and scheduling |
| tempdb   | Temporary objects                    |
| resource | Read-only system objects             |

To list databases:

```sql
select name from sys.databases;
```

Typical output:

```
master
tempdb
model
msdb
```

Any additional database is usually user-created and worth investigating.

---

# Default Configuration

After installation:

* Service runs as:

  ```
  NT SERVICE\MSSQLSERVER
  ```

* Default listening port:

  ```
  TCP 1433
  ```

* Authentication types:

  * Windows Authentication (default)
  * SQL Authentication (sa account)

## Windows Authentication

Uses:

* Local SAM database
* Or Active Directory domain controller

If a domain user with database access is compromised, it can enable lateral movement inside the domain.

---

# Dangerous MSSQL Settings

Common misconfigurations to check:

1. No encryption
   Clients connecting without TLS may expose credentials.

2. Self-signed certificates
   Can potentially be spoofed.

3. Named pipes enabled
   Example:

   ```
   \\10.129.201.248\pipe\sql\query
   ```

4. Weak or default sa credentials
   Administrators sometimes forget to disable or secure the sa account.

---

# Footprinting MSSQL

## Nmap Enumeration

Example scan:

```bash
sudo nmap -sV -p 1433 --script ms-sql-info 10.129.201.248
```

Useful NSE scripts:

* ms-sql-info
* ms-sql-ntlm-info
* ms-sql-dac
* ms-sql-dump-hashes
* ms-sql-config

Information gathered may include:

* Hostname
* Instance name
* MSSQL version
* Named pipes
* Domain details
* Clustering status

Example findings:

```
Microsoft SQL Server 2019
Version: 15.00.2000.00
Instance: MSSQLSERVER
Named Pipe: \\SQL-01\pipe\sql\query
```

---

## Metasploit Enumeration

Module:

```
auxiliary(scanner/mssql/mssql_ping)
```

Provides:

* ServerName
* InstanceName
* Version
* TCP port
* Named pipe

Useful for quick service discovery.

---

# Connecting with mssqlclient.py

Example:

```bash
python3 mssqlclient.py Administrator@10.129.201.248 -windows-auth
```

If encryption is required:

```
Encryption required, switching to TLS
```

After connecting:

```sql
select name from sys.databases;
```

Example output:

```
master
tempdb
model
msdb
Transactions
```

The presence of a custom database like `Transactions` suggests a user-created database worth enumerating.

---

# Post-Login Enumeration Steps

After authentication:

1. List databases:

   ```sql
   select name from sys.databases;
   ```

2. Switch database:

   ```sql
   use Transactions;
   ```

3. List tables:

   ```sql
   select name from sys.tables;
   ```

4. Dump table data:

   ```sql
   select * from users;
   ```

5. Identify current user:

   ```sql
   select SYSTEM_USER;
   ```

---

# Pentesting Perspective

In Windows domain environments, MSSQL is often a pivot point.

A compromised MSSQL server can lead to:

* xp_cmdshell abuse
* Command execution
* Credential harvesting
* Privilege escalation
* Lateral movement within Active Directory
