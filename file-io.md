# 10 — File I/O

Reading, writing, and working with files — JSON, YAML, text, and CSV.

---

## Table of Contents

- [Opening Files](#opening-files)
- [Reading Files](#reading-files)
- [Writing Files](#writing-files)
- [Appending to Files](#appending-to-files)
- [The with Statement (Context Manager)](#the-with-statement-context-manager)
- [File Modes](#file-modes)
- [Working with JSON](#working-with-json)
- [Working with YAML](#working-with-yaml)
- [Working with CSV](#working-with-csv)
- [Working with .env Files](#working-with-env-files)
- [File Path Operations](#file-path-operations)
- [Checking if Files/Dirs Exist](#checking-if-filesdirs-exist)
- [Directory Operations](#directory-operations)
- [Temporary Files](#temporary-files)
- [Common Patterns](#common-patterns)
- [Shell vs Python File Operations](#shell-vs-python-file-operations)

---

## Opening Files

```python
# Always use 'with' — it closes the file automatically:
with open("file.txt", "r") as f:
    content = f.read()

# Without 'with' (DON'T — you must remember to close):
f = open("file.txt", "r")
content = f.read()
f.close()                          # forgetting this = resource leak!
```

---

## Reading Files

### Read Entire File as String

```python
with open("file.txt") as f:        # "r" mode is default
    content = f.read()
print(content)
```

### Read All Lines as List

```python
with open("file.txt") as f:
    lines = f.readlines()          # ['line1\n', 'line2\n', 'line3\n']

# Strip newlines:
with open("file.txt") as f:
    lines = [line.strip() for line in f.readlines()]
```

### Read Line by Line (Memory Efficient)

```python
with open("file.txt") as f:
    for line in f:                 # iterates line by line
        line = line.strip()        # remove \n
        print(line)
```

### Read First N Lines

```python
with open("file.txt") as f:
    for i, line in enumerate(f):
        if i >= 10:                # read only first 10 lines
            break
        print(line.strip())
```

### Read Specific Line

```python
with open("file.txt") as f:
    lines = f.readlines()
    fifth_line = lines[4]          # 0-indexed: line 5 is index 4
```

---

## Writing Files

### Write (Overwrites!)

```python
with open("output.txt", "w") as f:
    f.write("Hello World\n")
    f.write("Second line\n")

# WARNING: "w" mode DESTROYS existing content!
```

### Write Multiple Lines

```python
lines = ["line 1", "line 2", "line 3"]
with open("output.txt", "w") as f:
    for line in lines:
        f.write(line + "\n")

# Or:
with open("output.txt", "w") as f:
    f.writelines(line + "\n" for line in lines)
```

### Write with print()

```python
with open("output.txt", "w") as f:
    print("Hello World", file=f)       # print to file instead of screen
    print("Second line", file=f)
```

---

## Appending to Files

```python
with open("log.txt", "a") as f:        # "a" = append (adds to end)
    f.write("New entry\n")
# Existing content is preserved, new content added at end
```

---

## The with Statement (Context Manager)

**Always use `with` for files.** It guarantees the file is closed, even if an error occurs.

```python
# with statement — safe:
with open("file.txt") as f:
    data = f.read()
# file is AUTOMATICALLY closed here

# Manual open — unsafe:
f = open("file.txt")
data = f.read()
# if an error happens here, file stays open!
f.close()
```

### Multiple Files

```python
with open("input.txt") as src, open("output.txt", "w") as dst:
    for line in src:
        dst.write(line.upper())
```

---

## File Modes

| Mode | Meaning | Creates? | Overwrites? |
|------|---------|----------|-------------|
| `"r"` | Read (default) | No (error if missing) | No |
| `"w"` | Write | Yes | Yes! Destroys existing |
| `"a"` | Append | Yes | No (adds to end) |
| `"x"` | Exclusive create | Yes (error if exists) | No |
| `"r+"` | Read + write | No | No |
| `"w+"` | Write + read | Yes | Yes! |
| `"rb"` | Read binary | No | No |
| `"wb"` | Write binary | Yes | Yes! |

---

## Working with JSON

JSON is the most common data format for APIs. Python makes it easy.

### Parse JSON String → Python

```python
import json

json_string = '{"name": "Alice", "age": 30, "tags": ["admin", "user"]}'
data = json.loads(json_string)     # string → dict/list

print(data["name"])                # "Alice"
print(data["tags"])                # ["admin", "user"]
```

### Convert Python → JSON String

```python
data = {"name": "Alice", "age": 30, "active": True}
json_string = json.dumps(data)                    # compact
json_pretty = json.dumps(data, indent=2)          # pretty-printed
json_sorted = json.dumps(data, indent=2, sort_keys=True)  # sorted keys
```

### Read JSON File

```python
with open("config.json") as f:
    data = json.load(f)            # file → dict/list
```

### Write JSON File

```python
with open("output.json", "w") as f:
    json.dump(data, f, indent=2)   # dict/list → file
```

### JSON ↔ Python Type Mapping

| JSON | Python |
|------|--------|
| `{}` object | `dict` |
| `[]` array | `list` |
| `"string"` | `str` |
| `123` number | `int` |
| `3.14` number | `float` |
| `true` | `True` |
| `false` | `False` |
| `null` | `None` |

---

## Working with YAML

YAML is common for configuration files. Requires `pip install pyyaml`.

### Read YAML File

```python
import yaml

with open("config.yaml") as f:
    data = yaml.safe_load(f)       # returns dict/list
```

### Write YAML File

```python
with open("output.yaml", "w") as f:
    yaml.dump(data, f, default_flow_style=False)
```

### Parse YAML String

```python
yaml_string = """
name: prod-cluster
cloud: aws
pools:
  - name: master
    count: 3
  - name: worker
    count: 5
"""
data = yaml.safe_load(yaml_string)
print(data["pools"][0]["name"])    # "master"
```

**Important:** Always use `yaml.safe_load()`, never `yaml.load()`. The unsafe version can execute arbitrary code.

---

## Working with CSV

```python
import csv

# Read CSV:
with open("servers.csv") as f:
    reader = csv.reader(f)
    for row in reader:             # each row is a list
        print(row)                 # ['web-01', '10.0.0.1', 'Running']

# Read CSV as dicts (using header row):
with open("servers.csv") as f:
    reader = csv.DictReader(f)
    for row in reader:             # each row is a dict
        print(row["name"], row["ip"])

# Write CSV:
with open("output.csv", "w", newline="") as f:
    writer = csv.writer(f)
    writer.writerow(["name", "ip", "status"])          # header
    writer.writerow(["web-01", "10.0.0.1", "Running"]) # data row
    writer.writerows([                                  # multiple rows
        ["db-01", "10.0.0.2", "Running"],
        ["cache-01", "10.0.0.3", "Stopped"],
    ])
```

---

## Working with .env Files

Load environment variables from a `.env` file. Requires `pip install python-dotenv`.

### .env File

```
PALETTE_BASE_URL=https://api.spectrocloud.com
PALETTE_API_KEY=abc123secret
PROJECT_NAME=my-project
```

### Loading

```python
from dotenv import load_dotenv
import os

load_dotenv()                      # loads .env from current directory
load_dotenv("/path/to/.env")       # or from specific path

url = os.getenv("PALETTE_BASE_URL")
key = os.getenv("PALETTE_API_KEY")
```

This is exactly how spectro-cli's `config.py` works.

---

## File Path Operations

### os.path (Traditional)

```python
import os.path

os.path.join("/tmp", "sub", "file.txt")         # "/tmp/sub/file.txt"
os.path.dirname("/tmp/sub/file.txt")            # "/tmp/sub"
os.path.basename("/tmp/sub/file.txt")           # "file.txt"
os.path.splitext("file.tar.gz")                 # ("file.tar", ".gz")
os.path.abspath("file.txt")                     # full absolute path
os.path.expanduser("~/file.txt")                # expand ~ to home dir
os.path.getsize("/tmp/file.txt")                # file size in bytes
```

### pathlib (Modern — Recommended)

```python
from pathlib import Path

p = Path("/tmp/sub/file.txt")
p.parent                                        # Path("/tmp/sub")
p.name                                          # "file.txt"
p.stem                                          # "file"
p.suffix                                        # ".txt"
p.exists()                                      # True/False
p.is_file()                                     # True/False
p.is_dir()                                      # False

# Build paths with /:
config = Path.home() / ".config" / "app" / "settings.json"

# Read/write shorthand:
content = Path("file.txt").read_text()
Path("file.txt").write_text("Hello")

# Glob:
for py_file in Path(".").glob("**/*.py"):
    print(py_file)
```

---

## Checking if Files/Dirs Exist

```python
import os

os.path.exists("/tmp/file.txt")               # file or dir exists?
os.path.isfile("/tmp/file.txt")               # is it a file?
os.path.isdir("/tmp/subdir")                  # is it a directory?

# pathlib:
from pathlib import Path
Path("/tmp/file.txt").exists()
Path("/tmp/file.txt").is_file()
Path("/tmp/subdir").is_dir()
```

### Shell Comparison

| Shell | Python |
|-------|--------|
| `[ -f "$file" ]` | `os.path.isfile(file)` |
| `[ -d "$dir" ]` | `os.path.isdir(dir)` |
| `[ -e "$path" ]` | `os.path.exists(path)` |
| `[ -s "$file" ]` (not empty) | `os.path.getsize(file) > 0` |
| `[ -r "$file" ]` (readable) | `os.access(file, os.R_OK)` |
| `[ -w "$file" ]` (writable) | `os.access(file, os.W_OK)` |

---

## Directory Operations

```python
import os
import shutil

# List directory:
os.listdir("/tmp")                             # ['file1', 'file2', 'subdir']

# Create directory:
os.mkdir("/tmp/newdir")                        # single directory
os.makedirs("/tmp/a/b/c", exist_ok=True)      # nested, no error if exists

# Remove directory:
os.rmdir("/tmp/emptydir")                      # empty directory only
shutil.rmtree("/tmp/fulldir")                  # directory with contents (rm -rf)

# Copy:
shutil.copy("src.txt", "dst.txt")             # copy file
shutil.copy2("src.txt", "dst.txt")            # copy file + metadata
shutil.copytree("srcdir/", "dstdir/")         # copy directory

# Move/rename:
shutil.move("old.txt", "new.txt")             # rename
shutil.move("file.txt", "/tmp/file.txt")      # move

# Walk directory tree:
for dirpath, dirnames, filenames in os.walk("/tmp"):
    for filename in filenames:
        full_path = os.path.join(dirpath, filename)
        print(full_path)
```

---

## Temporary Files

```python
import tempfile

# Temporary file (auto-deleted when closed):
with tempfile.NamedTemporaryFile(mode="w", suffix=".json", delete=False) as f:
    f.write('{"test": true}')
    print(f.name)                              # /tmp/tmpXXXXXX.json

# Temporary directory (auto-deleted when exiting with block):
with tempfile.TemporaryDirectory() as tmpdir:
    print(tmpdir)                              # /tmp/tmpXXXXXX
    # create files inside tmpdir...
# directory and all contents deleted here
```

---

## Common Patterns

### Read Config File

```python
import json

def load_config(path="config.json"):
    try:
        with open(path) as f:
            return json.load(f)
    except FileNotFoundError:
        return {}                              # return empty config if missing
```

### Read-Modify-Write JSON

```python
import json

# Load:
with open("data.json") as f:
    data = json.load(f)

# Modify:
data["updated"] = True
data["items"].append(new_item)

# Save:
with open("data.json", "w") as f:
    json.dump(data, f, indent=2)
```

### Process File Line by Line

```python
with open("access.log") as f:
    for line in f:
        line = line.strip()
        if "ERROR" in line:
            print(line)
```

### Safe File Write (Atomic)

```python
import os, tempfile

def safe_write(path, data):
    """Write to temp file first, then rename — prevents corruption."""
    dir_name = os.path.dirname(path) or "."
    with tempfile.NamedTemporaryFile("w", dir=dir_name, delete=False) as tmp:
        json.dump(data, tmp, indent=2)
        tmp_path = tmp.name
    os.replace(tmp_path, path)             # atomic rename
```

---

## Shell vs Python File Operations

| Operation | Shell | Python |
|-----------|-------|--------|
| Read file | `cat file` | `open("file").read()` |
| Read line by line | `while read line; do ... done < file` | `for line in open("file"):` |
| Write file | `echo "text" > file` | `open("file","w").write("text")` |
| Append | `echo "text" >> file` | `open("file","a").write("text")` |
| Copy | `cp src dst` | `shutil.copy("src","dst")` |
| Move | `mv src dst` | `shutil.move("src","dst")` |
| Delete file | `rm file` | `os.remove("file")` |
| Delete dir | `rm -rf dir` | `shutil.rmtree("dir")` |
| Create dir | `mkdir -p path` | `os.makedirs("path", exist_ok=True)` |
| List dir | `ls` | `os.listdir(".")` |
| Find files | `find . -name "*.py"` | `glob.glob("**/*.py", recursive=True)` |
| File size | `stat -c %s file` | `os.path.getsize("file")` |
| Parse JSON | `jq '.key' file` | `json.load(open("file"))["key"]` |

---

> **Practice:** Chapter `04-files-errors-classes.py` sections 1-4
> **Next:** [11 - Error Handling](11-error-handling.md)
