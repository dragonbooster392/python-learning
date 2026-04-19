# 09 — Modules, Imports, and Packages

How to use other people's code, organize your own, and install third-party libraries.

---

## Table of Contents

- [What Is a Module?](#what-is-a-module)
- [Import Styles](#import-styles)
- [How Python Finds Modules](#how-python-finds-modules)
- [__init__.py — Making Folders Importable](#initpy-making-folders-importable)
- [Standard Library — What Comes Built-in](#standard-library-what-comes-built-in)
- [Essential Standard Library Modules](#essential-standard-library-modules)
- [pip — Installing Third-Party Packages](#pip-installing-third-party-packages)
- [requirements.txt and setup.py](#requirementstxt-and-setuppy)
- [Relative vs Absolute Imports](#relative-vs-absolute-imports)
- [The __name__ == "__main__" Pattern](#the-name-main-pattern)
- [Common Import Patterns](#common-import-patterns)
- [Shell vs Python Imports](#shell-vs-python-imports)

---

## What Is a Module?

A **module** is just a `.py` file. When you import it, you can use its functions, classes, and variables.

```
utils.py          ← this is a module called "utils"
config.py         ← this is a module called "config"
client.py         ← this is a module called "client"
```

A **package** is a folder with an `__init__.py` file — it's a collection of modules.

```
spectro_cli/              ← this is a package
    __init__.py
    cli.py                ← module: spectro_cli.cli
    client.py             ← module: spectro_cli.client
    commands/             ← this is a sub-package
        __init__.py
        clusters.py       ← module: spectro_cli.commands.clusters
```

---

## Import Styles

### Import Entire Module

```python
import os
import sys
import json

# Usage: module.function()
os.path.exists("/tmp/file")
sys.exit(1)
json.dumps(data)
```

### Import Specific Items

```python
from os.path import exists, join
from sys import argv, exit
from json import loads, dumps

# Usage: directly (no module prefix)
exists("/tmp/file")
exit(1)
dumps(data)
```

### Import with Alias

```python
import datetime as dt
import numpy as np                  # common convention

# Usage: alias.function()
dt.datetime.now()
```

### Import Specific Item with Alias

```python
from datetime import datetime as dt
from os.path import exists as file_exists

dt.now()
file_exists("/tmp/file")
```

### Import Everything (Don't Do This)

```python
from os import *                    # imports EVERYTHING — pollutes namespace
# BAD: you don't know what was imported
# BAD: names might conflict with your variables
```

### Which Style to Use?

| Style | When to Use |
|-------|-------------|
| `import module` | Default — clear where each name comes from |
| `from module import thing` | When you use `thing` many times |
| `import module as alias` | When module name is long or conventional (np, pd, dt) |
| `from module import *` | Almost never — only in quick scripts |

---

## How Python Finds Modules

When you write `import something`, Python searches in this order:

1. **Current directory** (same folder as your script)
2. **PYTHONPATH** environment variable directories
3. **Standard library** (built-in modules)
4. **Site-packages** (pip-installed packages)

### Check the Search Path

```python
import sys
print(sys.path)                    # list of directories Python searches
```

### Common Import Errors

```python
# ModuleNotFoundError — module doesn't exist or isn't installed:
import nonexistent                  # ModuleNotFoundError
# Fix: pip install the package, or check the name

# ImportError — module exists but item doesn't:
from os import nonexistent          # ImportError
# Fix: check the correct name

# Circular import — two files import each other:
# file_a.py imports file_b, and file_b imports file_a
# Fix: restructure code, move shared code to a third file
```

---

## __init__.py — Making Folders Importable

A file named `__init__.py` in a folder tells Python: "This folder is a package."

### Without __init__.py

```
mytools/
    utils.py
```
```python
from mytools import utils           # ImportError! mytools is not a package
```

### With __init__.py

```
mytools/
    __init__.py                     # can be empty!
    utils.py
```
```python
from mytools import utils           # works!
from mytools.utils import helper    # works!
```

### What Goes in __init__.py

```python
# Option 1: Empty (most common) — just marks the folder as a package
# (empty file)

# Option 2: Re-export for convenience
from .cli import main
from .client import PaletteClient
from .config import Config
# Now users can do: from spectro_cli import PaletteClient
# Instead of: from spectro_cli.client import PaletteClient

# Option 3: Package-level constants
__version__ = "1.0.0"
__author__ = "Your Name"
```

### spectro-cli's __init__.py

```python
# src/spectro_cli/__init__.py
__version__ = "1.0.1"

# src/spectro_cli/commands/__init__.py
from . import clusters, profiles, pools, validate, workloads, ci, cd
# This makes all command modules importable from the commands package
```

---

## Standard Library — What Comes Built-in

Python includes **hundreds** of modules — no installation needed. Here are the most useful for scripting:

### File System

| Module | Purpose | Key Functions |
|--------|---------|---------------|
| `os` | OS interaction | `os.getenv()`, `os.getcwd()`, `os.listdir()`, `os.makedirs()` |
| `os.path` | Path manipulation | `os.path.exists()`, `os.path.join()`, `os.path.dirname()` |
| `pathlib` | Modern path handling | `Path("/tmp") / "file.txt"`, `.exists()`, `.read_text()` |
| `shutil` | File/dir operations | `shutil.copy()`, `shutil.rmtree()`, `shutil.move()` |
| `glob` | Pattern matching | `glob.glob("*.txt")`, `glob.glob("**/*.py", recursive=True)` |
| `tempfile` | Temporary files | `tempfile.mktemp()`, `tempfile.TemporaryDirectory()` |

### Data Formats

| Module | Purpose | Key Functions |
|--------|---------|---------------|
| `json` | JSON parsing | `json.loads()`, `json.dumps()`, `json.load()`, `json.dump()` |
| `csv` | CSV files | `csv.reader()`, `csv.writer()`, `csv.DictReader()` |
| `configparser` | INI-style configs | `config.read()`, `config.get()` |

### System and Process

| Module | Purpose | Key Functions |
|--------|---------|---------------|
| `sys` | System interface | `sys.argv`, `sys.exit()`, `sys.stdin`, `sys.stderr` |
| `subprocess` | Run external commands | `subprocess.run()`, `subprocess.check_output()` |
| `argparse` | CLI argument parsing | `ArgumentParser()`, `.add_argument()` |
| `shlex` | Shell-like parsing | `shlex.split("cmd --flag value")` |

### Text and Patterns

| Module | Purpose | Key Functions |
|--------|---------|---------------|
| `re` | Regular expressions | `re.search()`, `re.findall()`, `re.sub()`, `re.match()` |
| `string` | String constants | `string.ascii_letters`, `string.digits` |
| `textwrap` | Text formatting | `textwrap.dedent()`, `textwrap.wrap()` |

### Date and Time

| Module | Purpose | Key Functions |
|--------|---------|---------------|
| `datetime` | Date/time | `datetime.now()`, `datetime.strftime()`, `timedelta()` |
| `time` | Time utilities | `time.sleep()`, `time.time()` |

### Networking

| Module | Purpose | Key Functions |
|--------|---------|---------------|
| `urllib` | URL handling | `urllib.parse.urljoin()`, `urllib.parse.quote()` |
| `http` | HTTP constants | `http.HTTPStatus.OK` |
| `socket` | Low-level networking | `socket.gethostname()` |

### Other Useful Modules

| Module | Purpose | Key Functions |
|--------|---------|---------------|
| `collections` | Special containers | `defaultdict`, `Counter`, `OrderedDict`, `namedtuple` |
| `itertools` | Iteration tools | `chain()`, `product()`, `groupby()` |
| `functools` | Function tools | `partial()`, `lru_cache`, `reduce()` |
| `hashlib` | Hashing | `hashlib.md5()`, `hashlib.sha256()` |
| `base64` | Encoding | `base64.b64encode()`, `base64.b64decode()` |
| `logging` | Structured logging | `logging.info()`, `logging.error()` |
| `copy` | Object copying | `copy.copy()`, `copy.deepcopy()` |
| `pprint` | Pretty printing | `pprint.pprint(data)` |
| `uuid` | Unique IDs | `uuid.uuid4()` |
| `random` | Random numbers | `random.randint()`, `random.choice()` |
| `math` | Math functions | `math.ceil()`, `math.floor()`, `math.sqrt()` |

---

## Essential Standard Library Modules

### os — Operating System Interface

```python
import os

# Environment variables:
os.getenv("HOME")                          # get env var (returns None if missing)
os.getenv("PORT", "8080")                 # with default
os.environ["MY_VAR"] = "value"            # set env var

# Current directory:
os.getcwd()                                # /current/working/directory

# List files:
os.listdir("/tmp")                         # ['file1.txt', 'file2.log']

# Create directories:
os.makedirs("/tmp/a/b/c", exist_ok=True)  # create nested dirs (no error if exists)

# Check paths:
os.path.exists("/tmp/file")               # True/False
os.path.isfile("/tmp/file")               # is it a file?
os.path.isdir("/tmp/dir")                 # is it a directory?
os.path.join("/tmp", "sub", "file.txt")   # "/tmp/sub/file.txt" (OS-safe)
os.path.dirname("/tmp/sub/file.txt")      # "/tmp/sub"
os.path.basename("/tmp/sub/file.txt")     # "file.txt"
os.path.splitext("file.tar.gz")           # ("file.tar", ".gz")
```

### sys — System-Specific

```python
import sys

sys.argv                                   # command-line arguments list
sys.argv[0]                                # script name
sys.argv[1:]                               # arguments (without script name)
sys.exit(0)                                # exit with code 0 (success)
sys.exit(1)                                # exit with code 1 (failure)
sys.exit("Error message")                  # exit with message to stderr
sys.stdin                                  # standard input
sys.stdout                                 # standard output
sys.stderr                                 # standard error
sys.version                                # Python version string
sys.platform                               # "linux", "darwin", "win32"
```

### json — JSON Data

```python
import json

# Parse JSON string → Python dict/list:
data = json.loads('{"name": "Alice", "age": 30}')    # dict

# Convert Python → JSON string:
text = json.dumps(data)                               # compact
text = json.dumps(data, indent=2)                      # pretty-printed

# Read JSON file:
with open("data.json") as f:
    data = json.load(f)

# Write JSON file:
with open("data.json", "w") as f:
    json.dump(data, f, indent=2)
```

### subprocess — Run External Commands

```python
import subprocess

# Run a command and wait:
result = subprocess.run(["ls", "-la"], capture_output=True, text=True)
print(result.stdout)                       # command output
print(result.returncode)                   # exit code (0 = success)

# Run and get output directly:
output = subprocess.check_output(["hostname"], text=True).strip()

# Run with shell (less safe, avoid if possible):
result = subprocess.run("ls -la | grep .py", shell=True, capture_output=True, text=True)

# Check if command succeeded:
result = subprocess.run(["test", "-f", "/etc/hosts"])
if result.returncode == 0:
    print("File exists")
```

### re — Regular Expressions

```python
import re

text = "Server web-01 at 10.0.0.1 port 8080"

# Search for pattern:
match = re.search(r"\d+\.\d+\.\d+\.\d+", text)
if match:
    print(match.group())                   # "10.0.0.1"

# Find all matches:
numbers = re.findall(r"\d+", text)         # ["01", "10", "0", "0", "1", "8080"]

# Replace:
clean = re.sub(r"\d+", "X", text)         # "Server web-X at X.X.X.X port X"

# Split:
parts = re.split(r"\s+", text)             # split by whitespace

# Match at start:
if re.match(r"Server", text):
    print("Starts with Server")
```

### datetime — Dates and Times

```python
from datetime import datetime, timedelta

# Current time:
now = datetime.now()
print(now)                                 # 2026-03-31 15:30:00.123456

# Format as string:
now.strftime("%Y-%m-%d")                  # "2026-03-31"
now.strftime("%Y-%m-%d %H:%M:%S")        # "2026-03-31 15:30:00"
now.isoformat()                            # "2026-03-31T15:30:00.123456"

# Parse string to datetime:
dt = datetime.strptime("2026-03-31", "%Y-%m-%d")

# Math with dates:
tomorrow = now + timedelta(days=1)
last_week = now - timedelta(weeks=1)
diff = datetime(2026, 12, 31) - now       # timedelta object
print(diff.days)                           # days until end of year
```

---

## pip — Installing Third-Party Packages

`pip` is Python's package manager. Packages come from **PyPI** (pypi.org).

### Commands

```bash
pip install click                         # install latest version
pip install click==8.1.0                  # install specific version
pip install click>=8.0,<9.0              # install version range
pip install -r requirements.txt           # install from file
pip install -e .                          # install current project in dev mode
pip install --upgrade click               # upgrade to latest
pip uninstall click                       # remove
pip list                                  # show installed packages
pip show click                            # show package details
pip freeze                                # list installed with versions
pip freeze > requirements.txt             # save to file
```

### Where Packages Come From

1. **PyPI** (pypi.org) — the default public repository
2. **Private repositories** — companies often have their own
3. **Git repositories** — `pip install git+https://github.com/user/repo.git`
4. **Local files** — `pip install ./package.tar.gz`

### Key Third-Party Packages for CLI Scripting

| Package | Purpose | Install |
|---------|---------|---------|
| `click` | CLI framework | `pip install click` |
| `rich` | Beautiful terminal output | `pip install rich` |
| `requests` | HTTP client | `pip install requests` |
| `pyyaml` | YAML parsing | `pip install pyyaml` |
| `python-dotenv` | .env file loading | `pip install python-dotenv` |
| `tenacity` | Retry logic | `pip install tenacity` |

---

## requirements.txt and setup.py

### requirements.txt

A plain text list of packages and versions:

```
click>=8.0
requests>=2.28
rich>=13.0
pyyaml
python-dotenv
tenacity
```

```bash
pip install -r requirements.txt            # install all
```

### setup.py (spectro-cli uses this)

Defines a Python project — its name, version, dependencies, and entry point:

```python
from setuptools import setup, find_packages

setup(
    name="spectro-cli",
    version="1.0.1",
    packages=find_packages(where="src"),
    package_dir={"": "src"},
    install_requires=[
        "click>=8.0",
        "requests",
        "rich",
    ],
    entry_points={
        "console_scripts": [
            "spectro=spectro_cli.cli:main",    # command → function
        ],
    },
)
```

Key parts:
- `install_requires` — packages to install automatically
- `entry_points.console_scripts` — creates a system command (`spectro`) that runs `main()` from `spectro_cli/cli.py`

### Install a Project

```bash
pip install -e .                           # install current project in editable mode
# Now "spectro" command is available system-wide
```

---

## Relative vs Absolute Imports

### Absolute Import

Full path from the top-level package:

```python
from spectro_cli.client import PaletteClient
from spectro_cli.config import Config
from spectro_cli.commands.clusters import list_clusters
```

### Relative Import (Dot Notation)

Path relative to the **current file's location**:

```python
# In spectro_cli/commands/clusters.py:
from ..client import PaletteClient       # .. = go up one level (spectro_cli/)
from ..config import Config              # .. = parent package
from . import pools                      # . = same package (commands/)
```

| Notation | Meaning |
|----------|---------|
| `.` | Current package (same folder) |
| `..` | Parent package (one level up) |
| `...` | Grandparent package (two levels up) |

### When to Use Which

| Style | Use When |
|-------|----------|
| Absolute | Default, always works, clearer |
| Relative | Inside a package, when paths are long |

**spectro-cli uses relative imports** within the package (you'll see `from ..client import PaletteClient` in command files).

---

## The __name__ == "__main__" Pattern

### What Is __name__?

Every Python file has a built-in variable `__name__`:

| How the file is used | `__name__` value |
|----------------------|-----------------|
| Run directly: `python3 file.py` | `"__main__"` |
| Imported: `from file import something` | `"file"` (the module name) |

### The Pattern

```python
def main():
    print("Running the tool!")

if __name__ == "__main__":
    main()
```

This means:
- If you **run** the file directly → `main()` executes
- If you **import** from the file → `main()` does NOT execute

### Why It Matters

Without this guard, ALL top-level code runs when the file is imported:

```python
# BAD — no guard
print("Starting...")            # this runs even when imported!
do_stuff()                      # this too!

# GOOD — with guard
def do_stuff():
    print("Working")

if __name__ == "__main__":
    print("Starting...")        # only runs when executed directly
    do_stuff()
```

### Shell Equivalent

```bash
# Closest shell equivalent:
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    main                        # only run if executed, not sourced
fi
```

---

## Common Import Patterns

### Import at Top of File

Convention: all imports go at the top of the file, in this order:

```python
# 1. Standard library
import os
import sys
import json
from datetime import datetime

# 2. Third-party packages
import click
import requests
from rich.console import Console

# 3. Local/project imports
from .config import Config
from .client import PaletteClient
```

### Conditional Import

```python
# Import that might not be installed:
try:
    import yaml
except ImportError:
    yaml = None

# Later:
if yaml:
    data = yaml.safe_load(content)
else:
    print("PyYAML not installed")
```

### Lazy Import (Inside Function)

```python
def export_csv():
    import csv                     # only imported when this function is called
    # ... use csv module
```

Used when:
- The import is slow
- The module is only needed in one function
- You want to avoid import errors for optional features

---

## Shell vs Python Imports

| Concept | Shell | Python |
|---------|-------|--------|
| Use another file's code | `source file.sh` | `import module` or `from module import thing` |
| What happens | Runs every line in the file | Runs the file once, makes names available |
| Scope | Everything becomes global | Module has its own namespace |
| Run external command | `command arg` | `subprocess.run(["command", "arg"])` |
| Install tool | `apt install tool` | `pip install package` |
| Package manager | `apt`, `yum`, `brew` | `pip` |
| Package repository | OS package repos | PyPI (pypi.org) |
| PATH for finding | `$PATH` | `sys.path` |
| Version file | — | `requirements.txt`, `setup.py`, `Pipfile` |

---

> **Practice:** Chapter `03-functions-modules.py` sections 7-9
> **Next:** [10 - File I/O](10-file-io.md)
