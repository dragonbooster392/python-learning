# 01 — What Is Python & Installation

---

## What Is Python?

Python is a **general-purpose scripting language**. Like bash, you write text files with instructions and the computer executes them. But Python is designed for building larger tools, working with data, and talking to APIs — things that get messy in shell.

### Python vs Shell — When to Use What

| Situation | Use Shell | Use Python |
|-----------|-----------|------------|
| Run a few system commands | ✅ | ❌ Overkill |
| Pipe commands together (`grep \| awk \| sort`) | ✅ | ❌ |
| Quick one-liners | ✅ | ❌ |
| Parse JSON/YAML data | ❌ Painful | ✅ |
| Talk to REST APIs | ❌ `curl` gets ugly | ✅ `requests` library |
| Build a CLI tool with options/flags | ❌ `getopts` is limited | ✅ `click` library |
| Script longer than ~100 lines | ❌ Gets unreadable | ✅ |
| Error handling | ❌ Exit codes only | ✅ try/except |
| Works on all platforms | ❌ Bash-only | ✅ Linux, Mac, Windows |

### What Python Is NOT

- **Not compiled.** No build step. You write code, you run it. Just like shell scripts.
- **Not only for web.** Python is used for CLIs, DevOps, automation, data processing, AI, system tools — everything.
- **Not a shell replacement.** Shell is still better for quick one-liners and command piping. Use the right tool for the job.
- **Not slow for scripting purposes.** It's slower than C/Go for heavy computation, but for CLI tools, API calls, file processing — it's fast enough.

---

## How Python Runs Your Code

### Interpreted, Line by Line

When you run `python3 myscript.py`, Python reads your file **top to bottom**, executing each line immediately. No compile step.

```
You write:  myscript.py       (a text file with .py extension)
You run:    python3 myscript.py
Python:     reads line 1 → executes it
            reads line 2 → executes it
            reads line 3 → executes it
            ... until the file ends or an error occurs
```

This is exactly how `bash myscript.sh` works.

### What Happens Under the Hood

1. Python reads your `.py` file (source code — text)
2. Converts it to **bytecode** (a compact internal format — you'll see `.pyc` files)
3. Runs the bytecode in the **Python interpreter** (the `python3` program)

You don't need to think about steps 2-3. Just know: you write `.py`, you run `python3 file.py`.

---

## Installing Python

### Check if Python Is Installed

```bash
python3 --version       # should show Python 3.x.x
which python3           # shows the path: /usr/bin/python3
```

### Install on Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install python3 python3-pip python3-venv
```

### Install on Mac

```bash
brew install python3
```

### Install on RHEL/CentOS

```bash
sudo yum install python3 python3-pip
```

### Python 2 vs Python 3

- **Always use Python 3.** Python 2 is dead (end of life since 2020).
- The command is `python3` (not `python` which might point to Python 2 on older systems).
- On newer systems, `python` and `python3` are the same thing.

---

## Running Python Code

### Run a Script File

```bash
python3 myscript.py                    # run a .py file
python3 myscript.py arg1 arg2          # run with arguments
```

### Run a One-Liner

```bash
python3 -c "print('Hello')"           # like bash -c "echo Hello"
```

### Interactive Mode (REPL)

```bash
python3                                # opens interactive shell
>>> print("Hello")                     # type commands, see results immediately
>>> 2 + 3
5
>>> exit()                             # quit
```

The `>>>` prompt is Python's interactive mode. Great for quick testing. Like running `bash` with no script.

### Run as a Module

```bash
python3 -m json.tool data.json        # run the json.tool module (pretty-print JSON)
python3 -m venv myenv                  # run the venv module (create virtual environment)
python3 -m pip install click           # run pip as a module
```

### Shebang Line

You can make a `.py` file directly executable (like a shell script):

```python
#!/usr/bin/env python3
print("I can run without typing python3!")
```

```bash
chmod +x myscript.py
./myscript.py                          # runs with python3 automatically
```

---

## The #1 Rule: Indentation

This is the **most critical thing** to understand about Python. There are no `{ }` braces, no `fi`, no `done`, no `end`. Python uses **indentation** (spaces) to know what belongs inside what.

### Rules

- Use **4 spaces** per indentation level (not tabs, not 2 spaces — 4 is the standard).
- Everything at the same indent level belongs to the same block.
- When you see a **colon (`:`)** at the end of a line, the next line must be indented.
- When the indentation goes back, the block ends.

### Example — Shell vs Python

**Shell:**
```bash
if [ "$state" = "Running" ]; then
    echo "Running"
    echo "All good"
fi
echo "Done"
```

**Python:**
```python
if state == "Running":
    print("Running")          # inside the if (4 spaces)
    print("All good")         # still inside (same 4 spaces)
print("Done")                 # outside the if (back to 0 spaces)
```

The if-block ends when the indentation goes back to the previous level. No `fi` needed.

### Nested Indentation

```python
for cluster in clusters:              # level 0 → level 1
    if cluster["state"] == "Running": # level 1 → level 2
        print("healthy")             # level 2
    else:                             # back to level 1
        print("unhealthy")           # level 1 → level 2
print("done")                        # back to level 0
```

### Wrong Indentation = Crash

```python
if x > 5:
print("big")      # IndentationError! Must be indented after :
```

```python
if x > 5:
    print("big")
  print("number")   # IndentationError! Inconsistent indentation (2 spaces vs 4)
```

Python will refuse to run your code if indentation is wrong. This is actually a **good thing** — it forces readable code.

---

## File Extension and Structure

### .py Extension

Python files use `.py`. That's the only convention. No `.sh` equivalent — just `.py`.

### Basic File Structure

A well-organized Python file follows this pattern:

```python
#!/usr/bin/env python3           # 1. Shebang (optional)
"""Module docstring."""           # 2. Module description (optional)

import os                         # 3. Imports (standard library first)
import sys
from datetime import datetime

import click                      # 4. Imports (third-party packages)
import requests

from .config import Config        # 5. Imports (your own files)
from .client import PaletteClient

TIMEOUT = 30                      # 6. Constants (UPPERCASE by convention)
DEFAULT_ENV = "production"

class MyClient:                   # 7. Classes
    ...

def helper_function():            # 8. Functions
    ...

if __name__ == "__main__":        # 9. Entry point (explained in later section)
    main()
```

### Comments

```python
# This is a comment — Python ignores everything after #
# Exactly like shell comments

print("code")  # inline comment — code runs, comment is ignored

"""
This is a multi-line comment (technically a docstring).
Use it at the top of files, classes, or functions
to describe what they do.
"""
```

---

## Virtual Environments

### What Is a Virtual Environment?

A virtual environment is an **isolated Python installation** just for one project. It has its own packages, separate from the system Python.

### Why Do You Need It?

- Project A needs `requests==2.28`, Project B needs `requests==2.31`
- Without venv, they'd fight over the system-wide version
- With venv, each project has its own copy

### Shell Analogy

Think of it as a separate `PATH` + `lib/` for each project. Like having project-specific tool versions.

### Commands

```bash
# Create
python3 -m venv myenv              # creates a myenv/ folder with isolated Python

# Activate (your shell prompt changes to show the venv)
source myenv/bin/activate           # Linux/Mac
# Now pip install only installs in this environment

# Use
pip install click requests rich     # installed only inside myenv/

# Deactivate (go back to system Python)
deactivate

# Delete (it's just a folder)
rm -rf myenv
```

### Pipenv (What spectro-cli Uses)

spectro-cli uses **Pipenv** — a higher-level tool that manages virtual environments automatically:

```bash
pip install pipenv                  # install pipenv itself
pipenv install                      # reads Pipfile, creates venv, installs packages
pipenv shell                        # activate the venv
pipenv install click                # add a package to Pipfile + install it
```

The `Pipfile` is like `requirements.txt` but better organized.

### requirements.txt

The traditional way to list project dependencies:

```
click>=8.0
requests>=2.28
rich>=13.0
pyyaml
python-dotenv
```

```bash
pip install -r requirements.txt     # install everything listed
pip freeze > requirements.txt       # save current packages to file
```

---

## Summary

| Concept | Key Point |
|---------|-----------|
| Python | Interpreted scripting language, runs top-to-bottom |
| Indentation | 4 spaces = one level, defines all code blocks |
| File extension | `.py` |
| Run script | `python3 file.py` |
| Version | Always use Python 3 (not 2) |
| Virtual env | Isolated Python per project (`python3 -m venv name`) |
| pip | Package installer (`pip install packagename`) |

---

> **Next:** [02 - Variables](02-variables.md)
