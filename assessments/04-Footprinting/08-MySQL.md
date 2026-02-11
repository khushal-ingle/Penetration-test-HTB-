
# MySQL Enumeration Walkthrough

---

## Task Goal

The objective of this task is to enumerate the MySQL service running on the target host in order to:

1. Identify the **MySQL server version**
2. Test discovered weak credentials against the database
3. Enumerate databases and tables
4. Retrieve the email address of the customer **"Otto Lang"**

---

## Question.1

> Enumerate the MySQL server and determine the version in use. (Format: MySQL X.X.XX)

---

### Command executed

```bash
nmap 10.129.41.253 -sV -sC -p3306 --script=banner
```
<img width="595" height="196" alt="image" src="https://github.com/user-attachments/assets/92e3f078-f641-47e6-8311-152b0d044eb1" />

---

### What is happening in this screenshot

An Nmap scan is performed against port **3306**, the default MySQL port.

The scan uses:

* `-sV` to detect the service version
* `-sC` to run default scripts
* `--script=banner` to extract the service banner

---

### Highlighted result

```text
3306/tcp open  mysql  MySQL 8.0.27
```

---

### Explanation

The MySQL service banner clearly discloses the running version:

```
MySQL 8.0.27
```

This confirms the exact database version deployed on the system.

---

### Answer

```
MySQL 8.0.27
```

---

## Question.2

> During our penetration test, we found weak credentials "robin:robin". We should try these against the MySQL server. What is the email address of the customer "Otto Lang"?

---

## Step 1 — Attempt MySQL Login

### Initial command

```bash
mysql -u robin -p -h 10.129.35.247
```
<img width="652" height="106" alt="image" src="https://github.com/user-attachments/assets/9ca450bc-9157-4500-b9bb-b1d0cf915fb7" />

---

### What is happening

The login attempt initially fails due to a TLS/SSL certificate verification issue:

```text
ERROR 2026 (HY000): TLS/SSL error: self-signed certificate in certificate chain
```

---

## Step 2 — Bypass SSL Verification

### Command executed

```bash
mysql -u robin -p -h 10.129.35.247 --skip-ssl
```
<img width="655" height="235" alt="image" src="https://github.com/user-attachments/assets/e8ecebf1-8538-43f5-a69e-d13454f8e9fe" />

---

### What is happening

The `--skip-ssl` flag disables SSL verification, allowing authentication using the weak credentials:

```
Username: robin
Password: robin
```

The login succeeds, granting access to the MySQL console.

---

## Step 3 — Enumerate Databases

### Command executed

```sql
show databases;
```
<img width="278" height="211" alt="image" src="https://github.com/user-attachments/assets/50d69329-0b5e-4b7a-abeb-3eeca5eec6cc" />

---

### Result

```text
customers
information_schema
mysql
performance_schema
sys
```

---

### Explanation

The database `customers` appears to contain application data and is the most relevant for further enumeration.

---

## Step 4 — Inspect Tables Inside customers Database

### Commands executed

```sql
use customers;
show tables;
```
<img width="545" height="232" alt="image" src="https://github.com/user-attachments/assets/706d0c3d-af2e-4178-b15f-26bcb0003d54" />

---

### Result

```text
myTable
```

---

### Explanation

The database contains a table named `myTable`, which likely stores customer records.

---

## Step 5 — Inspect Table Structure

### Command executed

```sql
show columns in myTable;
```
<img width="620" height="283" alt="image" src="https://github.com/user-attachments/assets/9413e460-3110-4862-ab50-072168f5f924" />

---

### Result (Relevant Fields)

```text
id
name
email
country
postalZip
city
address
pan
cvv
```

---

### Explanation

The table contains a column named `email`, which is required to answer the question.

---

## Step 6 — Retrieve Email for "Otto Lang"

### Command executed

```sql
select email from myTable where name="Otto Lang";
```

---

### Highlighted result

```text
ultrices@google.htb
```

---

### Explanation

The query filters the table for the record where the name equals `"Otto Lang"` and retrieves the associated email address.

---

### Answer

```
ultrices@google.htb
```

---

## Final Answers Summary

| Question        | Answer                                            |
| --------------- | ------------------------------------------------- |
| MySQL Version   | MySQL 8.0.27                                      |
| Otto Lang Email | [ultrices@google.htb](mailto:ultrices@google.htb) |

