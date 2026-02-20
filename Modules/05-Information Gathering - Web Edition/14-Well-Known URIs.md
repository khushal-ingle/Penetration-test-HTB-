
# Well-Known URIs

The **.well-known** standard is defined in **RFC 8615**. It provides a **fixed, standard folder** inside a website where important information is stored. This folder is available at:

```
/.well-known/
```

This location is used to store important files related to:

* Website services
* Authentication systems
* Security policies
* Protocol configurations

Because the location is standard, browsers, applications, and security tools know **exactly where to look** for this information.

For example, if a website publishes its security contact details, they can be accessed at:

```
https://example.com/.well-known/security.txt
```

---

## Why .well-known Exists

Before .well-known, websites stored configuration files in random locations. This made discovery difficult.

The .well-known standard solves this by:

* Using a **fixed path**
* Making discovery **automatic**
* Ensuring **consistency across websites**

Clients can simply build a URL and fetch the required information without guessing paths.

---

## IANA and .well-known URIs

The **Internet Assigned Numbers Authority (IANA)** maintains an official registry of all approved `.well-known` URI names.

Each registered URI:

* Has a defined purpose
* Follows a standard format
* Is documented by an RFC or specification

Below are some commonly used `.well-known` URIs.

---

## Common .well-known URI Examples

| URI                          | Purpose                                       | Status      | Reference            |
| ---------------------------- | --------------------------------------------- | ----------- | -------------------- |
| security.txt                 | Contact details for reporting security issues | Permanent   | RFC 9116             |
| /.well-known/change-password | Standard link for changing user passwords     | Provisional | W3C Specification    |
| openid-configuration         | OpenID Connect configuration details          | Permanent   | OpenID Specification |
| assetlinks.json              | Verifies app and website ownership            | Permanent   | Google Specification |
| mta-sts.txt                  | Email security policy (MTA-STS)               | Permanent   | RFC 8461             |

This is only a small part of the full IANA registry. Many other `.well-known` URIs exist for different services and protocols.

---

## Web Recon and .well-known

In **web reconnaissance**, `.well-known` URIs are very useful. They often expose:

* Hidden endpoints
* Authentication systems
* Security-related configurations

These endpoints do not always appear in normal site navigation, but they can be accessed directly if you know the correct path.

One of the most useful `.well-known` URIs during recon is **openid-configuration**.

---

## openid-configuration Explained

The `openid-configuration` file is part of **OpenID Connect**, which is an identity system built on top of **OAuth 2.0**.

When an application wants to use OpenID Connect for login, it first fetches this file from:

```
https://example.com/.well-known/openid-configuration
```

This endpoint returns a **JSON document** that describes how authentication works on the site.

---

## Example openid-configuration Response

```json
{
  "issuer": "https://example.com",
  "authorization_endpoint": "https://example.com/oauth2/authorize",
  "token_endpoint": "https://example.com/oauth2/token",
  "userinfo_endpoint": "https://example.com/oauth2/userinfo",
  "jwks_uri": "https://example.com/oauth2/jwks",
  "response_types_supported": ["code", "token", "id_token"],
  "subject_types_supported": ["public"],
  "id_token_signing_alg_values_supported": ["RS256"],
  "scopes_supported": ["openid", "profile", "email"]
}
```

---

## What Information This Reveals

The `openid-configuration` file gives several important details.

### Endpoint Discovery

It clearly shows:

* **Authorization endpoint** – where login requests go
* **Token endpoint** – where access tokens are issued
* **Userinfo endpoint** – where user details are fetched

### Cryptographic Details

* **jwks_uri** shows where cryptographic keys are stored
* Helps understand how tokens are signed and verified

### Supported Features

* Allowed scopes (openid, profile, email)
* Supported response types
* Supported signing algorithms

All of this helps map how authentication works on the site.

---

## Why This Matters in Reconnaissance

From a reconnaissance point of view, `.well-known` URIs:

* Expose structured configuration data
* Reduce guessing
* Reveal authentication and security architecture

By checking `.well-known` paths, security professionals can:

* Identify login systems
* Discover hidden endpoints
* Understand security controls
* Plan further testing safely and efficiently

---

## Conclusion

The `.well-known` directory provides a standardized way to expose important website metadata. For web reconnaissance, it is a powerful source of information that can reveal authentication endpoints, security policies, and protocol configurations.

Exploring the IANA `.well-known` registry and testing available endpoints can significantly improve understanding of a website’s security setup and overall architecture.

