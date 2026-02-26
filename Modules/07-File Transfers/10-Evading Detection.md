

# Evading Detection 

## Why Evasion Works

Most detection controls rely on:

* Known bad command-line patterns
* Known malicious binaries
* Known malicious user-agent strings

Attackers evade these controls by **making malicious activity look normal**, not by doing something technically complex.

---

## User-Agent Evasion

### Why User-Agents Matter

Security teams often detect suspicious HTTP activity by flagging:

* PowerShell user agents
* Certutil user agents
* BITS user agents
* Non-browser HTTP clients

Because user-agent strings are **client-controlled**, they are trivial to change.

---

### PowerShell User-Agent Customization

PowerShell allows the user-agent string to be explicitly set. This means a PowerShell download can be made to look like it came from a common browser.

PowerShell exposes built-in browser user-agent templates such as:

* Internet Explorer
* Firefox
* Chrome
* Opera
* Safari

If an organization primarily uses Chrome internally, traffic identifying itself as Chrome may blend into normal activity.

---

### Why This Evades Detection

* SIEM rules often filter out common browser user agents
* Network tools may not inspect payload intent
* The traffic pattern (HTTP GET) looks legitimate

This bypasses **network-based detection**, not endpoint detection.

---

### Defensive Takeaway

User-agent inspection alone is insufficient. Detection must correlate:

* Process making the request
* Destination domain/IP
* File type downloaded
* Subsequent execution behavior

---

## Living off the Land (LOLBAS / GTFOBins)

### Why LOLBins Evade Controls

Application whitelisting often allows:

* Signed binaries
* Vendor-installed utilities
* System drivers and helpers

LOLBins abuse **trusted binaries** to perform untrusted actions.

---

### Example: Vendor Utilities

Some vendor-installed tools (e.g., hardware drivers, update utilities) include:

* Built-in download functionality
* Network access
* Execution permissions

Because these binaries:

* Are signed
* Are expected to run
* Are rarely monitored

They often bypass:

* Application control
* Basic command-line detection
* EDR allow rules

---

### Why This Is Effective

* No PowerShell required
* No custom malware dropped
* Uses binaries defenders rarely baseline
* Looks like normal system behavior

---

### Linux Equivalent: GTFOBins

On Linux systems, many standard utilities can be abused for:

* File downloads
* File uploads
* Command execution

Because these tools are core system utilities, blocking them outright is usually impossible.

---

## Combining Evasion Techniques

Advanced attackers combine:

* Legitimate binaries (LOLBins)
* Legitimate user agents
* Legitimate protocols (HTTP/HTTPS)
* Legitimate destinations (CDNs, cloud storage)

Each individual action looks benign. Only **correlation reveals intent**.

---

## Why Evasion Succeeds in Real Environments

* Incomplete baselining
* Over-reliance on signatures
* Limited process-to-network correlation
* Alert fatigue causing broad allow rules

---

## Defensive Countermeasures

### Strong Detection Strategies

* Baseline normal command-line usage
* Alert on rare binary + network combinations
* Monitor signed binaries performing uncommon actions
* Correlate download → execution chains
* Track first-seen binaries and first-seen destinations

---

### What Not to Rely On

* User-agent blacklists
* Single LOLBin detection rules
* Blocking tools without behavioral context

---

## Closing Perspective

File transfer itself is not suspicious.
**Context makes it suspicious.**

Understanding evasion techniques is critical not to bypass defenses, but to **design defenses that attackers cannot trivially evade**.

A mature security posture assumes:

* User-agents can be spoofed
* Signed binaries can be abused
* HTTP traffic is always suspect without context

---

