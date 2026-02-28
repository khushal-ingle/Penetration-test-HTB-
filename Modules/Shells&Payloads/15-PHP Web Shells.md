
# PHP Web Shells

## Why PHP Matters in Penetration Testing

**PHP (Hypertext Preprocessor)** is the most widely used server-side scripting language on the internet. According to W3Techs, PHP powers **over 78% of websites** whose backend language is known.

For a penetration tester, this means:

* If a web app is running PHP, **PHP-based web shells are a prime attack vector**
* PHP executes **server-side**, giving us OS-level command execution
* PHP is commonly used on **Linux web servers**, which are frequent pivot points

If you see files like:

* `login.php`
* `index.php`
* `config.php`

You should immediately consider **PHP web shell opportunities**.

---

## PHP Execution Model (Why Web Shells Work)

When a user submits a login form:

1. Browser sends username and password to the server
2. PHP processes the request on the server
3. PHP generates HTML as output

If we can upload a `.php` file:

* The server executes our code
* The browser becomes our command interface
* We gain **remote code execution (RCE)**

---

## Hands-On Scenario: rConfig (PHP-Based Application)

From earlier sections, we know:

* rConfig runs on **Linux**
* Backend language is **PHP**
* Version **3.9.6** is vulnerable
* File uploads are available (Vendor Logo upload)

This makes rConfig an ideal candidate for a **PHP web shell**.

---

## Goal

Upload a **PHP web shell** disguised as an image, bypass file-type validation, and execute OS commands via the browser.

---

## Choosing a PHP Web Shell

We use **WhiteWinterWolf’s PHP Web Shell** because:

* Lightweight
* Browser-based
* Supports command execution
* Commonly used in labs and real pentests

You can either:

* Download it
* Or copy the source code into a `.php` file

Example filename:

```text
connect.php
```

---

## Initial Upload Attempt (Expected Failure)

rConfig restricts uploads to image types:

* `.jpg`
* `.png`
* `.gif`

Uploading `connect.php` directly fails due to:

* Server-side MIME validation

This is normal.

---

## Bypassing File Type Restrictions with Burp Suite

### Step 1: Configure Burp Proxy

Browser proxy settings:

* IP: `127.0.0.1`
* Port: `8080`

This forces all HTTP traffic through Burp.

---

### Step 2: Intercept the Upload Request

1. Click **Add Vendor**
2. Select your `.php` web shell as the logo
3. Click **Save**
4. Burp intercepts the POST request

---

### Step 3: Modify Content-Type

Inside Burp, locate:

```http
Content-Type: application/x-php
```

Change it to:

```http
Content-Type: image/gif
```

Why this works:

* The server trusts the MIME type
* The file extension remains `.php`
* PHP still executes server-side

---

### Step 4: Forward the Request

* Forward the modified request
* Turn off interception
* Return to the browser

Success message:

```text
Added new vendor NetVen to Database
```

This confirms the file was uploaded.

---

## Locating the Uploaded Web Shell

Uploaded vendor logos are stored in:

```text
/images/vendor/
```

Navigate to:

```text
http://<target>/images/vendor/connect.php
```

---

## Web Shell Execution

When accessed:

* PHP executes on the server
* Commands are executed as the web server user (e.g., `apache`, `www-data`)
* Output is returned in the browser

This is a **non-interactive shell**, but it confirms RCE.

---

## Limitations of PHP Web Shells

Web shells are powerful, but they have drawbacks:

* No full TTY
* Command chaining may fail (`&&`, pipes)
* File operations are clumsy
* Sessions are unstable
* Files may be auto-deleted
* High forensic footprint

Because of this:

**A web shell should be treated as a foothold, not a final shell.**

---

## Correct Post-Web-Shell Workflow

Once PHP web shell access is confirmed:

1. Verify execution:

   ```bash
   whoami
   id
   uname -a
   ```
2. Enumerate environment
3. Use the web shell to:

   * Upload a reverse shell
   * Or execute a reverse shell one-liner
4. Catch callback on your listener
5. Upgrade to an interactive shell
6. Remove the web shell
7. Document everything

---

## Operational Security Considerations

During real engagements:

* Web apps may auto-clean uploaded files
* Leaving shells behind increases detection risk
* Files may be discovered during blue-team review

Best practice:

* Establish reverse shell
* Delete the PHP payload
* Log hashes, filenames, paths, timestamps
* Include artifacts in the final report

---

## Key Takeaways

* PHP is the most common web backend
* PHP file upload = potential RCE
* MIME-type manipulation bypasses many filters
* PHP web shells provide initial access
* Always upgrade to a reverse shell
* Clean up and document thoroughly

---


Tell me what you want to do next.
