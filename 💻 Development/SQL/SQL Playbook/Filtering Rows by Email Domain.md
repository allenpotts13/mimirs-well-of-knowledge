---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - like
  - string-matching
  - email
aliases:
  - Filtering by email suffix
publish: true
permalink: sql/filtering-rows-by-email-domain
---

# <span class="rune">ᚲ</span> Filtering Rows by Email Domain

> *Use LIKE with a leading wildcard to match everyone on a particular email domain.*

---

## 🎯 Problem

How do you find all employees whose email address is on the domain "@example.com"?

---

## 💻 SQL Solution

```sql
SELECT * FROM employees WHERE email LIKE '%@example.com';
```

---

## 📝 Notes

- This is the exact same technique as [[Matching Text Patterns with LIKE]] — a leading `%` wildcard matching anything before a fixed suffix — just applied to one of the most common real-world uses of it: filtering by domain.
- Because the wildcard is at the front, this can't use a standard index efficiently and forces a full scan; fine for small-to-medium tables, worth a second thought at large scale.
- If you only care about the domain matching regardless of case, wrap the column in `LOWER()` — see [[Converting Text to Lowercase]].

---

## 🔗 Related Notes

- [[Matching Text Patterns with LIKE]]
- [[Converting Text to Lowercase]]
