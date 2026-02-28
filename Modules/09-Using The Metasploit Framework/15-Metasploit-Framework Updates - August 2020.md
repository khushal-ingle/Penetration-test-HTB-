
# Metasploit Framework Updates – August 2020 (MSF6)

Updating from **MSF5 to MSF6** introduced major architectural changes. One important operational impact is that **sessions created with MSF5 are incompatible with MSF6**, and **payloads generated with MSF5 will not communicate correctly with MSF6 handlers**. This is due to fundamental changes in payload generation and communication.

---

## 1. Payload Generation Features

### End-to-End Encryption

* All Meterpreter implementations now support **full end-to-end encryption**:

  * Windows
  * Python
  * Java
  * Mettle
  * PHP
* Communication between attacker and target is encrypted by default.

### SMBv3 Client Support

* MSF6 introduces **SMBv3 client support**
* Enables modern Windows exploitation workflows
* Supports encrypted SMB traffic

### Polymorphic Windows Shellcode

* Windows shellcode generation now includes **polymorphism**
* Instructions are randomized on every build
* Improves evasion against:

  * Antivirus
  * IDS / IPS
  * Static signature engines

---

## 2. Expanded Encryption Capabilities

### Meterpreter Communication

* All Meterpreter payloads now use **AES encryption**
* Applies to:

  * Stagers
  * Command execution
  * Data exfiltration

### SMBv3 Encryption

* SMB operations now leverage **SMBv3 encryption**
* Makes signature-based detection of SMB exploitation significantly harder

**Result:**
Network-based IDS/IPS solutions have far less visibility into Metasploit traffic.

---

## 3. Cleaner Payload Artifacts

MSF6 significantly reduced the static fingerprints commonly used by defenders.

### DLL Improvements

* Windows Meterpreter DLLs now resolve API functions by **ordinal**, not name
* This removes recognizable function names from payload binaries

### ReflectiveLoader Changes

* The standard `ReflectiveLoader` export string is no longer present
* Prevents simple string-based detection

### Command Encoding

* Meterpreter commands are now represented as **integers**
* Previously, they were human-readable strings
* Reduces forensic and signature visibility

---

## 4. Plugin Changes

### Mimikatz Replacement

* The original **Mimikatz Meterpreter extension** was removed
* Replaced by **Kiwi**
* Attempting to load Mimikatz now transparently loads Kiwi

**Why this matters:**

* Kiwi is better maintained
* Improved compatibility with modern Windows versions
* Cleaner integration with MSF6

---

## 5. Payload Architecture Changes

### Polymorphic Stub Generation

* Static shellcode generation was replaced with **randomized instruction ordering**
* Every payload build produces a different binary layout

**Impact:**

* Breaks hash-based detection
* Increases difficulty of reliable AV signatures
* Makes payload reuse harder to detect

---

## 6. Operational Impact for Pentesters

After upgrading to MSF6:

* Old MSF5 sessions will die
* Old MSF5 payloads will not connect
* Handlers must match payload generation version
* Testing payloads against defenses becomes more reliable
* Pivoting and post-exploitation are safer and stealthier

---

## 7. Why These Changes Matter

From an offensive perspective:

* Better evasion
* Cleaner memory-only payloads
* Encrypted C2 traffic by default
* Reduced forensic footprint

From a defensive perspective:

* Signature-based detection becomes less effective
* Network monitoring has less visibility
* Memory inspection becomes more important

---

## 8. Closing Thoughts

Metasploit remains a **powerful and legitimate penetration testing framework** when used correctly.

Strengths:

* Highly extensible
* Excellent session and data tracking
* Strong post-exploitation tooling
* Industry-standard pivoting support

It is not mandatory to use Metasploit for every engagement. Skilled testers should use **tools that best fit their workflow**, whether that includes Metasploit or not.

---

## Practice Recommendations

To strengthen MSF skills:

* Use Hack The Box machines that allow Metasploit
* Re-solve older boxes using MSF6
* Practice pivoting in lab environments
* Explore payload generation and handler tuning
* Use Dante Pro Lab for real-world style pivoting

---
