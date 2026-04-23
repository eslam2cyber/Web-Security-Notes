# Cross-Site Scripting (XSS)

> Personal notes and lab documentation from PortSwigger Web Security Academy.

---

## What is XSS?

Cross-Site Scripting (XSS) is a vulnerability that allows an attacker to inject malicious JavaScript into a web page viewed by other users. The browser trusts the page's content — so if an attacker's script is part of that page, the browser will execute it.

**Core idea:**
```
User input → Injected into page → Browser executes it
```

---

## XSS Types

| Type | Where does the payload live? | Persists? |
|---|---|---|
| Reflected | Server reflects input back in response | ❌ No |
| Stored | Payload saved in database, shown to all users | ✅ Yes |
| DOM-based | Payload processed by JavaScript in the browser | ❌ No (client-side only) |

---

## Key Concepts

### Source
Where attacker-controlled data enters the application.
```
location.search    → query string (?q=...)
location.hash      → fragment (#...)
document.referrer  → referring URL
document.cookie    → cookies
```

### Sink
Where data is written to the page or executed.
```
innerHTML               → renders HTML
document.write()        → writes to DOM
eval()                  → executes JS
jQuery $()              → creates DOM elements
jQuery .attr()          → sets element attributes
```

**The vulnerability exists when:**
```
Source → (no sanitization) → Sink
```

---

## Lab Progress

### ✅ Completed

| Lab | Level | Key Takeaway |
|---|---|---|
| Reflected XSS into HTML context with nothing encoded | Apprentice | Basic `<script>alert(1)</script>` |
| Stored XSS into HTML context with nothing encoded | Apprentice | Payload stored, executes for every visitor |
| DOM XSS in `document.write` sink using `location.search` | Apprentice | `document.write` renders raw HTML from URL |
| DOM XSS in `innerHTML` sink using `location.search` | Apprentice | `innerHTML` parses HTML from URL |
| DOM XSS in jQuery `href` attribute sink using `location.search` | Apprentice | `attr("href")` + `javascript:` URL |
| DOM XSS in jQuery selector sink using hashchange event | Apprentice | `$()` parses HTML from `location.hash` |
| DOM XSS in `document.write` inside select element | Practitioner | Breaking out of `<select>` context |
| DOM XSS in AngularJS expression | Practitioner | `{{}}` executes JS without `<script>` |

### 🚧 Not Yet Completed — Requires Deeper JS Knowledge

| Lab | Level | What's Needed |
|---|---|---|
| Reflected XSS into attribute with angle brackets HTML-encoded | Apprentice | Attribute context escaping |
| Stored XSS into anchor href with double quotes HTML-encoded | Apprentice | `javascript:` in href |
| Reflected XSS into JS string with angle brackets HTML-encoded | Apprentice | Breaking out of JS string context |
| Reflected DOM XSS | Practitioner | Understanding JS response parsing |
| Stored DOM XSS | Practitioner | JS-based sink in stored context |
| Reflected XSS with most tags blocked | Practitioner | WAF bypass techniques |
| Reflected XSS with all tags blocked except custom | Practitioner | Custom HTML tags |
| Reflected XSS with SVG markup | Practitioner | SVG-based payloads |
| Reflected XSS in canonical link tag | Practitioner | `accesskey` attribute abuse |
| Reflected XSS into JS string (single quote + backslash escaped) | Practitioner | JS string escape bypass |
| Reflected XSS into JS string (angle brackets + double quotes encoded) | Practitioner | Single quote escape bypass |
| Stored XSS into onclick event | Practitioner | Event handler context |
| Reflected XSS into template literal | Practitioner | `${}` inside backtick strings |
| Exploiting XSS to steal cookies | Practitioner | Real-world exploitation |
| Exploiting XSS to capture passwords | Practitioner | Real-world exploitation |
| Exploiting XSS to bypass CSRF defenses | Practitioner | Chaining vulnerabilities |
| Reflected XSS with AngularJS sandbox escape (no strings) | Expert | Advanced AngularJS bypass |
| Reflected XSS with AngularJS sandbox escape and CSP | Expert | CSP + AngularJS |
| Reflected XSS with event handlers and href blocked | Expert | Advanced filter bypass |
| Reflected XSS in JS URL with characters blocked | Expert | URL context bypass |
| Reflected XSS with strict CSP + dangling markup | Practitioner | CSP bypass techniques |
| Reflected XSS protected by CSP with bypass | Expert | Full CSP bypass |

---

## Current Status & Next Steps

### What I understand well ✅
- The three types of XSS and how they differ
- Sources and sinks concept
- Basic DOM XSS via `document.write`, `innerHTML`
- jQuery-based DOM XSS (`attr()`, `$()`, `hashchange`)
- AngularJS template injection via `{{}}`

### What requires more work 🚧
- Injection contexts (attribute, JS string, template literal)
- Filter and WAF bypass techniques
- Real-world exploitation (cookie stealing, CSRF bypass)
- Advanced AngularJS sandbox escapes
- Content Security Policy (CSP) and how to bypass it

### Why I paused advanced labs
Most of the remaining labs require a **deeper understanding of JavaScript** — specifically how the browser parses JS strings, how encoding works at different contexts, and how to manipulate JS execution flow.

**I am currently improving my JavaScript skills with a focus on:**
- DOM manipulation
- String handling and encoding
- Event handling
- Reading and manipulating URLs (`location` object)

I will return to the advanced XSS labs once I have the JS foundation needed to understand and exploit these contexts properly.
