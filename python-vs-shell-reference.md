# 14 — Python vs Shell Quick Reference

A consolidated side-by-side reference for common operations. Use this as a cheat sheet when converting shell scripts to Python.

---

## Table of Contents

- [Variables and Data](#variables-and-data)
- [String Operations](#string-operations)
- [Conditions and Logic](#conditions-and-logic)
- [Loops](#loops)
- [Functions](#functions)
- [File Operations](#file-operations)
- [Directory Operations](#directory-operations)
- [Process and System](#process-and-system)
- [Input / Output](#input-output)
- [Error Handling](#error-handling)
- [Arrays / Lists / Dicts](#arrays-lists-dicts)
- [Text Processing](#text-processing)
- [Date and Time](#date-and-time)
- [Networking and HTTP](#networking-and-http)
- [JSON / YAML / Config](#json-yaml-config)
- [Script Organization](#script-organization)
- [Common One-Liners](#common-one-liners)

---

## Variables and Data

| Operation | Shell | Python |
|-----------|-------|--------|
| Assign | `name="prod"` | `name = "prod"` |
| Use variable | `echo "$name"` | `print(name)` |
| Integer | `count=5` | `count = 5` |
| Float | N/A (use `bc`) | `ratio = 0.5` |
| Boolean | `flag=true` (string) | `flag = True` |
| Null/empty | `val=""` | `val = None` |
| Check empty | `if [ -z "$val" ]` | `if not val:` |
| Check not empty | `if [ -n "$val" ]` | `if val:` |
| Environment var | `export KEY="val"` | `os.environ["KEY"] = "val"` |
| Read env var | `echo "$KEY"` | `os.environ.get("KEY", "default")` |
| Default value | `${var:-default}` | `var or "default"` / `dict.get(k, default)` |
| Type check | N/A | `isinstance(x, str)` |
| Type convert | N/A | `int("5")`, `str(5)`, `float("3.14")` |
| Constant | `readonly PI=3.14` | `PI = 3.14` (convention only) |
| Delete variable | `unset var` | `del var` |

---

## String Operations

| Operation | Shell | Python |
|-----------|-------|--------|
| Concatenate | `"${a}${b}"` | `a + b` or `f"{a}{b}"` |
| Length | `${#str}` | `len(s)` |
| Substring | `${str:0:5}` | `s[0:5]` or `s[:5]` |
| Replace first | `${str/old/new}` | `s.replace("old", "new", 1)` |
| Replace all | `${str//old/new}` | `s.replace("old", "new")` |
| Uppercase | `${str^^}` | `s.upper()` |
| Lowercase | `${str,,}` | `s.lower()` |
| Title case | N/A | `s.title()` |
| Strip whitespace | `echo "$str" \| xargs` | `s.strip()` |
| Strip left | N/A | `s.lstrip()` |
| Strip right | N/A | `s.rstrip()` |
| Split | `IFS=',' read -ra arr <<< "$str"` | `s.split(",")` |
| Join | `(IFS=','; echo "${arr[*]}")` | `",".join(items)` |
| Contains | `[[ "$str" == *"sub"* ]]` | `"sub" in s` |
| Starts with | `[[ "$str" == "pre"* ]]` | `s.startswith("pre")` |
| Ends with | `[[ "$str" == *"suf" ]]` | `s.endswith("suf")` |
| Regex match | `[[ "$str" =~ pattern ]]` | `re.search(pattern, s)` |
| Regex extract | `[[ "$str" =~ (pattern) ]]; ${BASH_REMATCH[1]}` | `re.search(r"(pattern)", s).group(1)` |
| Format / interpolate | `echo "name is $name"` | `f"name is {name}"` |
| Multiline | heredoc `<< EOF` | `"""triple quotes"""` |
| Repeat | `printf 'x%.0s' $(seq 1 5)` | `"x" * 5` |
| Reverse | `echo "$str" \| rev` | `s[::-1]` |

---

## Conditions and Logic

| Operation | Shell | Python |
|-----------|-------|--------|
| If/then/fi | `if [ cond ]; then ... fi` | `if cond:` |
| Else | `else` | `else:` |
| Elif | `elif [ cond ]; then` | `elif cond:` |
| And | `[ cond1 ] && [ cond2 ]` | `cond1 and cond2` |
| Or | `[ cond1 ] \|\| [ cond2 ]` | `cond1 or cond2` |
| Not | `! [ cond ]` | `not cond` |
| Equal (string) | `[ "$a" = "$b" ]` | `a == b` |
| Not equal | `[ "$a" != "$b" ]` | `a != b` |
| Greater than | `[ "$a" -gt "$b" ]` | `a > b` |
| Less than | `[ "$a" -lt "$b" ]` | `a < b` |
| Greater or equal | `[ "$a" -ge "$b" ]` | `a >= b` |
| Ternary | `[ cond ] && echo y \|\| echo n` | `"y" if cond else "n"` |
| Case/switch | `case $x in ... esac` | `match x:` (3.10+) or if/elif |
| Null check | `[ -z "$var" ]` | `if var is None:` |
| File exists | `[ -f "$path" ]` | `os.path.isfile(path)` |
| Dir exists | `[ -d "$path" ]` | `os.path.isdir(path)` |
| Path exists | `[ -e "$path" ]` | `os.path.exists(path)` |
| Readable | `[ -r "$path" ]` | `os.access(path, os.R_OK)` |
| Writable | `[ -w "$path" ]` | `os.access(path, os.W_OK)` |
| Executable | `[ -x "$path" ]` | `os.access(path, os.X_OK)` |

---

## Loops

| Operation | Shell | Python |
|-----------|-------|--------|
| For (list) | `for i in a b c; do ... done` | `for i in ["a", "b", "c"]:` |
| For (range) | `for i in $(seq 1 10); do ... done` | `for i in range(1, 11):` |
| For (files) | `for f in *.txt; do ... done` | `for f in glob.glob("*.txt"):` |
| While | `while [ cond ]; do ... done` | `while cond:` |
| Read lines | `while read line; do ... done < file` | `for line in open(file):` |
| Infinite loop | `while true; do ... done` | `while True:` |
| Break | `break` | `break` |
| Continue | `continue` | `continue` |
| Loop counter | `i=0; while ...; do ((i++)); done` | `for i, item in enumerate(items):` |
| Iterate dict | `for key in "${!arr[@]}"; do` | `for key, val in d.items():` |
| C-style for | `for ((i=0; i<10; i++)); do` | `for i in range(10):` |

---

## Functions

| Operation | Shell | Python |
|-----------|-------|--------|
| Define | `function name() { ... }` | `def name():` |
| Call | `name arg1 arg2` | `name(arg1, arg2)` |
| Arguments | `$1, $2, $@` | Named parameters |
| All args | `"$@"` | `*args` |
| Return value | `return 0` (exit code only) | `return value` |
| Return string | `echo "result"` (stdout capture) | `return "result"` |
| Default param | `${1:-default}` | `def f(x="default"):` |
| Named params | N/A | `def f(name="prod", cloud="aws"):` |
| Keyword-only | N/A | `def f(*, name):` |
| Variadic | `"$@"` | `def f(*args, **kwargs):` |
| Local var | `local var=value` | Variables are local by default |
| Global var | Vars are global by default | `global var` |
| Lambda | N/A | `lambda x: x * 2` |
| Docstring | `# comment` | `"""Description."""` |

---

## File Operations

| Operation | Shell | Python |
|-----------|-------|--------|
| Read file | `cat file` | `open(f).read()` |
| Read lines | `mapfile -t lines < file` | `open(f).readlines()` |
| Write file | `echo "text" > file` | `open(f, "w").write("text")` |
| Append | `echo "text" >> file` | `open(f, "a").write("text")` |
| File exists | `[ -f file ]` | `os.path.isfile(file)` |
| File size | `stat -c%s file` | `os.path.getsize(file)` |
| Delete file | `rm file` | `os.remove(file)` |
| Copy file | `cp src dst` | `shutil.copy2(src, dst)` |
| Move/rename | `mv old new` | `shutil.move(old, new)` or `os.rename(old, new)` |
| Permission | `chmod 755 file` | `os.chmod(file, 0o755)` |
| Temp file | `mktemp` | `tempfile.NamedTemporaryFile()` |
| File extension | `${file##*.}` | `os.path.splitext(file)[1]` |
| Basename | `basename "$path"` | `os.path.basename(path)` |
| Dirname | `dirname "$path"` | `os.path.dirname(path)` |
| Absolute path | `realpath "$path"` | `os.path.abspath(path)` |
| Join paths | `"$dir/$file"` | `os.path.join(dir, file)` |
| Glob | `ls *.txt` | `glob.glob("*.txt")` |
| Recursive glob | `find . -name "*.txt"` | `glob.glob("**/*.txt", recursive=True)` |
| Line count | `wc -l < file` | `sum(1 for _ in open(f))` |

---

## Directory Operations

| Operation | Shell | Python |
|-----------|-------|--------|
| Current dir | `pwd` | `os.getcwd()` |
| Change dir | `cd /path` | `os.chdir("/path")` |
| Create dir | `mkdir -p /path` | `os.makedirs("/path", exist_ok=True)` |
| Remove dir | `rm -rf /path` | `shutil.rmtree("/path")` |
| List dir | `ls /path` | `os.listdir("/path")` |
| Walk tree | `find /path` | `os.walk("/path")` |
| Temp dir | `mktemp -d` | `tempfile.mkdtemp()` |
| Home dir | `$HOME` or `~` | `os.path.expanduser("~")` |

---

## Process and System

| Operation | Shell | Python |
|-----------|-------|--------|
| Run command | `command args` | `subprocess.run(["cmd", "args"])` |
| Capture output | `result=$(command)` | `subprocess.run(cmd, capture_output=True, text=True).stdout` |
| Check exit code | `$?` | `result.returncode` |
| Run and check | `command \|\| exit 1` | `subprocess.run(cmd, check=True)` |
| Background | `command &` | `subprocess.Popen(cmd)` |
| Pipe | `cmd1 \| cmd2` | `subprocess.run(cmd1, stdout=PIPE) \| ...` |
| Exit script | `exit 1` | `sys.exit(1)` |
| Script args | `$0, $1, $@, $#` | `sys.argv[0], sys.argv[1], sys.argv[1:]` |
| PID | `$$` | `os.getpid()` |
| Sleep | `sleep 5` | `time.sleep(5)` |
| Signal trap | `trap handler SIGINT` | `signal.signal(signal.SIGINT, handler)` |
| Platform | `uname -s` | `sys.platform` or `platform.system()` |
| Which/find command | `which python` | `shutil.which("python")` |

---

## Input / Output

| Operation | Shell | Python |
|-----------|-------|--------|
| Print | `echo "text"` | `print("text")` |
| Print no newline | `echo -n "text"` | `print("text", end="")` |
| Print to stderr | `echo "err" >&2` | `print("err", file=sys.stderr)` |
| Read input | `read -p "Name: " name` | `name = input("Name: ")` |
| Read password | `read -sp "Pass: " pass` | `getpass.getpass("Pass: ")` |
| Printf format | `printf "%-10s %5d" "$name" "$count"` | `f"{name:<10} {count:5d}"` |
| Redirect to file | `command > file` | `print("text", file=open("file", "w"))` |
| /dev/null | `command > /dev/null 2>&1` | `subprocess.run(cmd, capture_output=True)` |
| Colored output | ANSI codes | Rich library / `\033[31m` |

---

## Error Handling

| Operation | Shell | Python |
|-----------|-------|--------|
| Check command | `if command; then` | `try: ... except:` |
| Exit on error | `set -e` | Exceptions propagate by default |
| Error trap | `trap handler ERR` | `except ExceptionType:` |
| Cleanup trap | `trap cleanup EXIT` | `finally:` block |
| Custom error | `echo "error" >&2; exit 1` | `raise ValueError("error")` |
| Error message | `echo "error" >&2` | `raise` or `print(..., file=sys.stderr)` |
| Ignore error | `command \|\| true` | `try: ... except: pass` |
| Assert | N/A | `assert condition, "message"` |

---

## Arrays / Lists / Dicts

| Operation | Shell (array) | Python (list) |
|-----------|--------------|--------------|
| Create | `arr=(a b c)` | `arr = ["a", "b", "c"]` |
| Access element | `${arr[0]}` | `arr[0]` |
| Length | `${#arr[@]}` | `len(arr)` |
| Append | `arr+=(d)` | `arr.append("d")` |
| Slice | `${arr[@]:1:2}` | `arr[1:3]` |
| Delete element | `unset arr[0]` | `del arr[0]` or `arr.pop(0)` |
| Contains | `[[ " ${arr[*]} " =~ " val " ]]` | `"val" in arr` |
| Iterate | `for i in "${arr[@]}"; do` | `for i in arr:` |
| All elements | `"${arr[@]}"` | `arr` (it's already a list) |
| Sort | `sorted=($(echo "${arr[@]}" \| tr ' ' '\n' \| sort))` | `sorted(arr)` or `arr.sort()` |
| Unique | `echo "${arr[@]}" \| tr ' ' '\n' \| sort -u` | `list(set(arr))` |
| Join | `(IFS=','; echo "${arr[*]}")` | `",".join(arr)` |

| Operation | Shell (assoc array) | Python (dict) |
|-----------|-------------------|----|
| Create | `declare -A d=([k1]=v1 [k2]=v2)` | `d = {"k1": "v1", "k2": "v2"}` |
| Access | `${d[key]}` | `d["key"]` or `d.get("key")` |
| Set | `d[key]=val` | `d["key"] = "val"` |
| Delete | `unset d[key]` | `del d["key"]` |
| Keys | `"${!d[@]}"` | `d.keys()` |
| Values | `"${d[@]}"` | `d.values()` |
| Check key | `[[ -v d[key] ]]` | `"key" in d` |

---

## Text Processing

| Operation | Shell | Python |
|-----------|-------|--------|
| grep (find lines) | `grep "pattern" file` | `[l for l in open(f) if "pattern" in l]` |
| grep -E (regex) | `grep -E "pat" file` | `[l for l in open(f) if re.search("pat", l)]` |
| sed (replace) | `sed 's/old/new/g' file` | `text.replace("old", "new")` |
| sed (regex) | `sed -E 's/pat/rep/g' file` | `re.sub(r"pat", "rep", text)` |
| awk (extract field) | `awk '{print $2}' file` | `line.split()[1]` |
| awk (with separator) | `awk -F: '{print $1}' file` | `line.split(":")[0]` |
| cut | `cut -d: -f1` | `line.split(":")[0]` |
| sort | `sort file` | `sorted(lines)` |
| sort numeric | `sort -n file` | `sorted(lines, key=int)` |
| sort reverse | `sort -r file` | `sorted(lines, reverse=True)` |
| uniq | `sort \| uniq` | `list(dict.fromkeys(items))` |
| uniq -c (count) | `sort \| uniq -c` | `Counter(items)` |
| head | `head -n 5 file` | `lines[:5]` |
| tail | `tail -n 5 file` | `lines[-5:]` |
| wc -l | `wc -l < file` | `len(lines)` |
| tr (translate) | `echo "ABC" \| tr 'A-Z' 'a-z'` | `"ABC".lower()` |
| tr -d (delete chars) | `echo "a1b2" \| tr -d '0-9'` | `re.sub(r"\d", "", "a1b2")` |
| tee | `command \| tee file` | Write to both file and stdout |
| xargs | `cat list \| xargs command` | Loop in Python |

---

## Date and Time

| Operation | Shell | Python |
|-----------|-------|--------|
| Now | `date` | `datetime.datetime.now()` |
| UTC | `date -u` | `datetime.datetime.utcnow()` |
| Epoch | `date +%s` | `int(time.time())` |
| Format | `date +"%Y-%m-%d"` | `datetime.now().strftime("%Y-%m-%d")` |
| Parse | `date -d "2024-01-15"` | `datetime.strptime("2024-01-15", "%Y-%m-%d")` |
| Add days | `date -d "+3 days"` | `now + timedelta(days=3)` |
| Timestamp | `date +%s` | `time.time()` |
| Timer | `time command` | `start = time.time(); ...; elapsed = time.time() - start` |

---

## Networking and HTTP

| Operation | Shell | Python |
|-----------|-------|--------|
| HTTP GET | `curl -s URL` | `requests.get(url)` |
| HTTP POST | `curl -X POST -d 'data' URL` | `requests.post(url, json=data)` |
| HTTP with headers | `curl -H "Auth: token" URL` | `requests.get(url, headers={"Auth": "token"})` |
| Download file | `wget URL -O file` | `urllib.request.urlretrieve(url, file)` |
| Response body | `curl -s URL` | `response.text` or `response.json()` |
| Status code | `curl -s -o /dev/null -w "%{http_code}" URL` | `response.status_code` |
| Check success | Test `$?` | `response.raise_for_status()` |
| Session/cookies | Hard (need cookie jar) | `session = requests.Session()` |
| Timeout | `curl --connect-timeout 5 URL` | `requests.get(url, timeout=5)` |

---

## JSON / YAML / Config

| Operation | Shell | Python |
|-----------|-------|--------|
| Parse JSON | `jq '.key' file` | `json.load(open(f))["key"]` |
| Create JSON | `jq -n '{key: "val"}'` | `json.dumps({"key": "val"})` |
| Pretty JSON | `jq '.' file` | `json.dumps(data, indent=2)` |
| JSON extract | `echo "$json" \| jq -r '.name'` | `data["name"]` |
| Parse YAML | `yq '.key' file` | `yaml.safe_load(open(f))["key"]` |
| Write YAML | Hard | `yaml.dump(data, open(f, "w"))` |
| .env file | `source .env` | `dotenv.load_dotenv()` |
| INI config | Hard | `configparser.ConfigParser()` |
| Read CSV | `while IFS=, read ...` | `csv.reader(open(f))` |

---

## Script Organization

| Concept | Shell | Python |
|---------|-------|--------|
| Shebang | `#!/bin/bash` | `#!/usr/bin/env python3` |
| Main guard | N/A (runs everything) | `if __name__ == "__main__":` |
| Import | `source file.sh` | `import module` / `from module import func` |
| Package | Directory with scripts | Directory with `__init__.py` |
| Dependencies | System packages | `pip install` / `requirements.txt` |
| Virtual env | N/A | `python3 -m venv .venv` |
| Entry point | `./script.sh` | `console_scripts` in setup.py |
| Argument parsing | `getopts` / manual `$1` | `argparse` / `click` |
| Config loading | `source config.sh` | `dotenv` / `json.load` / `yaml.safe_load` |
| Logging | `echo` to file | `logging` module |
| Exit codes | `exit 0` / `exit 1` | `sys.exit(0)` / `sys.exit(1)` |

---

## Common One-Liners

### Shell → Python Translations

```bash
# Shell: Find files modified in last 24h
find /path -mtime -1 -type f
```
```python
# Python:
import os, time
cutoff = time.time() - 86400
[f for f in glob.glob("/path/**", recursive=True)
 if os.path.isfile(f) and os.path.getmtime(f) > cutoff]
```

---

```bash
# Shell: Count lines with "ERROR" in log
grep -c "ERROR" app.log
```
```python
# Python:
sum(1 for line in open("app.log") if "ERROR" in line)
```

---

```bash
# Shell: Get unique IPs from access log
awk '{print $1}' access.log | sort -u
```
```python
# Python:
sorted(set(line.split()[0] for line in open("access.log")))
```

---

```bash
# Shell: Replace in all files
find . -name "*.txt" -exec sed -i 's/old/new/g' {} +
```
```python
# Python:
import glob
for f in glob.glob("**/*.txt", recursive=True):
    text = open(f).read().replace("old", "new")
    open(f, "w").write(text)
```

---

```bash
# Shell: JSON API call
curl -s https://api.example.com/data | jq '.items[].name'
```
```python
# Python:
import requests
names = [item["name"] for item in requests.get("https://api.example.com/data").json()["items"]]
```

---

```bash
# Shell: Monitor file for changes
tail -f /var/log/app.log | grep --line-buffered "ERROR"
```
```python
# Python:
import time
with open("/var/log/app.log") as f:
    f.seek(0, 2)  # go to end
    while True:
        line = f.readline()
        if line and "ERROR" in line:
            print(line, end="")
        elif not line:
            time.sleep(0.1)
```

---

```bash
# Shell: Parallel execution
for host in host1 host2 host3; do
    ssh "$host" "uptime" &
done
wait
```
```python
# Python:
from concurrent.futures import ThreadPoolExecutor
import subprocess

hosts = ["host1", "host2", "host3"]
with ThreadPoolExecutor() as executor:
    results = executor.map(
        lambda h: subprocess.run(["ssh", h, "uptime"], capture_output=True, text=True),
        hosts,
    )
```

---

> **This is the final concept file.** Go back to [Concepts Overview](index.md) for the full index.
