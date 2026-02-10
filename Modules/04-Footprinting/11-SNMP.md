

---

## 1. What is SNMP?

**SNMP (Simple Network Management Protocol)** is a **UDP-based protocol** used to:

* Monitor network devices
* Manage configurations remotely
* Collect performance & system information

### SNMP-enabled devices include:

* Routers & switches
* Servers (Linux / Windows)
* Firewalls
* Printers
* IoT & embedded devices

📌 **Current secure version:** SNMPv3
📌 **Most commonly misconfigured in real environments:** SNMPv1 & SNMPv2c

---

## 2. SNMP Architecture (How it Works)

SNMP uses a **manager–agent model**:

| Component                 | Description                                        |
| ------------------------- | -------------------------------------------------- |
| **SNMP Manager (Client)** | Monitoring system (Nagios, Zabbix, attacker tools) |
| **SNMP Agent (Server)**   | Runs on device, exposes information                |
| **MIB**                   | Blueprint describing what can be queried           |
| **OID**                   | Unique identifier for each value                   |

---

## 3. SNMP Ports & Communication

| Port        | Purpose                        |
| ----------- | ------------------------------ |
| **UDP 161** | Queries (GET / SET)            |
| **UDP 162** | Traps (server → client alerts) |

### Polling vs Traps

* **Polling:** Client requests data
* **Trap:** Server sends alert automatically when an event occurs

---

## 4. MIB (Management Information Base)

* A **text file** defining:

  * What information exists
  * How to access it
  * Data type & permissions

📌 **Important:**

* MIB **does NOT store data**
* It only explains **where and how data is stored**

### MIB Format

* Written in **ASN.1**
* Structured like a **tree**

---

## 5. OID (Object Identifier)

An **OID** is a **unique numeric path** inside the MIB tree.

### Example:

```
1.3.6.1.2.1.1.1.0
```

### Meaning:

| Part    | Description    |
| ------- | -------------- |
| 1.3.6.1 | ISO / Internet |
| 2.1     | Management     |
| 1       | System         |
| 1.0     | OS Description |

📌 **Longer OID = More specific information**

---

## 6. SNMP Versions Comparison

### 🔴 SNMPv1

* ❌ No authentication
* ❌ No encryption
* ❌ Plaintext community strings
* ✔ Still used in small networks

---

### 🟠 SNMPv2c

* Community-based authentication
* ❌ Still plaintext
* ✔ Faster & more features than v1

📌 **v2c = v2 + community string**

---

### 🟢 SNMPv3 (Secure)

* ✔ Username & password
* ✔ Encryption
* ✔ Integrity checks
* ❌ Complex to configure

| Feature                | v1 | v2c | v3 |
| ---------------------- | -- | --- | -- |
| Authentication         | ❌  | ❌   | ✅  |
| Encryption             | ❌  | ❌   | ✅  |
| Commonly Misconfigured | ✅  | ✅   | ❌  |

---

## 7. Community Strings

Community strings act like **passwords**.

### Common defaults:

```
public   → read-only
private  → read-write
```

### Problems:

* Sent **in plaintext**
* Often reused across systems
* Frequently left unchanged

📌 **If attacker gets community string → full system visibility**

---

## 8. SNMP Daemon Configuration (Linux)

### Config file:

```
/etc/snmp/snmpd.conf
```

### Example (cleaned):

```
sysLocation    Sitting on the Dock of the Bay
sysContact     me@example.org
agentaddress  127.0.0.1,[::1]

view systemonly included .1.3.6.1.2.1.1
rocommunity public default -V systemonly
```

---

## 9. ⚠️ Dangerous SNMP Settings

| Setting              | Risk                          |
| -------------------- | ----------------------------- |
| `rwuser noauth`      | Full access without auth      |
| `rwcommunity public` | Full read/write via plaintext |
| No IP restriction    | Anyone can query              |

🚨 **This leads to full system enumeration and sometimes RCE**

---

## 10. Footprinting SNMP (Pentesting View)

### Step 1: Check if SNMP is open

```
nmap -sU -p 161 <IP>
```

---

## 11. SNMP Enumeration with `snmpwalk`

### Command:

```bash
snmpwalk -v2c -c public 10.129.14.128
```

### What it does:

* Walks the entire OID tree
* Dumps system information

### You can extract:

* OS version
* Hostname
* Uptime
* Installed software
* Users
* Network interfaces
* Running processes

📌 **Equivalent to internal system enumeration**

---

## 12. Brute-Forcing Community Strings (`onesixtyone`)

### Install:

```bash
sudo apt install onesixtyone
```

### Command:

```bash
onesixtyone -c snmp.txt 10.129.14.128
```

### Output:

```
10.129.14.128 [public] Linux Ubuntu ...
```

📌 **Once community string is known → full SNMP access**

---

## 13. Enumerating Specific OIDs (`braa`)

### Install:

```bash
sudo apt install braa
```

### Command:

```bash
braa public@10.129.14.128:.1.3.6.*
```

### Purpose:

* Fast brute-force of OIDs
* Useful when MIBs are unknown

---

## 14. Why SNMP is Dangerous in Pentesting

SNMP leaks:

* OS details
* Installed software
* Emails & usernames
* Network layout
* Sometimes credentials

💣 **Misconfigured SNMP = internal access without login**

---

## 15. Attacker Workflow (Interview-Ready)

```
Nmap → SNMP detected
↓
onesixtyone → community string
↓
snmpwalk → full system dump
↓
braa → targeted OID enumeration
↓
Privilege escalation / lateral movement
```

---

## 16. Final Advice (Very Important)

> SNMP is a **blessing for admins**
> SNMP is a **nightmare for security**

🧪 **Best practice to learn SNMP:**
👉 Set up a VM, install `snmpd`, misconfigure it, then attack it yourself.

---

