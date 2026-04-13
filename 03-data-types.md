# 03 — Data Types

Every value in Python has a type. This page covers each type in depth, when to use it, and how they interact.

---

## Table of Contents

- [Overview — All Types at a Glance](#overview-all-types-at-a-glance)
- [int — Whole Numbers](#int-whole-numbers)
- [float — Decimal Numbers](#float-decimal-numbers)
- [bool — True or False](#bool-true-or-false)
- [str — Text](#str-text)
- [NoneType — Nothing](#nonetype-nothing)
- [list — Ordered Collection](#list-ordered-collection)
- [dict — Key-Value Pairs](#dict-key-value-pairs)
- [tuple — Unchangeable List](#tuple-unchangeable-list)
- [set — Unique Items Only](#set-unique-items-only)
- [bytes — Raw Binary Data](#bytes-raw-binary-data)
- [Type Hierarchy](#type-hierarchy)
- [Type Conversion Summary](#type-conversion-summary)
- [How to Choose the Right Type](#how-to-choose-the-right-type)

---

## Overview — All Types at a Glance

| Type | Category | Mutable? | Ordered? | Example | Common Use |
|------|----------|----------|----------|---------|------------|
| `int` | Numeric | No | — | `42` | Counts, IDs, ports |
| `float` | Numeric | No | — | `3.14` | Prices, measurements |
| `bool` | Numeric* | No | — | `True` | Flags, conditions |
| `str` | Sequence | No | Yes | `"hello"` | Text, names, messages |
| `list` | Sequence | **Yes** | Yes | `[1, 2, 3]` | Collections, API results |
| `tuple` | Sequence | No | Yes | `(1, 2)` | Fixed data, return values |
| `dict` | Mapping | **Yes** | Yes** | `{"a": 1}` | JSON data, config, records |
| `set` | Collection | **Yes** | No | `{1, 2, 3}` | Unique items, membership |
| `bytes` | Sequence | No | Yes | `b"data"` | Binary files, network |
| `NoneType` | Special | No | — | `None` | Empty/absent values |

*`bool` is technically a subclass of `int` — `True` equals `1`, `False` equals `0`.
**`dict` is ordered by insertion order since Python 3.7.

---

## int — Whole Numbers

Integers are whole numbers with no decimal point. No size limit.

### Creating

```python
x = 42
negative = -5
zero = 0
big = 1_000_000              # underscores for readability (= 1000000)
```

### Number Bases

```python
decimal = 255                 # base 10 (normal)
hex_num = 0xFF                # hexadecimal (base 16) = 255
octal = 0o377                 # octal (base 8) = 255
binary = 0b11111111           # binary (base 2) = 255
```

### Key Facts

- **No size limit.** Unlike C/Java, Python ints can be arbitrarily large: `2 ** 1000` works fine.
- **Immutable.** `x = 5; x += 1` doesn't modify 5 — it creates a new int (6) and re-points x.
- **Integer division** (`//`) truncates: `7 // 2 = 3` (not 3.5).
- **No `++` or `--`.** Use `x += 1` or `x -= 1`.

### Useful Functions

```python
abs(-42)                      # 42 (absolute value)
pow(2, 10)                    # 1024 (same as 2 ** 10)
divmod(17, 5)                 # (3, 2) — quotient and remainder
max(3, 7, 1)                  # 7
min(3, 7, 1)                  # 1
```

---

## float — Decimal Numbers

Floating-point numbers represent decimals.

### Creating

```python
pi = 3.14159
price = 19.99
negative = -0.5
scientific = 1.5e3            # 1500.0 (1.5 × 10³)
tiny = 2.5e-4                 # 0.00025 (2.5 × 10⁻⁴)
from_int = float(42)          # 42.0
```

### The Precision Problem

Floats are stored in binary, so some decimals can't be represented exactly:

```python
0.1 + 0.2                    # 0.30000000000000004 (NOT 0.3!)
```

This is true in **every** programming language. For scripting, it rarely matters. For money calculations, use the `decimal` module.

### Special Float Values

```python
float('inf')                  # positive infinity
float('-inf')                 # negative infinity
float('nan')                  # NaN (not a number)

import math
math.isinf(float('inf'))     # True
math.isnan(float('nan'))     # True
```

### Rounding

```python
round(3.7)                    # 4
round(3.14159, 2)             # 3.14 (2 decimal places)
round(2.5)                    # 2 (Python uses "banker's rounding" — rounds to even)
round(3.5)                    # 4

import math
math.floor(3.7)               # 3 (always round down)
math.ceil(3.2)                # 4 (always round up)
```

---

## bool — True or False

Booleans represent truth values.

### Only Two Values

```python
is_ready = True               # note: capital T
is_done = False               # note: capital F
```

### Shell vs Python

| Shell | Python |
|-------|--------|
| `true` (lowercase, a command) | `True` (uppercase, a value) |
| `false` (lowercase, a command) | `False` (uppercase, a value) |
| Exit code `0` = success/true | `True` = true |
| Exit code `1` = failure/false | `False` = false |

### bool Is Actually a Number

```python
True == 1                     # True
False == 0                    # True
True + True                   # 2
True * 10                     # 10
```

This means you can count `True` values:

```python
statuses = [True, False, True, True, False]
sum(statuses)                 # 3 (counts the True values)
```

### Comparisons Return bool

```python
5 > 3                         # True
5 == 3                        # False
"abc" in "abcdef"             # True
```

### Boolean Operators

```python
True and False                # False (both must be True)
True or False                 # True (at least one must be True)
not True                      # False (flip)
not False                     # True
```

### Short-Circuit Evaluation

Python stops evaluating as soon as it knows the result:

```python
# 'and' — stops at first False:
False and expensive_function()    # expensive_function() never runs!

# 'or' — stops at first True:
True or expensive_function()      # expensive_function() never runs!
```

This matters in patterns like:

```python
# Safe: if result is None, .get() never runs
result = data and data.get("key")
```

---

## str — Text

Covered in depth in [04 - Strings](04-strings.md). Quick summary:

- Text wrapped in quotes: `"hello"` or `'hello'`
- **Immutable** — methods return new strings, don't modify in place
- f-strings for interpolation: `f"Hello {name}"`
- Rich set of methods: `.upper()`, `.split()`, `.strip()`, `.replace()`, etc.

---

## NoneType — Nothing

`None` is Python's null/nothing/unset value. There's only ONE `None` object.

### When You Encounter None

```python
# 1. Explicit assignment
result = None

# 2. Function without return
def do_work():
    print("done")
x = do_work()                 # x is None

# 3. Dict.get() for missing key
d = {"a": 1}
d.get("b")                    # None (key "b" doesn't exist)

# 4. Default parameter values
def func(x=None):
    if x is None:
        x = []                # create default inside function
```

### Checking

```python
if x is None:                 # CORRECT — use 'is'
if x is not None:             # CORRECT

if x == None:                 # works but NOT recommended
if x:                         # checks truthiness, not None specifically
                              # 0, "", [] also fail this check
```

### None vs Empty vs Zero vs False

| Value | Type | Meaning |
|-------|------|---------|
| `None` | NoneType | "Not set" / "No value" / "Absent" |
| `""` | str | Empty text — value exists, it's just empty |
| `0` | int | Zero — value exists, it's zero |
| `False` | bool | Explicit false |
| `[]` | list | Empty list — container exists, it's just empty |

These are all **different things** but all **falsy** (treated as False in conditions).

---

## list — Ordered Collection

Lists hold multiple items in order. They're mutable — you can add, remove, and change items.

Covered in depth in [07 - Data Structures](07-data-structures.md). Quick reference:

```python
fruits = ["apple", "banana", "cherry"]
fruits[0]                     # "apple" (first item)
fruits[-1]                    # "cherry" (last item)
fruits.append("date")         # add to end
fruits.remove("banana")       # remove by value
len(fruits)                   # 3 (count items)
"apple" in fruits             # True (check membership)
```

---

## dict — Key-Value Pairs

Dictionaries map keys to values. They're the Python equivalent of JSON objects.

Covered in depth in [07 - Data Structures](07-data-structures.md). Quick reference:

```python
server = {"name": "web-01", "ip": "10.0.0.1"}
server["name"]                # "web-01" (access by key)
server["port"] = 8080         # add/update
server.get("missing")         # None (safe access)
"name" in server              # True (check key exists)
```

---

## tuple — Unchangeable List

Tuples are like lists but **immutable** — once created, you can't add, remove, or change items.

```python
point = (10, 20)              # parentheses (or even without)
x, y = point                  # unpack: x=10, y=20
```

### When to Use Tuples

| Use Case | Why Tuple |
|----------|-----------|
| Function returning multiple values | Natural: `return name, ip` |
| Data that shouldn't change | Protection against accidental modification |
| Dictionary keys | Lists can't be dict keys, tuples can |
| Unpacking values | Clean: `host, port = get_server()` |

### Tuple vs List

| Feature | List `[]` | Tuple `()` |
|---------|-----------|------------|
| Mutable? | Yes — add, remove, change | No — fixed once created |
| Syntax | `[1, 2, 3]` | `(1, 2, 3)` |
| Use for | Collections that change | Fixed data, return values |
| Performance | Slightly slower | Slightly faster |
| As dict key? | No | Yes |

### Single-Item Tuple Gotcha

```python
not_a_tuple = (42)            # just the number 42 with parentheses!
is_a_tuple = (42,)            # a tuple with one item — note the comma
```

---

## set — Unique Items Only

Sets store unique items. Duplicates are automatically removed. Order is not guaranteed.

```python
tags = {"web", "prod", "web"}  # {"web", "prod"} — duplicate removed
```

### When to Use Sets

```python
# Remove duplicates from a list:
names = ["Alice", "Bob", "Alice", "Charlie", "Bob"]
unique = list(set(names))           # ["Alice", "Bob", "Charlie"]

# Fast membership testing (faster than list):
allowed = {"admin", "editor", "viewer"}
if role in allowed:                 # O(1) lookup — instant
    print("OK")

# Set operations:
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}
a | b                               # {1, 2, 3, 4, 5, 6} — union
a & b                               # {3, 4} — intersection
a - b                               # {1, 2} — difference
a ^ b                               # {1, 2, 5, 6} — symmetric difference
```

---

## bytes — Raw Binary Data

Bytes represent raw binary data. Used for files, network, encoding.

```python
data = b"hello"                     # bytes literal (prefix b)
data = "hello".encode("utf-8")      # string → bytes
text = data.decode("utf-8")         # bytes → string

# Reading a binary file:
with open("image.png", "rb") as f:
    content = f.read()              # content is bytes
```

For scripting, you'll encounter bytes when:
- Working with file uploads/downloads
- Encoding/decoding text
- Network responses (raw)

Most of the time, you'll work with strings. Bytes are for special cases.

---

## Type Hierarchy

How Python types relate to each other:

```
object ← everything inherits from this
├── int
│   └── bool                   ← bool IS a subclass of int
├── float
├── str
├── bytes
├── NoneType
├── list
├── tuple
├── dict
├── set
└── ... (many more)
```

Key insight: `bool` inherits from `int`. That's why `True + True = 2` and `isinstance(True, int)` returns `True`.

---

## Type Conversion Summary

### Implicit (Automatic)

Python sometimes converts types automatically:

```python
3 + 2.5               # int + float → float (5.5)
True + 3              # bool + int → int (4)
```

### Explicit (Manual)

You must convert yourself:

```python
"42" + 8              # TypeError! Can't add string + int
int("42") + 8         # 50 — convert first
```

### Quick Reference

| Convert to | Function | Example |
|------------|----------|---------|
| Integer | `int()` | `int("42")` → `42` |
| Float | `float()` | `float("3.14")` → `3.14` |
| String | `str()` | `str(42)` → `"42"` |
| Boolean | `bool()` | `bool(0)` → `False` |
| List | `list()` | `list("abc")` → `['a','b','c']` |
| Tuple | `tuple()` | `tuple([1,2])` → `(1, 2)` |
| Set | `set()` | `set([1,1,2])` → `{1, 2}` |
| Dict | `dict()` | `dict([("a",1)])` → `{"a": 1}` |

---

## How to Choose the Right Type

| I need to store... | Use | Why |
|--------------------|-----|-----|
| A single text value | `str` | Text data |
| A whole number | `int` | Counts, IDs, ports |
| A decimal number | `float` | Measurements, percentages |
| A yes/no flag | `bool` | Conditions |
| "Nothing" / "not set yet" | `None` | Absence of value |
| An ordered collection I'll modify | `list` | Most common collection |
| A fixed group of values | `tuple` | Return values, constants |
| Named fields (like a record) | `dict` | JSON data, config, server info |
| Unique items / membership testing | `set` | Remove duplicates, fast lookup |
| Raw binary | `bytes` | Files, network, encoding |

---

> **Practice:** Chapter `01-absolute-basics.py` section 5, Chapter `02-data-structures.py` all sections
> **Next:** [04 - Strings](04-strings.md)
