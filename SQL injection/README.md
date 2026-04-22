# SQL Injection (SQLi)

A collection of notes and solved labs from PortSwigger Web Security Academy.

---

## What is SQL Injection?

SQL Injection is a web security vulnerability that allows an attacker to interfere 
with the queries that an application makes to its database. It can allow an attacker 
to view, modify, or delete data they are not normally able to access.

---

## Lab Progress

| # | Lab Name | Difficulty | Status |
|---|----------|------------|--------|
| 1 | SQLi vulnerability in WHERE clause | Apprentice | ✅ Solved |
| 2 | SQLi vulnerability allowing login bypass | Apprentice | ✅ Solved |
| 3 | Querying database type and version on Oracle | Practitioner | ✅ Solved |
| 4 | Querying database type and version on MySQL and Microsoft | Practitioner | ✅ Solved |
| 5 | Listing database contents on non-Oracle databases | Practitioner | ✅ Solved |
| 6 | Listing database contents on Oracle | Practitioner | ✅ Solved |
| 7 | UNION attack, determining number of columns | Practitioner | ✅ Solved |
| 8 | UNION attack, finding a column containing text | Practitioner | ✅ Solved |
| 9 | UNION attack, retrieving data from other tables | Practitioner | ✅ Solved |
| 10 | UNION attack, retrieving multiple values in single column | Practitioner | ✅ Solved |
| 11 | Blind SQLi with conditional responses | Practitioner | ✅ Solved |
| 12 | Blind SQLi with conditional errors | Practitioner | ✅ Solved |
| 13 | Visible error-based SQLi | Practitioner | ✅ Solved |
| 14 | Blind SQLi with time delays | Practitioner | ✅ Solved |
| 15 | Blind SQLi with time delays and information retrieval | Practitioner | ✅ Solved |
| 16 | Blind SQLi with out-of-band interaction | Practitioner | ⏳ Skipped (Burp Pro required) |
| 17 | Blind SQLi with out-of-band data exfiltration | Practitioner | ⏳ Skipped (Burp Pro required) |
| 18 | SQLi with filter bypass via XML encoding | Practitioner | ✅ Solved |

---

## Topics Covered

- [Basic SQLi](./01-basic-sqli.md)
- [UNION Attacks](./02-union-attacks.md)
- [Blind SQLi](./03-blind-sqli.md)
- [Filter Bypass](./04-filter-bypass.md)

---

*Labs source: [PortSwigger Web Security Academy](https://portswigger.net/web-security/sql-injection)*
