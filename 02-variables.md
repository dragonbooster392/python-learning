# 02 — Variables

Everything in Python is stored in variables. This page covers everything about variables you need for scripting.

---

## Table of Contents

- [What Is a Variable?](#what-is-a-variable)
- [Creating Variables](#creating-variables)
- [Shell vs Python Variables](#shell-vs-python-variables)
- [Naming Rules](#naming-rules)
- [Naming Conventions](#naming-conventions)
- [Dynamic Typing](#dynamic-typing)
- [Multiple Assignment](#multiple-assignment)
- [Constants](#constants)
- [Variable Types at a Glance](#variable-types-at-a-glance)
- [Type Checking](#type-checking)
- [Type Conversion (Casting)](#type-conversion-casting)
- [Mutable vs Immutable](#mutable-vs-immutable)
- [Truthy and Falsy Values](#truthy-and-falsy-values)
- [None — The Empty Value](#none-the-empty-value)
- [Variable Scope (Where Variables Live)](#variable-scope-where-variables-live)
- [Deleting Variables](#deleting-variables)
- [Common Mistakes](#common-mistakes)

---

## What Is a Variable?

A variable is a **name that holds a value**. Think of it as a labeled box — you put something in, and you use the label to get it back later.

```python
name = "Alice"        # create a variable called "name", put "Alice" in it
print(name)           # use the variable → prints: Alice

name = "Bob"          # same box, new value — "Alice" is gone
print(name)           # prints: Bob
```

A variable is **not** the value itself. It's a **reference** — a name pointing to a value stored in memory.

---

## Creating Variables

Variables are created the moment you assign a value. No declaration keyword needed.

```python
name = "Alice"           # string
age = 30                 # integer
price = 19.99            # float (decimal)
is_admin = True          # boolean
result = None            # nothing/empty
```

That's it. No `var`, `let`, `declare`, `local`, `readonly`. Just `name = value`.

---

## Shell vs Python Variables

| Concept | Shell | Python |
|---------|-------|--------|
| Create | `NAME="Alice"` (NO spaces around `=`!) | `name = "Alice"` (spaces are fine, preferred) |
| Read | `echo $NAME` | `print(name)` |
| Prefix | `$` required to read | No `$` — ever |
| Quotes | Required: `NAME="Alice"` | Optional for assignment, but strings need quotes for value |
| Spaces | `NAME = "Alice"` → **ERROR** in shell | `name = "Alice"` → perfectly fine |
| Case | Case-sensitive | Case-sensitive |
| Scope | Global by default, `local` inside functions | Local inside functions, global at file level |
| Export | `export NAME` for child processes | `os.environ["NAME"]` for env vars |
| Unset | `unset NAME` | `del name` |
| Check exists | `[ -z "${NAME+x}" ]` | Check with `try/except` or `in` |

### The Biggest Difference

In shell, you use `$` to READ a variable but NOT when setting it:
```bash
NAME="Alice"       # setting — no $
echo "$NAME"       # reading — needs $
```

In Python, you never use `$`:
```python
name = "Alice"     # setting — just the name
print(name)        # reading — just the name
```

---

## Naming Rules

### Must Follow (or Python crashes)

| Rule | Valid Examples | Invalid Examples | Why Invalid |
|------|--------------|-----------------|-------------|
| Start with letter or `_` | `name`, `_private`, `_x` | `2name`, `@name` | Can't start with number or symbol |
| Only letters, numbers, `_` | `my_var`, `count2`, `x` | `my-var`, `my var`, `my.var` | Hyphens, spaces, dots not allowed |
| Not a reserved word | `my_class`, `state` | `class`, `if`, `for`, `return` | Python uses these |
| Case-sensitive | `name` ≠ `Name` ≠ `NAME` | — | These are three different variables |

### Python Reserved Words (Cannot Use as Variable Names)

```
False   True    None    and     as      assert  async   await
break   class   continue def    del     elif    else    except
finally for     from    global  if      import  in      is
lambda  nonlocal not    or      pass    raise   return  try
while   with    yield
```

---

## Naming Conventions

Python has strong conventions (not enforced, but everyone follows them):

| Style | Usage | Examples |
|-------|-------|---------|
| `snake_case` | Variables, functions, methods, modules | `cluster_name`, `api_key`, `get_clusters()` |
| `UPPER_SNAKE_CASE` | Constants (values that never change) | `MAX_RETRIES`, `DEFAULT_TIMEOUT` |
| `PascalCase` | Class names | `PaletteClient`, `ProfileBuilder` |
| `_leading_underscore` | Private/internal (don't use from outside) | `_helper()`, `_internal_state` |
| `__double_leading` | Name mangling (very private) | `__secret` — rarely used in scripting |
| `__dunder__` | Special Python methods | `__init__`, `__name__`, `__str__` |

### Good vs Bad Names

```python
# GOOD — clear, descriptive, snake_case
cluster_name = "prod-cluster"
api_key = "abc123"
is_ready = True
retry_count = 3
max_timeout = 60

# BAD — unclear, wrong convention
x = "prod-cluster"          # what is x?
CN = "prod-cluster"          # abbreviation unclear
clusterName = "prod"         # camelCase (Java/JS style, not Python)
CLUSTER_NAME = "prod"        # UPPER_CASE is for constants only
```

---

## Dynamic Typing

Python is **dynamically typed** — you don't declare what type a variable holds. Python figures it out from the value.

```python
x = 42              # x is an int
x = "hello"         # now x is a str — Python doesn't care
x = [1, 2, 3]       # now x is a list — still fine
x = True            # now x is a bool
```

### How This Differs

| Language | Style | Example |
|----------|-------|---------|
| Java, Go, C | **Static** — you must declare the type | `int x = 42;` — x can ONLY be int |
| Python, Shell, Ruby | **Dynamic** — type determined by value | `x = 42` — x can be anything later |

### Pros and Cons

| Pros | Cons |
|------|------|
| Less typing, faster to write | Easier to accidentally put wrong type |
| No compile step needed | Bugs can hide until runtime |
| More flexible | IDE can't always predict types |

### Type Hints (Optional Annotations)

Python 3.5+ lets you **optionally** annotate types. It's documentation — Python doesn't enforce it.

```python
name: str = "Alice"
age: int = 30
is_ready: bool = True

def greet(name: str) -> str:     # takes str, returns str
    return f"Hello {name}"
```

You'll see this in some projects. It helps IDEs and readability but is **completely optional** for scripting.

---

## Multiple Assignment

### Assign Same Value to Multiple Variables

```python
x = y = z = 0                   # all three are 0
```

### Assign Multiple Values in One Line

```python
name, age, city = "Alice", 30, "NYC"
# name = "Alice", age = 30, city = "NYC"
```

### Swap Values

```python
a, b = b, a                     # swap without a temp variable!
# Shell needs: tmp=$a; a=$b; b=$tmp
```

### Unpacking (From Functions, Lists, Tuples)

```python
# Function returns multiple values:
def get_server():
    return "web-01", "10.0.0.1"

name, ip = get_server()          # name = "web-01", ip = "10.0.0.1"

# From a list:
first, second, third = [1, 2, 3]

# Ignore values with _:
name, _ = get_server()           # only care about name, discard ip

# Capture rest with *:
first, *rest = [1, 2, 3, 4, 5]  # first = 1, rest = [2, 3, 4, 5]
```

---

## Constants

Python has **no real constants** — there's no way to make a variable truly unchangeable. The convention is to use `UPPER_SNAKE_CASE` to signal "don't change this."

```python
MAX_RETRIES = 3
DEFAULT_TIMEOUT = 30
API_VERSION = "v1"
BASE_URL = "https://api.example.com"
```

Anyone can technically change `MAX_RETRIES`, but the naming tells developers: "this is meant to be a fixed value."

---

## Variable Types at a Glance

| Type | Python Name | What It Holds | Example | Shell Equivalent |
|------|-------------|--------------|---------|-----------------|
| Text | `str` | Characters/text | `"hello"` | `NAME="hello"` |
| Whole number | `int` | Integer, no decimal | `42` | `COUNT=42` |
| Decimal number | `float` | Decimal/fractional | `3.14` | No direct equivalent |
| True/False | `bool` | `True` or `False` | `True` | Exit code 0/1 |
| Nothing | `NoneType` | Absence of value | `None` | Unset variable |
| List | `list` | Ordered collection | `[1, 2, 3]` | `arr=(1 2 3)` |
| Dictionary | `dict` | Key-value pairs | `{"a": 1}` | `declare -A map` |
| Tuple | `tuple` | Unchangeable list | `(1, 2)` | No equivalent |
| Set | `set` | Unique items only | `{1, 2, 3}` | No equivalent |
| Bytes | `bytes` | Raw binary data | `b"data"` | No equivalent |

---

## Type Checking

### Check What Type a Variable Is

```python
type(42)             # <class 'int'>
type("hello")        # <class 'str'>
type(3.14)           # <class 'float'>
type(True)           # <class 'bool'>
type(None)           # <class 'NoneType'>
type([1, 2])         # <class 'list'>
type({"a": 1})       # <class 'dict'>
```

### Check If a Variable IS a Certain Type

```python
isinstance(42, int)           # True
isinstance("hi", str)         # True
isinstance(42, str)           # False

# Check multiple types at once:
isinstance(42, (int, float))  # True — is it int OR float?
```

### type() vs isinstance()

- `type(x) == int` — exact match only (strict)
- `isinstance(x, int)` — also matches subclasses (recommended)

```python
# bool is a subclass of int in Python!
type(True) == int             # False (type is bool, not int)
isinstance(True, int)         # True (bool inherits from int)
```

---

## Type Conversion (Casting)

Converting a value from one type to another.

### String ↔ Number

```python
# String to integer
int("42")                     # 42
int("0xFF", 16)               # 255 (from hex)
int("0b1010", 2)              # 10 (from binary)
int("42abc")                  # ValueError! Can't convert

# String to float
float("3.14")                 # 3.14
float("42")                   # 42.0

# Number to string
str(42)                       # "42"
str(3.14)                     # "3.14"
str(True)                     # "True"
```

### Number ↔ Number

```python
int(3.7)                      # 3 (truncates, does NOT round)
int(3.2)                      # 3
float(42)                     # 42.0
round(3.7)                    # 4 (rounds)
round(3.14159, 2)             # 3.14 (round to 2 decimal places)
```

### To Boolean

```python
bool(0)                       # False
bool(1)                       # True
bool(42)                      # True (any non-zero = True)
bool("")                      # False (empty string)
bool("hello")                 # True (non-empty string)
bool([])                      # False (empty list)
bool([1, 2])                  # True (non-empty list)
bool(None)                    # False
```

### To List

```python
list("hello")                 # ['h', 'e', 'l', 'l', 'o']
list((1, 2, 3))               # [1, 2, 3] (tuple → list)
list({1, 2, 3})               # [1, 2, 3] (set → list, order varies)
list(range(5))                # [0, 1, 2, 3, 4]
list({"a": 1, "b": 2})        # ['a', 'b'] (dict → list of keys)
```

### Conversion Table

| From → To | `str()` | `int()` | `float()` | `bool()` | `list()` |
|-----------|---------|---------|-----------|----------|----------|
| `"42"` | `"42"` | `42` | `42.0` | `True` | `['4','2']` |
| `42` | `"42"` | `42` | `42.0` | `True` | Error |
| `3.14` | `"3.14"` | `3` | `3.14` | `True` | Error |
| `True` | `"True"` | `1` | `1.0` | `True` | Error |
| `False` | `"False"` | `0` | `0.0` | `False` | Error |
| `None` | `"None"` | Error | Error | `False` | Error |
| `""` | `""` | Error | Error | `False` | `[]` |
| `[]` | `"[]"` | Error | Error | `False` | `[]` |

---

## Mutable vs Immutable

This is a concept that **does not exist in shell** but is critical in Python.

### What Does It Mean?

- **Mutable:** Can be changed after creation. You can modify it in place.
- **Immutable:** Cannot be changed after creation. Any "change" creates a new value.

### Which Types Are Which?

| Mutable (changeable) | Immutable (unchangeable) |
|----------------------|-------------------------|
| `list` — `[1, 2, 3]` | `str` — `"hello"` |
| `dict` — `{"a": 1}` | `int` — `42` |
| `set` — `{1, 2, 3}` | `float` — `3.14` |
| | `bool` — `True` |
| | `tuple` — `(1, 2)` |
| | `None` |

### Why It Matters

#### Lists are mutable — changes happen in place:

```python
fruits = ["apple", "banana"]
fruits.append("cherry")        # modifies the SAME list
print(fruits)                  # ["apple", "banana", "cherry"]

backup = fruits                # backup points to the SAME list!
backup.append("date")
print(fruits)                  # ["apple", "banana", "cherry", "date"] — BOTH changed!
```

#### Strings are immutable — "changes" create new strings:

```python
name = "Alice"
upper_name = name.upper()     # creates a NEW string
print(name)                   # "Alice" — original unchanged
print(upper_name)             # "ALICE" — new string
```

### The Copy Problem

Because mutable objects share references, changing one can unexpectedly change another:

```python
# PROBLEM — both point to same list
a = [1, 2, 3]
b = a                          # b and a are the SAME list
b.append(4)
print(a)                       # [1, 2, 3, 4] — a changed too!

# SOLUTION — make a copy
a = [1, 2, 3]
b = a.copy()                   # b is a SEPARATE list
b.append(4)
print(a)                       # [1, 2, 3] — a is unchanged
```

For dicts: use `d.copy()` for shallow copy.
For nested structures: use `import copy; copy.deepcopy(x)` for full deep copy.

---

## Truthy and Falsy Values

Every value in Python can be treated as `True` or `False` in an if statement. Python doesn't require booleans.

### Falsy Values (Treated as False)

| Value | Type | Why Falsy |
|-------|------|-----------|
| `False` | bool | It IS false |
| `None` | NoneType | Nothing/empty |
| `0` | int | Zero |
| `0.0` | float | Zero |
| `""` | str | Empty string |
| `[]` | list | Empty list |
| `{}` | dict | Empty dict |
| `()` | tuple | Empty tuple |
| `set()` | set | Empty set |

### Truthy Values (Treated as True)

**Everything else** — any non-zero number, any non-empty string, any non-empty collection.

### Practical Use

```python
name = ""
if not name:                   # empty string is falsy
    print("Name is empty!")

items = []
if items:                      # empty list is falsy → False → skip
    print("Has items")
else:
    print("No items")          # this runs

count = 0
if count:                      # 0 is falsy
    print("Has count")         # skipped

result = None
if result:                     # None is falsy
    print("Has result")        # skipped
```

### The `or` Default Pattern

```python
# Use 'or' to provide a default when a value might be None/empty/0:
name = user_input or "Anonymous"
# If user_input is falsy (None, "", etc.), name becomes "Anonymous"

# Same pattern used in spectro-cli:
items = api_response.get("items") or []
# If items is None, use empty list instead
```

---

## None — The Empty Value

`None` is Python's "nothing." It means "no value" or "not set."

### Creating

```python
result = None                  # explicitly set to nothing
```

### When You Get None

```python
# 1. Function with no return:
def do_stuff():
    print("working")
    # no return statement

x = do_stuff()                 # x is None

# 2. Dict .get() when key missing:
d = {"name": "Alice"}
value = d.get("age")           # None (key doesn't exist)

# 3. Variables not yet assigned a real value:
best_match = None              # will be set later in a loop
```

### Checking for None

```python
# CORRECT way:
if result is None:
    print("No result")

if result is not None:
    print(f"Got: {result}")

# WRONG way (works but not recommended):
if result == None:             # works but style violation
    print("No result")
```

Always use `is None` and `is not None`. The `is` operator checks identity (same object), `==` checks value equality. For None, `is` is correct and faster.

---

## Variable Scope (Where Variables Live)

### Two Main Scopes

| Scope | Where Created | Lifetime | Visibility |
|-------|--------------|----------|------------|
| **Local** | Inside a function | Exists only while function runs | Only within that function |
| **Global** | Outside any function (file level) | Exists for entire program | Visible everywhere in the file |

### How It Works

```python
name = "Alice"              # GLOBAL — visible everywhere in this file

def greet():
    message = "Hello"       # LOCAL — only exists inside greet()
    print(f"{message}, {name}")   # can READ global "name"

greet()                     # prints: Hello, Alice
print(name)                 # works: "Alice" (global)
print(message)              # ERROR: message is not defined (local to greet)
```

### Modifying Globals Inside Functions

```python
count = 0

def increment():
    count += 1              # ERROR: UnboundLocalError!
    # Python thinks count is local because you're assigning to it

def increment():
    global count            # declare: "I mean the global count"
    count += 1              # now works

# BUT: avoid global. Better pattern:
def increment(count):
    return count + 1        # pass in, return out — no global needed

count = increment(count)
```

### Shell Comparison

| Shell | Python |
|-------|--------|
| All variables are global by default | Variables in functions are local by default |
| `local VAR` makes it local | Variables in functions are already local |
| `export VAR` for child processes | `os.environ["VAR"]` for env vars |

---

## Deleting Variables

```python
x = 42
del x                       # x no longer exists
print(x)                    # NameError: name 'x' is not defined
```

You rarely need `del` in scripting. It's like `unset` in shell. Mostly used for:
- Removing dict keys: `del my_dict["key"]`
- Removing list items: `del my_list[0]`
- Freeing memory for large objects (rare)

---

## Common Mistakes

### 1. Using `$` (Shell Habit)
```python
# WRONG:
print($name)           # SyntaxError!

# CORRECT:
print(name)
```

### 2. No Spaces Around `=` (Shell Habit)
```python
# Shell: NAME="Alice"  (no spaces — required)
# Python: name = "Alice"  (spaces — fine and preferred)

# Both work in Python:
name="Alice"           # works but not standard
name = "Alice"         # standard Python style
```

### 3. Using Hyphens in Variable Names
```python
# WRONG:
my-var = "hello"       # SyntaxError! Python reads this as my MINUS var

# CORRECT:
my_var = "hello"       # use underscores
```

### 4. Forgetting Quotes on Strings
```python
# WRONG:
name = Alice           # NameError: name 'Alice' is not defined
                       # Python thinks Alice is a variable name!

# CORRECT:
name = "Alice"         # quotes make it a string value
```

### 5. Confusing = and ==
```python
x = 5                  # ASSIGNMENT: put 5 into x
x == 5                 # COMPARISON: is x equal to 5? Returns True/False
```

### 6. Assuming Variables Are Copied
```python
a = [1, 2, 3]
b = a                  # b points to SAME list, not a copy!
b.append(4)
print(a)               # [1, 2, 3, 4] — surprise!

b = a.copy()           # NOW b is a separate copy
```

---

> **Practice:** Chapter `01-absolute-basics.py` sections 3-5
> **Next:** [03 - Data Types](03-data-types.md)
