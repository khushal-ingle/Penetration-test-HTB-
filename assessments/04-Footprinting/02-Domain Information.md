# Practical Step-by-Step Plan (Domain Information)

---

## **Step 1: Visit the Company Website**

**Why?**  
This is the first and safest way to understand what the company does and what systems they might use.

- Open the main website like a normal visitor
- Read what services they provide (hosting, IoT, security, etc.)  
- Note technologies, portals, login pages, tools mentioned

 Helps you learn the company’s structure before scanning.

---

## **Step 2: Check SSL Certificate**

**Why?**  
SSL certificates often contain extra subdomains that the company is actively using.

- View the SSL certificate of the website
- Look for extra domains/subdomains listed inside it

Example:

- `support.company.com`
- `mail.company.com`

Quick way to discover hidden company services.

---

## **Step 3: Find Subdomains using crt.sh**

**Why?**  
crt.sh shows subdomains from certificate transparency logs, even if they are not public on the website.

- Use Certificate Transparency logs to discover hidden subdomains

Command:

```bash
curl -s "https://crt.sh/?q=company.com&output=json" | jq .
```

Extract unique subdomains:

```bash
curl -s "https://crt.sh/?q=company.com&output=json" |
jq -r '.[].name_value' | sort -u
```

 Finds many subdomains without active scanning.

---

## **Step 4: Resolve Subdomains to IPs**

**Why?**  
Not all subdomains are real or active. Resolving tells you which ones actually point to servers.

- Check which subdomains point to real servers
Command:

```bash
host sub.company.com
```

Automate:

```bash
for i in $(cat subdomains.txt); do host $i; done
```

 Helps confirm valid targets for testing.

---

## **Step 5: Identify Company-Hosted Servers**

**Why?**  
Some servers belong to third parties (Google, Cloudflare). You should only test servers owned by the company.

- Filter out third-party hosted servers
- Keep only company owned IPs

Save IPs:

```bash
cut -d" " -f4 > ip-addresses.txt
```

Avoids testing unauthorized infrastructure.

---

## **Step 6: Use Shodan for External Exposure**

**Why?**  
Shodan shows what the Internet already knows about the company’s servers, including open ports and services.

Command:

```bash
shodan host <IP>
```

This reveals:

- SSH, HTTP, HTTPS, Mail, DNS, etc.
Helps identify exposed services without scanning yourself.

---

## **Step 7: Perform DNS Record Enumeration**

**Why?**  
DNS records provide important infrastructure details like mail servers, hosting providers, and security settings.

Command:

```bash
dig any company.com
```

Look for:

- **A record** → Server IPs
- **MX record** → Mail providers
- **NS record** → Hosting provider
- **TXT record** → Verification + Security info

 DNS is a goldmine for passive recon.

---

## **Step 8: Analyze TXT Records for Tools & Providers**

**Why?**  
TXT records can reveal third-party platforms used by the company, which may become attack surfaces.

TXT records may show:

- Google Workspace
- Atlassian (Jira)
- Mailgun API
- LogMeIn Remote Access
- Office365 / Azure storage

 Helps understand external services and possible weaknesses.

---

#  Final Output Checklist

**Why?**  
At the end, you will have a clear map of the company’s online footprint.

You will collect:

- Subdomain list
- Company-hosted IP list
- Open ports/services from Shodan
- DNS records + providers used
- Targets for deeper active testing

Ready for the next phase: scanning and exploitation.
