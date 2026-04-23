# XSS Bypasses & CSP

> 🚧 This section will be completed in the advanced phase of my learning path.

---

## WAF & Filter Bypass

Web Application Firewalls (WAFs) and input filters try to block XSS payloads. Bypassing them requires understanding what exactly is being blocked and finding alternative payloads.

**Common bypass techniques:**
- Using alternative tags (`<svg>`, `<details>`, `<video>`)
- Using custom HTML tags
- Case manipulation (`<ScRiPt>`)
- Encoding (`&#x61;&#x6C;&#x65;&#x72;&#x74;`)

**Labs:** 🚧
- Reflected XSS with most tags and attributes blocked
- Reflected XSS with all tags blocked except custom ones
- Reflected XSS with some SVG markup allowed
- Reflected XSS in canonical link tag

---

## Content Security Policy (CSP)

CSP is a browser security mechanism that restricts which scripts can execute on a page. A strong CSP can prevent XSS even if injection is possible.

**CSP example:**
```
Content-Security-Policy: script-src 'self'
```
This only allows scripts from the same origin — blocking injected scripts.

**Bypass techniques:**
- Dangling markup attacks
- JSONP endpoints on whitelisted domains
- Script gadgets

**Labs:** 🚧
- Reflected XSS protected by very strict CSP, with dangling markup attack
- Reflected XSS protected by CSP, with CSP bypass

---

## AngularJS Sandbox Escape

Newer versions of AngularJS introduced a "sandbox" to prevent `{{ }}` from accessing dangerous objects. Sandbox escapes are techniques to break out of this restriction.

**Labs:** 🚧
- Reflected XSS with AngularJS sandbox escape without strings
- Reflected XSS with AngularJS sandbox escape and CSP

---

> 📌 These are advanced topics that require a solid understanding of JavaScript, browser security models, and HTTP headers. Will be tackled after completing the foundational phases.
