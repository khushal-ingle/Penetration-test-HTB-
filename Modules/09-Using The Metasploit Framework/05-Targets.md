
## Targets in Metasploit

In Metasploit, a **target** defines **which exact operating system, version, service pack, and application combination** an exploit is crafted to work against.

An exploit may work differently depending on:

* OS version
* Service pack
* Application version
* Architecture (x86 / x64)
* Language pack
* Memory layout

Targets allow Metasploit to **adapt the exploit logic** to match those differences.

---

## Showing Targets

### Without selecting an exploit

If you try to list targets without loading an exploit module:

```bash
show targets
```

Output:

```
[-] No exploit module selected.
```

This is expected behavior. Targets are **exploit-specific**.

---

### After selecting an exploit

Once an exploit is loaded:

```bash
use exploit/windows/smb/ms17_010_psexec
show targets
```

Example output:

```
Id  Name
--  ----
0   Automatic
```

This means:

* The exploit handles target detection internally
* You usually do **not** need to manually select a target

---

## Automatic vs Manual Targets

### Automatic Target (Id 0)

* Metasploit detects the target OS/service automatically
* Recommended when detection is reliable
* Common in modern exploits

Example:

```
0  Automatic
```

---

### Manual Targets

Some exploits require **explicit target selection**.

These are common in:

* Browser exploits
* Client-side exploits
* Older Windows exploits
* Exploits using ROP chains or return addresses

---

## Example: MS12-063 Internet Explorer Exploit

Exploit:

```
exploit/windows/browser/ie_execcommand_uaf
```

This exploit has **multiple targets** because:

* Internet Explorer versions differ
* OS versions differ
* Memory layout differs per combination

---

### Viewing Target List

```bash
show targets
```

Output:

```
Id  Name
--  ----
0   Automatic
1   IE 7 on Windows XP SP3
2   IE 8 on Windows XP SP3
3   IE 7 on Windows Vista
4   IE 8 on Windows Vista
5   IE 8 on Windows 7
6   IE 9 on Windows 7
```

Each entry represents:

* A specific IE version
* Running on a specific Windows version
* With a compatible exploit configuration

---

## Selecting a Target Manually

If you **know** the exact version running on the victim:

```bash
set target 6
```

Example:

```
target => 6
```

This forces Metasploit to use:

```
IE 9 on Windows 7
```

Manual selection improves:

* Reliability
* Stability
* Exploit success rate

---

## Why Targets Matter Internally

Targets exist because **memory addresses are not universal**.

They change due to:

* OS version
* Service packs
* Language packs
* Application builds
* ASLR
* Loaded modules

---

### Return Addresses

Targets are often defined by:

* `jmp esp`
* `pop pop ret`
* ROP chain addresses
* Register jumps

These addresses differ between:

* Windows XP vs Windows 7
* English vs non-English OS
* IE 8 vs IE 9

That is why **one exploit can have many targets**.

---

## How Targets Are Identified During Exploit Development

To correctly define targets, exploit developers typically:

1. Obtain the vulnerable binaries
2. Analyze memory layout
3. Locate suitable return addresses
4. Test reliability
5. Hardcode or calculate addresses per target

Tools commonly used:

* msfpescan
* Immunity Debugger
* WinDbg
* Mona.py

---

## Practical Guidance for Real Engagements

Use **Automatic target** when:

* The exploit supports it
* Detection is reliable
* The service is well fingerprinted

Use **Manual target selection** when:

* The exploit is unstable
* You know the exact OS/app version
* Browser or client-side exploits are involved

---

## Key Takeaways

* Targets adapt exploits to specific OS/application versions
* `show targets` works only after selecting an exploit
* Automatic is safest when supported
* Manual targets increase reliability when details are known
* Targets exist due to memory and address differences
* Wrong target selection = exploit failure or crash

---
