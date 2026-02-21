
# Web Spidering Walkthrough (ReconSpider)

---

## Task Goal

The objective of this task is to spider the **inlanefreight.com** website to:

1. Crawl publicly accessible pages
2. Extract comments and hidden references
3. Identify the **location where future reports will be stored**

---

## Question

> After spidering inlanefreight.com, identify the location where future reports will be stored. Respond with the full domain, e.g., files.inlanefreight.com.

---

## Step 1: Install required dependency (Scrapy)

### Command executed

```bash
pip3 install scrapy
```

### What is happening

ReconSpider is built on top of the **Scrapy** framework.
This step ensures that the required crawling library is installed so the script can run correctly.

---

## Step 2: Download ReconSpider

### Command executed

```bash
wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip
```
<img width="912" height="562" alt="image" src="https://github.com/user-attachments/assets/f3cffdae-e49e-4480-bc6e-cdbd4d4d93dc" />

### What is happening

The ReconSpider tool is downloaded directly from the Hack The Box Academy module storage.
The tool is provided as a ZIP archive.

---

## Step 3: Extract ReconSpider

### Command executed

```bash
unzip ReconSpider.zip
```

### What is happening

The ZIP archive is extracted, revealing the main script:

```
ReconSpider.py
```

This script will be used to crawl the target website.

---

## Step 4: Run ReconSpider against the target domain

### Command executed

```bash
python3 ReconSpider.py https://www.inlanefreight.com/
```

### What is happening

ReconSpider starts crawling **inlanefreight.com** and gathers data such as:

* HTML comments
* Email addresses
* Hidden links
* JavaScript references

During execution, Scrapy initializes and reports its environment and versions.

---

## Step 5: Review generated results

<img width="690" height="150" alt="image" src="https://github.com/user-attachments/assets/c8039792-3257-4bed-a056-2815b336d1ac" />


After completion, ReconSpider generates an output file:

```
results.json
```

### Command executed

```bash
cat results.json
```

### What is happening

The JSON file contains structured data collected during spidering, including comments embedded in the website’s source code.

---

## Step 6: Extract comments from the results

### Command executed

```bash
cat results.json | jq '.comments'
```
<img width="802" height="411" alt="image" src="https://github.com/user-attachments/assets/4ba464e5-fc1e-419b-bfc1-333a239021ba" />

### What is happening

The `jq` tool is used to filter and display only the **HTML comments** collected during crawling.

---

## Highlighted Finding

Inside the extracted comments, the following message is found:

```text
TODO: change the location of future reports to inlanefreight-comp133.s3.amazonaws.htb
```

This clearly indicates where future reports will be stored.

---

## Answer

```
inlanefreight-comp133.s3.amazonaws.htb
```

---

## Final Answer Summary

| Question                                                              | Answer                                 |
| --------------------------------------------------------------------- | -------------------------------------- |
| Location where future reports will be stored after spidering the site | inlanefreight-comp133.s3.amazonaws.htb |

---

