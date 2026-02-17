
# Web Reconnaissance – Introduction 

## 1. What is Web Reconnaissance?

Web Reconnaissance is the **first and most critical phase** of a web application security assessment. It focuses on **systematically collecting information** about a target website or web application before attempting any exploitation.

It is part of the **Information Gathering** phase in the Penetration Testing Process and acts as the foundation for all later stages.

Think of reconnaissance as:

* Understanding *what exists*
* Identifying *what is exposed*
* Learning *where weaknesses may lie*

Poor recon = blind exploitation
Strong recon = precise, low-noise attacks

---


## 2. Penetration Testing Process Flow

<img width="2058" height="747" alt="PT-process" src="https://github.com/user-attachments/assets/b103deec-2822-46ae-991f-592f6b0e1800" />

---


1. Pre-Engagement
2. Information Gathering (Reconnaissance)
3. Vulnerability Assessment
4. Exploitation
5. Post-Exploitation
6. Lateral Movement
7. Proof-of-Concept
8. Post-Engagement / Reporting

Web reconnaissance mainly belongs to **Information Gathering**, but its findings directly influence **Vulnerability Assessment and Exploitation**.

---

## 3. Goals of Web Reconnaissance

### 3.1 Identifying Assets

* Discover publicly accessible components:

  * Domains & subdomains
  * IP addresses
  * Web pages & directories
  * Technologies and frameworks in use
* Helps build a **complete map of the target’s online presence**

### 3.2 Discovering Hidden Information

* Identify unintentionally exposed data:

  * Backup files
  * Configuration files
  * Old endpoints
  * Internal documentation
* Often leads to **direct attack vectors**

### 3.3 Analysing the Attack Surface

* Determine:

  * Entry points
  * Exposed services
  * Weak configurations
* Helps prioritise **high-risk areas**

### 3.4 Gathering Intelligence

* Collect data useful for:

  * Targeted exploitation
  * Social engineering
* Examples:

  * Employee names & emails
  * Technology stack patterns
  * Behavioural insights

---

## 4. Why Reconnaissance Matters

* **Attackers** use recon to:

  * Tailor attacks
  * Avoid detection
  * Exploit precise weaknesses
* **Defenders** use recon to:

  * Identify exposures
  * Patch vulnerabilities early
  * Reduce attack surface

Reconnaissance is **not optional**—it defines the quality of the entire assessment.

---

## 5. Types of Web Reconnaissance

Web reconnaissance is divided into two main categories:

1. Active Reconnaissance
2. Passive Reconnaissance

Understanding both is essential for choosing the **right balance between visibility and stealth**.

---

## 6. Active Reconnaissance

Active reconnaissance involves **direct interaction with the target system** to extract information.

### Characteristics

* Sends packets or requests to the target
* Provides accurate and real-time data
* Higher risk of detection

### Common Active Recon Techniques

| Technique              | Purpose                            | Risk        |
| ---------------------- | ---------------------------------- | ----------- |
| Port Scanning          | Identify open ports & services     | High        |
| Vulnerability Scanning | Detect known vulnerabilities       | High        |
| Network Mapping        | Understand network topology        | Medium–High |
| Banner Grabbing        | Identify service/software versions | Low         |
| OS Fingerprinting      | Detect operating system            | Low         |
| Service Enumeration    | Identify exact service versions    | Low         |
| Web Spidering          | Discover directories & endpoints   | Low–Medium  |

### Key Point

Active recon gives **deep visibility**, but careless execution can:

* Trigger IDS/IPS
* Alert SOC teams
* Break engagement rules

---

## 7. Passive Reconnaissance

Passive reconnaissance gathers information **without directly touching the target**.

### Characteristics

* No interaction with target systems
* Extremely stealthy
* Relies on publicly available data

### Common Passive Recon Techniques

| Technique                | Information Gained           | Risk     |
| ------------------------ | ---------------------------- | -------- |
| Search Engine Queries    | Employees, leaks, documents  | Very Low |
| WHOIS Lookups            | Domain ownership & contacts  | Very Low |
| DNS Analysis             | Subdomains & infrastructure  | Very Low |
| Web Archive Analysis     | Old endpoints & content      | Very Low |
| Social Media Analysis    | People & organisational data | Very Low |
| Code Repository Analysis | Leaked credentials, code     | Very Low |

### Key Point

Passive recon is:

* Ideal for early-stage recon
* Useful for stealth operations
* Limited to what is already exposed

---

## 8. Active vs Passive Recon (Quick Comparison)

| Aspect                  | Active                      | Passive              |
| ----------------------- | --------------------------- | -------------------- |
| Interaction with target | Yes                         | No                   |
| Detection risk          | High                        | Very Low             |
| Accuracy                | High                        | Medium               |
| Stealth                 | Low                         | High                 |
| Best use case           | Internal or permitted tests | External OSINT recon |

---


