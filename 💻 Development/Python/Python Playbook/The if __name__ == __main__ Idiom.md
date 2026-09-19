---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - modules
  - main-idiom
  - script-vs-module
aliases:
  - "if __name__ == \"__main__\":"
  - Python Entry Point
publish: true
permalink: python/the-if-name-main-idiom
---

# <span class="rune">ᛟ</span> The if __name__ == "__main__" Idiom

> *Every Python file has a hidden variable called `__name__`, and its value depends entirely on how the file was reached — run directly, it's `"__main__"`; imported by something else, it's the module's own name. That one difference is the entire idiom.*

---

## 🎯 Purpose

Python automatically sets a special variable, `__name__`, in every module — its value is `"__main__"` if the file was run directly as a script, or the module's actual name if it was imported by another file. The `if __name__ == "__main__":` idiom uses this to let a file serve **both** as a reusable, importable module and as a standalone, runnable script, with entry-point code that only executes in the second case.

---

## 🧠 Key Ideas

- `__name__` is automatically set by Python for every module — its value depends entirely on *how the file is being executed*, not anything about the file's own content.
- When a file is run directly (`python script.py`), Python sets `__name__` to the literal string `"__main__"` inside that file.
- When the same file is instead *imported* by another module (`import script`), Python sets `__name__` to the module's actual name (`"script"`) inside it — never `"__main__"`.
- `if __name__ == "__main__":` therefore guards code that should run **only when the file is executed directly**, not when it's merely imported for its functions/classes — this is the standard, idiomatic way to give a Python file a proper "entry point," conceptually similar to Java's `public static void main(String[] args)`.
- This idiom is what allows a single file to be both a reusable library (its functions/classes importable elsewhere without side effects) and a standalone, runnable script (with its own demo/CLI/test code) at the same time.

---

## ⚙️ How It Works

Every module-level statement in a `.py` file runs the moment that file is executed, regardless of whether it was run directly or imported — this includes any "demo" or "main" code left unguarded at the top level. Wrapping that entry-point code in `if __name__ == "__main__":` means it only actually executes when the check evaluates true, which happens exclusively when the file itself was the one directly invoked by `python filename.py` — an import from elsewhere sets `__name__` to something else entirely, so the guarded block is simply skipped, leaving only the module's function/class *definitions* to actually run (which is exactly the desired behavior when importing a module purely for reuse).

```text
# calculator.py
def add(a, b):
    return a + b

print("This always runs, on import OR direct execution!")   # NOT guarded — runs every time

if __name__ == "__main__":
    print("This ONLY runs when calculator.py is executed directly")
    print(add(2, 3))
```

```text
python calculator.py
# "This always runs..."
# "This ONLY runs when calculator.py is executed directly"
# 5

# vs., from a DIFFERENT file:
import calculator
# "This always runs..."     ← printed (module-level code always executes on import)
# (the guarded block is SKIPPED — __name__ is "calculator" here, not "__main__")
```

---

## 💻 Examples

```python
# math_utils.py — a reusable module WITH its own runnable demo/test code
def add(a, b):
    return a + b

def multiply(a, b):
    return a * b

def main():
    print("Testing math_utils functions:")
    print(f"add(2, 3) = {add(2, 3)}")
    print(f"multiply(4, 5) = {multiply(4, 5)}")

if __name__ == "__main__":
    main()          # ONLY runs if math_utils.py is executed directly

# Running it directly:
# $ python math_utils.py
# Testing math_utils functions:
# add(2, 3) = 5
# multiply(4, 5) = 20

# Importing it elsewhere — the demo code does NOT run
# other_file.py
from math_utils import add
result = add(10, 20)   # works fine — main() never executed, no unwanted console output

# A common real-world pattern — a script that's ALSO a CLI tool
import sys

def process(filename):
    print(f"Processing {filename}")

if __name__ == "__main__":
    if len(sys.argv) < 2:
        print("Usage: python script.py <filename>")
    else:
        process(sys.argv[1])

# Checking __name__ directly, for illustration
print(__name__)   # "__main__" if run directly; the module's own name if imported
```

---

## 🚀 Real World Applications

- Writing a module that's both importable (for its functions/classes) and directly runnable (for a demo, a CLI, or a quick manual test) without the two purposes interfering
- Building command-line tools where the argument-parsing and execution logic should only run when the script is invoked directly, not when its helper functions are imported elsewhere
- Providing lightweight, ad-hoc "does this work" test/demo code inside a module without it accidentally running every time the module is imported by something else
- Following convention when reading unfamiliar Python codebases — nearly every runnable Python script uses this exact idiom as its entry point

---

## ⚖️ Advantages

- Allows a single file to cleanly serve both as a reusable library and a standalone executable script, without any conflict between the two roles.
- Prevents demo, testing, or CLI-entry-point code from unexpectedly running as a side effect whenever the file is merely imported for its actual functionality elsewhere.
- Extremely lightweight — a single `if` statement, using a variable Python already provides automatically, with no additional setup or boilerplate required.

---

## ⚠️ Limitations

- The idiom itself doesn't prevent *all* module-level code from running on import — only code specifically placed inside the `if __name__ == "__main__":` block is guarded; anything else at module level still executes on both import and direct execution.
- Forgetting the idiom entirely on a script intended to also be imported means any "demo"/entry-point code runs unconditionally every single time the file is imported, which is rarely the intended behavior.
- The idiom is Python-specific — developers coming from languages with an explicit, separate `main()` entry point construct (like Java) sometimes find the "it's just a variable check" mechanism initially unintuitive.

---

## 🚨 Common Mistakes

- Placing script-only logic (demo code, CLI argument handling) directly at module level without the `if __name__ == "__main__":` guard, causing it to run unexpectedly every time the file is imported elsewhere.
- Assuming `__name__` is somehow related to the file's actual filename — it's specifically about *how the file was invoked* (`"__main__"` when run directly, the module's dotted name when imported), not a literal reflection of the filename on disk.
- Forgetting this idiom entirely and being confused when a module "printing stuff" or "running its demo" every time it's imported, when the intent was for that to only happen on direct execution.
- Writing all of a script's actual logic directly inside the `if __name__ == "__main__":` block instead of in properly named, separately-testable functions (like the `main()` example above) that the guard simply calls.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 6.1.1, "Executing modules as scripts"

---

## 💡 Wisdom from Mímir

Treat `if __name__ == "__main__":` as the default, near-automatic bottom of every standalone Python script you write, the same reflexive habit as `@Override` in Java — it costs one line, and it's the difference between a file that behaves predictably whether run directly or imported, and one that surprises whoever imports it later with unexpected console output or side effects.

---

## 🔗 Related Notes

- [[Modules and the import System]]
- [[Python Codex]]
