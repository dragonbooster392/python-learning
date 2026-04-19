# 11 — Error Handling

How to catch, handle, and create errors gracefully instead of crashing.

---

## Table of Contents

- [What Are Exceptions?](#what-are-exceptions)
- [try / except — Catching Errors](#try-except-catching-errors)
- [Multiple except Blocks](#multiple-except-blocks)
- [else and finally](#else-and-finally)
- [Getting Error Details](#getting-error-details)
- [Common Exception Types](#common-exception-types)
- [Raising Exceptions](#raising-exceptions)
- [Custom Exception Classes](#custom-exception-classes)
- [Exception Hierarchy](#exception-hierarchy)
- [When to Catch vs When to Crash](#when-to-catch-vs-when-to-crash)
- [Common Patterns](#common-patterns)
- [Shell vs Python Error Handling](#shell-vs-python-error-handling)

---

## What Are Exceptions?

An **exception** is an error that happens during execution. Without handling, it crashes your script.

```python
# This crashes:
result = 10 / 0                    # ZeroDivisionError: division by zero
data = {"a": 1}
data["b"]                          # KeyError: 'b'
int("hello")                       # ValueError: invalid literal
```

In shell, errors are just exit codes (0 = success, non-zero = failure). Python is more structured — each error has a **type** and a **message**.

---

## try / except — Catching Errors

Wrap risky code in `try`, handle the error in `except`:

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Can't divide by zero!")
    result = 0
```

### Structure

```python
try:
    # code that might fail
    risky_operation()
except SomeError:
    # what to do if SomeError occurs
    handle_error()
```

- Code in `try` runs normally.
- If an error occurs, Python jumps to the matching `except` block.
- If no error occurs, `except` is skipped entirely.

### Shell Comparison

```bash
# Shell — check exit code:
if command; then
    echo "Success"
else
    echo "Failed with exit code $?"
fi

# Shell — trap:
trap 'echo "Error on line $LINENO"' ERR
```

```python
# Python — structured exception handling:
try:
    result = command()
except CommandError as e:
    print(f"Failed: {e}")
```

---

## Multiple except Blocks

Catch different error types separately:

```python
try:
    data = json.loads(raw_text)
    name = data["name"]
    count = int(data["count"])
except json.JSONDecodeError:
    print("Invalid JSON")
except KeyError as e:
    print(f"Missing field: {e}")
except ValueError:
    print("count is not a number")
except Exception as e:
    print(f"Unexpected error: {e}")
```

### Rules

- Python tries `except` blocks **in order** — first match wins.
- Put **specific** exceptions first, **general** ones last.
- `except Exception` catches (almost) everything — use as last resort.
- **Never use bare `except:`** (without a type) — it catches system exits and keyboard interrupts too.

### Catching Multiple Types in One Block

```python
try:
    process(data)
except (ValueError, TypeError, KeyError) as e:
    print(f"Bad data: {e}")
```

---

## else and finally

### else — Runs Only if No Error

```python
try:
    result = compute()
except ComputeError:
    print("Computation failed")
else:
    print(f"Success: {result}")    # only runs if try succeeded
```

### finally — ALWAYS Runs

```python
try:
    f = open("file.txt")
    data = f.read()
except FileNotFoundError:
    print("File not found")
finally:
    f.close()                      # runs even if error occurred!
    print("Cleanup done")          # ALWAYS runs
```

### Full Structure

```python
try:
    # risky code
    result = do_work()
except SpecificError as e:
    # handle specific error
    log_error(e)
except Exception as e:
    # handle any other error
    log_unexpected(e)
else:
    # only if try succeeded (no error)
    save_result(result)
finally:
    # ALWAYS runs — cleanup
    cleanup()
```

---

## Getting Error Details

### The Error Message

```python
try:
    int("hello")
except ValueError as e:            # 'as e' captures the error object
    print(f"Error: {e}")           # "Error: invalid literal for int() with base 10: 'hello'"
    print(type(e))                 # <class 'ValueError'>
```

### Getting the Traceback

```python
import traceback

try:
    risky_code()
except Exception as e:
    print(f"Error: {e}")
    traceback.print_exc()          # prints full stack trace (for debugging)

    # Or capture as string:
    tb_string = traceback.format_exc()
```

---

## Common Exception Types

| Exception | When It Happens | Example |
|-----------|----------------|---------|
| `ValueError` | Wrong value for operation | `int("hello")` |
| `TypeError` | Wrong type for operation | `"5" + 3` |
| `KeyError` | Dict key doesn't exist | `d["missing_key"]` |
| `IndexError` | List index out of range | `[1,2,3][10]` |
| `AttributeError` | Object has no such attribute | `"hello".missing()` |
| `NameError` | Variable not defined | `print(undefined_var)` |
| `FileNotFoundError` | File doesn't exist | `open("nonexistent.txt")` |
| `PermissionError` | No permission | `open("/root/secret")` |
| `IOError` | General I/O problem | Disk full, file locked |
| `ConnectionError` | Network problem | Can't reach server |
| `TimeoutError` | Operation timed out | Network timeout |
| `JSONDecodeError` | Invalid JSON | `json.loads("not json")` |
| `ModuleNotFoundError` | Import failed | `import nonexistent` |
| `ImportError` | Can't import name | `from os import nonexistent` |
| `ZeroDivisionError` | Division by zero | `10 / 0` |
| `StopIteration` | Iterator exhausted | `next(empty_iterator)` |
| `RuntimeError` | General runtime error | Various |
| `NotImplementedError` | Not yet implemented | Placeholder in abstract class |
| `SystemExit` | `sys.exit()` called | — |
| `KeyboardInterrupt` | User pressed Ctrl+C | — |

---

## Raising Exceptions

You can **create and throw** errors yourself:

```python
# Raise a built-in exception:
if age < 0:
    raise ValueError("Age cannot be negative")

if not api_key:
    raise ValueError("API key is required")

# Raise with no message:
raise RuntimeError

# Re-raise the current exception (inside except block):
try:
    result = api_call()
except ConnectionError:
    log_error("API failed")
    raise                          # re-raises the same error
```

### When to Raise

- When a function receives invalid input
- When a required condition isn't met
- When you want the caller to handle the problem
- When continuing would produce wrong results

---

## Custom Exception Classes

Create your own error types for clearer error handling:

```python
# Define custom exceptions:
class SpectroError(Exception):
    """Base error for all spectro errors."""
    pass

class AuthError(SpectroError):
    """Authentication failed."""
    pass

class NotFoundError(SpectroError):
    """Resource not found."""
    pass

class APIError(SpectroError):
    """API returned an error."""
    def __init__(self, message, status_code=None):
        super().__init__(message)
        self.status_code = status_code
```

### Using Custom Exceptions

```python
# Raise:
if response.status_code == 401:
    raise AuthError("Invalid API key")

if response.status_code == 404:
    raise NotFoundError(f"Cluster '{name}' not found")

if response.status_code >= 400:
    raise APIError(f"HTTP {response.status_code}", status_code=response.status_code)

# Catch:
try:
    client.get_cluster(name)
except NotFoundError:
    print(f"Cluster {name} doesn't exist")
except AuthError:
    print("Please check your API key")
except SpectroError as e:
    print(f"Spectro error: {e}")           # catches any SpectroError subclass
```

### Why Custom Exceptions?

- **Clarity:** `AuthError` is clearer than `Exception`
- **Granularity:** Catch specific errors, let others propagate
- **Hierarchy:** Catch all project errors with the base class

spectro-cli defines its custom exceptions in `exceptions.py`.

---

## Exception Hierarchy

Python exceptions form a hierarchy. Catching a parent catches all children:

```
BaseException
├── SystemExit                      # sys.exit()
├── KeyboardInterrupt               # Ctrl+C
├── GeneratorExit
└── Exception                       # catch this for "normal" errors
    ├── ValueError
    ├── TypeError
    ├── KeyError
    ├── IndexError
    ├── FileNotFoundError
    ├── ConnectionError
    ├── RuntimeError
    ├── StopIteration
    └── ... (many more)
```

### Key Rule

- **Catch `Exception`** for general errors (not `BaseException`)
- `BaseException` includes `SystemExit` and `KeyboardInterrupt` — you usually don't want to catch those

```python
# GOOD — catches "normal" errors:
except Exception as e:

# BAD — catches Ctrl+C and sys.exit() too:
except BaseException as e:

# WORST — same as BaseException:
except:
```

---

## When to Catch vs When to Crash

### Catch When

- You can **recover** meaningfully (use default value, retry, skip item)
- You need to **clean up** resources
- You want to give a **user-friendly error message**
- The error is **expected** (file might not exist, network might fail)

### Let It Crash When

- The error is a **programming bug** (should be fixed, not hidden)
- You **can't recover** anyway
- You're in development/debugging (stack trace is helpful)
- The caller should handle it (let it propagate up)

### Anti-Patterns (Don't Do These)

```python
# BAD — silently swallows ALL errors:
try:
    result = do_work()
except:
    pass

# BAD — catches everything with no distinction:
try:
    result = do_work()
except Exception:
    print("Something went wrong")        # useless error message

# GOOD — specific handling:
try:
    result = do_work()
except ConnectionError:
    print("Network error — retrying...")
    result = retry_work()
except ValueError as e:
    print(f"Invalid data: {e}")
    sys.exit(1)
```

---

## Common Patterns

### LBYL vs EAFP

Python prefers **EAFP** (Easier to Ask Forgiveness than Permission):

```python
# LBYL (Look Before You Leap) — check first, then do:
if "name" in data:
    name = data["name"]
else:
    name = "unknown"

# EAFP (Easier to Ask Forgiveness) — just do it, catch error:
try:
    name = data["name"]
except KeyError:
    name = "unknown"

# BEST — use .get() which handles it:
name = data.get("name", "unknown")
```

### Retry Pattern

```python
import time

def retry(func, max_attempts=3, delay=1):
    for attempt in range(1, max_attempts + 1):
        try:
            return func()
        except ConnectionError:
            if attempt == max_attempts:
                raise                      # give up after max attempts
            print(f"Attempt {attempt} failed, retrying in {delay}s...")
            time.sleep(delay)
            delay *= 2                     # exponential backoff
```

### Context Manager for Cleanup

```python
try:
    connection = connect_to_database()
    result = connection.query("SELECT ...")
finally:
    connection.close()                     # always cleanup

# Better — use 'with':
with connect_to_database() as connection:
    result = connection.query("SELECT ...")
# auto-closed
```

### Exit with Error

```python
import sys

try:
    config = load_config()
except FileNotFoundError:
    print("Error: config file not found", file=sys.stderr)
    sys.exit(1)
```

### Log and Re-raise

```python
try:
    result = api_call()
except Exception as e:
    logger.error(f"API call failed: {e}")
    raise                                  # let caller handle it too
```

---

## Shell vs Python Error Handling

| Concept | Shell | Python |
|---------|-------|--------|
| Check command success | `if command; then` | `try: ... except:` |
| Exit code | `$?` | Exception type + message |
| Error types | Just exit codes (1, 2, 127...) | Named: `ValueError`, `KeyError`, etc. |
| Error message | `echo "error" >&2` | `raise ValueError("error")` |
| Stop on error | `set -e` | Exceptions propagate by default |
| Trap errors | `trap 'handler' ERR` | `except` block |
| Cleanup | `trap 'cleanup' EXIT` | `finally` block |
| Custom errors | Exit codes (1-255) | Custom exception classes |
| Error details | Exit code only | Full stack trace + message |

---

> **Practice:** Chapter `04-files-errors-classes.py` sections 5-6
> **Next:** [12 - Classes and OOP](12-classes-oop.md)
