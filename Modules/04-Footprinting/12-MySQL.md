
## 1. What is MySQL?

MySQL is an **open-source relational database management system (RDBMS)** developed and supported by **Oracle**.

A database is a structured collection of data designed for efficient storage, retrieval, and management. MySQL is optimized for high performance while using minimal disk space.

* Uses **SQL (Structured Query Language)**
* Stores data in **tables (rows and columns)**
* Works on a **client–server architecture**
* Commonly stored or backed up as `.sql` files
  Example: `wordpress.sql`

---

## 2. MySQL Architecture (Client–Server Model)

### MySQL Server

The MySQL server is the core database engine responsible for:

* Data storage
* Query execution
* User authentication
* Access control and permissions

Default data directory:

```
/var/lib/mysql
```

### MySQL Clients

Clients communicate with the server using SQL queries.

Examples:

* `mysql` CLI tool
* Web applications (PHP, Python, Java)
* CMS platforms

Clients can:

* Insert, update, delete, and retrieve data
* Execute multiple queries simultaneously

Access can be:

* Localhost only (recommended)
* Internal network
* Public internet (high security risk)

---

## 3. Real-World Example: WordPress

WordPress uses MySQL to store:

* Posts and pages
* Usernames
* Password hashes
* Site configuration
* Plugin and theme data

In secure setups, the database is accessible only from `localhost`. Misconfigurations often expose it to attackers.

---

## 4. MySQL in Web Application Stacks

Common technology stacks:

* **LAMP**: Linux, Apache, MySQL, PHP
* **LEMP**: Linux, Nginx, MySQL, PHP

Typical stored data:

* Usernames and email addresses
* Password hashes
* Roles and permissions
* Form inputs
* Internal and external links
* Application-specific values

Sensitive data such as passwords should never be stored in plaintext. They are usually hashed using one-way encryption methods at the application level.

---

## 5. MariaDB vs MySQL

**MariaDB** is a fork of MySQL.

Reason:

* After MySQL was acquired by Oracle, the original developers created MariaDB
* Fully open-source
* Highly compatible with MySQL
* Same commands and syntax in most cases

---

## 6. Default MySQL Configuration (Linux)

Main configuration file:

```
/etc/mysql/mysql.conf.d/mysqld.cnf
```

Key defaults:

* Port: `3306`
* Runs as OS user: `mysql`
* Socket file:

  ```
  /var/run/mysqld/mysqld.sock
  ```
* Data directory:

  ```
  /var/lib/mysql
  ```

Incorrect file permissions on configuration files can expose database credentials.

---

## 7. Dangerous and Security-Relevant Settings

| Setting            | Description                  |
| ------------------ | ---------------------------- |
| `user`             | OS user running MySQL        |
| `password`         | May be stored in plaintext   |
| `admin_address`    | Admin network interface      |
| `debug`            | Enables verbose debug output |
| `sql_warnings`     | Displays SQL warnings        |
| `secure_file_priv` | Restricts file import/export |

Security risks:

* Credential leakage
* Information disclosure via error messages
* SQL injection exploitation
* Potential file read/write through SQL

Verbose error output should never be exposed to users.

---

## 8. Footprinting the MySQL Service

Default MySQL port:

```
TCP 3306
```

### Nmap Scan Example

```bash
nmap -sV -sC -p3306 --script mysql* <IP>
```

Information that may be discovered:

* MySQL version
* Authentication plugin
* Valid usernames
* Weak or empty passwords

Scan results must always be manually verified due to possible false positives.

---

## 9. Interacting with a MySQL Server

### Login without password

```bash
mysql -u root -h <IP>
```

### Login with password

```bash
mysql -u root -pPASSWORD -h <IP>
```

---

## 10. Important Default Databases

| Database             | Purpose                      |
| -------------------- | ---------------------------- |
| `mysql`              | Users, roles, privileges     |
| `information_schema` | Metadata (ANSI standard)     |
| `performance_schema` | Performance monitoring       |
| `sys`                | Aggregated system statistics |

These databases are critical during enumeration and post-exploitation.

---

## 11. Essential MySQL Commands

| Command                                    | Description          |
| ------------------------------------------ | -------------------- |
| `show databases;`                          | List all databases   |
| `use <database>;`                          | Select a database    |
| `show tables;`                             | List tables          |
| `show columns from <table>;`               | View table structure |
| `select * from <table>;`                   | Dump table data      |
| `select * from <table> where col="value";` | Filter records       |

These commands are fundamental for:

* SQL injection exploitation
* Database enumeration
* Credential harvesting

---

## 12. Security Best Practices

* Do not expose MySQL to the internet
* Disable remote root login
* Never allow empty passwords
* Restrict file operations using `secure_file_priv`
* Use least-privilege database users
* Suppress SQL error output in production
* Regularly audit configuration and permissions
