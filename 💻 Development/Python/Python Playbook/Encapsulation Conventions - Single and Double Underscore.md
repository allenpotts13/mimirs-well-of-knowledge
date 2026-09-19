---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - oop
  - encapsulation
  - name-mangling
aliases:
  - Name Mangling Python
  - "We are all consenting adults here"
publish: true
permalink: python/encapsulation-conventions-single-and-double-underscore
---

# <span class="rune">ᛟ</span> Encapsulation Conventions - Single and Double Underscore

> *Python's philosophy on privacy is famously summarized as "we're all consenting adults here" — there is no true private, only an increasingly strong series of polite requests not to touch something.*

---

## 🎯 Purpose

Unlike Java's compiler-enforced `private`/`protected`/`public` (see [[Access Modifiers]] in the Java Codex), Python has **no true access control** at all — visibility is entirely a matter of naming convention that every developer is trusted to respect. A single leading underscore (`_name`) signals "internal use, please don't touch," while a double leading underscore (`__name`) triggers actual **name mangling**, a mechanism originally designed to avoid naming collisions in subclasses, not to provide real privacy.

---

## 🧠 Key Ideas

- `_name` (single leading underscore) is a **convention only** — it signals "this is intended as internal/protected," but nothing in the language actually prevents external code from accessing it; it's a request, not a rule.
- `__name` (double leading underscore, no trailing underscores) triggers **name mangling**: Python internally renames the attribute to `_ClassName__name`, making it *harder* (not impossible) to accidentally access or override from outside — or from a subclass.
- Name mangling exists specifically to prevent **accidental** name collisions in subclass hierarchies, not to provide genuine security or true privacy — a determined caller can still access `obj._ClassName__name` directly.
- `__dunder__` (double leading AND trailing underscores) is reserved for Python's own special/magic methods (see [[Dunder Methods - __init__, __str__, __repr__, and __eq__]]) — never use this pattern for your own regular attribute or method names.
- The community convention, sometimes summarized as "we're all consenting adults here," favors single-underscore `_name` for most internal-use attributes, reserving double-underscore `__name` specifically for the narrower case of avoiding subclass name collisions.

---

## ⚙️ How It Works

When Python parses a class body and encounters an attribute or method name starting with `__` (and not also ending with `__`), it automatically rewrites every reference to that name, prefixing it with the class name: `self.__value` inside class `Account` actually becomes `self._Account__value` internally. This means a subclass defining its *own* `__value` attribute doesn't accidentally collide with or override the parent's, since the parent's mangled name includes the parent's own class name — but it also means the "privacy" is really just a name transformation, fully visible and accessible to anyone who knows (or looks up) the actual mangled name.

```text
class Account:
    def __init__(self):
        self.__balance = 100          # internally becomes self._Account__balance

account = Account()
# account.__balance                    # AttributeError — the mangled name is DIFFERENT
account._Account__balance               # 100 — still fully accessible, just less convenient to guess
```

---

## 💻 Examples

```python
class BankAccount:
    def __init__(self, balance):
        self.balance = balance            # PUBLIC — no restriction implied at all
        self._pin = "1234"                  # single underscore — "internal use," a polite request
        self.__account_number = "12345678"    # double underscore — NAME MANGLED

    def show_internal(self):
        print(self._pin)                        # works fine from WITHIN the class
        print(self.__account_number)              # ALSO fine — mangling is transparent from inside the class

account = BankAccount(100)
print(account.balance)          # 100 — totally fine, it's public by convention
print(account._pin)               # "1234" — ALSO accessible — Python does not enforce the underscore at all
# print(account.__account_number) # AttributeError — the name was MANGLED

print(account._BankAccount__account_number)  # "12345678" — STILL accessible via the mangled name

# Name mangling's REAL purpose — avoiding accidental subclass collisions
class Base:
    def __init__(self):
        self.__value = "base"        # mangled to _Base__value

    def show(self):
        print(self.__value)            # always refers to _Base__value, from WITHIN Base

class Derived(Base):
    def __init__(self):
        super().__init__()
        self.__value = "derived"       # mangled to _Derived__value — a DIFFERENT attribute entirely!

d = Derived()
d.show()          # "base" — Base.show() still sees ITS OWN mangled _Base__value, unaffected by Derived's
```

---

## 🚀 Real World Applications

- Using a single leading underscore (`_internal_method`) to signal "this is an implementation detail, don't rely on it externally" throughout a library or module's public API
- Using double leading underscores specifically when defining a base class intended to be subclassed, to avoid an unintentional attribute name collision from a subclass
- Reading unfamiliar Python code and correctly interpreting `_name` as "treat as internal" without assuming any actual enforced protection exists
- Recognizing `__dunder__`-style names as reserved for Python's own special methods, never appropriating that pattern for custom application code

---

## ⚖️ Advantages

- Convention-based privacy is lightweight and flexible — it communicates intent clearly without the ceremony of Java's formal access modifier system.
- Name mangling solves the genuine, narrow problem of accidental subclass attribute collisions without requiring a heavier mechanism.
- The "consenting adults" philosophy trusts developers with introspection and flexibility that a strictly enforced access system would prevent (useful for debugging, testing, and legitimate metaprogramming).

---

## ⚠️ Limitations

- There is genuinely no way to make an attribute truly inaccessible from outside a class in Python — anyone can bypass both the single-underscore convention and the double-underscore name mangling if they choose to.
- Relying purely on convention means enforcement depends entirely on team discipline and code review, not the language itself.
- Name mangling's mechanism (and its very existence) surprises many developers coming from languages with real access control, since double underscore *looks* like it should mean "truly private" but doesn't provide that guarantee.

---

## 🚨 Common Mistakes

- Assuming `__name` provides genuine privacy the way Java's `private` does — it only renames the attribute; it doesn't prevent access, just makes it slightly less convenient and less discoverable.
- Using double-underscore naming reflexively for every "should be internal" attribute, when a single underscore is the more idiomatic, more commonly expected convention for that purpose — double underscore is specifically for the subclass-collision-avoidance case.
- Defining custom methods with a `__dunder__`-style name (both leading and trailing double underscores) for non-magic purposes, potentially colliding with a real or future Python special method name.
- Being confused when a subclass's `__value` doesn't seem to "override" the parent's `__value` — each gets independently mangled per-class, so they're actually two entirely separate attributes, not one overridden one.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 9.6, "Private Variables" (name mangling explanation)
- PEP 8: "Style Guide for Python Code" — naming conventions section

---

## 💡 Wisdom from Mímir

Default to a single leading underscore (`_name`) for "internal use" attributes in ordinary code — reserve the double-underscore name-mangling behavior specifically for base classes designed to be subclassed, where avoiding an accidental attribute collision is the actual, narrow problem being solved. Reaching for double underscore as a general-purpose "make this private" habit misunderstands what it's actually for.

---

## 🔗 Related Notes

- [[Classes and Objects in Python]]
- [[Class Methods, Static Methods, and Properties]]
- [[Dunder Methods - __init__, __str__, __repr__, and __eq__]]
- [[Python Codex]]
