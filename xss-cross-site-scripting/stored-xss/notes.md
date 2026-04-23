# Stored XSS

## How It Works

The malicious payload is **saved** in the application's database (comment, profile, message, etc.) and then displayed to other users. Every user who views the page executes the payload.

```
Attacker submits malicious input
        ↓
Application saves it to database
        ↓
Any user views the page
        ↓
Browser executes the stored script
```

> ⚠️ Stored XSS is **persistent** — no need to trick the victim into clicking a link. Anyone visiting the page is affected.

---

## Basic Example

**Vulnerable feature:** Blog comments

**Attacker submits:**
```html
<script>alert(document.cookie)</script>
```

**What gets saved in the database:**
```
<script>alert(document.cookie)</script>
```

**What every visitor sees (rendered in browser):**
→ Script executes and sends their cookie to the attacker.

---

## Lab: Stored XSS into HTML context with nothing encoded

**Level:** Apprentice ✅ Solved

**Scenario:** Comment section stores and renders input with no sanitization.

**Payload:**
```html
<script>alert(1)</script>
```

**Why it works:** The comment is saved as-is and rendered in the page HTML. Every user who loads the page triggers the alert.

---

## Stored vs Reflected

| | Reflected | Stored |
|---|---|---|
| Payload location | URL / request | Database |
| Persistence | ❌ No | ✅ Yes |
| Who is affected | Only victim who clicks link | Every user who visits the page |
| Social engineering needed | ✅ Yes | ❌ No |
| Severity | High | Critical |

---

## Key Takeaways

- Stored XSS is more dangerous than Reflected — no user interaction beyond visiting the page
- Common injection points: comments, usernames, profile bios, product reviews, support tickets
- Even if input is sanitized on display, check if it's stored raw and displayed elsewhere
