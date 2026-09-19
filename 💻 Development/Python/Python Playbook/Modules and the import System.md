---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - modules
  - imports
  - packages
aliases:
  - Python import statement
  - Circular Import
publish: true
permalink: python/modules-and-the-import-system
---

# <span class="rune">ᛟ</span> Modules and the import System

> *Every Python file is already a module the moment it exists — there's no special declaration needed, no `package` keyword to write. Importing it just means running it once and keeping whatever names it defined.*

---

## 🎯 Purpose

Any `.py` file is automatically a **module** in Python — no special syntax required — and a folder containing an `__init__.py` file (or, since Python 3.3+, even without one, as an "implicit namespace package") becomes a **package**, a way of organizing related modules into a hierarchy. The `import` system is how one file gains access to names defined in another, and understanding exactly *when* that imported code actually runs resolves a lot of otherwise-confusing behavior.

---

## 🧠 Key Ideas

- `import module_name` runs that module's entire top-level code **once** (the first time it's imported anywhere in the running program) and binds the name `module_name` to the resulting module object; subsequent imports of the same module reuse the already-loaded result instead of re-running it.
- `from module_name import specific_name` pulls a specific function, class, or variable directly into the current namespace, rather than requiring `module_name.specific_name` everywhere it's used.
- A package is a directory containing modules, typically marked with an `__init__.py` file (which itself can contain code, run once when the package is first imported) — `import package.module` then works exactly like importing a nested module.
- A **circular import** — module A imports module B, which imports module A — can cause confusing `ImportError`s or partially-initialized modules, since Python detects the module already being in the process of loading and returns whatever partial state exists so far.
- `if __name__ == "__main__":` (covered fully in [[The if __name__ == __main__ Idiom]]) lets a module distinguish between being run directly versus being imported by something else.

---

## ⚙️ How It Works

The first time `import module_name` executes anywhere in a running program, Python locates the corresponding `.py` file, executes its entire top-level code from start to finish exactly once, and caches the resulting module object in `sys.modules`. Every subsequent `import module_name` anywhere else in the program — even in a completely different file — simply retrieves that already-cached module object instead of re-running the file's code again, which is precisely why module-level state (a module-level variable, a database connection opened at import time) is genuinely shared and persistent across every part of a program that imports it.

```text
# utils.py
print("utils.py is loading!")
def helper(): pass

# main.py
import utils     # "utils.py is loading!" printed HERE — the FIRST time
import utils      # printed NOTHING the second time — already cached in sys.modules
```

---

## 💻 Examples

```python
# utils.py
def add(a, b):
    return a + b

PI = 3.14159

# main.py — several ways to import
import utils
print(utils.add(2, 3))     # 5 — accessed via the module name
print(utils.PI)              # 3.14159

from utils import add, PI     # pulls specific names directly into THIS namespace
print(add(2, 3))                # 5 — no "utils." prefix needed now

import utils as u              # aliasing — common for long or frequently-used module names
print(u.add(2, 3))

from utils import *              # imports EVERYTHING public — generally discouraged, pollutes namespace

# Package structure example:
# myapp/
#     __init__.py
#     models.py
#     utils.py

# from another file:
from myapp import models
from myapp.utils import helper_function

# Circular import problem — module_a.py imports module_b, module_b imports module_a
# module_a.py
# import module_b
# def func_a(): return module_b.func_b()

# module_b.py
# import module_a           # if module_a hasn't finished loading yet, this can fail or return a partial module
# def func_b(): return module_a.func_a()

# A common fix — import INSIDE the function, deferring the import until it's actually needed
def func_a():
    import module_b            # deferred import — module_b is fully loaded by the time this actually runs
    return module_b.func_b()
```

---

## 🚀 Real World Applications

- Organizing a real project's code into logical modules and packages, mirroring the same structural benefits Java's [[Packages and Imports|package system]] provides
- Understanding why module-level state (a cache, a loaded configuration) is shared across every part of a program that imports that module, since the module only ever runs once
- Diagnosing and resolving circular import errors by restructuring dependencies or deferring an import inside a function
- Choosing between `import module` (namespaced, always clear where a name came from) and `from module import name` (concise, but can obscure a name's origin) based on the situation

---

## ⚖️ Advantages

- Every file being automatically a module, with no special declaration syntax, keeps the mental model simple and consistent.
- Module caching via `sys.modules` means a module's expensive setup work (loading a large dataset, establishing a connection) only ever happens once, no matter how many other files import it.
- The package system provides a clean, hierarchical way to organize a large codebase into logically related groups of modules.

---

## ⚠️ Limitations

- Circular imports are a genuinely common and sometimes confusing problem in larger codebases, often requiring a restructuring of dependencies (or a deferred, function-local import) to resolve.
- `from module import *` pollutes the importing file's namespace with every public name from the source module, making it unclear where any given name actually originated — strongly discouraged outside of very specific, deliberate cases.
- A module's top-level code running immediately (and only once) on first import means expensive or side-effecting code accidentally placed at module level (rather than inside a function) runs unexpectedly early, and only that one time.

---

## 🚨 Common Mistakes

- Using `from module import *` out of convenience, making it unclear later which module a given name actually came from, and risking silent name collisions between different imported modules.
- Writing circular imports (two modules importing each other directly) without realizing Python's import caching can produce a partially-initialized module in the circular chain, causing confusing `AttributeError`s or `ImportError`s.
- Placing expensive or side-effecting code directly at a module's top level (outside any function), not realizing it will run immediately and automatically the very first time that module is imported anywhere.
- Assuming re-importing a module (`import module_name` a second time, elsewhere) re-runs its code — it doesn't; the cached result from the first import is reused.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 6, "Modules"
- Python documentation: "The import system" (the full technical reference)

---

## 💡 Wisdom from Mímir

Whenever an imported module seems to be missing something it should obviously have, check for a circular import before assuming anything else is wrong — Python's caching of partially-loaded modules during a circular dependency chain produces exactly this kind of confusing, "it should be there but isn't yet" symptom.

---

## 🔗 Related Notes

- [[Virtual Environments and pip]]
- [[The if __name__ == __main__ Idiom]]
- [[Python Codex]]
