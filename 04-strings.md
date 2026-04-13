# 04 — Strings

Strings are the most heavily used data type in scripting. This page covers everything you need.

---

## Table of Contents

- [Creating Strings](#creating-strings)
- [Single vs Double vs Triple Quotes](#single-vs-double-vs-triple-quotes)
- [Escape Characters](#escape-characters)
- [Raw Strings](#raw-strings)
- [String Length](#string-length)
- [Accessing Characters (Indexing)](#accessing-characters-indexing)
- [Slicing (Extracting Parts)](#slicing-extracting-parts)
- [String Concatenation (Joining)](#string-concatenation-joining)
- [f-strings (Formatted Strings)](#f-strings-formatted-strings)
- [Format Specifiers](#format-specifiers)
- [String Methods — Complete Reference](#string-methods-complete-reference)
- [Checking Content](#checking-content)
- [Splitting and Joining](#splitting-and-joining)
- [Stripping (Trimming Whitespace)](#stripping-trimming-whitespace)
- [Replacing](#replacing)
- [Case Conversion](#case-conversion)
- [Searching and Finding](#searching-and-finding)
- [Padding and Alignment](#padding-and-alignment)
- [String Testing Methods](#testing-methods)
- [Multi-line Strings](#multi-line-strings)
- [String Immutability](#string-immutability)
- [Common String Patterns in Scripting](#common-string-patterns-in-scripting)
- [Shell vs Python String Operations](#shell-vs-python-string-operations)

---

## Creating Strings

A string is any text wrapped in quotes.

```python
name = "Alice"                     # double quotes
name = 'Alice'                     # single quotes — identical
name = """Alice"""                 # triple quotes — usually for multi-line
```

**Empty string:**

```python
empty = ""
empty = ''
```

**String from other types:**

```python
str(42)                            # "42"
str(3.14)                          # "3.14"
str(True)                          # "True"
str(None)                          # "None"
str([1, 2, 3])                     # "[1, 2, 3]"
```

---

## Single vs Double vs Triple Quotes

Single and double quotes are **identical in behavior**. Use whichever you prefer, or pick the one that avoids escaping:

```python
# These are the same:
msg = "hello world"
msg = 'hello world'

# Use the other quote type when your string contains one kind:
msg = "it's a test"                # contains single quote → use double
msg = 'He said "hello"'           # contains double quotes → use single

# Or escape with backslash:
msg = 'it\'s a test'              # escaped single quote
msg = "He said \"hello\""         # escaped double quotes
```

**Triple quotes** are for multi-line strings:

```python
message = """This is line 1
This is line 2
This is line 3"""

# Also works with single quotes:
message = '''Line 1
Line 2
Line 3'''
```

---

## Escape Characters

Special characters inside strings:

| Escape | Meaning | Example | Result |
|--------|---------|---------|--------|
| `\n` | Newline | `"line1\nline2"` | Two lines |
| `\t` | Tab | `"col1\tcol2"` | Tab-separated |
| `\\` | Literal backslash | `"C:\\Users"` | `C:\Users` |
| `\'` | Literal single quote | `'it\'s'` | `it's` |
| `\"` | Literal double quote | `"say \"hi\""` | `say "hi"` |
| `\0` | Null character | — | Used in binary |

---

## Raw Strings

Prefix `r` to disable escape processing. Backslashes are treated as literal characters.

```python
# Normal string:
path = "C:\new\test"               # \n and \t are interpreted as newline/tab!

# Raw string:
path = r"C:\new\test"              # backslashes are literal — useful for paths

# Also useful for regex patterns:
import re
pattern = r"\d+\.\d+"             # the \d is literal, not an escape
```

---

## String Length

```python
len("hello")                       # 5
len("")                            # 0
len("hello world")                 # 11 (space counts)
```

Shell equivalent: `${#variable}` or `echo -n "$var" | wc -c`

---

## Accessing Characters (Indexing)

Strings are sequences — you can access individual characters by position.

```python
word = "Python"
#       P y t h o n
#       0 1 2 3 4 5      (forward index)
#      -6-5-4-3-2-1      (reverse index)

word[0]                            # "P"  — first character
word[1]                            # "y"  — second character
word[5]                            # "n"  — sixth character
word[-1]                           # "n"  — last character
word[-2]                           # "o"  — second to last
```

### Important

- **Indexing starts at 0**, not 1.
- **Negative indices** count from the end: `-1` is last, `-2` is second to last.
- Accessing an index that doesn't exist raises `IndexError`.
- **You cannot modify by index** (strings are immutable): `word[0] = "J"` → Error!

---

## Slicing (Extracting Parts)

Slicing extracts a portion of a string: `string[start:end:step]`

```python
word = "Python"

word[0:3]                          # "Pyt"  — characters 0, 1, 2 (end exclusive!)
word[2:5]                          # "tho"  — characters 2, 3, 4
word[:3]                           # "Pyt"  — from start to 2 (shorthand)
word[3:]                           # "hon"  — from 3 to end
word[:]                            # "Python" — full copy
word[::2]                          # "Pto"  — every 2nd character
word[::-1]                         # "nohtyP" — reversed!
```

### Slice Rules

- `start` is **included** (default: 0)
- `end` is **excluded** (default: end of string)
- `step` is the skip count (default: 1)
- **Out-of-range slices don't crash** — they just stop at the boundary

Shell equivalent: `${var:start:length}` — but Python is `start:end` (not `start:length`).

---

## String Concatenation (Joining)

### With `+`

```python
first = "Hello"
last = "World"
full = first + " " + last         # "Hello World"
```

### With `+=`

```python
result = ""
result += "Hello "
result += "World"                  # "Hello World"
```

### Repeating with `*`

```python
line = "-" * 40                    # "----------------------------------------"
indent = "  " * 3                  # "      " (6 spaces)
```

### Don't Concatenate in Loops — Use join()

```python
# SLOW (creates a new string every iteration):
result = ""
for word in words:
    result += word + " "

# FAST (one operation):
result = " ".join(words)
```

---

## f-strings (Formatted Strings)

**The most important string feature in Python.** Put `f` before the quote, use `{...}` for expressions.

```python
name = "Alice"
age = 30

# Basic variable insertion:
f"Name: {name}"                    # "Name: Alice"
f"Age: {age}"                      # "Age: 30"

# Any expression inside { }:
f"In 5 years: {age + 5}"          # "In 5 years: 35"
f"Upper: {name.upper()}"          # "Upper: ALICE"
f"Length: {len(name)}"            # "Length: 5"

# Calling methods:
f"First 3: {name[:3]}"           # "First 3: Ali"

# Conditional:
f"Status: {'adult' if age >= 18 else 'minor'}"   # "Status: adult"

# Accessing dict values:
server = {"name": "web-01", "ip": "10.0.0.1"}
f"Server: {server['name']} at {server['ip']}"    # "Server: web-01 at 10.0.0.1"
```

### Shell Comparison

| Shell | Python |
|-------|--------|
| `"Hello $NAME"` | `f"Hello {name}"` |
| `"Hello ${NAME}"` | `f"Hello {name}"` |
| `"Files: $(ls \| wc -l)"` | `f"Files: {len(os.listdir())}"` |

### Multiline f-strings

```python
info = f"""
Server: {name}
IP:     {ip}
Status: {status}
"""
```

### Braces in f-strings

To include literal `{` or `}`, double them:

```python
f"Use {{braces}} like this"       # "Use {braces} like this"
```

---

## Format Specifiers

Inside f-string `{}`, after a colon `:`, you can control formatting:

### Numbers

```python
n = 1234567
f"{n:,}"                           # "1,234,567"     — thousands separator
f"{n:_}"                           # "1_234_567"     — underscore separator

pi = 3.14159
f"{pi:.2f}"                        # "3.14"          — 2 decimal places
f"{pi:.4f}"                        # "3.1416"        — 4 decimal places
f"{pi:10.2f}"                      # "      3.14"    — right-aligned in 10-char field

pct = 0.856
f"{pct:.1%}"                       # "85.6%"         — percentage format
```

### Alignment and Padding

```python
name = "Alice"
f"{name:<20}"                      # "Alice               " — left-align in 20 chars
f"{name:>20}"                      # "               Alice" — right-align
f"{name:^20}"                      # "       Alice        " — center
f"{name:-^20}"                     # "-------Alice--------" — center with fill char
f"{name:.<20}"                     # "Alice..............." — left-align, fill with dots
```

### Integer Formatting

```python
n = 42
f"{n:05d}"                         # "00042"        — zero-padded, 5 digits
f"{n:x}"                           # "2a"           — hexadecimal
f"{n:X}"                           # "2A"           — hexadecimal uppercase
f"{n:o}"                           # "52"           — octal
f"{n:b}"                           # "101010"       — binary
f"{n:#x}"                          # "0x2a"         — hex with prefix
f"{n:#b}"                          # "0b101010"     — binary with prefix
```

### Quick Reference

| Specifier | Meaning | Example | Result |
|-----------|---------|---------|--------|
| `:.2f` | 2 decimal places | `f"{3.14159:.2f}"` | `"3.14"` |
| `:,` | Thousands separator | `f"{1000000:,}"` | `"1,000,000"` |
| `:.1%` | Percentage | `f"{0.856:.1%}"` | `"85.6%"` |
| `:>10` | Right-align in 10 chars | `f"{'hi':>10}"` | `"        hi"` |
| `:<10` | Left-align in 10 chars | `f"{'hi':<10}"` | `"hi        "` |
| `:^10` | Center in 10 chars | `f"{'hi':^10}"` | `"    hi    "` |
| `:05d` | Zero-pad to 5 digits | `f"{42:05d}"` | `"00042"` |

---

## String Methods — Complete Reference

Every string has these methods built in. Call with a dot: `"hello".upper()`

**Important:** All string methods return a **new string**. They never modify the original (strings are immutable).

### Case Conversion

| Method | Input | Output | Shell Equivalent |
|--------|-------|--------|-----------------|
| `.upper()` | `"hello"` | `"HELLO"` | `tr '[:lower:]' '[:upper:]'` |
| `.lower()` | `"HELLO"` | `"hello"` | `tr '[:upper:]' '[:lower:]'` |
| `.title()` | `"hello world"` | `"Hello World"` | — |
| `.capitalize()` | `"hello world"` | `"Hello world"` | — |
| `.swapcase()` | `"Hello"` | `"hELLO"` | — |
| `.casefold()` | `"Straße"` | `"strasse"` | Aggressive lowercase (for comparison) |

### Searching

| Method | What it does | Returns |
|--------|-------------|---------|
| `.find("x")` | Find first position of "x" | Index, or `-1` if not found |
| `.rfind("x")` | Find last position of "x" | Index, or `-1` |
| `.index("x")` | Find first position of "x" | Index, or **raises ValueError** |
| `.count("x")` | Count occurrences | Number |
| `.startswith("x")` | Starts with "x"? | `True`/`False` |
| `.endswith("x")` | Ends with "x"? | `True`/`False` |

```python
text = "Hello World Hello"
text.find("Hello")                 # 0 (first occurrence)
text.rfind("Hello")                # 12 (last occurrence)
text.find("xyz")                   # -1 (not found)
text.count("Hello")                # 2
text.startswith("Hello")           # True
text.endswith("Hello")             # True
```

### Splitting and Joining

| Method | What it does | Example |
|--------|-------------|---------|
| `.split(sep)` | Split into list | `"a,b,c".split(",")` → `["a","b","c"]` |
| `.split()` | Split by whitespace | `"a b  c".split()` → `["a","b","c"]` |
| `.rsplit(sep, n)` | Split from right | `"a.b.c".rsplit(".", 1)` → `["a.b","c"]` |
| `.splitlines()` | Split by newlines | `"a\nb\nc".splitlines()` → `["a","b","c"]` |
| `sep.join(list)` | Join list into string | `",".join(["a","b"])` → `"a,b"` |
| `.partition(sep)` | Split into 3 parts | `"a=b".partition("=")` → `("a","=","b")` |

### Stripping (Trimming)

| Method | What it does |
|--------|-------------|
| `.strip()` | Remove whitespace from both ends |
| `.lstrip()` | Remove whitespace from left only |
| `.rstrip()` | Remove whitespace from right only |
| `.strip("xy")` | Remove specific characters from both ends |

```python
"  hello  ".strip()               # "hello"
"  hello  ".lstrip()              # "hello  "
"  hello  ".rstrip()              # "  hello"
"***hello***".strip("*")          # "hello"
```

### Replacing

| Method | What it does |
|--------|-------------|
| `.replace(old, new)` | Replace all occurrences |
| `.replace(old, new, count)` | Replace first N occurrences |

```python
"Hello World".replace("World", "Python")       # "Hello Python"
"aaa".replace("a", "b", 2)                     # "bba" (only first 2)
```

### Padding and Alignment

| Method | What it does | Example |
|--------|-------------|---------|
| `.ljust(width)` | Left-align, pad right | `"hi".ljust(10)` → `"hi        "` |
| `.rjust(width)` | Right-align, pad left | `"hi".rjust(10)` → `"        hi"` |
| `.center(width)` | Center | `"hi".center(10)` → `"    hi    "` |
| `.zfill(width)` | Zero-fill | `"42".zfill(5)` → `"00042"` |

### Testing Methods

| Method | What it checks | Example |
|--------|---------------|---------|
| `.isdigit()` | All digits? | `"123".isdigit()` → `True` |
| `.isalpha()` | All letters? | `"abc".isalpha()` → `True` |
| `.isalnum()` | All letters or digits? | `"abc123".isalnum()` → `True` |
| `.isspace()` | All whitespace? | `"  \t".isspace()` → `True` |
| `.isupper()` | All uppercase? | `"HELLO".isupper()` → `True` |
| `.islower()` | All lowercase? | `"hello".islower()` → `True` |
| `.istitle()` | Title case? | `"Hello World".istitle()` → `True` |
| `.isnumeric()` | All numeric? | `"½".isnumeric()` → `True` |

---

## Checking Content

### `in` Operator

```python
"World" in "Hello World"          # True
"xyz" not in "Hello World"        # True
```

Shell equivalent: `[[ "$text" == *"World"* ]]`

### Comparison

```python
"abc" == "abc"                     # True (equal)
"abc" == "ABC"                     # False (case-sensitive!)
"abc" < "def"                      # True (alphabetical)
"abc" != "xyz"                     # True (not equal)
```

Case-insensitive comparison:

```python
"Hello".lower() == "hello".lower()  # True
```

---

## Splitting and Joining

These are the most-used string operations in scripting.

### Splitting

```python
# Split CSV line:
line = "Alice,30,NYC"
parts = line.split(",")            # ["Alice", "30", "NYC"]
name = parts[0]                    # "Alice"
age = int(parts[1])                # 30

# Split by whitespace (any amount):
"  hello   world  ".split()        # ["hello", "world"]

# Split with limit:
"a.b.c.d".split(".", 2)           # ["a", "b", "c.d"] (split max 2 times)

# Split from right:
"a.b.c.d".rsplit(".", 1)          # ["a.b.c", "d"] (split once from right)
# Useful for getting file extension:
"report.2024.csv".rsplit(".", 1)  # ["report.2024", "csv"]
```

### Joining

```python
# Join list into string:
", ".join(["Alice", "Bob", "Charlie"])    # "Alice, Bob, Charlie"
"\n".join(["line 1", "line 2"])            # "line 1\nline 2"
"/".join(["usr", "local", "bin"])          # "usr/local/bin"
"".join(["a", "b", "c"])                   # "abc" (no separator)
```

Shell equivalent: `IFS=','; echo "${array[*]}"`

---

## Stripping (Trimming Whitespace)

```python
text = "  hello  "
text.strip()                       # "hello" (both sides)
text.lstrip()                      # "hello  " (left only)
text.rstrip()                      # "  hello" (right only)

# Strip specific characters:
"###hello###".strip("#")           # "hello"
"..hello..".strip(".")             # "hello"

# Common pattern — strip newlines from file lines:
for line in file:
    line = line.strip()            # remove \n and whitespace
```

---

## Replacing

```python
text = "Hello World"
text.replace("World", "Python")    # "Hello Python"

# Remove characters (replace with nothing):
"Hello!!".replace("!", "")         # "Hello"

# Chain replacements:
text.replace("H", "J").replace("o", "0")   # "Jell0 W0rld"
```

---

## Case Conversion

```python
"hello world".upper()              # "HELLO WORLD"
"HELLO WORLD".lower()              # "hello world"
"hello world".title()              # "Hello World" (each word capitalized)
"hello world".capitalize()         # "Hello world" (only first letter)
"Hello".swapcase()                 # "hELLO"
```

---

## Searching and Finding

```python
text = "Hello World Hello"

# Find position of substring (returns -1 if not found):
text.find("World")                 # 6
text.find("xyz")                   # -1

# Find from right:
text.rfind("Hello")                # 12 (last occurrence)

# Count occurrences:
text.count("Hello")                # 2
text.count("l")                    # 4

# Check start/end:
text.startswith("Hello")           # True
text.endswith("Hello")             # True
text.startswith(("Hello", "Hi"))   # True (tuple of options)
```

---

## Padding and Alignment

```python
# For table-like output:
name = "Alice"
age = "30"

name.ljust(15)                     # "Alice          "
age.rjust(5)                       # "   30"
"Title".center(20)                 # "       Title        "
"42".zfill(5)                      # "00042"
```

---

## Multi-line Strings

```python
# Triple quotes preserve newlines:
message = """Dear Alice,

Thank you for your order.
It will arrive on Monday.

Best regards,
Bob"""

# Triple quotes also useful for long strings without line breaks:
query = (
    "SELECT name, age "
    "FROM users "
    "WHERE active = true"
)
# Parentheses let you break the line; strings auto-concatenate

# \n in regular strings:
message = "Line 1\nLine 2\nLine 3"
```

---

## String Immutability

Strings **cannot be modified in place**. Every "modification" creates a new string.

```python
name = "Alice"
name[0] = "B"                     # TypeError! Strings are immutable

# Instead, create a new string:
name = "B" + name[1:]             # "Blice"

# All methods return NEW strings:
upper = name.upper()              # new string "ALICE"
print(name)                       # still "Alice" — unchanged

# To "modify", reassign:
name = name.upper()               # now name points to "ALICE"
```

---

## Common String Patterns in Scripting

### Build a URL

```python
base = "https://api.example.com"
version = "v1"
resource = "clusters"
uid = "abc-123"
url = f"{base}/{version}/{resource}/{uid}"
```

### Parse a Key=Value Line

```python
line = "PALETTE_API_KEY=abc123secret"
key, value = line.split("=", 1)    # split only on first =
# key = "PALETTE_API_KEY", value = "abc123secret"
```

### Extract Filename and Extension

```python
path = "/var/log/app.log"
filename = path.rsplit("/", 1)[-1]           # "app.log"
name, ext = filename.rsplit(".", 1)          # "app", "log"
```

### Build CSV Output

```python
headers = ["Name", "IP", "Status"]
row = ["web-01", "10.0.0.1", "Running"]
print(",".join(headers))
print(",".join(row))
```

### Check and Clean User Input

```python
user_input = "  Yes  "
cleaned = user_input.strip().lower()         # "yes"
if cleaned in ("yes", "y"):
    print("Confirmed")
```

### Multi-line String with Indentation

```python
# textwrap.dedent removes common leading whitespace:
import textwrap
msg = textwrap.dedent("""\
    Name: {name}
    Age: {age}
    City: {city}
""").format(name="Alice", age=30, city="NYC")
```

---

## Shell vs Python String Operations

| Operation | Shell | Python |
|-----------|-------|--------|
| Get length | `${#var}` | `len(var)` |
| Substring | `${var:0:3}` | `var[0:3]` or `var[:3]` |
| Replace | `${var/old/new}` | `var.replace("old", "new")` |
| Replace all | `${var//old/new}` | `var.replace("old", "new")` (replaces all by default) |
| To upper | `${var^^}` | `var.upper()` |
| To lower | `${var,,}` | `var.lower()` |
| Strip prefix | `${var#pattern}` | `var.removeprefix("prefix")` (3.9+) |
| Strip suffix | `${var%pattern}` | `var.removesuffix("suffix")` (3.9+) |
| Contains | `[[ "$var" == *"sub"* ]]` | `"sub" in var` |
| Starts with | `[[ "$var" == prefix* ]]` | `var.startswith("prefix")` |
| Ends with | `[[ "$var" == *suffix ]]` | `var.endswith("suffix")` |
| Split | `IFS=',' read -ra arr <<< "$var"` | `var.split(",")` |
| Join | `IFS=','; echo "${arr[*]}"` | `",".join(arr)` |
| Trim | `xargs` or `sed` | `var.strip()` |
| Regex match | `[[ "$var" =~ pattern ]]` | `re.search(pattern, var)` |
| Interpolation | `"Hello $NAME"` | `f"Hello {name}"` |
| Concatenation | `"$a$b"` or `"${a}${b}"` | `a + b` or `f"{a}{b}"` |

---

> **Practice:** Chapter `01-absolute-basics.py` section 6
> **Next:** [05 - Operators](05-operators.md)
