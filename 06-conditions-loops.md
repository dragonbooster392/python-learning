# 06 — Conditions and Loops

Control flow — how your code makes decisions and repeats actions.

---

## Table of Contents

- [if / elif / else — Making Decisions](#if-elif-else-making-decisions)
- [Indentation Defines Blocks](#indentation-defines-blocks)
- [Comparison Patterns](#comparison-patterns)
- [Ternary (Inline If)](#ternary-inline-if)
- [for Loop — Iterating Over Things](#for-loop-iterating-over-things)
- [range() — Generating Numbers](#range-generating-numbers)
- [enumerate() — Loop with Index](#enumerate-loop-with-index)
- [while Loop — Repeat While Condition True](#while-loop-repeat-while-condition-true)
- [break — Exit Loop Early](#break-exit-loop-early)
- [continue — Skip to Next Iteration](#continue-skip-to-next-iteration)
- [else on Loops](#else-on-loops)
- [Nested Loops](#nested-loops)
- [Loop Patterns for Scripting](#loop-patterns-for-scripting)
- [pass — Do Nothing Placeholder](#pass-do-nothing-placeholder)
- [match / case (Python 3.10+)](#match-case-python-310)
- [Shell vs Python Control Flow](#shell-vs-python-control-flow)

---

## if / elif / else — Making Decisions

The most basic control structure. Run different code based on conditions.

### Structure

```python
if condition:
    # runs if condition is True
elif another_condition:
    # runs if first was False but this is True
else:
    # runs if all above were False
```

### Rules

1. **Colon (`:`)** is required after `if`, `elif`, `else`
2. **Indentation** (4 spaces) defines what's inside the block
3. `elif` is optional — use as many as needed
4. `else` is optional — catches everything not caught above
5. **Only one block runs** — first matching condition wins, rest are skipped

### Examples

```python
# Simple check:
if age >= 18:
    print("Adult")

# If-else:
if balance > 0:
    print("Positive")
else:
    print("Zero or negative")

# If-elif-else:
status = "Running"
if status == "Running":
    print("All good")
elif status == "Provisioning":
    print("Still starting up")
elif status == "Error":
    print("Something went wrong!")
else:
    print(f"Unknown status: {status}")
```

### Combining Conditions

```python
# AND — both must be true:
if age >= 18 and has_ticket:
    print("Can enter")

# OR — at least one must be true:
if is_admin or is_owner:
    print("Access granted")

# NOT — flip the condition:
if not is_deleted:
    print("Active")

# Complex:
if (state == "Running" or state == "Ready") and not is_maintenance:
    print("Available")
```

### Checking Multiple Values

```python
# Instead of:
if state == "Running" or state == "Ready" or state == "Active":

# Use 'in':
if state in ("Running", "Ready", "Active"):
    print("OK")

# Check if NOT one of several values:
if state not in ("Error", "Failed", "Deleted"):
    print("OK")
```

### Checking for None / Empty

```python
# Check for None:
if result is None:
    print("No result")

if result is not None:
    print(f"Got: {result}")

# Check for empty (truthy/falsy):
if not items:                      # empty list, empty string, None, 0
    print("Nothing here")

if items:                          # non-empty list, non-empty string
    print("Has items")
```

---

## Indentation Defines Blocks

The indented code after a colon belongs to that block. When indentation returns to the previous level, the block ends.

```python
print("Before")                    # always runs

if x > 10:
    print("Inside if")             # only if x > 10
    print("Still inside if")       # only if x > 10
    if x > 100:
        print("Inside nested if")  # only if x > 100
    print("Back to first if")      # only if x > 10

print("After — always runs")       # always runs (back to base indentation)
```

There is **no `fi`, no `end`, no `}`**. The indentation IS the structure.

---

## Comparison Patterns

### Value Comparisons

```python
if x == 5:            # equal
if x != 5:            # not equal
if x > 5:             # greater than
if x < 5:             # less than
if x >= 5:            # greater or equal
if x <= 5:            # less or equal
```

### Chained Comparisons

```python
if 0 < age < 150:                  # between 0 and 150
if 1 <= x <= 10:                   # between 1 and 10 inclusive
```

### Type Checking

```python
if isinstance(x, str):             # is x a string?
if isinstance(x, (int, float)):    # is x a number?
```

### Dictionary Key Checking

```python
if "name" in config:               # does key exist?
    value = config["name"]

# Or use .get() to avoid the check:
value = config.get("name", "default")    # returns "default" if missing
```

---

## Ternary (Inline If)

One-line if-else expression — returns a value.

```python
# Full form:
value = true_result if condition else false_result

# Examples:
label = "adult" if age >= 18 else "minor"
status = "ON" if is_active else "OFF"
items = result if result is not None else []
```

### Shell Comparison

```bash
# Shell:
[[ $age -ge 18 ]] && echo "adult" || echo "minor"

# Python:
label = "adult" if age >= 18 else "minor"
```

### Don't Overuse

If the logic is complex, use a regular if-else. Ternary is for **simple** one-liners only.

```python
# TOO COMPLEX for ternary:
result = "A" if x > 90 else "B" if x > 80 else "C" if x > 70 else "F"

# Better as regular if-elif:
if x > 90:
    result = "A"
elif x > 80:
    result = "B"
elif x > 70:
    result = "C"
else:
    result = "F"
```

---

## for Loop — Iterating Over Things

Python's `for` loop iterates over items in a sequence (list, string, range, dict, etc.)

### Basic Syntax

```python
for item in collection:
    # do something with item
```

### Iterating Over Different Types

```python
# List:
for fruit in ["apple", "banana", "cherry"]:
    print(fruit)

# String (character by character):
for char in "Hello":
    print(char)                    # H, e, l, l, o

# Dictionary keys:
config = {"url": "http://...", "key": "abc"}
for key in config:
    print(f"{key}: {config[key]}")

# Dictionary key-value pairs:
for key, value in config.items():
    print(f"{key}: {value}")

# Set:
for tag in {"web", "prod", "v2"}:
    print(tag)                     # order not guaranteed

# File lines:
with open("file.txt") as f:
    for line in f:
        print(line.strip())
```

---

## range() — Generating Numbers

`range()` creates a sequence of numbers for looping.

| Call | Generates | Note |
|------|-----------|------|
| `range(5)` | 0, 1, 2, 3, 4 | Start at 0, end exclusive |
| `range(1, 6)` | 1, 2, 3, 4, 5 | Custom start, end exclusive |
| `range(0, 10, 2)` | 0, 2, 4, 6, 8 | Step by 2 |
| `range(10, 0, -1)` | 10, 9, 8, ..., 1 | Count down |
| `range(5, 0, -1)` | 5, 4, 3, 2, 1 | Count down |

### Critical: End Is EXCLUSIVE

`range(1, 5)` gives 1, 2, 3, 4 — **not** 5. To include 5, use `range(1, 6)`.

### Shell Comparison

```bash
# Shell:
for i in $(seq 1 10); do echo $i; done
for i in {1..10}; do echo $i; done

# Python:
for i in range(1, 11):       # note: 11 to include 10
    print(i)
```

---

## enumerate() — Loop with Index

When you need both the **position** and the **value**:

```python
fruits = ["apple", "banana", "cherry"]

for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")
# 0: apple
# 1: banana
# 2: cherry

# Start counting from 1:
for num, fruit in enumerate(fruits, start=1):
    print(f"{num}. {fruit}")
# 1. apple
# 2. banana
# 3. cherry
```

### Without enumerate (Don't Do This)

```python
# WRONG way — manual counter:
i = 0
for fruit in fruits:
    print(f"{i}: {fruit}")
    i += 1

# RIGHT way — use enumerate:
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")
```

---

## while Loop — Repeat While Condition True

Keeps running as long as the condition is `True`.

```python
count = 0
while count < 5:
    print(count)
    count += 1                     # MUST change condition or infinite loop!
# prints: 0, 1, 2, 3, 4
```

### Common Uses

```python
# Wait for a condition:
while not is_ready():
    time.sleep(5)
    print("Waiting...")

# Read until end:
while True:
    line = input("Enter (q to quit): ")
    if line == "q":
        break
    print(f"You said: {line}")

# Retry loop:
attempts = 0
while attempts < 3:
    try:
        result = risky_operation()
        break                      # success → exit loop
    except Exception:
        attempts += 1
        print(f"Attempt {attempts} failed")
```

### Infinite Loop

```python
# This runs forever until break:
while True:
    command = input("> ")
    if command == "exit":
        break
    process(command)
```

### Shell Comparison

```bash
# Shell:
while [ "$count" -lt 5 ]; do
    echo "$count"
    count=$((count + 1))
done

# Python:
count = 0
while count < 5:
    print(count)
    count += 1
```

---

## break — Exit Loop Early

Immediately stops the loop and continues after it.

```python
for i in range(100):
    if i == 5:
        break                      # stop at 5
    print(i)
# prints: 0, 1, 2, 3, 4

# Find first match:
for cluster in clusters:
    if cluster["state"] == "Running":
        found = cluster
        break                      # stop looking once found
```

---

## continue — Skip to Next Iteration

Skips the rest of the current iteration and moves to the next one.

```python
for i in range(5):
    if i == 2:
        continue                   # skip 2
    print(i)
# prints: 0, 1, 3, 4

# Skip invalid items:
for item in items:
    if item is None:
        continue                   # skip None items
    process(item)
```

---

## else on Loops

Python has an unusual feature: `else` on a loop runs **only if the loop completed normally** (no `break`).

```python
# Find a value — else runs if NOT found:
for cluster in clusters:
    if cluster["name"] == target:
        print(f"Found: {cluster}")
        break
else:
    print(f"Not found: {target}")     # only runs if break never happened
```

This is uncommon but useful for search patterns.

---

## Nested Loops

Loops inside loops — be careful with indentation.

```python
for env in ["prod", "staging", "dev"]:
    for role in ["web", "db", "cache"]:
        print(f"{env}-{role}")
# prod-web, prod-db, prod-cache, staging-web, ...

# Breaking from nested loops:
found = False
for env in environments:
    for server in env["servers"]:
        if server["name"] == target:
            found = True
            break                  # only breaks inner loop!
    if found:
        break                     # break outer loop too
```

---

## Loop Patterns for Scripting

### Build a List from a Loop

```python
# Basic:
results = []
for item in data:
    if item["status"] == "active":
        results.append(item["name"])

# Comprehension (same result, one line):
results = [item["name"] for item in data if item["status"] == "active"]
```

### Build a Dict from a Loop

```python
name_to_ip = {}
for server in servers:
    name_to_ip[server["name"]] = server["ip"]

# Comprehension:
name_to_ip = {s["name"]: s["ip"] for s in servers}
```

### Count Items

```python
count = 0
for item in items:
    if item["state"] == "Running":
        count += 1

# Or use sum with a generator:
count = sum(1 for item in items if item["state"] == "Running")
```

### Find First Match

```python
found = None
for item in items:
    if item["name"] == target:
        found = item
        break
# found is None if not found, or the item if found

# Or use next():
found = next((item for item in items if item["name"] == target), None)
```

### Iterate with zip() — Parallel Iteration

```python
names = ["web-01", "db-01", "cache-01"]
ips = ["10.0.0.1", "10.0.0.2", "10.0.0.3"]

for name, ip in zip(names, ips):
    print(f"{name}: {ip}")
# web-01: 10.0.0.1
# db-01: 10.0.0.2
# cache-01: 10.0.0.3
```

### Loop Over Dict Items

```python
config = {"url": "https://api.example.com", "key": "abc", "timeout": 30}

# Keys only:
for key in config:
    print(key)

# Values only:
for value in config.values():
    print(value)

# Both key and value:
for key, value in config.items():
    print(f"{key} = {value}")
```

---

## pass — Do Nothing Placeholder

`pass` is a placeholder that does nothing. Use it when Python requires code but you haven't written it yet.

```python
# Empty function (will implement later):
def process_cluster():
    pass

# Empty if branch:
if state == "Running":
    pass                           # TODO: handle this
elif state == "Error":
    alert_admin()

# Empty class:
class CustomError(Exception):
    pass

# Empty loop body:
for item in items:
    pass                           # placeholder
```

---

## match / case (Python 3.10+)

Python 3.10 added structural pattern matching — like shell's `case` statement.

```python
status = "Running"

match status:
    case "Running":
        print("All good")
    case "Provisioning" | "Creating":
        print("Starting up")
    case "Error":
        print("Problem!")
    case _:
        print(f"Unknown: {status}")    # _ is the default/catch-all
```

### Shell Comparison

```bash
case "$status" in
    Running)     echo "All good" ;;
    Error)       echo "Problem!" ;;
    *)           echo "Unknown" ;;
esac
```

### Advanced Patterns

```python
# Match with conditions:
match command.split():
    case ["get", name]:
        get_item(name)
    case ["set", name, value]:
        set_item(name, value)
    case ["delete", name]:
        delete_item(name)
    case _:
        print("Unknown command")
```

**Note:** If your Python is older than 3.10, use `if/elif/else` instead — it works everywhere.

---

## Shell vs Python Control Flow

### If/Else

```bash
# Shell:
if [ "$state" = "Running" ]; then
    echo "Running"
elif [ "$state" = "Error" ]; then
    echo "Error"
else
    echo "Unknown"
fi
```

```python
# Python:
if state == "Running":
    print("Running")
elif state == "Error":
    print("Error")
else:
    print("Unknown")
```

### For Loop

```bash
# Shell:
for item in apple banana cherry; do
    echo "$item"
done

for i in $(seq 1 5); do
    echo "$i"
done

for file in *.txt; do
    echo "$file"
done
```

```python
# Python:
for item in ["apple", "banana", "cherry"]:
    print(item)

for i in range(1, 6):
    print(i)

import glob
for file in glob.glob("*.txt"):
    print(file)
```

### While Loop

```bash
# Shell:
count=0
while [ "$count" -lt 5 ]; do
    echo "$count"
    count=$((count + 1))
done
```

```python
# Python:
count = 0
while count < 5:
    print(count)
    count += 1
```

### Case Statement

```bash
# Shell:
case "$status" in
    Running)  echo "OK" ;;
    Error)    echo "BAD" ;;
    *)        echo "UNKNOWN" ;;
esac
```

```python
# Python (3.10+):
match status:
    case "Running":  print("OK")
    case "Error":    print("BAD")
    case _:          print("UNKNOWN")

# Python (any version):
if status == "Running":    print("OK")
elif status == "Error":    print("BAD")
else:                      print("UNKNOWN")
```

---

> **Practice:** Chapter `01-absolute-basics.py` sections 10-11
> **Next:** [07 - Data Structures](07-data-structures.md)
