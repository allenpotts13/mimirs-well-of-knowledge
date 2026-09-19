---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - environment
  - pip
  - virtual-environments
aliases:
  - venv
  - requirements.txt
publish: true
permalink: python/virtual-environments-and-pip
---

# <span class="rune">ᛟ</span> Virtual Environments and pip

> *Without a virtual environment, every Python project on a machine is quietly fighting over the same single set of installed packages — a version bump for one project's dependency is a silent, unannounced breaking change for every other project sharing that same global install.*

---

## 🎯 Purpose

A **virtual environment** creates an isolated, self-contained Python installation (with its own separate set of installed packages) for a specific project — solving the "dependency conflict between projects" problem that arises the moment two projects on the same machine need different, incompatible versions of the same library. `pip` is Python's standard package installer, used to install, upgrade, and manage packages inside that isolated environment.

---

## 🧠 Key Ideas

- `python -m venv myenv` creates a new virtual environment in a folder called `myenv`, containing its own isolated Python interpreter and package directory, entirely separate from the system-wide Python installation.
- **Activating** a virtual environment (`source myenv/bin/activate` on macOS/Linux, `myenv\Scripts\activate` on Windows) changes the current shell session so that `python` and `pip` commands refer to *that environment's* isolated interpreter and packages, not the global system one.
- `pip install package_name` installs a package into whichever environment is currently active — installing without first activating a virtual environment installs into the global system Python, exactly the situation virtual environments exist to avoid.
- `pip freeze > requirements.txt` captures the exact currently-installed package versions into a file; `pip install -r requirements.txt` recreates that exact same set of dependencies elsewhere — the standard way to make a project's dependencies reproducible for another developer or a deployment environment.
- Each project should generally have its **own** virtual environment, kept out of version control (typically via `.gitignore`) — `requirements.txt` (the list of dependencies) is what gets committed, not the environment folder itself.

---

## ⚙️ How It Works

A virtual environment is, mechanically, just a self-contained directory containing a copy (or symlink) of the Python interpreter plus an empty (or selectively populated) `site-packages` directory for installed libraries — activating it modifies the current shell's `PATH` so that typing `python` or `pip` resolves to the versions inside that folder instead of the system-wide ones. This isolation means installing `requests==2.28` in one project's virtual environment has zero effect on any other project's environment (or the global system Python) — each virtual environment's installed packages are entirely independent of every other one.

```text
System Python (shared, global) ── installing here affects EVERY project using the system Python

Project A's venv/ ── isolated packages, ONLY affects Project A
Project B's venv/ ── isolated packages, ONLY affects Project B
                       (can hold entirely DIFFERENT versions of the SAME package, with zero conflict)
```

---

## 💻 Examples

```bash
# Create a virtual environment (run once, per project)
python -m venv venv

# Activate it — must be done EVERY time you start a new terminal session for this project
source venv/bin/activate       # macOS/Linux
venv\Scripts\activate            # Windows

# Confirm it's active — the prompt typically shows (venv) prefix, and this confirms it directly
which python                     # points INTO the venv folder, not the system Python

# Install packages — installed into the ACTIVE environment only
pip install requests
pip install flask==2.3.0          # pinning a specific version

# List what's installed
pip list

# Capture exact dependencies for reproducibility
pip freeze > requirements.txt

# requirements.txt might look like:
# requests==2.31.0
# flask==2.3.0

# Recreate the SAME environment elsewhere (a new machine, a CI pipeline, a teammate's setup)
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt    # installs the EXACT same versions

# Deactivate when done working in this project
deactivate
```

```gitignore
# .gitignore — never commit the environment folder itself
venv/
__pycache__/
*.pyc
```

---

## 🚀 Real World Applications

- Isolating each project's dependencies to avoid version conflicts between unrelated projects sharing the same machine
- Sharing `requirements.txt` with teammates or committing it to version control so anyone can recreate an identical, working environment
- Setting up a reproducible environment in a CI/CD pipeline or production deployment, matching exactly what was tested locally
- Experimenting with a new or updated package version in an isolated environment without risking breaking any other project on the same machine

---

## ⚖️ Advantages

- Completely eliminates the classic "it works on my machine" dependency-version mismatch problem between different projects.
- `requirements.txt` makes a project's exact dependency set fully reproducible for any other developer or deployment target.
- Keeping the system-wide Python installation clean of project-specific packages avoids a slow accumulation of unrelated, potentially conflicting libraries over time.

---

## ⚠️ Limitations

- Forgetting to activate the correct virtual environment before running `pip install` silently installs into the wrong location (often the global system Python), causing confusing "why isn't this package found" errors later.
- Virtual environments themselves are tied to a specific machine's file paths — they shouldn't be copied between machines or committed to version control; only `requirements.txt` should travel with the project.
- `pip freeze` captures every currently installed package, including transitive dependencies not directly requested — this can make `requirements.txt` larger and harder to read than a hand-curated list of only the project's direct dependencies.

---

## 🚨 Common Mistakes

- Installing packages globally (without an activated virtual environment) out of habit, gradually accumulating a cluttered, potentially conflicting global Python installation.
- Forgetting to activate the virtual environment in a new terminal session, then being confused why a package that was "definitely installed" isn't found — it was installed into a different, currently-inactive environment.
- Committing the virtual environment folder itself to version control instead of `.gitignore`-ing it and committing only `requirements.txt`.
- Not regenerating `requirements.txt` after installing new packages, leaving it out of sync with what the project actually needs to run correctly.

---

## 📖 Further Reading

- Python documentation: `venv` — Creation of virtual environments
- pip documentation: "Requirements Files"

---

## 💡 Wisdom from Mímir

Make activating the correct virtual environment the very first thing you check whenever a package "should be installed but isn't found" — it's one of the most common early-career Python confusions, and it's almost always a currently-inactive (or entirely different) environment, not a genuinely missing package.

---

## 🔗 Related Notes

- [[Modules and the import System]]
- [[Python Codex]]
