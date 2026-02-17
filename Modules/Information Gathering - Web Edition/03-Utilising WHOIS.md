
# Utilising WHOIS in Cybersecurity Investigations

WHOIS is a **query and response protocol** used to retrieve information from databases that store details about **registered internet resources**, mainly:

* Domain names
* IP address ranges
* Autonomous Systems (AS)

In cybersecurity, WHOIS is heavily used during:

* **Initial reconnaissance**
* **Incident response**
* **Threat intelligence gathering**
* **Attribution and infrastructure analysis**

WHOIS acts like an **internet registry record**, helping analysts understand **who owns a domain, when it was registered, and how it is managed**.

---

## Scenario 1: Phishing Investigation (Detailed Breakdown)

### Situation Overview

An email security gateway flags a suspicious email delivered to multiple employees.
The email:

* Claims to be from the company’s **bank**
* Urges recipients to **click a link**
* Pressures users to **update account information**

This behavior matches a **classic phishing attack pattern**, prompting further investigation.

---

### Use of WHOIS

A security analyst performs a **WHOIS lookup** on the domain embedded in the email link.

---

### WHOIS Findings and Their Significance

#### 1. Registration Date: Recently Registered Domain

* The domain was registered **only a few days ago**

**Why this matters:**

* Legitimate banks and financial institutions usually operate domains registered **years ago**
* Threat actors commonly register domains shortly before launching phishing campaigns
* Short-lived domains reduce the chance of early detection and takedown

---

#### 2. Registrant Information: Privacy Protected

* Registrant details are hidden behind a **privacy service**

**Why this matters:**

* Privacy services are not malicious by default
* However, attackers frequently abuse them to:

  * Avoid identification
  * Delay attribution
  * Reduce accountability

---

#### 3. Name Servers: Bulletproof Hosting Provider

* The domain uses name servers linked to a **known bulletproof hosting provider**

**Why this matters:**

* Bulletproof hosts are notorious for:

  * Ignoring abuse complaints
  * Hosting phishing, malware, and C2 infrastructure
* This strongly suggests **malicious intent**

---

### Analyst Conclusion

The combination of:

* Recent domain registration
* Hidden registrant identity
* Suspicious hosting infrastructure

creates **multiple high-confidence red flags**.

---

### Response Actions

* Domain is **blocked** by IT and security controls
* Employees are **warned** about the phishing attempt
* WHOIS-linked IPs and hosting infrastructure are investigated further

---

### Extended Value of WHOIS

Further analysis of:

* Hosting provider
* Associated IP addresses

may uncover:

* Additional phishing domains
* Shared attacker infrastructure

---

## Scenario 2: Malware Analysis (Detailed Breakdown)

### Situation Overview

A security researcher is analysing a **new malware strain** that:

* Infects multiple systems
* Communicates with a remote server
* Receives commands
* Exfiltrates stolen data

This remote server acts as a **Command-and-Control (C2)** server.

---

### Use of WHOIS

The researcher performs a **WHOIS lookup** on the domain used by the C2 server.

---

### WHOIS Findings and Their Significance

#### 1. Registrant: Anonymous Individual

* Domain registered using a **free email service**
* Email provider is known for enabling anonymity

**Why this matters:**

* Threat actors avoid traceable corporate or ISP-linked emails
* Free email services reduce attribution
* Indicates deliberate operational security (OPSEC)

---

#### 2. Registrant Location: High Cybercrime Prevalence

* Address is located in a country frequently associated with cybercrime activity

**Why this matters:**

* While not definitive proof, geographic patterns:

  * Help analysts assess risk
  * Support threat profiling
* Often seen in malware infrastructure

---

#### 3. Registrar: Weak Abuse Policies

* Domain registered through a registrar known for **lax abuse enforcement**

**Why this matters:**

* Malicious actors prefer registrars that:

  * Respond slowly to takedown requests
  * Require extensive proof before action
* Increases the operational lifespan of malicious domains

---

### Analyst Conclusion

The WHOIS data suggests the C2 domain is:

* Malicious
* Likely hosted on **compromised or bulletproof infrastructure**

---

### Response Actions

* WHOIS data used to:

  * Identify hosting provider
  * Submit abuse notifications
  * Support coordinated takedown efforts

---

## Scenario 3: Threat Intelligence Report (Detailed Breakdown)

### Situation Overview

A cybersecurity firm tracks a **sophisticated threat actor group** targeting **financial institutions**.

The goal is to create a **comprehensive threat intelligence report**.

---

### WHOIS Data Collection

Analysts gather WHOIS records for:

* Multiple domains
* Linked to past attack campaigns

---

### Patterns Identified Through WHOIS Analysis

#### 1. Registration Date Clustering

* Domains registered in **groups**
* Often shortly before major attacks

**Why this matters:**

* Indicates planned campaign timelines
* Helps predict future attack windows

---

#### 2. Registrant Aliases and Fake Identities

* Different names used across domains
* No consistent personal details

**Why this matters:**

* Suggests identity obfuscation
* Common tradecraft among advanced threat actors

---

#### 3. Shared Name Servers

* Multiple domains use the **same name servers**

**Why this matters:**

* Strong indicator of shared infrastructure
* Helps link otherwise unrelated domains

---

#### 4. Takedown History

* Many domains taken down after attacks

**Why this matters:**

* Indicates prior:

  * Law enforcement involvement
  * Security vendor interventions
* Confirms malicious use of infrastructure

---

### Intelligence Outcome

WHOIS analysis enables analysts to:

* Build a detailed **threat actor profile**
* Understand attacker **TTPs**
* Generate **Indicators of Compromise (IOCs)**

These IOCs help other organizations:

* Detect early-stage attacks
* Block domains proactively

---

## Using WHOIS on Linux (From Given Content)

### Installation

WHOIS may not be installed by default.

```bash
sudo apt update
sudo apt install whois -y
```

---

### Performing a WHOIS Lookup

```bash
whois facebook.com
```

---

## WHOIS Output Analysis: facebook.com (Detailed)

### Domain Registration Information

* **Registrar:** RegistrarSafe, LLC
* **Creation Date:** 1997-03-29
* **Expiry Date:** 2033-03-30

**Interpretation:**

* Long registration history indicates legitimacy
* Extended expiry date reflects long-term ownership

---

### Domain Ownership

* **Registrant Organization:** Meta Platforms, Inc.
* **Registrant Contact:** Domain Admin

**Interpretation:**

* Confirms ownership aligns with expectations
* Corporate ownership contrasts sharply with phishing or malware domains

---

### Domain Status Flags

* clientDeleteProhibited
* clientTransferProhibited
* clientUpdateProhibited
* serverDeleteProhibited
* serverTransferProhibited
* serverUpdateProhibited

**Interpretation:**

* Prevents unauthorized changes
* Indicates strong administrative and registry-level protection

---

### Name Servers

* A.NS.FACEBOOK.COM
* B.NS.FACEBOOK.COM
* C.NS.FACEBOOK.COM
* D.NS.FACEBOOK.COM

**Interpretation:**

* DNS infrastructure fully controlled internally
* Common practice for large, mature organizations

---

### Overall Assessment

The WHOIS record:

* Matches expectations for a legitimate enterprise domain
* Demonstrates stability, security, and proper management

However:

* WHOIS alone does not expose internal vulnerabilities
* Must be combined with other reconnaissance methods

---

# Final Key Takeaways (From Given Content)

* WHOIS is essential for **early-stage investigation**
* Red flags include:

  * Recent registration
  * Hidden registrant details
  * Suspicious name servers
* WHOIS supports:

  * Phishing detection
  * Malware C2 identification
  * Threat actor profiling
* Pattern analysis across WHOIS records strengthens **threat intelligence**
* WHOIS is powerful but **not sufficient alone**

---

If you want next:

* **Ultra-condensed revision notes**
* **SOC analyst answer format**
* **HTB / CEH / PNPT exam mapping**
* **WHOIS red-flag checklist**

Just tell me 👍
