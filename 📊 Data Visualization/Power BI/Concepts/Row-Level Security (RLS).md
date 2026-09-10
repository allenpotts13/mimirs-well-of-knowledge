---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Advanced
tags:
  - power-bi
  - security
aliases:
  - RLS
  - Row-Level Security
publish: true
---

# 📚 Row-Level Security (RLS)

> Restricts which rows a user sees when they open a report — the same report, the same visuals, different data depending on who's looking.

---

## 🎯 Purpose

RLS lets one report serve many audiences safely — a regional sales manager sees only their region's data, automatically, without needing a separate report per region.

---

## 🧠 Key Ideas

- Defined through **roles** in Power BI Desktop, each with a DAX filter expression applied to one or more tables.
- Filters propagate through relationships — restricting a dimension table (like Region) automatically restricts the connected fact table too, following the same logic as any other filter.
- **Static RLS** — the filter is a fixed value, one role per group (e.g. a "West Region" role).
- **Dynamic RLS** — the filter uses `USERPRINCIPALNAME()` to look up the logged-in user against a table, automatically applying the correct filter per person without needing a role per person.
- Roles are assigned to users/groups after publishing, in the Power BI Service.

---

## ⚙️ How It Works

```text
Desktop: define a role's filter, e.g.
  [Region] = "West"

Service: assign users/groups to that role

At view time: the DAX filter applies automatically,
  restricting every visual as if the user had
  manually filtered to their region
```

Dynamic RLS instead looks up the current user against a mapping table:

```dax
[RegionManagerEmail] = USERPRINCIPALNAME()
```

---

## 💻 Examples

```dax
// Static role filter, applied to a "West Region" role
[Region] = "West"

// Dynamic role filter, one role handles every user
[ManagerEmail] = USERPRINCIPALNAME()
```

---

## 🚀 Real World Applications

- One sales report, each regional manager sees only their own region
- HR reports where employees can only see their own department's data
- Multi-tenant reports where each customer only sees their own organization's rows

---

## ⚖️ Advantages

- One report and one dataset serve many audiences securely, instead of maintaining separate copies.
- Dynamic RLS scales to any number of users without adding a role per person.
- Enforced at the data engine level — can't be bypassed by editing the visual.

---

## ⚠️ Limitations

- Only restricts rows visible in visuals — doesn't hide measure totals calculated with `ALL()` unless the filter is designed to account for that.
- Testing RLS requires "View As Role" in Desktop or the Service, since it doesn't apply to the report's author/owner by default.
- Dynamic RLS adds some query overhead per user, looking up their access on every query.

---

## 🚨 Common Mistakes

- Assuming RLS applies automatically to the report author or admins — object-level owners typically bypass RLS unless explicitly tested against.
- Forgetting that a measure using `CALCULATE(..., ALL(Table))` can accidentally expose totals RLS was supposed to hide.
- Building static roles per user instead of dynamic RLS, creating a maintenance burden as the user list grows.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Always test RLS with "View As Role" before publishing — the difference between "the report looks right to me" and "the report looks right to the person it's actually restricted for" is exactly the gap RLS bugs hide in.

---

## 🔗 Related Notes

- [[Data Modeling and Relationships]]
- [[The CALCULATE Function]]
