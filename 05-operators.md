# 05 — Operators

Operators are symbols that perform operations on values. This page covers every operator you'll use in scripting.

---

## Table of Contents

- [Arithmetic Operators](#arithmetic-operators)
- [Assignment Operators](#assignment-operators)
- [Comparison Operators](#comparison-operators)
- [Logical Operators (and / or / not)](#logical-operators-and-or-not)
- [Membership Operators (in / not in)](#membership-operators-in-not-in)
- [Identity Operators (is / is not)](#identity-operators-is-is-not)
- [Bitwise Operators](#bitwise-operators)
- [Operator Precedence](#operator-precedence)
- [Shell vs Python Operators](#shell-vs-python-operators)

---

## Arithmetic Operators

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| `+` | Addition | `10 + 3` | `13` |
| `-` | Subtraction | `10 - 3` | `7` |
| `*` | Multiplication | `10 * 3` | `30` |
| `/` | Division (always float) | `10 / 3` | `3.333...` |
| `//` | Floor division (truncate) | `10 // 3` | `3` |
| `%` | Modulo (remainder) | `10 % 3` | `1` |
| `**` | Exponent (power) | `2 ** 3` | `8` |

### Key Points

- **`/` always returns float**, even for whole numbers: `10 / 2` → `5.0`
- **`//` always rounds down** (toward negative infinity): `7 // 2` → `3`, `-7 // 2` → `-4`
- **`%` (modulo)** gives the remainder: `17 % 5` → `2` (17 ÷ 5 = 3 remainder 2)
- **`**` is power**: `2 ** 10` → `1024`
- **No `++` or `--`** in Python. Use `x += 1` or `x -= 1`.

### With Strings and Lists

```python
# + concatenates strings:
"Hello" + " " + "World"           # "Hello World"

# * repeats strings:
"-" * 40                           # "----------------------------------------"

# + concatenates lists:
[1, 2] + [3, 4]                   # [1, 2, 3, 4]

# * repeats lists:
[0] * 5                           # [0, 0, 0, 0, 0]
```

---

## Assignment Operators

| Operator | Meaning | Equivalent |
|----------|---------|------------|
| `=` | Assign value | `x = 5` |
| `+=` | Add and assign | `x = x + 5` |
| `-=` | Subtract and assign | `x = x - 5` |
| `*=` | Multiply and assign | `x = x * 5` |
| `/=` | Divide and assign | `x = x / 5` |
| `//=` | Floor divide and assign | `x = x // 5` |
| `%=` | Modulo and assign | `x = x % 5` |
| `**=` | Power and assign | `x = x ** 5` |

### Common Usage

```python
count = 0
count += 1                         # increment by 1
count += 10                        # increment by 10

total = 100
total -= 25                        # subtract
total *= 2                         # double

# Also works with strings:
message = "Hello"
message += " World"                # "Hello World"

# And lists:
items = [1, 2]
items += [3, 4]                    # [1, 2, 3, 4]
```

### Walrus Operator `:=` (Python 3.8+)

Assigns a value AND returns it in one expression:

```python
# Without walrus:
line = input("Enter: ")
while line != "quit":
    print(f"You said: {line}")
    line = input("Enter: ")

# With walrus:
while (line := input("Enter: ")) != "quit":
    print(f"You said: {line}")
```

Used in spectro-cli patterns like:

```python
if (result := client.get_cluster(name)) is not None:
    print(result)
```

You won't need this for basic scripting, but you'll see it in code.

---

## Comparison Operators

All comparisons return `True` or `False`.

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| `==` | Equal | `5 == 5` | `True` |
| `!=` | Not equal | `5 != 3` | `True` |
| `<` | Less than | `3 < 5` | `True` |
| `>` | Greater than | `5 > 3` | `True` |
| `<=` | Less than or equal | `5 <= 5` | `True` |
| `>=` | Greater than or equal | `5 >= 3` | `True` |

### Important: `=` vs `==`

```python
x = 5                              # ASSIGNMENT: put 5 into x
x == 5                             # COMPARISON: is x equal to 5? → True
```

This is a common mistake. Python will give `SyntaxError` if you use `=` in a condition — it protects you.

### Chained Comparisons

Python allows chaining, which is cleaner than shell:

```python
# Python (clean):
if 0 < age < 120:
    print("Valid age")

# Equivalent to:
if 0 < age and age < 120:
    print("Valid age")

# Shell (verbose):
# if [ "$age" -gt 0 ] && [ "$age" -lt 120 ]; then
```

### Comparing Different Types

```python
5 == 5.0                           # True (int and float compare naturally)
5 == "5"                           # False (different types — no auto-conversion!)
True == 1                          # True (bool is a subclass of int)
False == 0                         # True
None == False                      # False (None is NOT False)
```

### String Comparison

```python
"abc" == "abc"                     # True
"abc" == "ABC"                     # False (case-sensitive!)
"abc" < "def"                      # True (alphabetical/lexicographic order)
"apple" < "banana"                 # True
```

---

## Logical Operators (and / or / not)

Combine conditions together.

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| `and` | Both must be True | `True and False` | `False` |
| `or` | At least one must be True | `True or False` | `True` |
| `not` | Flip True/False | `not True` | `False` |

### Shell Comparison

| Shell | Python |
|-------|--------|
| `&&` | `and` |
| `\|\|` | `or` |
| `!` | `not` |

### Truth Tables

**`and`** — returns True only if BOTH are True:

| A | B | A and B |
|---|---|---------|
| True | True | True |
| True | False | False |
| False | True | False |
| False | False | False |

**`or`** — returns True if AT LEAST ONE is True:

| A | B | A or B |
|---|---|--------|
| True | True | True |
| True | False | True |
| False | True | True |
| False | False | False |

**`not`** — flips the value:

| A | not A |
|---|-------|
| True | False |
| False | True |

### Short-Circuit Evaluation

Python stops evaluating as soon as it knows the answer:

```python
# 'and' stops at first falsy value:
False and print("never runs")      # print() never executes

# 'or' stops at first truthy value:
True or print("never runs")        # print() never executes
```

### Practical: Default Values with `or`

```python
name = user_input or "Anonymous"
# If user_input is empty/None/falsy → name = "Anonymous"
# If user_input has a value → name = user_input
```

### Practical: Safe Access with `and`

```python
result = data and data.get("items")
# If data is None/empty → result = None (skips .get())
# If data has value → result = data.get("items")
```

### What `and`/`or` Actually Return

They DON'T always return True/False — they return one of the actual values:

```python
"hello" and "world"                # "world" (first truthy → continue → return last)
"" and "world"                     # "" (first falsy → stop → return it)
"hello" or "world"                 # "hello" (first truthy → stop → return it)
"" or "world"                      # "world" (first falsy → continue → return last)
0 or "" or None or "found!"       # "found!" (keep going until truthy)
```

---

## Membership Operators (in / not in)

Check if a value exists inside a collection.

| Operator | Meaning | Example | Result |
|----------|---------|---------|--------|
| `in` | Is inside? | `"a" in "abc"` | `True` |
| `not in` | Is NOT inside? | `"x" not in "abc"` | `True` |

### Works With

```python
# Strings — checks for substring:
"World" in "Hello World"           # True
"xyz" in "Hello World"             # False

# Lists — checks for element:
3 in [1, 2, 3, 4]                  # True
99 in [1, 2, 3]                    # False

# Dicts — checks for KEY (not value):
"name" in {"name": "Alice"}        # True
"Alice" in {"name": "Alice"}       # False (Alice is a value, not a key)

# Sets — checks for element (very fast):
"admin" in {"admin", "user"}       # True

# Tuples:
2 in (1, 2, 3)                     # True
```

### Shell Comparison

```bash
# Shell: check if string contains substring
[[ "$text" == *"World"* ]]

# Python:
"World" in text
```

Python's `in` is much cleaner.

---

## Identity Operators (is / is not)

Check if two variables point to the **exact same object** in memory.

| Operator | Meaning |
|----------|---------|
| `is` | Same object in memory |
| `is not` | Different objects in memory |

### When to Use

**Only use `is` for:**

```python
x is None                         # checking for None
x is not None                     # checking for not-None
x is True                         # checking exact True (rarely needed)
x is False                        # checking exact False (rarely needed)
```

**Never use `is` for value comparison:**

```python
# WRONG:
x is 5                            # might work for small numbers, unreliable!
name is "Alice"                    # unreliable!

# CORRECT:
x == 5
name == "Alice"
```

### `is` vs `==`

- `is` → "Are these the same object?" (identity)
- `==` → "Do these have the same value?" (equality)

```python
a = [1, 2, 3]
b = [1, 2, 3]
a == b                             # True (same value)
a is b                             # False (different objects in memory)

c = a
a is c                             # True (c points to same object as a)
```

---

## Bitwise Operators

Used for low-level bit manipulation. You'll rarely need these in scripting, but they exist:

| Operator | Name | Example | Result |
|----------|------|---------|--------|
| `&` | AND | `5 & 3` | `1` |
| `\|` | OR | `5 \| 3` | `7` |
| `^` | XOR | `5 ^ 3` | `6` |
| `~` | NOT | `~5` | `-6` |
| `<<` | Left shift | `5 << 1` | `10` |
| `>>` | Right shift | `5 >> 1` | `2` |

**Note:** `|` and `&` on **sets** do union and intersection, which is more commonly used in scripting:

```python
set_a = {1, 2, 3}
set_b = {3, 4, 5}
set_a | set_b                      # {1, 2, 3, 4, 5} — union
set_a & set_b                      # {3} — intersection
```

---

## Operator Precedence

When multiple operators are used, Python follows this order (highest priority first):

| Priority | Operators | Description |
|----------|-----------|-------------|
| 1 (highest) | `**` | Exponent |
| 2 | `~`, `+x`, `-x` | Unary NOT, positive, negative |
| 3 | `*`, `/`, `//`, `%` | Multiply, divide, floor-div, modulo |
| 4 | `+`, `-` | Add, subtract |
| 5 | `<<`, `>>` | Bit shifts |
| 6 | `&` | Bitwise AND |
| 7 | `^` | Bitwise XOR |
| 8 | `\|` | Bitwise OR |
| 9 | `==`, `!=`, `<`, `>`, `<=`, `>=`, `is`, `in` | Comparison |
| 10 | `not` | Logical NOT |
| 11 | `and` | Logical AND |
| 12 (lowest) | `or` | Logical OR |

### In Practice

```python
2 + 3 * 4                         # 14 (not 20 — * before +)
(2 + 3) * 4                       # 20 (parentheses override)

True or False and False            # True (and evaluated before or)
(True or False) and False          # False (parentheses override)
```

**Rule of thumb:** When in doubt, use parentheses. They make intent clear and prevent bugs.

---

## Shell vs Python Operators

### Arithmetic

| Operation | Shell | Python |
|-----------|-------|--------|
| Add | `$((a + b))` | `a + b` |
| Subtract | `$((a - b))` | `a - b` |
| Multiply | `$((a * b))` | `a * b` |
| Divide | `$((a / b))` (integer) | `a / b` (float) or `a // b` (integer) |
| Remainder | `$((a % b))` | `a % b` |
| Power | `$((a ** b))` | `a ** b` |
| Increment | `((a++))` or `a=$((a+1))` | `a += 1` |

### Comparison

| Shell | Python | Meaning |
|-------|--------|---------|
| `-eq` | `==` | Equal |
| `-ne` | `!=` | Not equal |
| `-lt` | `<` | Less than |
| `-gt` | `>` | Greater than |
| `-le` | `<=` | Less or equal |
| `-ge` | `>=` | Greater or equal |
| `=` or `==` (string) | `==` | String equal |
| `!=` (string) | `!=` | String not equal |

### Logical

| Shell | Python |
|-------|--------|
| `&&` | `and` |
| `\|\|` | `or` |
| `!` | `not` |

### String/File Tests

| Shell | Python |
|-------|--------|
| `-z "$var"` (empty) | `not var` or `var == ""` |
| `-n "$var"` (not empty) | `var` or `var != ""` |
| `-f "$file"` (file exists) | `os.path.isfile(file)` |
| `-d "$dir"` (dir exists) | `os.path.isdir(dir)` |
| `-e "$path"` (exists) | `os.path.exists(path)` |

---

> **Practice:** Chapter `01-absolute-basics.py` sections 7, 9
> **Next:** [06 - Conditions and Loops](06-conditions-loops.md)
