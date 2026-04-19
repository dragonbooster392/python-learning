# 13 — Decorators, Generators, and Advanced Topics

Advanced Python features you'll encounter in real-world CLI tools and libraries.

---

## Table of Contents

- [Decorators](#decorators)
  - [What Is a Decorator?](#what-is-a-decorator)
  - [How Decorators Work](#how-decorators-work)
  - [Writing Your Own Decorator](#writing-your-own-decorator)
  - [Decorators with Arguments](#decorators-with-arguments)
  - [Common Built-in Decorators](#common-built-in-decorators)
  - [Library Decorators You'll See](#library-decorators-youll-see)
  - [Stacking Decorators](#stacking-decorators)
- [Generators](#generators)
  - [What Is a Generator?](#what-is-a-generator)
  - [Generator Expressions](#generator-expressions)
  - [When to Use Generators](#when-to-use-generators)
- [Context Managers](#context-managers)
  - [What Is a Context Manager?](#what-is-a-context-manager)
  - [Writing Your Own](#writing-your-own-context-manager)
  - [contextlib.contextmanager](#using-contextlibcontextmanager)
- [Comprehensions — Advanced](#comprehensions-advanced)
- [Type Hints](#type-hints)
- [\*args and \*\*kwargs Patterns](#args-and-kwargs-patterns)
- [Unpacking and Spreading](#unpacking-and-spreading)
- [Closures](#closures)
- [Useful Standard Library Tools](#useful-standard-library-tools)
- [Shell vs Python Comparison](#shell-vs-python-comparison)

---

## Decorators

### What Is a Decorator?

A decorator is a function that **wraps another function** to modify or extend its behavior — without changing the original function's code.

You've already seen decorators in spectro-cli:

```python
@click.command()                   # makes a function into a CLI command
@click.option("--name")            # adds a CLI option
@retry(stop=stop_after_attempt(3)) # auto-retry on failure
@staticmethod                      # no self parameter needed
@property                          # attribute-like access
```

The `@` symbol is the decorator syntax.

### How Decorators Work

A decorator is just syntactic sugar:

```python
# This:
@my_decorator
def my_function():
    pass

# Is exactly the same as:
def my_function():
    pass
my_function = my_decorator(my_function)
```

So `@decorator` means: **pass this function to `decorator()` and replace it with the result**.

### Writing Your Own Decorator

#### Simple Decorator — Timer

```python
import time
import functools

def timer(func):
    """Measure how long a function takes."""
    @functools.wraps(func)         # preserves original function name/docs
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        elapsed = time.time() - start
        print(f"{func.__name__} took {elapsed:.2f}s")
        return result
    return wrapper

@timer
def slow_operation():
    time.sleep(1)
    return "done"

result = slow_operation()          # prints: "slow_operation took 1.00s"
print(result)                      # "done"
```

#### Pattern Breakdown

```python
def my_decorator(func):                    # takes the original function
    @functools.wraps(func)                 # preserve original metadata
    def wrapper(*args, **kwargs):          # replacement function
        # --- before the original function ---
        print("Before")

        result = func(*args, **kwargs)     # call the original

        # --- after the original function ---
        print("After")

        return result                      # return original result
    return wrapper                         # return the replacement
```

#### Logging Decorator

```python
import functools
import logging

logger = logging.getLogger(__name__)

def log_call(func):
    """Log every call to a function."""
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        logger.info(f"Calling {func.__name__}(args={args}, kwargs={kwargs})")
        try:
            result = func(*args, **kwargs)
            logger.info(f"{func.__name__} returned {result}")
            return result
        except Exception as e:
            logger.error(f"{func.__name__} raised {type(e).__name__}: {e}")
            raise
    return wrapper

@log_call
def get_cluster(name):
    return {"name": name, "status": "active"}
```

#### Error Handling Decorator

```python
import functools
import sys

def handle_errors(func):
    """Catch exceptions and print user-friendly messages."""
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        try:
            return func(*args, **kwargs)
        except FileNotFoundError as e:
            print(f"Error: File not found — {e}", file=sys.stderr)
            sys.exit(1)
        except PermissionError:
            print("Error: Permission denied", file=sys.stderr)
            sys.exit(1)
        except Exception as e:
            print(f"Error: {e}", file=sys.stderr)
            sys.exit(1)
    return wrapper

@handle_errors
def main():
    config = load_config()
    process(config)
```

### Decorators with Arguments

When a decorator needs its own arguments, you add another layer:

```python
import functools

def retry(max_attempts=3, delay=1):
    """Retry decorator with configurable attempts."""
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(1, max_attempts + 1):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts:
                        raise
                    print(f"Attempt {attempt} failed: {e}")
                    time.sleep(delay)
        return wrapper
    return decorator

@retry(max_attempts=5, delay=2)
def api_call():
    response = requests.get("https://api.example.com")
    response.raise_for_status()
    return response.json()
```

### Common Built-in Decorators

| Decorator | What It Does | See Section |
|-----------|-------------|-------------|
| `@property` | Make method act like attribute | [12 - Classes](12-classes-oop.md) |
| `@staticmethod` | Method that doesn't need `self` | [12 - Classes](12-classes-oop.md) |
| `@classmethod` | Method that gets `cls` instead of `self` | [12 - Classes](12-classes-oop.md) |
| `@dataclass` | Auto-generate `__init__`, `__repr__`, etc. | [12 - Classes](12-classes-oop.md) |
| `@functools.wraps` | Preserve wrapped function metadata | Above |
| `@functools.lru_cache` | Cache function results | Below |
| `@abstractmethod` | Mark method as must-override | ABC module |

#### @functools.lru_cache — Memoization

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def expensive_lookup(name):
    """Result is cached — repeated calls return instantly."""
    print(f"Looking up {name}...")
    return api_call(f"/lookup/{name}")

expensive_lookup("prod")           # prints "Looking up prod...", makes API call
expensive_lookup("prod")           # returns cached result instantly
expensive_lookup("dev")            # prints "Looking up dev...", makes API call
```

### Library Decorators You'll See

#### Click (CLI Framework)

```python
@click.group()                     # group of commands
def cli():
    pass

@cli.command()                     # command within group
@click.option("--name", required=True)
@click.option("--cloud", type=click.Choice(["aws", "azure", "gcp"]))
@click.argument("action")
def cluster(name, cloud, action):
    pass
```

#### Tenacity (Retry)

```python
from tenacity import retry, stop_after_attempt, wait_exponential

@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, max=10),
)
def unreliable_api_call():
    return requests.get("https://api.example.com").json()
```

### Stacking Decorators

Multiple decorators apply bottom-up:

```python
@decorator_a          # applied second (outer)
@decorator_b          # applied first (inner)
def my_function():
    pass

# Same as:
my_function = decorator_a(decorator_b(my_function))
```

Real example from spectro-cli:

```python
@cli.command()
@click.option("--name", required=True)
@click.option("--cloud", default="aws")
@click.pass_context
def create_cluster(ctx, name, cloud):
    pass
```

---

## Generators

### What Is a Generator?

A generator is a function that **yields values one at a time** instead of building and returning the whole list. This saves memory for large datasets.

```python
# Regular function — builds entire list in memory:
def get_numbers(n):
    result = []
    for i in range(n):
        result.append(i * 2)
    return result                  # all values at once

# Generator — yields one at a time:
def get_numbers_gen(n):
    for i in range(n):
        yield i * 2                # produces one value, pauses

# Usage is the same:
for num in get_numbers(5):
    print(num)                     # 0, 2, 4, 6, 8

for num in get_numbers_gen(5):
    print(num)                     # 0, 2, 4, 6, 8
```

### How yield Works

- `yield` returns a value and **pauses** the function
- Next iteration **resumes** from where it paused
- When the function ends, the generator is exhausted

```python
def count_up_to(n):
    print("Starting")
    i = 1
    while i <= n:
        print(f"About to yield {i}")
        yield i
        print(f"Resumed after yielding {i}")
        i += 1
    print("Done")

gen = count_up_to(3)
print(next(gen))                   # Starting → About to yield 1 → prints 1
print(next(gen))                   # Resumed after yielding 1 → About to yield 2 → prints 2
print(next(gen))                   # Resumed after yielding 2 → About to yield 3 → prints 3
# next(gen)                        # Resumed after yielding 3 → Done → StopIteration
```

### Practical Examples

#### Reading Large Files Line by Line

```python
def read_large_file(path):
    """Read a file without loading it all into memory."""
    with open(path) as f:
        for line in f:
            yield line.strip()

# Process a 10GB log file without running out of memory:
for line in read_large_file("/var/log/huge.log"):
    if "ERROR" in line:
        print(line)
```

#### Paginated API Results

```python
def get_all_clusters(client, page_size=50):
    """Fetch all clusters, handling pagination."""
    offset = 0
    while True:
        page = client.get(f"/clusters?limit={page_size}&offset={offset}")
        if not page["items"]:
            break
        for cluster in page["items"]:
            yield cluster
        offset += page_size

# Use like a simple list:
for cluster in get_all_clusters(client):
    print(cluster["name"])
```

### Generator Expressions

Like list comprehensions, but with `()` instead of `[]`:

```python
# List comprehension — builds entire list:
squares = [x**2 for x in range(1000000)]       # uses lots of memory

# Generator expression — yields one at a time:
squares = (x**2 for x in range(1000000))       # uses almost no memory

# Use in functions that accept iterables:
total = sum(x**2 for x in range(1000000))      # no extra parentheses needed
has_error = any(line.startswith("ERROR") for line in open("log.txt"))
```

### When to Use Generators

| Use Generator | Use List |
|---------------|----------|
| Large/infinite data | Small data you need to reuse |
| Reading files line by line | Need random access (indexing) |
| Streaming/pipelines | Need to know length |
| Memory-constrained | Need to iterate multiple times |

---

## Context Managers

### What Is a Context Manager?

A context manager handles **setup and cleanup** automatically using `with`:

```python
# The resource is automatically cleaned up:
with open("file.txt") as f:       # setup: open file
    data = f.read()
# cleanup: f.close() called automatically, even if error occurred
```

### Writing Your Own Context Manager

#### Using a Class

```python
class Timer:
    """Time a block of code."""
    def __enter__(self):
        self.start = time.time()
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        self.elapsed = time.time() - self.start
        print(f"Elapsed: {self.elapsed:.2f}s")
        return False               # don't suppress exceptions

with Timer():
    time.sleep(1)
# prints: "Elapsed: 1.00s"
```

#### Using contextlib.contextmanager

Much simpler — write a generator with one `yield`:

```python
from contextlib import contextmanager

@contextmanager
def timer():
    """Time a block of code."""
    start = time.time()
    yield                          # code inside 'with' runs here
    elapsed = time.time() - start
    print(f"Elapsed: {elapsed:.2f}s")

with timer():
    time.sleep(1)
```

#### Practical: Temporary Directory

```python
from contextlib import contextmanager
import tempfile
import shutil

@contextmanager
def temp_workspace():
    """Create a temp directory, clean up when done."""
    path = tempfile.mkdtemp()
    try:
        yield path                 # give the path to the with block
    finally:
        shutil.rmtree(path)        # always clean up

with temp_workspace() as workspace:
    # work in the temp directory
    config_path = os.path.join(workspace, "config.json")
    with open(config_path, "w") as f:
        json.dump({"key": "value"}, f)
# temp directory is deleted here
```

#### Practical: Change Directory

```python
@contextmanager
def cd(path):
    """Temporarily change directory (like pushd/popd)."""
    old_dir = os.getcwd()
    os.chdir(path)
    try:
        yield
    finally:
        os.chdir(old_dir)

with cd("/tmp"):
    print(os.getcwd())            # /tmp
print(os.getcwd())                # back to original
```

---

## Comprehensions — Advanced

Beyond basic list comprehensions (covered in [07-data-structures.md](07-data-structures.md)).

### Nested Comprehensions

```python
# Flatten a list of lists:
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flat = [num for row in matrix for num in row]
# [1, 2, 3, 4, 5, 6, 7, 8, 9]

# Read order: for row in matrix → for num in row → num
```

### Comprehension with Function Calls

```python
# Process and filter:
names = ["  Alice  ", "  Bob  ", "", "  Charlie  "]
clean = [n.strip() for n in names if n.strip()]
# ["Alice", "Bob", "Charlie"]
```

### Dict Comprehension — Advanced

```python
# Swap keys and values:
original = {"a": 1, "b": 2, "c": 3}
swapped = {v: k for k, v in original.items()}
# {1: "a", 2: "b", 3: "c"}

# Group by first letter:
words = ["apple", "avocado", "banana", "blueberry", "cherry"]
grouped = {}
for w in words:
    grouped.setdefault(w[0], []).append(w)
# {"a": ["apple", "avocado"], "b": ["banana", "blueberry"], "c": ["cherry"]}
```

### Set Comprehension

```python
# Unique first letters:
words = ["apple", "avocado", "banana", "blueberry"]
first_letters = {w[0] for w in words}
# {"a", "b"}
```

### When NOT to Use Comprehensions

```python
# TOO COMPLEX — use a regular loop:
result = [
    transform(item)
    for group in data
    for item in group.items
    if item.is_valid()
    if item.category in allowed
]

# CLEARER as a loop:
result = []
for group in data:
    for item in group.items:
        if item.is_valid() and item.category in allowed:
            result.append(transform(item))
```

---

## Type Hints

Type hints are **optional annotations** that document what types a function expects. They don't enforce anything at runtime but help editors and tools catch errors.

```python
# Without type hints:
def greet(name):
    return f"Hello, {name}"

# With type hints:
def greet(name: str) -> str:
    return f"Hello, {name}"
```

### Common Type Hints

```python
from typing import Optional, List, Dict, Tuple, Union

# Basic types:
name: str = "prod"
count: int = 5
ratio: float = 0.5
active: bool = True

# Function signatures:
def get_cluster(name: str) -> dict:
    ...

def find_clusters(cloud: str, limit: int = 10) -> List[dict]:
    ...

def get_status(name: str) -> Optional[str]:       # can return str or None
    ...

# Complex types:
config: Dict[str, str] = {"key": "value"}
point: Tuple[float, float] = (1.0, 2.0)
value: Union[str, int] = "hello"                   # str or int

# Python 3.10+ (simpler syntax):
def get_status(name: str) -> str | None:           # instead of Optional[str]
    ...
```

### When to Use Type Hints

- **Function signatures** — most valuable, documents the interface
- **Complex data structures** — when return type isn't obvious
- **Library/shared code** — helps other users
- **Skip for** simple scripts, obvious types, local variables

---

## *args and **kwargs Patterns

### Passing Through Arguments

```python
def wrapper(func, *args, **kwargs):
    """Call func with any arguments."""
    print(f"Calling {func.__name__}")
    return func(*args, **kwargs)

wrapper(print, "hello", "world", end="!\n")
# Calling print
# hello world!
```

### Building Keyword Arguments Dynamically

```python
def create_cluster(**kwargs):
    """Create cluster from keyword arguments."""
    defaults = {
        "cloud": "aws",
        "region": "us-east-1",
        "size": 3,
    }
    config = {**defaults, **kwargs}    # kwargs override defaults
    print(f"Creating cluster: {config}")

create_cluster(name="prod", size=5)
# Creating cluster: {"cloud": "aws", "region": "us-east-1", "size": 5, "name": "prod"}
```

### Collect Remaining Arguments

```python
def head(first, *rest):
    """Get first element, rest goes into tuple."""
    print(f"First: {first}")
    print(f"Rest: {rest}")

head(1, 2, 3, 4)
# First: 1
# Rest: (2, 3, 4)
```

---

## Unpacking and Spreading

### Tuple/List Unpacking

```python
# Basic:
a, b, c = [1, 2, 3]

# Swap:
a, b = b, a

# Star unpacking (Python 3):
first, *middle, last = [1, 2, 3, 4, 5]
# first=1, middle=[2,3,4], last=5

head, *tail = [1, 2, 3, 4]
# head=1, tail=[2,3,4]

# Ignore values:
_, name, _ = ("id_123", "Alice", "extra")
```

### Dict Unpacking (Spreading)

```python
defaults = {"cloud": "aws", "region": "us-east-1"}
overrides = {"region": "eu-west-1", "name": "prod"}

config = {**defaults, **overrides}
# {"cloud": "aws", "region": "eu-west-1", "name": "prod"}

# Spread into function call:
def create_cluster(name, cloud, region):
    print(f"{name} on {cloud} in {region}")

create_cluster(**config)
# "prod on aws in eu-west-1"
```

### List Unpacking (Spreading)

```python
base_args = ["--verbose", "--timeout=30"]
extra_args = ["--cloud", "aws"]
all_args = [*base_args, *extra_args]
# ["--verbose", "--timeout=30", "--cloud", "aws"]
```

---

## Closures

A closure is a function that remembers variables from its enclosing scope:

```python
def make_multiplier(factor):
    def multiply(n):
        return n * factor          # 'factor' is remembered
    return multiply

double = make_multiplier(2)
triple = make_multiplier(3)

print(double(5))                   # 10
print(triple(5))                   # 15
```

### Practical: Logger Factory

```python
def make_logger(prefix):
    def log(message):
        print(f"[{prefix}] {message}")
    return log

info = make_logger("INFO")
error = make_logger("ERROR")

info("Starting up")               # [INFO] Starting up
error("Connection failed")        # [ERROR] Connection failed
```

### Closures Power Decorators

Decorators are closures — `wrapper` remembers `func`:

```python
def timer(func):                   # func is captured by closure
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)    # func is remembered
        print(f"{time.time() - start:.2f}s")
        return result
    return wrapper
```

---

## Useful Standard Library Tools

### itertools — Iteration Helpers

```python
from itertools import chain, groupby, islice, zip_longest

# Chain multiple iterables:
all_items = list(chain([1, 2], [3, 4], [5, 6]))
# [1, 2, 3, 4, 5, 6]

# Take first N items:
first_five = list(islice(huge_generator(), 5))

# Group by key:
data = [("aws", "c1"), ("azure", "c2"), ("aws", "c3")]
data.sort(key=lambda x: x[0])     # must be sorted first
for cloud, clusters in groupby(data, key=lambda x: x[0]):
    print(f"{cloud}: {list(clusters)}")
```

### collections — Specialized Containers

```python
from collections import Counter, defaultdict, OrderedDict

# Count occurrences:
words = ["aws", "azure", "aws", "gcp", "aws"]
counts = Counter(words)
# Counter({"aws": 3, "azure": 1, "gcp": 1})
print(counts.most_common(2))      # [("aws", 3), ("azure", 1)]

# Dict with default values:
groups = defaultdict(list)
for name, cloud in [("c1", "aws"), ("c2", "azure"), ("c3", "aws")]:
    groups[cloud].append(name)
# {"aws": ["c1", "c3"], "azure": ["c2"]}
```

### functools — Function Tools

```python
from functools import partial, reduce

# Partial application — pre-fill some arguments:
import json
pretty_json = partial(json.dumps, indent=2, sort_keys=True)
print(pretty_json({"b": 2, "a": 1}))

# Reduce — cumulative operation:
from functools import reduce
total = reduce(lambda a, b: a + b, [1, 2, 3, 4])     # 10
```

### textwrap — Text Formatting

```python
import textwrap

long_text = "This is a very long string that needs to be wrapped to fit within a certain width for display purposes."
print(textwrap.fill(long_text, width=40))
# This is a very long string that needs
# to be wrapped to fit within a certain
# width for display purposes.

# Dedent (remove common leading whitespace):
text = textwrap.dedent("""
    Name: prod
    Cloud: aws
    Region: us-east-1
""").strip()
```

---

## Shell vs Python Comparison

| Concept | Shell | Python |
|---------|-------|--------|
| Wrap a command | Function calling function | Decorator (`@my_decorator`) |
| Caching | Save to file | `@lru_cache` |
| Retry | `for i in 1 2 3; do cmd && break; done` | `@retry` decorator |
| Iterate large file | `while read line; do ...; done < file` | Generator with `yield` |
| Cleanup guarantee | `trap cleanup EXIT` | `with` statement / `finally` |
| Temp directory | `mktemp -d; trap 'rm -rf $tmp' EXIT` | `with tempfile.TemporaryDirectory()` |
| Type checking | N/A | Type hints + mypy |
| Spread args | `"$@"` | `*args, **kwargs` |
| Associative array default | `${arr[key]:-default}` | `defaultdict(list)` |
| Count occurrences | `sort | uniq -c` | `Counter(items)` |

---

> **Practice:** Chapter `05-real-world-patterns.py`
> **Next:** [14 - Python vs Shell Quick Reference](14-python-vs-shell-reference.md)
