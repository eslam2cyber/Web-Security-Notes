# Reflected XSS

## How It Works

The application takes user input from the request (usually the URL) and includes it directly in the response without sanitization. The browser then renders and executes it.

```
Attacker crafts malicious URL
        ↓
Victim clicks the link
        ↓
Server reflects input back in HTML response
        ↓
Browser executes the script
```

> ⚠️ Reflected XSS is **non-persistent** — the payload is not stored. The victim must be tricked into clicking a crafted link.

---

## Basic Example

**Vulnerable code (server-side):**
```
Search results for: <user input here>
```

**Normal request:**
```
https://site.com/search?q=hello
→ Search results for: hello
```

**Malicious request:**
```
https://site.com/search?q=<script>alert(1)</script>
→ Search results for: <script>alert(1)</script>
```

The browser sees a `<script>` tag and executes it.

---

## Lab: Reflected XSS into HTML context with nothing encoded

**Level:** Apprentice ✅ Solved

**Scenario:** Search box reflects input directly into the HTML response with no encoding.

**Payload:**
```html
<script>alert(1)</script>
```

**Why it works:** No sanitization. The input lands directly inside the HTML body as a `<script>` tag, which the browser executes immediately.

---

## Key Takeaways

- Always check where your input appears in the response
- If input lands in HTML body with no encoding → try `<script>` directly
- Reflected XSS requires **social engineering** to deliver (the victim must click the link)
- Often found in: search boxes, error messages, URL parameters reflected on page
