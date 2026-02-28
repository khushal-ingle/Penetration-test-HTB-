
## Why Databases Matter in Metasploit

During real engagements, you quickly accumulate:

* Multiple hosts
* Dozens of open services
* Scan results from Nmap, Nessus, etc.
* Credentials, hashes, loot
* Notes, vulnerabilities, exploitation attempts

Trying to track all of this manually becomes unreliable very fast.

**The Metasploit database solves this problem** by acting as a centralized intelligence store for everything you discover.

---

## Database Backend Used by Metasploit

Metasploit uses **PostgreSQL** as its database backend.

The database allows msfconsole to:

* Store scan results permanently
* Correlate hosts, services, vulnerabilities, and creds
* Auto-populate exploit parameters (RHOSTS, ports, services)
* Import and export data from third-party tools

---

## Checking PostgreSQL Status

Before using the database, PostgreSQL must be running.

```bash
sudo service postgresql status
```

If it is not running:

```bash
sudo systemctl start postgresql
```

---

## Initializing the Metasploit Database

Metasploit provides a helper tool called **msfdb**.

### Initialize the database:

```bash
sudo msfdb init
```

This will:

* Create PostgreSQL users
* Create `msf` and `msf_test` databases
* Generate `database.yml`
* Create the database schema

If the database is already configured, Metasploit will skip initialization safely.

---

## Verifying Database Connection

Once msfconsole is started, verify connectivity:

```bash
db_status
```

Expected output:

```
[*] Connected to msf. Connection type: postgresql.
```

If you see this, the database is working correctly.

---

## Common Database Recovery Steps (When Things Break)

If you encounter database issues:

```bash
msfdb reinit
cp /usr/share/metasploit-framework/config/database.yml ~/.msf4/
sudo service postgresql restart
msfconsole -q
```

Then recheck:

```bash
db_status
```

---

## Core Database Commands in msfconsole

You can view database-related commands using:

```bash
help database
```

Key commands you should remember:

| Command     | Purpose                    |
| ----------- | -------------------------- |
| `db_status` | Check database connection  |
| `db_import` | Import scan results        |
| `db_export` | Export database data       |
| `db_nmap`   | Run Nmap and store results |
| `hosts`     | View discovered hosts      |
| `services`  | View discovered services   |
| `vulns`     | View vulnerabilities       |
| `creds`     | View credentials           |
| `loot`      | View collected loot        |
| `workspace` | Manage projects            |

---

## Workspaces: Organizing Your Engagement

Workspaces act like **project folders**.

Each workspace has its own:

* Hosts
* Services
* Credentials
* Notes
* Loot

### View current workspaces:

```bash
workspace
```

### Create a new workspace:

```bash
workspace -a Target_1
```

### Switch workspace:

```bash
workspace Target_1
```

### Delete a workspace:

```bash
workspace -d Target_1
```

Using workspaces is **mandatory in real engagements** to avoid mixing data.

---

## Importing Scan Results into the Database

Metasploit supports importing scan output from tools like:

* Nmap
* Nessus
* Nexpose

### Preferred format:

**Nmap XML**

### Import example:

```bash
db_import Target.xml
```

After importing, data becomes immediately searchable.

---

## Viewing Stored Hosts

```bash
hosts
```

This shows:

* IP addresses
* OS information
* Hostnames
* Comments
* Tags

You can also:

* Add hosts manually
* Rename hosts
* Add comments
* Set RHOSTS automatically from results

Example:

```bash
hosts -R
```

This sets `RHOSTS` using the current host list.

---

## Viewing Stored Services

```bash
services
```

This shows:

* Open ports
* Protocols
* Service names
* Versions

You can filter by:

* Port
* Protocol
* Service name
* Host

Example:

```bash
services -p 445
```

---

## Running Nmap Directly from msfconsole

Instead of switching terminals:

```bash
db_nmap -sS -sV 10.10.10.8
```

Advantages:

* Results are automatically stored
* Hosts and services populate instantly
* No manual importing required

---

## Credentials Management (`creds`)

The `creds` command stores:

* Plaintext passwords
* NTLM hashes
* SSH keys
* Database hashes
* Cracked credentials

### View all credentials:

```bash
creds
```

### Add credentials manually:

```bash
creds add user:admin password:Password123
```

### Filter credentials:

```bash
creds -s smb
creds -p 22
creds -t ntlm
```

### Export for cracking:

```bash
creds -o hashes.jtr
```

---

## Loot Management (`loot`)

Loot refers to **extracted sensitive files**, such as:

* SAM dumps
* `/etc/shadow`
* Config files
* Database dumps

### View loot:

```bash
loot
```

### Add loot manually:

```bash
loot -f hashdump.txt -i "SAM dump" -a 10.10.10.40 -t hashdump
```

---

## Exporting the Database (Backup)

Always export your data after an engagement.

```bash
db_export -f xml backup.xml
```

You can later restore it using:

```bash
db_import backup.xml
```

---

## Why Databases Are Critical in Real Engagements

Without the database:

* You lose context
* You repeat scans
* You miss attack paths
* You forget credentials
* You misconfigure exploits

With the database:

* Exploits auto-fill parameters
* Pivoting becomes easier
* Credential reuse is obvious
* Reporting is faster and cleaner

---

## Exam and Lab Takeaways

* Always verify `db_status`
* Use workspaces for every target
* Prefer `db_nmap` over manual scans
* Import XML, not plain text
* Use `hosts -R` and `services -R`
* Always export results at the end

---

