
## What Is a Payload in Metasploit

A **payload** is the part of an attack that runs **after exploitation**.
Its job is to execute code on the target system and typically:

* Return a shell
* Open a Meterpreter session
* Execute commands
* Establish persistence
* Provide post-exploitation capabilities

In short:

* **Exploit** → gains code execution
* **Payload** → decides *what happens after*

---

## Payload Naming Convention

Payload names describe:

```
<platform>/<arch>/<payload_type>/<connection_type>
```

Examples:

* `windows/x64/shell_reverse_tcp`
* `windows/x64/meterpreter/reverse_tcp`

A `/` in the name means the payload is **staged**.

---

## Types of Payloads in Metasploit

Metasploit payloads are divided into **three main types**:

1. Single Payloads
2. Stagers
3. Stages

---

## 1. Single Payloads (Inline Payloads)

Single payloads contain **everything in one piece of shellcode**.

Example:

```
windows/x64/shell_reverse_tcp
```

### Characteristics

* No staging
* Entire payload sent at once
* More stable
* Larger size
* Less stealthy
* Some exploits cannot handle the size

### Use when:

* Exploit space is sufficient
* Stability is more important than stealth
* AV/IPS evasion is not critical

---

## 2. Stagers

A **stager** is a very small payload whose only job is to:

* Connect back to the attacker
* Prepare memory
* Download the next stage

Examples:

```
reverse_tcp
reverse_http
bind_tcp
```

### Characteristics

* Very small
* Highly reliable
* Bypasses payload size limitations
* Often firewall-friendly (reverse connections)

### NX vs NO-NX Stagers

* NX CPUs enforce DEP (Data Execution Prevention)
* NX-compatible stagers use `VirtualAlloc`
* Slightly larger but more reliable on modern systems
* Default in most Windows payloads

---

## 3. Stages

A **stage** is the main payload delivered *after* the stager connects.

Examples:

* Meterpreter
* VNC injection
* PowerShell session

### Characteristics

* No strict size limits
* Delivered over an established connection
* Enables advanced functionality
* Loaded entirely in memory

---

## Staged Payload Flow (Very Important)

1. Exploit triggers code execution
2. **Stage 0 (Stager)** runs
3. Victim connects back to attacker
4. **Stage 1 (Stage)** is downloaded
5. Full payload executes

This is why staged payloads are:

* Smaller initially
* More stealthy
* More flexible

---

## Reverse vs Bind Payloads

### Reverse Payloads (Most Common)

Victim connects back to attacker.

Advantages:

* Bypasses inbound firewall rules
* More reliable in enterprise networks
* Preferred in real engagements

Examples:

```
reverse_tcp
reverse_https
reverse_http
```

---

### Bind Payloads

Victim opens a port and listens.

Disadvantages:

* Often blocked by firewalls
* Requires inbound access
* Less reliable

Examples:

```
bind_tcp
bind_ipv6_tcp
```

---

## Meterpreter Payload

Meterpreter is a **memory-resident, advanced payload**.

### Key Features

* Runs entirely in memory
* No files written to disk
* Encrypted communication
* Stable session handling
* Modular extensions

### Capabilities

* Privilege escalation
* Hash dumping
* Keystroke logging
* Screenshots
* Webcam and microphone access
* Token impersonation
* Process migration
* Pivoting

### Why Meterpreter Is Preferred

* Stealthy
* Powerful
* Highly extensible
* Professional-grade post-exploitation

---

## Meterpreter vs Normal Shell

| Feature           | CMD Shell | Meterpreter |
| ----------------- | --------- | ----------- |
| Runs in memory    | No        | Yes         |
| AV evasion        | Poor      | Good        |
| Post-exploitation | Limited   | Extensive   |
| Stability         | Medium    | High        |
| Automation        | Low       | High        |

---

## Searching Payloads Efficiently

Payload lists are large. Use `grep`.

Example:

```bash
grep meterpreter show payloads
```

Filter further:

```bash
grep meterpreter grep reverse_tcp show payloads
```

Count results:

```bash
grep -c meterpreter show payloads
```

---

## Selecting a Payload

After selecting an exploit:

```bash
set payload <number>
```

Example:

```bash
set payload windows/x64/meterpreter/reverse_tcp
```

---

## Payload Options (Common)

Once selected, new options appear:

| Option   | Purpose           |
| -------- | ----------------- |
| LHOST    | Attacker IP       |
| LPORT    | Listening port    |
| EXITFUNC | How payload exits |

---

## Running the Exploit

After setting:

* RHOSTS
* LHOST
* Payload

Run:

```bash
run
```

If successful:

* Meterpreter session opens
* You get a new interactive prompt

---

## Meterpreter vs Windows CMD

Meterpreter prompt:

```
meterpreter >
```

Windows CMD prompt:

```
C:\>
```

To drop into CMD:

```bash
shell
```

To return:

```bash
exit
```

---

## Common Windows Payloads (Quick Reference)

| Payload                 | Purpose            |
| ----------------------- | ------------------ |
| shell_reverse_tcp       | Basic shell        |
| meterpreter/reverse_tcp | Advanced session   |
| powershell/reverse_tcp  | PowerShell access  |
| vncinject/reverse_tcp   | GUI access         |
| exec                    | Run single command |

---

## Key Exam & Real-World Takeaways

* Exploit gets execution, payload gets control
* Staged payloads are more stealthy
* Reverse payloads are more reliable
* Meterpreter is the professional choice
* Payload choice affects detection and stability
* Always match payload architecture (x86/x64)

---

