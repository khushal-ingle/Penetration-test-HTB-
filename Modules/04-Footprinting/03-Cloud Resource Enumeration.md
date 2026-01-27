## Cloud Resource Enumeration (With Reasons)

---
### **1. Collect Subdomains**

First, gather all company subdomains (from tools like Amass, Subfinder, crt.sh).

 **Why this step?**  
Subdomains often reveal hidden services like cloud portals, storage systems, admin dashboards, and internal apps.

---
### **2. Find IP Addresses**

Check which subdomains point to real servers:

```bash
for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done
```

Look for cloud-related domains like:

- `amazonaws.com`
    
- `blob.core.windows.net`
    
- `storage.googleapis.com`
    

 **Why this step?**  
This helps identify which subdomains are hosted on cloud infrastructure and may contain exposed cloud resources.

---
### **3. Identify Cloud Storage Links**

If you see something like:

- `s3-website-us-west-2.amazonaws.com`
    

It may indicate an AWS S3 bucket.

 **Why this step?**  
Cloud storage links are important because misconfigured buckets can allow public access to sensitive company data.

---
### **4. Use Google Dorks (Passive Search)**

Search for exposed files stored in cloud buckets.

 AWS:

```
intext:companyname inurl:amazonaws.com
```

 Azure:

```
intext:companyname inurl:blob.core.windows.net
```

This can reveal PDFs, documents, backups, etc.

 **Why this step?**  
Google may already index publicly accessible cloud files, making it an easy way to discover accidental leaks.

---
### **5. Check Website Source Code**

Open company websites and inspect the page source (`Ctrl+U`).

Look for cloud storage URLs used for:

- Images
    
- JavaScript
    
- PDFs
    

 **Why this step?**  
Web applications often load resources directly from cloud storage, which can expose bucket names and file paths.

---
### **6. Use Domain Infrastructure Tools**

Use sites like **domain.glass** to learn about:

- Cloudflare protection
    
- Hosting providers
    
- SSL details
    

 **Why this step?**  
These tools provide a clearer view of the company’s hosting setup and security protections.

---
### **7. Search on GrayHatWarfare**

Go to **GrayHatWarfare** and search for:

- Company name
    
- Abbreviations
    

It helps find public cloud buckets and files.

 **Why this step?**  
GrayHatWarfare is useful for quickly discovering public storage buckets and the files inside them.

---
### **8. Look for Sensitive Leaks**

Sometimes misconfigured buckets expose:

- `id_rsa` (private SSH keys)
    
- config files
    
- internal documents
    

These are critical findings.

 **Why this step?**  
Sensitive files like SSH keys or configs can lead to full server compromise if downloaded by attackers.

---
### **9. Report the Risk**

If public access is possible, note:

 Misconfigured cloud storage  
 Leaked sensitive files  
 Possible unauthorized access

 **Why this step?**  
Reporting helps the company fix the issue before attackers exploit the exposed data.

---

## Summary

Use subdomains + DNS + Google Dorks + GrayHatWarfare to find exposed AWS/Azure/GCP storage and report misconfigurations safely.
