# DOM-Based XSS

## How It Works

Unlike Reflected and Stored XSS, DOM XSS happens **entirely in the browser**. The server is not involved. JavaScript on the page reads attacker-controlled data (source) and writes it to the page unsafely (sink).

```
Attacker controls part of the URL or page data  ← SOURCE
        ↓
JavaScript reads it
        ↓
JavaScript writes it to the DOM unsafely         ← SINK
        ↓
Browser executes the injected script
```

> The server never sees the malicious payload. The vulnerability is in the **client-side JavaScript**.

---

## Sources (Where data comes from)

```javascript
location.search      // everything after ? in the URL
location.hash        // everything after # in the URL
location.href        // full URL
document.referrer    // the referring page URL
document.cookie      // cookies
```

---

## Sinks (Where data is written unsafely)

```javascript
// HTML sinks — parse and render HTML
element.innerHTML
element.outerHTML
document.write()
document.writeln()

// JS execution sinks — execute strings as code
eval()
setTimeout("string")
setInterval("string")
new Function("string")

// jQuery sinks
$()                  // selector / HTML creator
.html()              // like innerHTML
.attr("href", ...)   // sets attributes
```

---

## The Golden Rule

```
If data flows from a SOURCE to a SINK with no sanitization → DOM XSS
```

---

## Labs Covered

| File | Sink | Source | Status |
|---|---|---|---|
| [document-write.md](./document-write.md) | `document.write()` | `location.search` | ✅ |
| [innerHTML.md](./innerHTML.md) | `innerHTML` | `location.search` | ✅ |
| [jquery.md](./jquery.md) | `attr()` + `$()` | `location.search` + `location.hash` | ✅ |
| [angularjs.md](./angularjs.md) | `{{}}` expression | HTML body | ✅ |

---

## Key Takeaways

- DOM XSS requires reading the **client-side JS**, not just the server response
- Use browser DevTools to trace data flow from source to sink
- Tools like Burp Suite DOM Invader can automate source/sink detection
