
## What Is Meterpreter

**Meterpreter** is an advanced, in-memory payload used in Metasploit for **post-exploitation**.
It is designed to give the attacker **stable, stealthy, and powerful control** over a compromised system.

Unlike a normal shell, Meterpreter:

* Runs entirely **in memory**
* Uses **DLL injection**
* Communicates over **encrypted channels**
* Provides **hundreds of built-in post-exploitation features**

This is why Meterpreter is often called the **“Swiss Army Knife of pentesting.”**

---

## Core Design Goals of Meterpreter

Meterpreter was built with three main goals:

### 1. Stealthy

* No files written to disk (memory-resident)
* No new visible processes created
* Injects itself into an existing process
* Supports **process migration**
* Uses **AES-encrypted communication**

This leaves minimal forensic artifacts and reduces detection.

---

### 2. Powerful

Meterpreter provides:

* File system access
* Process manipulation
* Credential dumping
* Token impersonation
* Screenshot and webcam access
* Network pivoting
* Registry manipulation
* Privilege escalation support

All of this without spawning a traditional OS shell unless required.

---

### 3. Extensible

Meterpreter is **modular**:

* New features can be loaded at runtime
* Extensions are downloaded dynamically
* No recompilation required

Examples of extensions:

* `stdapi`
* `priv`
* `kiwi` (Mimikatz)
* `railgun`

---

## How Meterpreter Works (Execution Flow)

When a Meterpreter exploit succeeds, the following happens:

1. **Stager executes on the target**

   * Examples: `reverse_tcp`, `bind_tcp`, `reverse_https`

2. **Reflective DLL Injection**

   * The payload loads a DLL into memory using a reflective loader
   * No disk write required

3. **Encrypted Communication Established**

   * AES-encrypted channel created between target and attacker
   * Metasploit negotiates session parameters

4. **Extensions Loaded**

   * `stdapi` always loads
   * `priv` loads if elevated privileges exist
   * All extensions are encrypted in transit

---

## Meterpreter vs Normal Shell

| Feature                 | Normal Shell | Meterpreter |
| ----------------------- | ------------ | ----------- |
| Runs in memory          | No           | Yes         |
| Encrypted traffic       | No           | Yes         |
| Process migration       | No           | Yes         |
| Post-exploitation tools | Very limited | Extensive   |
| AV evasion              | Weak         | Strong      |
| Pivoting                | No           | Yes         |

---

## Meterpreter Command Categories

After getting a Meterpreter session, use:

```
help
```

Commands are grouped logically.

---

### Core Commands

Used to manage the session itself:

* `background`
* `sessions`
* `migrate`
* `load`
* `resource`
* `transport`

---

### File System Commands

Navigate and manipulate files:

* `ls`, `cd`, `pwd`
* `upload`, `download`
* `rm`, `mkdir`
* `search`

---

### Process & System Commands

Interact with OS internals:

* `ps`
* `kill`
* `getpid`
* `getuid`
* `sysinfo`
* `shell`

---

### Privilege Commands

Used after elevation:

* `getsystem`
* `hashdump`
* `lsa_dump_sam`
* `lsa_dump_secrets`
* `steal_token`
* `rev2self`

---

### Network Commands

Useful for pivoting:

* `ifconfig`
* `route`
* `portfwd`
* `arp`
* `netstat`

---

## Process Migration (Critical Concept)

Meterpreter initially runs inside the exploited process, which may:

* Crash
* Be unstable
* Have limited privileges

To improve stability and privileges:

```
ps
migrate <pid>
```

Common migration targets:

* `explorer.exe`
* `svchost.exe`
* `services.exe`

Migration:

* Keeps the session alive
* Helps bypass restrictions
* Improves persistence

---

## Token Impersonation

Windows uses **access tokens** to define privileges.

If Meterpreter can access another process token:

```
steal_token <pid>
```

To revert:

```
rev2self
```

This allows:

* User impersonation
* Privilege escalation without exploits
* Access to restricted resources

---

## Privilege Escalation Workflow with Meterpreter

Typical real-world flow:

1. Get Meterpreter session as low-priv user
2. Enumerate processes and privileges
3. Run local exploit suggester
4. Execute recommended local exploit
5. Spawn new Meterpreter session
6. Verify SYSTEM access

Example:

```
getuid
background
use post/multi/recon/local_exploit_suggester
set SESSION 1
run
```

---

## Multiple Sessions After PrivEsc

After successful escalation:

* A **new Meterpreter session** is created
* Old session remains active
* SYSTEM session is separate

Always verify:

```
sessions
sessions -i <id>
getuid
```

---

## Credential Dumping with Meterpreter

Once SYSTEM is obtained:

### Dump SAM Hashes

```
hashdump
```

### Dump LSA Secrets

```
lsa_dump_secrets
```

### Dump SAM via LSA

```
lsa_dump_sam
```

These credentials can be used for:

* Pass-the-hash
* Lateral movement
* Domain compromise
* Pivoting

---

## Cleaning Up Artifacts

Some exploits drop temporary files (e.g., ASP, EXE, DLL).

Best practice:

* Delete dropped files manually
* Migrate away from exploited process
* Kill suspicious processes if needed

Leaving artifacts increases detection risk.

---

## Why Meterpreter Is So Important

Meterpreter is not just a shell. It is a **post-exploitation platform** that allows you to:

* Fully enumerate a compromised system
* Escalate privileges efficiently
* Extract credentials
* Pivot into internal networks
* Maintain stealth and control

Most professional penetration tests rely heavily on Meterpreter once initial access is achieved.

---

## Key Takeaways

* Meterpreter runs entirely in memory
* Communication is AES encrypted
* It is modular, extensible, and stealthy
* Supports migration, pivoting, and credential theft
* Ideal for post-exploitation and internal movement
* Far more powerful than a normal shell

