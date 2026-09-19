---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - standard-library
  - file-io
  - pathlib
aliases:
  - open() Function Python
  - pathlib vs os.path
publish: true
permalink: python/working-with-files-in-python
---

# <span class="rune">ᛟ</span> Working with Files in Python

> *`open()` without `with` is a file handle you're personally responsible for closing — and the exact moment you forget, on whatever code path throws an exception first, is the exact moment that responsibility quietly becomes a leak.*

---

## 🎯 Purpose

Python's built-in `open()` function is the standard way to read and write files, almost always paired with a [[Context Managers and the with Statement|`with` statement]] to guarantee the file is closed properly. The modern `pathlib` module (Python 3.4+) provides an object-oriented alternative to the older `os.path` string-based approach for working with filesystem paths, and is now generally the preferred, more readable choice.

---

## 🧠 Key Ideas

- `open(path, mode)` returns a file object — common modes: `'r'` (read, default), `'w'` (write, **overwrites** the entire file), `'a'` (append), `'rb'`/`'wb'` (binary mode).
- Always pair `open()` with `with` — this guarantees the file is closed automatically, even if an exception occurs while reading or writing, exactly the pattern covered generally in [[Context Managers and the with Statement]].
- `'w'` mode **truncates** the file immediately upon opening — even before any writing happens, the previous content is already gone; `'a'` mode is the safe choice when appending without destroying existing content.
- `pathlib.Path` represents a filesystem path as an object with intuitive methods and operators (`path / "subfolder" / "file.txt"` for joining paths, `.exists()`, `.is_file()`, `.read_text()`) — the modern, idiomatic replacement for `os.path`'s string-manipulation-based functions.
- Reading a file line by line (`for line in f:`) is memory-efficient for large files, since it reads one line at a time rather than loading the entire file into memory at once — unlike `f.read()`, which loads everything immediately.

---

## ⚙️ How It Works

`with open(path, mode) as f:` calls `__enter__` on the returned file object (which simply returns the file object itself), and guarantees `__exit__` — which closes the file — runs when the block ends, regardless of whether it ended normally or via an exception. This is precisely the context-manager mechanism from [[Context Managers and the with Statement]], applied to the single most common real-world use case in the entire language: files must always eventually be closed to release the underlying OS file handle, and `with` makes that guarantee automatic rather than something to remember manually every time.

```text
with open("data.txt", "w") as f:    # 'w' mode ALREADY truncated the file the instant open() ran
    f.write("Hello")
# f.close() called AUTOMATICALLY here, even if .write() had raised an exception
```

---

## 💻 Examples

```python
# Reading an entire file
with open("data.txt", "r") as f:
    content = f.read()          # loads the WHOLE file into memory as one string

# Reading line by line — memory-efficient for large files
with open("data.txt", "r") as f:
    for line in f:                 # ONE line in memory at a time
        print(line.strip())          # .strip() removes the trailing newline

# Reading all lines into a list
with open("data.txt", "r") as f:
    lines = f.readlines()            # a list of strings, one per line, newlines INCLUDED

# Writing — 'w' OVERWRITES the entire file immediately
with open("output.txt", "w") as f:
    f.write("Hello, World!\n")
    f.write("Second line\n")

# Appending — safely adds to the end, without destroying existing content
with open("log.txt", "a") as f:
    f.write("New log entry\n")

# The modern, object-oriented pathlib approach
from pathlib import Path

path = Path("data") / "reports" / "2026.txt"    # cross-platform path joining, no manual "/" or "\\" needed
print(path.exists())        # True/False
print(path.is_file())         # True/False
print(path.suffix)              # ".txt"
print(path.stem)                 # "2026"

content = path.read_text()        # reads the WHOLE file — shorthand for open()+read()+close()
path.write_text("New content")     # writes the WHOLE file — shorthand for open()+write()+close()

for file in Path("data").glob("*.txt"):    # find all .txt files in a directory
    print(file)

# The classic mistake — 'w' truncates IMMEDIATELY, even before any writing happens
with open("important.txt", "w") as f:   # existing content is ALREADY GONE the instant this line runs
    pass
```

---

## 🚀 Real World Applications

- Reading configuration files, log files, or data files line by line without loading enormous files entirely into memory
- Using `pathlib` for cross-platform-safe path construction, avoiding manual string concatenation with OS-specific separators
- Appending to a log file safely over the lifetime of a running program, without risking accidental data loss from `'w'` mode
- Checking file/directory existence and metadata (`Path.exists()`, `Path.stat()`) before attempting to read or write

---

## ⚖️ Advantages

- `with` guarantees file handles are always properly closed, eliminating an entire category of resource leaks from forgetting a manual `.close()`.
- `pathlib`'s object-oriented, operator-based path construction (`/` for joining) is dramatically more readable than manual string concatenation with `os.path.join()`.
- Line-by-line file iteration provides genuine memory efficiency for processing very large files.

---

## ⚠️ Limitations

- `'w'` mode's immediate truncation behavior is a real, easy-to-trigger data-loss risk if the intended mode was actually `'a'` (append) or `'r'` (read).
- `pathlib` and the older `os.path` module coexist in most real codebases — recognizing both styles remains necessary even though `pathlib` is generally preferred for new code.
- Binary mode (`'rb'`/`'wb'`) is required for non-text files (images, executables) — using text mode on binary data produces encoding errors or corrupted data.

---

## 🚨 Common Mistakes

- Opening a file with `'w'` mode intending to append, and immediately, silently destroying the file's existing content the moment `open()` runs — before a single `.write()` call has even happened.
- Forgetting `with`, opening a file manually and relying on a later explicit `.close()` call that might never be reached if an exception occurs first.
- Using `.read()` on a genuinely huge file, loading the entire thing into memory at once, when line-by-line iteration (or reading in fixed-size chunks) would have been far more memory-efficient.
- Mixing `os.path` string-based path construction with `pathlib.Path` objects inconsistently across the same codebase, when standardizing on `pathlib` for new code is generally clearer.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 7.2, "Reading and Writing Files"
- Python documentation: `pathlib` — Object-oriented filesystem paths

---

## 💡 Wisdom from Mímir

Before opening any existing file with `'w'` mode, pause and ask whether `'a'` (append) was actually intended — `'w'` truncates the instant `open()` runs, with no confirmation and no undo, and this is one of the most common, entirely preventable ways to accidentally destroy real data in a Python script.

---

## 🔗 Related Notes

- [[Context Managers and the with Statement]]
- [[Working with JSON in Python]]
- [[Python Codex]]
