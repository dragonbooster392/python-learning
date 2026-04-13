# 07 — Data Structures

Lists, dictionaries, tuples, and sets — the containers that hold your data.

---

## Table of Contents

- [Lists — Ordered, Mutable Collections](#lists-ordered-mutable-collections)
- [List Methods — Complete Reference](#list-methods-complete-reference)
- [List Slicing](#list-slicing)
- [List Comprehension](#list-comprehension)
- [Dictionaries — Key-Value Pairs](#dictionaries-key-value-pairs)
- [Dict Methods — Complete Reference](#dict-methods-complete-reference)
- [Dict Comprehension](#dict-comprehension)
- [Tuples — Immutable Sequences](#tuples-immutable-sequences)
- [Sets — Unique Items](#sets-unique-items)
- [Set Operations](#set-operations)
- [Nested Structures (JSON-like Data)](#nested-structures-json-like-data)
- [Choosing the Right Structure](#choosing-the-right-structure)
- [Common Patterns](#common-patterns)

---

## Lists — Ordered, Mutable Collections

A list holds items in order. You can add, remove, change, and reorder items.

### Creating

```python
fruits = ["apple", "banana", "cherry"]          # from literal
empty = []                                       # empty list
numbers = list(range(1, 6))                      # [1, 2, 3, 4, 5]
copies = [0] * 5                                 # [0, 0, 0, 0, 0]
from_string = list("hello")                      # ['h', 'e', 'l', 'l', 'o']
```

### Accessing Items

```python
fruits = ["apple", "banana", "cherry", "date"]
#          0        1         2         3       (forward index)
#         -4       -3        -2        -1       (reverse index)

fruits[0]                      # "apple"    (first)
fruits[2]                      # "cherry"   (third)
fruits[-1]                     # "date"     (last)
fruits[-2]                     # "cherry"   (second to last)
```

### Modifying

```python
fruits[0] = "avocado"          # replace first item
fruits.append("elderberry")    # add to end
fruits.insert(1, "blueberry")  # insert at position 1
fruits.extend(["fig", "grape"])# add multiple items
fruits.remove("banana")        # remove by value (first occurrence)
del fruits[0]                  # remove by index
last = fruits.pop()            # remove and return last item
item = fruits.pop(2)           # remove and return item at index 2
fruits.clear()                 # remove ALL items
```

### Checking

```python
"apple" in fruits              # True/False (is it in the list?)
len(fruits)                    # number of items
fruits.count("apple")          # how many times "apple" appears
fruits.index("banana")         # position of "banana" (raises ValueError if not found)
```

### Sorting

```python
fruits.sort()                  # sort in place (alphabetical)
fruits.sort(reverse=True)      # sort in place (reverse)
sorted_copy = sorted(fruits)   # return new sorted list (original unchanged)

# Sort by custom key:
servers = [{"name": "web", "load": 85}, {"name": "db", "load": 30}]
servers.sort(key=lambda s: s["load"])      # sort by load value
```

---

## List Methods — Complete Reference

| Method | What it does | Returns |
|--------|-------------|---------|
| `.append(item)` | Add item to end | `None` (modifies in place) |
| `.extend(iterable)` | Add multiple items | `None` |
| `.insert(index, item)` | Insert item at position | `None` |
| `.remove(item)` | Remove first occurrence | `None` (raises ValueError if missing) |
| `.pop(index)` | Remove and return item at index | The removed item |
| `.pop()` | Remove and return last item | The removed item |
| `.clear()` | Remove all items | `None` |
| `.index(item)` | Find position of item | Index (raises ValueError if missing) |
| `.count(item)` | Count occurrences | Number |
| `.sort()` | Sort in place | `None` |
| `.sort(reverse=True)` | Sort descending | `None` |
| `.sort(key=func)` | Sort by custom function | `None` |
| `.reverse()` | Reverse in place | `None` |
| `.copy()` | Shallow copy | New list |

---

## List Slicing

Extract portions of a list (same syntax as string slicing):

```python
nums = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

nums[2:5]                      # [2, 3, 4]      (index 2 to 4)
nums[:3]                       # [0, 1, 2]      (first 3)
nums[7:]                       # [7, 8, 9]      (from index 7 to end)
nums[-3:]                      # [7, 8, 9]      (last 3)
nums[::2]                      # [0, 2, 4, 6, 8] (every 2nd)
nums[::-1]                     # [9, 8, 7, ...0] (reversed)
nums[1:8:2]                    # [1, 3, 5, 7]   (1 to 7, step 2)
```

### Slice Assignment (Modifying)

```python
nums = [0, 1, 2, 3, 4]
nums[1:3] = [10, 20]          # [0, 10, 20, 3, 4] — replace index 1-2
nums[1:3] = [10, 20, 30]      # insert more items than removed
nums[1:4] = []                # delete index 1-3
```

---

## List Comprehension

A shorthand to build lists from loops — one of Python's most powerful features.

### Basic Form

```python
# Long way:
squares = []
for x in range(5):
    squares.append(x ** 2)

# Comprehension:
squares = [x ** 2 for x in range(5)]          # [0, 1, 4, 9, 16]
```

### With Filter

```python
# Only even numbers:
evens = [x for x in range(10) if x % 2 == 0]  # [0, 2, 4, 6, 8]

# Running clusters only:
running = [c["name"] for c in clusters if c["state"] == "Running"]
```

### With Transform

```python
# Uppercase all names:
upper_names = [name.upper() for name in names]

# Extract IPs from servers:
ips = [server["ip"] for server in servers]
```

### When NOT to Use

- When the logic is complex (multiple conditions, nested ifs)
- When readability suffers
- Use a regular loop instead for clarity

---

## Dictionaries — Key-Value Pairs

Dicts map **keys** to **values**. The most important data structure for scripting — JSON data is dicts.

### Creating

```python
server = {"name": "web-01", "ip": "10.0.0.1", "port": 8080}
empty = {}
from_pairs = dict([("a", 1), ("b", 2)])        # {"a": 1, "b": 2}
from_keys = dict.fromkeys(["a", "b", "c"], 0)  # {"a": 0, "b": 0, "c": 0}
```

### Accessing Values

```python
server["name"]                 # "web-01" (raises KeyError if missing!)
server.get("name")             # "web-01" (returns None if missing)
server.get("missing", "N/A")   # "N/A" (returns default if missing)
```

### Rule: Use `.get()` for Safety

```python
# RISKY — crashes if key doesn't exist:
value = data["key"]             # KeyError if "key" is missing!

# SAFE — returns None (or your default) if missing:
value = data.get("key")         # None if missing
value = data.get("key", "default")  # "default" if missing
```

### Modifying

```python
server["port"] = 9090          # update existing key
server["status"] = "Running"   # add new key
del server["port"]             # delete a key
removed = server.pop("status") # delete and return value
server.update({"ip": "10.0.0.2", "role": "web"})  # update multiple
```

### Iterating

```python
# Keys:
for key in server:
    print(key)                 # name, ip, port

# Values:
for value in server.values():
    print(value)

# Both:
for key, value in server.items():
    print(f"{key}: {value}")
```

### Checking

```python
"name" in server               # True (checks KEYS, not values)
"web-01" in server             # False (this is a value, not a key)
len(server)                    # number of key-value pairs
```

---

## Dict Methods — Complete Reference

| Method | What it does | Returns |
|--------|-------------|---------|
| `.get(key)` | Get value safely | Value or `None` |
| `.get(key, default)` | Get value with default | Value or default |
| `.keys()` | All keys | View of keys |
| `.values()` | All values | View of values |
| `.items()` | All key-value pairs | View of (key, value) tuples |
| `.pop(key)` | Remove key and return value | Value (KeyError if missing) |
| `.pop(key, default)` | Remove key, return default if missing | Value or default |
| `.update(other_dict)` | Merge another dict in | `None` |
| `.setdefault(key, value)` | Get key, or set it if missing | Value |
| `.clear()` | Remove all items | `None` |
| `.copy()` | Shallow copy | New dict |
| `dict.fromkeys(keys, value)` | Create dict from keys | New dict |

---

## Dict Comprehension

Build dicts from loops — just like list comprehension but with `{}` and `key: value`.

```python
# Basic:
name_lengths = {name: len(name) for name in ["Alice", "Bob", "Charlie"]}
# {"Alice": 5, "Bob": 3, "Charlie": 7}

# From list of dicts:
name_to_ip = {s["name"]: s["ip"] for s in servers}
# {"web-01": "10.0.0.1", "db-01": "10.0.0.2"}

# With filter:
running = {c["name"]: c["state"] for c in clusters if c["state"] == "Running"}

# Swap keys and values:
original = {"a": 1, "b": 2}
swapped = {v: k for k, v in original.items()}    # {1: "a", 2: "b"}
```

---

## Tuples — Immutable Sequences

Like lists, but **cannot be changed** after creation.

### Creating

```python
point = (10, 20)               # parentheses
single = (42,)                 # ONE-item tuple needs trailing comma!
not_tuple = (42)               # this is just the number 42!
empty = ()
from_list = tuple([1, 2, 3])  # convert from list
```

### Using

```python
point = (10, 20)
x = point[0]                  # 10
y = point[1]                  # 20

# Unpacking:
x, y = point                  # x=10, y=20
name, ip = ("web-01", "10.0.0.1")

# Ignore values:
name, _ = get_server_info()   # only care about name

# Capture rest:
first, *rest = (1, 2, 3, 4)   # first=1, rest=[2, 3, 4]
```

### When to Use Tuples

- **Function returning multiple values:** `return name, ip` returns a tuple
- **Dictionary keys:** Lists can't be keys, tuples can
- **Data that shouldn't change:** Coordinates, config values
- **Unpacking assignments:** `x, y, z = some_function()`

---

## Sets — Unique Items

Sets hold unique items only. Duplicates are automatically removed. **No order guaranteed.**

### Creating

```python
tags = {"web", "prod", "web"}   # {"web", "prod"} — duplicate removed
empty = set()                    # NOT {} — that's an empty dict!
from_list = set([1, 1, 2, 3])  # {1, 2, 3}
```

### Modifying

```python
tags.add("v2")                  # add one item
tags.update(["cache", "fast"])  # add multiple
tags.remove("web")              # remove (KeyError if missing)
tags.discard("web")             # remove (no error if missing)
tags.pop()                      # remove and return arbitrary item
```

---

## Set Operations

| Operation | Operator | Method | Result |
|-----------|----------|--------|--------|
| Union | `a \| b` | `a.union(b)` | All items from both |
| Intersection | `a & b` | `a.intersection(b)` | Items in both |
| Difference | `a - b` | `a.difference(b)` | Items in a but not b |
| Symmetric diff | `a ^ b` | `a.symmetric_difference(b)` | Items in one but not both |
| Subset | `a <= b` | `a.issubset(b)` | Is a entirely inside b? |
| Superset | `a >= b` | `a.issuperset(b)` | Does a contain all of b? |

```python
a = {"web", "db", "cache"}
b = {"db", "cache", "queue"}

a | b                          # {"web", "db", "cache", "queue"}
a & b                          # {"db", "cache"}
a - b                          # {"web"}
b - a                          # {"queue"}
```

### Practical Uses

```python
# Remove duplicates:
names = ["Alice", "Bob", "Alice", "Charlie"]
unique = list(set(names))            # ["Alice", "Bob", "Charlie"]

# Find common items:
team1 = {"Alice", "Bob", "Charlie"}
team2 = {"Bob", "David", "Charlie"}
both_teams = team1 & team2           # {"Bob", "Charlie"}

# Fast membership testing (O(1) vs O(n) for lists):
allowed_roles = {"admin", "editor", "viewer"}
if user_role in allowed_roles:       # instant lookup
    grant_access()
```

---

## Nested Structures (JSON-like Data)

Real-world data is almost always **nested** — lists of dicts, dicts with lists, etc. This is what JSON looks like in Python.

### API Response Pattern

```python
# This is what you get from an API (parsed JSON):
response = {
    "items": [
        {
            "metadata": {"name": "prod-cluster", "uid": "abc-123"},
            "spec": {"cloudType": "aws"},
            "status": {"state": "Running"}
        },
        {
            "metadata": {"name": "dev-cluster", "uid": "def-456"},
            "spec": {"cloudType": "azure"},
            "status": {"state": "Provisioning"}
        }
    ],
    "listmeta": {"count": 2}
}
```

### Accessing Nested Data

```python
# Get the list of items:
items = response["items"]

# Get first cluster name:
name = response["items"][0]["metadata"]["name"]    # "prod-cluster"

# Get all cluster names:
names = [item["metadata"]["name"] for item in response["items"]]

# Safe access (in case keys are missing):
state = response.get("items", [{}])[0].get("status", {}).get("state", "Unknown")
```

### Modifying Nested Data

```python
# Change a nested value:
response["items"][0]["status"]["state"] = "Stopped"

# Add to a nested list:
response["items"].append({"metadata": {"name": "new-cluster"}})
```

### Looping Over Nested Data

```python
for item in response["items"]:
    name = item["metadata"]["name"]
    state = item["status"]["state"]
    cloud = item["spec"]["cloudType"]
    print(f"{name}: {state} on {cloud}")
```

---

## Choosing the Right Structure

| I need... | Use | Why |
|-----------|-----|-----|
| Ordered collection, will modify | `list` | Add/remove/change items |
| Key-value mapping | `dict` | Look up by name/key |
| Fixed group of values | `tuple` | Immutable, for return values |
| Unique items only | `set` | Auto-removes duplicates |
| Ordered + unique | `dict.fromkeys(items)` | Dict keys are unique + ordered |
| Stack (LIFO) | `list` | Use `.append()` and `.pop()` |
| Queue (FIFO) | `collections.deque` | Efficient both-end operations |

### Performance Comparison

| Operation | List | Dict | Set |
|-----------|------|------|-----|
| Access by index | Fast | N/A | N/A |
| Access by key | N/A | Very fast | N/A |
| Check if contains | Slow (searches all) | Very fast | Very fast |
| Add item | Fast (append) | Very fast | Very fast |
| Remove item | Slow (searches) | Very fast | Very fast |
| Ordered? | Yes | Yes (3.7+) | No |

---

## Common Patterns

### Merge Two Dicts

```python
# Python 3.9+:
merged = dict1 | dict2

# Python 3.5+:
merged = {**dict1, **dict2}

# Older:
merged = dict1.copy()
merged.update(dict2)
```

### Default Dict Values

```python
# Count occurrences:
counts = {}
for word in words:
    counts[word] = counts.get(word, 0) + 1

# Or use collections.defaultdict:
from collections import defaultdict
counts = defaultdict(int)
for word in words:
    counts[word] += 1              # auto-initializes to 0
```

### Group Items

```python
# Group servers by role:
by_role = {}
for server in servers:
    role = server["role"]
    if role not in by_role:
        by_role[role] = []
    by_role[role].append(server)

# Or with defaultdict:
from collections import defaultdict
by_role = defaultdict(list)
for server in servers:
    by_role[server["role"]].append(server)
```

### Flatten a List of Lists

```python
nested = [[1, 2], [3, 4], [5, 6]]
flat = [item for sublist in nested for item in sublist]    # [1, 2, 3, 4, 5, 6]
```

### Sort Dict by Value

```python
scores = {"Alice": 90, "Bob": 75, "Charlie": 88}
sorted_names = sorted(scores, key=scores.get, reverse=True)   # ["Alice", "Charlie", "Bob"]
```

---

> **Practice:** Chapter `02-data-structures.py` all sections
> **Next:** [08 - Functions](08-functions.md)
