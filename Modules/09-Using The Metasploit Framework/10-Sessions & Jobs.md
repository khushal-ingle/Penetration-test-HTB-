
## What Are Sessions in Metasploit

A **session** is a **live communication channel** established between Metasploit and a compromised target after a successful exploit or payload execution.

Each session represents:

* One compromised host
* One payload instance
* One active control interface (shell, Meterpreter, etc.)

Metasploit can manage **multiple sessions simultaneously**, which allows you to:

* Control several machines at once
* Pivot from one system to another
* Run post-exploitation modules without re-exploiting

This multi-session capability is one of Metasploit’s biggest strengths.

---

## Types of Sessions

Common session types include:

* **Meterpreter sessions**

  * Advanced, memory-resident
  * Rich command set
  * Most commonly used

* **Shell sessions**

  * Basic command shells
  * Limited functionality
  * OS-native (cmd, bash, sh)

Each session is assigned a **unique ID**.

---

## Backgrounding a Session

Once a session is active, you can **send it to the background** while keeping it alive.

### Why background a session?

* To launch another exploit
* To run post-exploitation modules
* To manage multiple targets
* To keep access while working elsewhere

### How to background a session

From a Meterpreter session:

```bash
background
```

Or using keyboard:

```
CTRL + Z
```

After confirmation, you return to:

```text
msf6 >
```

The session **continues running** in the background.

---

## Listing Active Sessions

To view all current sessions:

```bash
sessions
```

Example output:

```text
Id  Name  Type                     Information                 Connection
--  ----  ----                     -----------                 ----------
1         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ MS01  10.10.10.129:443 -> 10.10.10.205:50501
```

Important fields:

* **Id**: Session identifier
* **Type**: Meterpreter or shell
* **Information**: User + host
* **Connection**: Attacker ↔ Target

---

## Interacting With a Session

To interact with a specific session:

```bash
sessions -i <session_id>
```

Example:

```bash
sessions -i 1
```

This drops you directly back into that session’s interface (Meterpreter or shell).

This is extremely useful when:

* Running multiple sessions
* Switching between hosts
* Performing post-exploitation

---

## Using Sessions With Post-Exploitation Modules

Many **post modules** require an **existing session** instead of a new exploit.

Typical workflow:

1. Exploit a target → get session
2. Background the session
3. Search for post modules
4. Set SESSION option
5. Run module on that session

Example:

```bash
set SESSION 1
run
```

Post modules include:

* Credential harvesting
* Privilege escalation suggesters
* Internal network enumeration
* Token impersonation
* Persistence

---

## When Sessions Die

Sessions are not permanent. They can die if:

* Payload crashes
* Target reboots
* Network connectivity drops
* Antivirus kills the payload
* User logs out (for some shells)

When a session dies, it disappears from:

```bash
sessions
```

---

## Jobs vs Sessions (Very Important)

### Session

* Interactive access to a target
* Represents a compromised system
* You can issue commands

### Job

* Background task running in Metasploit
* No direct interaction
* Often listeners, scanners, handlers

A session may be **created by a job**, but they are not the same thing.

---

## Jobs: Why They Matter

If you start an exploit or handler that binds to a port, stopping it with:

```bash
CTRL + C
```

may **not release the port**.

That port remains in use because the task is still running as a **job**.

---

## Viewing Running Jobs

List all jobs:

```bash
jobs -l
```

Example:

```text
Id  Name                    Payload                    Payload opts
--  ----                    -------                    ------------
0   Exploit: multi/handler  generic/shell_reverse_tcp  tcp://10.10.14.34:4444
```

---

## Killing Jobs

Kill a specific job:

```bash
jobs -k <job_id>
```

Kill all jobs:

```bash
jobs -K
```

This is how you **free ports** and stop background listeners safely.

---

## Running Exploits as Jobs

Instead of blocking your terminal, you can run exploits in the background.

Use:

```bash
exploit -j
```

This:

* Starts the exploit as a job
* Keeps msfconsole usable
* Allows parallel operations

Example output:

```text
Exploit running as background job 0.
Started reverse TCP handler on 10.10.14.34:4444
```

---

## Sessions + Jobs Workflow (Real Pentest Style)

1. Start handler as job
2. Exploit target → session created
3. Background session
4. Exploit another target
5. Switch between sessions
6. Run post modules
7. Kill jobs when ports are needed

---

## Key Commands to Memorize

```bash
sessions
sessions -i <id>
background
jobs -l
jobs -k <id>
jobs -K
exploit -j
```

---

##  Takeaways

* Sessions = access to targets
* Jobs = background Metasploit tasks
* You can have many sessions at once
* Backgrounding does NOT kill access
* Post modules require sessions
* Ports remain occupied until jobs are killed

---
