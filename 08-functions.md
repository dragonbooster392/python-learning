# 08 — Functions

Functions are reusable blocks of code. They're the building blocks of any script.

---

## Table of Contents

- [What Is a Function?](#what-is-a-function)
- [Defining and Calling](#defining-and-calling)
- [Parameters and Arguments](#parameters-and-arguments)
- [Default Values](#default-values)
- [Keyword Arguments](#keyword-arguments)
- [*args — Variable Positional Arguments](#args-variable-positional-arguments)
- [**kwargs — Variable Keyword Arguments](#kwargs-variable-keyword-arguments)
- [Return Values](#return-values)
- [Returning Multiple Values](#returning-multiple-values)
- [Docstrings](#docstrings)
- [Lambda Functions](#lambda-functions)
- [Functions as Arguments (Callbacks)](#functions-as-arguments-callbacks)
- [Scope and Closures](#scope-and-closures)
- [Built-in Functions Reference](#built-in-functions-reference)
- [Common Patterns](#common-patterns)
- [Shell vs Python Functions](#shell-vs-python-functions)

---

## What Is a Function?

A function is a **named, reusable block of code**. You define it once, then call it whenever you need it.

### Why Use Functions?

| Without Functions | With Functions |
|-------------------|----------------|
| Copy-paste same code everywhere | Write once, call many times |
| Hard to read long scripts | Named blocks = self-documenting |
| Fix a bug in 10 places | Fix in one place |
| Can't test individual parts | Test each function separately |

---

## Defining and Calling

### Define with `def`

```python
def greet(name):                   # define: def keyword, name, parameters, colon
    print(f"Hello, {name}!")       # body: indented code

greet("Alice")                     # call: use function name, pass arguments
greet("Bob")                       # can call as many times as needed
```

### Shell Comparison

```bash
# Shell:
greet() {
    echo "Hello, $1!"
}
greet "Alice"
```

```python
# Python:
def greet(name):
    print(f"Hello, {name}!")
greet("Alice")
```

### Key Differences from Shell

| Aspect | Shell | Python |
|--------|-------|--------|
| Define | `func() { ... }` | `def func():` |
| Parameters | `$1`, `$2`, `$3` | Named: `name`, `age` |
| Call | `func arg1 arg2` | `func(arg1, arg2)` — parentheses required |
| Return value | `echo` (captured with `$()`) | `return value` |
| Scope | Global by default | Local by default |

---

## Parameters and Arguments

**Parameters** are the variable names in the definition. **Arguments** are the values you pass when calling.

```python
def add(a, b):          # a, b are PARAMETERS
    return a + b

result = add(3, 5)      # 3, 5 are ARGUMENTS
```

### Positional Arguments

Arguments matched by position — order matters:

```python
def create_server(name, ip, role):
    print(f"{name}: {ip} ({role})")

create_server("web-01", "10.0.0.1", "web")      # positional: order matters
```

### Keyword Arguments

Arguments matched by name — order doesn't matter:

```python
create_server(name="web-01", ip="10.0.0.1", role="web")    # keyword
create_server(role="web", name="web-01", ip="10.0.0.1")    # any order
create_server("web-01", role="web", ip="10.0.0.1")         # mix (positional first)
```

---

## Default Values

Parameters can have default values. If the caller doesn't provide them, the default is used.

```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Alice")                     # "Hello, Alice!" (uses default)
greet("Alice", "Hi")               # "Hi, Alice!" (overrides default)
greet("Alice", greeting="Hey")     # "Hey, Alice!" (keyword override)
```

### Important Rule

Parameters with defaults must come **after** parameters without defaults:

```python
def func(a, b, c=10):             # OK — c has default, comes last
    pass

def func(a, b=5, c):             # SyntaxError! — b has default but c doesn't
    pass
```

### Gotcha: Mutable Default Arguments

```python
# WRONG — the list is shared across ALL calls!
def add_item(item, items=[]):
    items.append(item)
    return items

add_item("a")                      # ['a']
add_item("b")                      # ['a', 'b'] — the SAME list!

# CORRECT — use None as default, create inside:
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

This is a classic Python gotcha. Default mutable objects (lists, dicts) are created **once** when the function is defined, not each time it's called.

---

## Keyword Arguments

### Keyword-Only Arguments

Use `*` to force arguments to be keyword-only (must be passed by name):

```python
def connect(host, port, *, timeout=30, verify=True):
    # timeout and verify MUST be passed by name
    pass

connect("localhost", 8080, timeout=60)         # OK
connect("localhost", 8080, 60)                 # TypeError! timeout is keyword-only
```

### Positional-Only Arguments (Python 3.8+)

Use `/` to force arguments to be positional-only:

```python
def power(base, exp, /):
    return base ** exp

power(2, 10)                       # OK
power(base=2, exp=10)             # TypeError! must be positional
```

---

## *args — Variable Positional Arguments

Accept any number of positional arguments as a **tuple**:

```python
def print_all(*args):
    print(f"Got {len(args)} arguments:")
    for arg in args:
        print(f"  - {arg}")

print_all("Alice", "Bob", "Charlie")
# Got 3 arguments:
#   - Alice
#   - Bob
#   - Charlie

print_all()                        # Got 0 arguments (empty tuple)
```

### With Regular Parameters

```python
def greet(greeting, *names):
    for name in names:
        print(f"{greeting}, {name}!")

greet("Hello", "Alice", "Bob")
# Hello, Alice!
# Hello, Bob!
```

### Unpacking a List into Arguments

```python
args = [1, 2, 3]
func(*args)                        # same as func(1, 2, 3)
```

---

## **kwargs — Variable Keyword Arguments

Accept any number of keyword arguments as a **dict**:

```python
def create_server(**kwargs):
    print(f"Creating server with:")
    for key, value in kwargs.items():
        print(f"  {key}: {value}")

create_server(name="web-01", ip="10.0.0.1", role="web")
# Creating server with:
#   name: web-01
#   ip: 10.0.0.1
#   role: web
```

### Combining All Parameter Types

```python
def func(a, b, *args, **kwargs):
    print(f"a={a}, b={b}")
    print(f"extra positional: {args}")
    print(f"extra keyword: {kwargs}")

func(1, 2, 3, 4, x=10, y=20)
# a=1, b=2
# extra positional: (3, 4)
# extra keyword: {'x': 10, 'y': 20}
```

### Order Rule

```
def func(positional, /, normal, *args, keyword_only, **kwargs):
```

### Unpacking a Dict into Arguments

```python
options = {"timeout": 30, "verify": True}
func(**options)                    # same as func(timeout=30, verify=True)
```

This pattern is used heavily in spectro-cli when passing options to API requests:

```python
response = self.session.get(url, **kwargs)
```

---

## Return Values

Functions can send a value back using `return`.

```python
def add(a, b):
    return a + b

result = add(3, 5)                 # result = 8
print(add(10, 20))                 # prints: 30
```

### Key Facts

- `return` immediately exits the function.
- Code after `return` never runs.
- If there's no `return`, the function returns `None`.
- `return` with no value also returns `None`.

```python
def check(x):
    if x > 100:
        return "too big"
    if x < 0:
        return "negative"
    return "ok"                    # catches everything else

def do_work():
    print("working...")
    # no return → returns None

result = do_work()                 # result is None
```

---

## Returning Multiple Values

Python functions can return multiple values — they actually return a **tuple**.

```python
def get_server_info():
    return "web-01", "10.0.0.1", 8080

# Unpack into separate variables:
name, ip, port = get_server_info()

# Or keep as tuple:
info = get_server_info()           # ("web-01", "10.0.0.1", 8080)
print(info[0])                     # "web-01"

# Ignore values you don't need:
name, _, _ = get_server_info()     # only care about name
name, *_ = get_server_info()       # same — ignore all the rest
```

---

## Docstrings

A **docstring** is a string at the very start of a function that describes what it does:

```python
def deploy_cluster(name, cloud_type="aws"):
    """Deploy a new cluster to the specified cloud.

    Args:
        name: The cluster name.
        cloud_type: Cloud provider (default: "aws").

    Returns:
        The cluster UID as a string.

    Raises:
        AuthError: If the API key is invalid.
    """
    ...
```

- Triple-quoted string as the first statement in a function.
- Accessible via `help(deploy_cluster)` or `deploy_cluster.__doc__`.
- Not required for scripting, but good practice for functions others will use.

---

## Lambda Functions

A **lambda** is an anonymous (unnamed), one-line function.

```python
# Regular function:
def double(x):
    return x * 2

# Lambda equivalent:
double = lambda x: x * 2

# Usage:
double(5)                          # 10
```

### Where Lambdas Shine

Lambdas are best used inline, as throwaway functions:

```python
# Sort by a key:
servers.sort(key=lambda s: s["name"])

# Sort by multiple criteria:
servers.sort(key=lambda s: (s["role"], s["name"]))

# Filter:
active = list(filter(lambda s: s["state"] == "Running", servers))

# Map:
names = list(map(lambda s: s["name"], servers))
```

### When NOT to Use

- Multi-line logic — use `def`
- Complex conditions — use `def`
- Used more than once — give it a name with `def`

---

## Functions as Arguments (Callbacks)

Functions can be passed as arguments to other functions:

```python
def apply(func, value):
    return func(value)

def double(x):
    return x * 2

def square(x):
    return x ** 2

apply(double, 5)                   # 10
apply(square, 5)                   # 25
```

### Common Built-in Functions That Take Functions

```python
# sorted — custom sort key:
sorted(names, key=len)             # sort by length
sorted(names, key=str.lower)       # case-insensitive sort

# map — apply function to each item:
list(map(str.upper, ["a", "b"]))   # ["A", "B"]

# filter — keep items where function returns True:
list(filter(str.isdigit, ["1", "a", "2"]))   # ["1", "2"]

# max/min — with custom key:
longest = max(names, key=len)      # longest name
```

---

## Scope and Closures

### Scope Review

```python
x = "global"                       # global scope

def outer():
    y = "outer"                    # local to outer

    def inner():
        z = "inner"               # local to inner
        print(x)                   # can read global
        print(y)                   # can read outer (closure!)
        print(z)                   # can read own local

    inner()
    # z is NOT accessible here

# y and z are NOT accessible here
```

### Closures

A **closure** is when an inner function "remembers" variables from its enclosing function:

```python
def make_multiplier(factor):
    def multiply(x):
        return x * factor         # "remembers" factor from make_multiplier
    return multiply

double = make_multiplier(2)
triple = make_multiplier(3)

double(5)                          # 10
triple(5)                          # 15
```

Closures are used internally by decorators and Click's command system.

### nonlocal

To modify an enclosing (non-global) variable:

```python
def counter():
    count = 0
    def increment():
        nonlocal count            # "I mean the count from counter()"
        count += 1
        return count
    return increment

c = counter()
c()                                # 1
c()                                # 2
```

---

## Built-in Functions Reference

Always available — no import needed:

| Function | Purpose | Example |
|----------|---------|---------|
| `print()` | Output to screen | `print("hello")` |
| `len()` | Length | `len([1,2,3])` → `3` |
| `type()` | Check type | `type(42)` → `<class 'int'>` |
| `isinstance()` | Check if type | `isinstance(42, int)` → `True` |
| `range()` | Number sequence | `range(5)` → 0,1,2,3,4 |
| `enumerate()` | Index + value | `enumerate(["a","b"])` → (0,"a"),(1,"b") |
| `zip()` | Pair up lists | `zip([1,2],["a","b"])` → (1,"a"),(2,"b") |
| `sorted()` | Sorted copy | `sorted([3,1,2])` → `[1,2,3]` |
| `reversed()` | Reverse iterator | `list(reversed([1,2,3]))` → `[3,2,1]` |
| `any()` | Any truthy? | `any([False,True])` → `True` |
| `all()` | All truthy? | `all([True,True])` → `True` |
| `map()` | Apply to each | `list(map(str, [1,2]))` → `["1","2"]` |
| `filter()` | Keep matching | `list(filter(None, [0,1,""]))` → `[1]` |
| `max()` | Largest | `max(3,7,1)` → `7` |
| `min()` | Smallest | `min(3,7,1)` → `1` |
| `sum()` | Total | `sum([1,2,3])` → `6` |
| `abs()` | Absolute value | `abs(-5)` → `5` |
| `round()` | Round number | `round(3.7)` → `4` |
| `int()` | Convert to int | `int("42")` → `42` |
| `str()` | Convert to string | `str(42)` → `"42"` |
| `float()` | Convert to float | `float("3.14")` → `3.14` |
| `bool()` | Convert to bool | `bool(0)` → `False` |
| `list()` | Convert to list | `list("abc")` → `['a','b','c']` |
| `dict()` | Convert to dict | `dict(a=1, b=2)` → `{"a":1,"b":2}` |
| `set()` | Convert to set | `set([1,1,2])` → `{1,2}` |
| `tuple()` | Convert to tuple | `tuple([1,2])` → `(1,2)` |
| `input()` | User input | `input("Name: ")` |
| `open()` | Open file | `open("f.txt")` |
| `dir()` | List attributes | `dir(object)` |
| `help()` | Show documentation | `help(print)` |
| `exit()` | Exit script | `exit(1)` |
| `id()` | Memory address | `id(x)` |
| `hash()` | Hash value | `hash("hello")` |
| `callable()` | Is it callable? | `callable(print)` → `True` |
| `getattr()` | Get attribute | `getattr(obj, "name")` |
| `hasattr()` | Has attribute? | `hasattr(obj, "name")` |
| `vars()` | Object's `__dict__` | `vars(obj)` |

---

## Common Patterns

### Guard Clause (Early Return)

```python
# AVOID deep nesting:
def process(item):
    if item is not None:
        if item["state"] != "Deleted":
            if item["type"] == "cluster":
                do_work(item)

# USE guard clauses:
def process(item):
    if item is None:
        return
    if item["state"] == "Deleted":
        return
    if item["type"] != "cluster":
        return
    do_work(item)
```

### Factory Function

```python
def create_client(env="prod"):
    if env == "prod":
        return PaletteClient("https://api.prod.com", get_prod_key())
    else:
        return PaletteClient("https://api.dev.com", get_dev_key())
```

### Wrapper Function

```python
def with_logging(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"Done: {func.__name__}")
        return result
    return wrapper
```

---

## Shell vs Python Functions

| Aspect | Shell | Python |
|--------|-------|--------|
| Define | `func() { ... }` | `def func():` |
| Parameters | `$1`, `$2`, `$@` | Named: `a`, `b` |
| All args | `$@` | `*args` |
| Arg count | `$#` | `len(args)` |
| Call | `func arg1` | `func(arg1)` — parens required |
| Return value | `echo` + capture `$()` | `return value` |
| Exit status | `return 0` or `return 1` | `sys.exit(0)` or `sys.exit(1)` |
| Local vars | `local var=value` | All vars are local by default |
| Global vars | Default | `global var` declaration needed |
| Default values | Parameter expansion: `${1:-default}` | `def func(x="default")` |
| Named params | Not built-in | Built-in: `func(name="Alice")` |

---

> **Practice:** Chapter `03-functions-modules.py` sections 1-6
> **Next:** [09 - Modules and Imports](09-modules-imports.md)
