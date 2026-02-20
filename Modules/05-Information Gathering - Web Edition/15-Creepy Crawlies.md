
# Creepy Crawlies

Web crawling can be large and complex, but you do not have to do it alone. There are many web crawling tools available that make this process easier. These tools automatically crawl websites, saving time and effort, and allow you to focus on analyzing the collected data instead of manually browsing pages.

Each crawling tool has its own strengths and is useful for different situations.

---

## Popular Web Crawlers

### **Burp Suite Spider**

Burp Suite is a popular web application testing platform. It includes an active crawler called **Spider**, which is very effective at:

* Mapping web applications
* Finding hidden pages and directories
* Discovering potential security issues

---

### **OWASP ZAP (Zed Attack Proxy)**

OWASP ZAP is a free and open-source web application security scanner. It supports both automated and manual testing and includes a built-in spider that:

* Crawls web applications
* Identifies pages and parameters
* Helps detect possible vulnerabilities

---

### **Scrapy (Python Framework)**

Scrapy is a powerful Python framework used to build custom web crawlers. It is known for:

* Extracting structured data
* Handling complex crawling logic
* Automating data collection

Its flexibility makes it ideal for custom reconnaissance and large crawling tasks.

---

### **Apache Nutch (Scalable Crawler)**

Apache Nutch is an open-source crawler written in Java. It is designed for:

* Large-scale crawling
* Crawling entire websites or large domains

Although it requires more setup and technical knowledge, it is very powerful for large reconnaissance projects.

---

## Ethical Crawling Considerations

No matter which tool you use, ethical and responsible crawling is very important.

* Always get permission before crawling a website
* Avoid aggressive or excessive requests
* Do not overload the target server
* Respect the website’s rules and limitations

Ignoring these practices can cause service disruption or legal issues.

---

## Scrapy

In this section, we use **Scrapy** with a custom spider for reconnaissance on **inlanefreight.com**.

If you want deeper knowledge about crawling and spidering techniques, you can refer to the **Using Web Proxies** module, which is also part of CWES.

---

## Installing Scrapy

Before using Scrapy, make sure it is installed on your system. You can install it using `pip`, the Python package manager.

```bash
pip3 install scrapy
```

This command installs Scrapy and all required dependencies, preparing your system to run the crawler.

---

## ReconSpider

ReconSpider is a custom Scrapy spider used for reconnaissance.

### Download and Extract ReconSpider

Run the following commands to download and extract the spider:

```bash
wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip
unzip ReconSpider.zip
```

---

### Running ReconSpider

After extraction, run the spider using:

```bash
python3 ReconSpider.py http://inlanefreight.com
```

You can replace `inlanefreight.com` with any domain you want to crawl.

The spider will start crawling the website and collecting useful data.

---

## results.json

Once the crawl is complete, the extracted data is saved in a file called **results.json**.
You can open this file using any text editor.

### Example JSON Structure

```json
{
    "emails": [
        "lily.floid@inlanefreight.com",
        "cvs@inlanefreight.com"
    ],
    "links": [
        "https://www.themeansar.com",
        "https://www.inlanefreight.com/index.php/offices/"
    ],
    "external_files": [
        "https://www.inlanefreight.com/wp-content/uploads/2020/09/goals.pdf"
    ],
    "js_files": [
        "https://www.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=3.3.2"
    ],
    "form_fields": [],
    "images": [
        "https://www.inlanefreight.com/wp-content/uploads/2021/03/AboutUs_01-1024x810.png"
    ],
    "videos": [],
    "audio": [],
    "comments": [
        "<!-- #masthead -->"
    ]
}
```

---

## Understanding the JSON Output

Each key in the JSON file represents a different type of data collected from the website.

| JSON Key       | Description                          |
| -------------- | ------------------------------------ |
| emails         | Email addresses found on the website |
| links          | Internal and external URLs           |
| external_files | Files like PDFs and documents        |
| js_files       | JavaScript files used by the site    |
| form_fields    | Form input fields (empty here)       |
| images         | Image URLs                           |
| videos         | Video URLs (empty here)              |
| audio          | Audio file URLs (empty here)         |
| comments       | HTML comments from page source       |

---

## Why This Data Is Useful

By reviewing `results.json`, you can:

* Understand the website structure
* Find contact information
* Identify external files and scripts
* Discover hidden comments and clues
* Locate potential areas for further testing

---

## Conclusion

Web crawling tools make reconnaissance faster and more effective. Tools like Burp Suite Spider, OWASP ZAP, Scrapy, and Apache Nutch automate the crawling process and collect valuable information. Using Scrapy with ReconSpider provides a structured and detailed view of a website, helping you identify key assets and potential targets for deeper analysis.
