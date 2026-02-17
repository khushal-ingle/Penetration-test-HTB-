
# WHOIS — Reconnaissance

## Introduction

**WHOIS** is a query/response protocol used to retrieve registration details about:

* Domain names
* IP address ranges
* Autonomous Systems (ASNs)

Think of WHOIS as a **public identity record** for internet resources.

---

## What WHOIS Provides

A typical WHOIS record includes:

* **Domain Name** – The registered domain (e.g., `example.com`)
* **Registrar** – Company that registered the domain (GoDaddy, Namecheap, Amazon Registrar, etc.)
* **Registrant Contact** – Owner of the domain (person or organization)
* **Administrative Contact** – Person responsible for domain management
* **Technical Contact** – Person handling technical/domain issues
* **Creation Date** – When the domain was first registered
* **Expiration Date** – When the domain registration expires
* **Name Servers** – DNS servers responsible for domain resolution

---

## Example WHOIS Lookup

```bash
whois inlanefreight.com
```

Relevant extracted fields:

* **Registrar**: Amazon Registrar
* **Creation Date**: 2019-08-05
* **Last Updated**: 2023-07-03
* **Domain ID**: 2420436757_DOMAIN_COM-VRSN

---

## History of WHOIS

WHOIS originated in the **1970s** during the ARPANET era.

The system was created at the **Stanford Research Institute (SRI)** Network Information Center (NIC) under the leadership of **Elizabeth Feinler**.

Purpose:

* Track network users
* Map hostnames
* Maintain ownership of network resources

This early database later evolved into today’s global WHOIS infrastructure.

---

## Why WHOIS Matters in Web Recon

WHOIS data is extremely valuable during the **passive reconnaissance phase** of penetration testing.

### 1. Identifying Key Personnel

* Names
* Email addresses
* Phone numbers

**Use case**:

* Social engineering
* Phishing target identification
* Username format discovery

---

### 2. Discovering Network Infrastructure

* Name servers
* Hosting providers
* IP ranges

**Use case**:

* Identify hosting environment
* Detect third-party services
* Spot misconfigurations

---

### 3. Historical WHOIS Analysis

Using tools like **WhoisFreaks** or SecurityTrails, testers can:

* Track ownership changes
* Identify old email addresses
* Discover abandoned infrastructure

**Use case**:

* Find forgotten assets
* Locate legacy domains
* Correlate infrastructure reuse

---

## Pentester Takeaway

WHOIS is:

* **Passive** (no interaction with target systems)
* **Low noise**
* **High value** during early recon

Always perform WHOIS **before**:

* DNS brute-forcing
* Subdomain enumeration
* Active scanning

---

