
# robots.txt

Imagine visiting a large house where some rooms have a sign saying “Private.” You are allowed to move around the open areas, but you are expected not to enter those private rooms.
In the web world, **robots.txt** works in a similar way. It tells bots and crawlers which parts of a website they are allowed to visit and which parts they should avoid.

---

## What is robots.txt?

**robots.txt** is a simple text file placed in the main (root) directory of a website. For example:

```
https://www.example.com/robots.txt
```

It follows the **Robots Exclusion Standard**, which is a set of rules that explains how web crawlers should behave when visiting a website.
This file contains instructions called **directives** that tell bots which pages or folders they can or cannot crawl.

---

## How robots.txt Works

The rules inside robots.txt are written for **user-agents**, which are names that identify different bots and crawlers.

Example:

```
User-agent: *
Disallow: /private/
```

This rule means:

* `*` = all bots
* Bots are not allowed to visit any URL that starts with `/private/`

robots.txt can also:

* Allow access to specific folders
* Slow down crawlers to protect the server
* Share sitemap locations to help crawlers

---

## Understanding robots.txt Structure

The robots.txt file is a plain text file stored in the root of the website.
It is divided into **records**, and each record is separated by a blank line.

Each record has two main parts:

### User-agent

This tells which bot the rules apply to.

* `*` means all bots
* Specific bots can be named, like Googlebot or Bingbot

### Directives

These are the rules that tell bots what they can or cannot do.

---

## Common robots.txt Directives

| Directive   | Meaning                  | Example                                        |
| ----------- | ------------------------ | ---------------------------------------------- |
| Disallow    | Do not crawl this path   | `Disallow: /admin/`                            |
| Allow       | Crawl this path          | `Allow: /public/`                              |
| Crawl-delay | Wait before next request | `Crawl-delay: 10`                              |
| Sitemap     | Location of sitemap      | `Sitemap: https://www.example.com/sitemap.xml` |

---

## Why Respect robots.txt?

robots.txt is not a strong security control, but most good bots and search engines respect it.

### Protecting the Server

It helps reduce heavy traffic from bots, which can slow down or crash a website.

### Protecting Sensitive Content

It helps prevent private pages from appearing in search engine results.

### Legal and Ethical Reasons

Ignoring robots.txt may break a website’s rules or terms of service. In some cases, accessing restricted data can cause legal trouble.

---

## robots.txt in Web Reconnaissance

For security testing and reconnaissance, robots.txt can give useful hints about a website.

### Finding Hidden Directories

Disallowed paths often show folders the website owner wants to hide, such as:

* Admin panels
* Backup folders
* Internal tools

### Understanding Website Structure

Allowed and disallowed paths help you understand how the website is organized.

### Detecting Traps

Some websites add fake paths in robots.txt to catch bad bots. Finding these shows that the site owner is security-aware.

---

## Analyzing robots.txt (Example)

```
User-agent: *
Disallow: /admin/
Disallow: /private/
Allow: /public/

User-agent: Googlebot
Crawl-delay: 10

Sitemap: https://www.example.com/sitemap.xml
```


---

## What This Example Tells Us

From this file, we learn that:

* All bots are blocked from `/admin/` and `/private/`
* All bots can access `/public/`
* Googlebot must wait 10 seconds between requests
* A sitemap is provided to help crawlers

This suggests:

* An admin panel likely exists at `/admin/`
* Private data may be stored in `/private/`
* The website owner wants controlled crawling

---

## Conclusion

robots.txt helps guide how bots interact with a website. While it does not block attackers, it gives valuable information about hidden paths and website structure. In web reconnaissance, reading robots.txt carefully can reveal important clues that help build a better understanding of the target website.

