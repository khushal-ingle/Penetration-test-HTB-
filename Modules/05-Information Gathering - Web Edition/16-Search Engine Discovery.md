
# Search Engine Discovery

Search engines help us find information on the internet quickly. We usually use them for normal searches like news, tutorials, or answers to questions. But search engines can do much more than that.

In **web reconnaissance**, search engines are powerful tools for collecting information about websites, organisations, and even individuals. This method is called **search engine discovery** or **OSINT (Open Source Intelligence)** gathering.

Search engine discovery means using search engines in a smart way to find information that is already public but not always easy to notice. By using special search commands and techniques, security professionals can discover:

* Employee details
* Sensitive documents
* Hidden login pages
* Exposed files or credentials

All of this information comes from data already indexed by search engines.

---

## Why Search Engine Discovery Matters

Search engine discovery is an important part of web reconnaissance for several reasons.

### Open Source

The information collected is publicly available. This makes it a legal and ethical way to gather intelligence.

### Breadth of Information

Search engines index a huge part of the internet, giving access to many different data sources.

### Ease of Use

Anyone can use search engines. No advanced technical skills are required.

### Cost-Effective

Search engines are free, making them a powerful and low-cost reconnaissance tool.

---

## Uses of Search Engine Discovery

Information gathered from search engines can be used in many areas.

* **Security Assessment**
  Finding exposed data, weak points, and possible attack paths.

* **Competitive Intelligence**
  Learning about competitors’ products, services, and business strategies.

* **Investigative Journalism**
  Discovering hidden connections, financial data, or unethical activity.

* **Threat Intelligence**
  Tracking malicious activity, identifying threats, and predicting attacks.

---

## Limitations of Search Engine Discovery

Search engines do not index everything. Some information may be:

* Hidden behind logins
* Blocked by robots.txt
* Removed or never indexed

Because of this, search engine discovery should be combined with other reconnaissance techniques.

---

## Search Operators

Search operators are special commands that give you more control over search results. They help you find very specific information instead of general results.

Although search engines may have small differences, most operators work in a similar way across platforms.

---

## Common Search Operators

| Operator          | Description                   | Example                               | What It Does                       |
| ----------------- | ----------------------------- | ------------------------------------- | ---------------------------------- |
| site:             | Limits results to one website | `site:example.com`                    | Finds pages only from example.com  |
| inurl:            | Looks for words in the URL    | `inurl:login`                         | Finds login pages                  |
| filetype:         | Searches for file types       | `filetype:pdf`                        | Finds PDF files                    |
| intitle:          | Searches page titles          | `intitle:"confidential report"`       | Finds pages with that title        |
| intext: / inbody: | Searches page content         | `intext:"password reset"`             | Finds pages with this text         |
| cache:            | Shows cached page             | `cache:example.com`                   | Views old version of a page        |
| link:             | Finds backlinks               | `link:example.com`                    | Finds sites linking to example.com |
| related:          | Finds similar websites        | `related:example.com`                 | Finds related sites                |
| info:             | Shows page info               | `info:example.com`                    | Basic site information             |
| define:           | Shows definitions             | `define:phishing`                     | Explains phishing                  |
| numrange:         | Searches number ranges        | `numrange:1000-2000`                  | Finds numbers in range             |
| allintext:        | All words in body             | `allintext:admin password`            | Finds pages with both words        |
| allinurl:         | All words in URL              | `allinurl:admin panel`                | Finds admin panel URLs             |
| allintitle:       | All words in title            | `allintitle:confidential report 2023` | Finds matching titles              |
| AND               | Requires all terms            | `site:example.com AND login`          | Narrows results                    |
| OR                | Any term can match            | `"linux" OR "ubuntu"`                 | Broadens results                   |
| NOT               | Excludes terms                | `site:bank.com NOT login`             | Removes login pages                |
| *                 | Wildcard                      | `user* manual`                        | Matches any word                   |
| ..                | Number range                  | `100..500`                            | Searches between numbers           |
| " "               | Exact phrase                  | `"information security policy"`       | Exact match                        |
| -                 | Excludes term                 | `news -sports`                        | Removes sports results             |

---

## Google Dorking

**Google Dorking**, also called **Google Hacking**, is the practice of using search operators in advanced ways to find sensitive information using **Google Search**.

It does not hack systems directly. Instead, it finds information that websites accidentally expose.

Many common dorks are listed in the **Google Hacking Database (GHDB)**.

---

## Common Google Dork Examples

### Finding Login Pages

```
site:example.com inurl:login
site:example.com (inurl:login OR inurl:admin)
```

---

### Finding Exposed Files

```
site:example.com filetype:pdf
site:example.com (filetype:xls OR filetype:docx)
```

---

### Finding Configuration Files

```
site:example.com inurl:config.php
site:example.com (ext:conf OR ext:cnf)
```

---

### Finding Database Backups

```
site:example.com inurl:backup
site:example.com filetype:sql
```


---

## Conclusion

Search engine discovery is a powerful and simple reconnaissance technique. By using search engines and advanced operators, large amounts of useful information can be collected without touching the target directly.

When used responsibly, search engine discovery helps security professionals understand a target’s public exposure, identify risks, and plan further testing more effectively.


