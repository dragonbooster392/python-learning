# python-learning

# Prompt to Reproduce Python Learning Concept Files

Generate a complete Python learning guide as 14 separate markdown files targeting Linux/shell users who already know bash scripting but are learning Python from scratch. The content should bridge shell knowledge to Python.

---

## Files to generate

```
01-what-is-python.md
02-variables.md
03-data-types.md
04-strings.md
05-operators.md
06-conditions-loops.md
07-data-structures.md
08-functions.md
09-modules-imports.md
10-file-io.md
11-error-handling.md
12-classes-oop.md
13-decorators-generators-advanced.md
14-python-vs-shell-reference.md
```

---

## Target audience

- DevOps / SRE / sysadmin who writes bash daily
- Knows shell variables, loops, conditionals, pipes, grep, awk, jq, curl
- Zero Python experience
- Goal: learn Python well enough to read and build CLI tools (like a Click-based CLI that talks to REST APIs)

---

## Style rules (follow strictly)

1. **Title format:** `# NN — Topic Name` (e.g., `# 02 — Variables`)
2. **One-line subtitle** immediately after the title — a plain sentence describing what the file covers
3. **Table of Contents** with markdown anchor links after the subtitle (for every file except 01)
4. **Use `---` horizontal rules** to separate major sections
5. **Every section has a `## Heading`**, subsections use `### Heading`
6. **Shell vs Python comparison tables** in every file — use markdown tables with columns: `| Concept | Shell | Python |`
7. **Code blocks:** Use ` ```python ` for Python, ` ```bash ` for shell, ` ```text ` for plain output. Every concept must have a runnable code example.
8. **Inline code** for all variable names, function names, keywords, filenames — always use backticks
9. **Tone:** Direct, practical, no fluff. Explain like talking to someone who already codes in another language. No "In this section we will learn..." — just teach.
10. **Bold** for key terms on first introduction only
11. **No emojis** in headings or body (except in tables where ✅ / ❌ are used for yes/no comparisons)
12. **Each file should be 400–800 lines** of content — comprehensive, not superficial
13. **Always relate back to shell:** "In bash you'd do X, in Python it's Y"
14. **Common mistakes section** in most files — show wrong code + the error + the fix
15. **Summary table** near the end of each file — `| Concept | Key Point |` format

---

## Footer format (every file)

End each file with:

```
---

> **Practice:** Chapter `NN-practice-file.py` sections X-Y
> **Next:** [NN - Next Topic](NN-next-topic.md)
```

The last file (14) should instead say:

```
> **This is the final concept file.** Go back to [Concepts Overview](index.md) for the full index.
```

---

## Practice file cross-references

- Files 01–05 reference practice `01-absolute-basics.py`
- Files 06–07 reference practice `02-data-structures.py`
- Files 08–09 reference practice `03-functions-modules.py`
- Files 10–12 reference practice `04-files-errors-classes.py`
- File 13 references practice `04-files-errors-classes.py`
- File 14 has no practice reference

---

## Content per file

### 01-what-is-python.md (~340 lines)

- What Python is (compared to shell), when to use which (table with ✅/❌)
- What Python is NOT (compiled, web-only, shell replacement, slow)
- How Python runs code (interpreted, bytecode under the hood)
- Installing Python (check version, Ubuntu/Debian, Mac, RHEL/CentOS)
- Python 2 vs 3 (always use 3)
- Running code: script file, one-liner `-c`, REPL interactive mode, `-m` module, shebang line
- **Indentation** — the #1 rule: 4 spaces, colon starts block, no braces/fi/done. Show shell vs Python side-by-side. Show wrong indentation = crash.
- File extension and structure: `.py`, standard file layout (shebang → imports → constants → classes → functions → main guard)
- Comments: `#`, inline, docstrings `"""`
- Virtual environments: what, why, shell analogy, venv commands, pipenv, requirements.txt
- Summary table

### 02-variables.md (~670 lines)

- What is a variable (labeled box analogy, it's a reference)
- Creating variables (no declaration keyword)
- Shell vs Python variables comparison table (create, read, prefix, quotes, spaces, scope, export, unset)
- The biggest difference: `$` in shell, none in Python
- Naming rules (must/invalid table, reserved words list)
- Naming conventions table (`snake_case`, `UPPER_SNAKE`, `PascalCase`, `_private`, `__dunder__`)
- Good vs Bad names examples
- Dynamic typing (no declarations, comparison table with Java/Go, pros/cons, type hints optional)
- Multiple assignment (same value, multiple values, swap)
- Constants (`UPPER_CASE` convention, no enforcement)
- Variable types at a glance (table: type, example, shell equivalent)
- Type checking (`type()`, `isinstance()`)
- Type conversion / casting (`int()`, `str()`, `float()`, `bool()` + what's truthy/falsy)
- Mutable vs Immutable (table, gotcha with lists)
- Truthy and Falsy values (table of all falsy values, practical patterns)
- `None` (what it is, check with `is None`, shell comparison)
- Variable scope (local in functions, global at file level, `global` keyword, LEGB rule)
- Deleting variables (`del`, existence check)
- Common mistakes (with error examples)

### 03-data-types.md (~475 lines)

- Everything is an object
- Core types table: `int`, `float`, `bool`, `str`, `None` + `complex`, `bytes`
- Collection types table: `list`, `tuple`, `dict`, `set`
- Each type with examples & shell comparison
- Type hierarchy diagram (text-based)
- Type conversion between all types
- When to use which type (decision guide)
- Common gotchas (float precision, bool is int subclass, mutable default args)
- Summary table

### 04-strings.md (~730 lines)

- Creating strings (single, double, triple quotes, raw strings)
- Escape characters table
- String indexing and slicing (with ASCII diagram of indices)
- f-strings (formatted string literals) — the primary way to embed values
- Format specifiers (width, precision, alignment, comma separator, percentage)
- All string methods organized by category (check, search, modify, split/join, strip, align)
- Multiline strings
- String comparison and sorting
- Useful patterns (build strings, join lists, template-style, regex basics)
- Shell vs Python string operations comparison table
- Common mistakes
- Summary table

### 05-operators.md (~465 lines)

- Arithmetic operators table (`+`, `-`, `*`, `/`, `//`, `%`, `**`)
- Assignment operators table (`=`, `+=`, `-=`, `*=`, `/=`, `//=`, `%=`, `**=`)
- Comparison operators table (`==`, `!=`, `>`, `<`, `>=`, `<=`) with shell equivalents
- Logical operators (`and`, `or`, `not`) — not `&&`, `||`, `!`
- Membership operators (`in`, `not in`)
- Identity operators (`is`, `is not`) — vs `==`
- Bitwise operators table
- Walrus operator `:=` (Python 3.8+)
- Operator precedence table
- Shell vs Python operators comparison table
- Common mistakes
- Summary table

### 06-conditions-loops.md (~750 lines)

- `if` / `elif` / `else`: structure, rules, examples
- Combining conditions (`and`, `or`, `not`)
- Checking multiple values with `in`
- Checking `None`/empty
- Ternary (inline if)
- `for` loop: iterating lists, dicts, strings, `range()`
- `enumerate()` — loop with index
- `while` loop
- `break`, `continue`
- `else` on loops (unique Python feature)
- Nested loops
- Loop patterns for scripting (filter, collect, find first, count, transform, process dicts)
- `pass` placeholder
- `match`/`case` (Python 3.10+)
- Shell vs Python control flow comparison table (`if`, `for`, `while`, `case`, `break`, `continue`)
- Common mistakes
- Summary table

### 07-data-structures.md (~550 lines)

- Lists: create, access, slice, modify, methods table, comprehensions, nested
- Dictionaries: create, access, methods table, iteration, comprehensions, nested
- Tuples: create, immutability, when to use, unpacking
- Sets: create, operations (union, intersection, difference), when to use
- Choosing the right structure (decision table)
- Real-world nested data (JSON-like API response processing)
- Shell vs Python comparison
- Common mistakes
- Summary table

### 08-functions.md (~624 lines)

- Defining functions (`def`, naming, docstrings)
- Parameters and arguments (positional, keyword, default values)
- Return values (single, multiple via tuple, early return)
- `*args` and `**kwargs`
- Type hints on functions
- Scope inside functions (local vs global, `global` keyword)
- Lambda (anonymous) functions
- Functions as first-class objects (passing functions, `map`/`filter`)
- Useful built-in functions table (`print`, `len`, `type`, `range`, `enumerate`, `zip`, `sorted`, `reversed`, `map`, `filter`, `any`, `all`, `min`, `max`, `sum`, `isinstance`, `dir`, `help`, `vars`, `getattr`)
- Shell vs Python comparison
- Common mistakes
- Summary table

### 09-modules-imports.md (~685 lines)

- What is a module (any `.py` file)
- Import styles: `import`, `from...import`, `as` alias
- The import search path (`sys.path`)
- Standard library highlights with examples: `os`, `sys`, `pathlib`, `json`, `yaml`, `subprocess`, `datetime`, `re`, `argparse`, `shutil`, `tempfile`, `logging`, `collections`
- Third-party packages and pip
- `__init__.py` and packages
- Relative vs absolute imports
- `__name__ == "__main__"` guard (with detailed explanation of what happens when importing vs running)
- Creating your own module (multi-file project layout)
- Shell vs Python comparison (`source` vs `import`)
- Common mistakes
- Summary table

### 10-file-io.md (~540 lines)

- Opening files (`with` statement, why always use `with`)
- Reading files (full, lines, line-by-line, first N lines)
- Writing files (`write`, `writelines`, overwrite vs create)
- Appending to files
- File modes table (`r`, `w`, `a`, `x`, `b`, `+`)
- Working with JSON (read, write, pretty print, API response handling)
- Working with YAML (read, write, multi-doc)
- Working with CSV (`reader`, `writer`, `DictReader`, `DictWriter`)
- Working with `.env` files (`python-dotenv`)
- File path operations (`pathlib`: join, parent, stem, exists, glob)
- Checking existence (file, dir)
- Directory operations (create, list, walk, remove)
- Temporary files (`tempfile`)
- Common patterns (find-replace in files, process all files in dir, read config, write report)
- Shell vs Python file operations comparison table

### 11-error-handling.md (~500 lines)

- What are exceptions (vs shell exit codes)
- `try` / `except`: structure, shell comparison
- Multiple `except` blocks
- `else` and `finally`
- Getting error details (`as e`, `str(e)`, `type(e)`)
- Common exception types table (`ValueError`, `TypeError`, `KeyError`, `IndexError`, `FileNotFoundError`, `AttributeError`, `ImportError`, `PermissionError`, `ConnectionError`, `TimeoutError`, `ZeroDivisionError`, `OSError`, `RuntimeError`, `StopIteration`, `JSONDecodeError`)
- Raising exceptions (`raise`)
- Custom exception classes
- Exception hierarchy (text diagram)
- When to catch vs when to crash (guidelines)
- Common patterns (retry logic, default on failure, cleanup, validate input, wrap library errors, log and re-raise)
- Shell vs Python error handling comparison table
- Summary table

### 12-classes-oop.md (~787 lines)

- What is a class (blueprint analogy, shell equivalent table)
- Creating a class (empty, naming convention `PascalCase`)
- `__init__` constructor (parameters, defaults, key points)
- `self` (instance reference, why always first parameter)
- Attributes and methods (data + behavior)
- Class vs instance attributes
- Practical example: API Client class
- Practical example: Config Manager class
- String representation (`__str__`, `__repr__`)
- Properties (`@property`, getter/setter)
- Inheritance (base class, `super()`, overriding, when to use)
- `@staticmethod` and `@classmethod`
- Private and protected by convention (`_` and `__`)
- Dataclasses (`@dataclass`, less boilerplate)
- When to use classes vs plain functions (decision guide)
- Classes in a real CLI codebase (show how a CLI tool uses classes for Client, Config, models)
- Shell vs Python comparison table
- Summary table

### 13-decorators-generators-advanced.md (~860 lines)

- Decorators: what they are, `@` syntax, how they work (sugar for wrapping), writing your own (timer, logger, retry, require_auth), decorators with arguments, common built-in decorators (`@property`, `@staticmethod`, `@classmethod`, `@functools.wraps`, `@functools.lru_cache`), library decorators (`click.command`, `click.option`), stacking decorators
- Generators: what they are (`yield` vs `return`), generator expressions, when to use (memory efficiency)
- Context managers: what they are (`with` statement), writing your own (`__enter__`/`__exit__`), `contextlib.contextmanager`
- Advanced comprehensions: nested, dict, set, conditional
- Type hints: basic types, `Optional`, `Union`, `List`, `Dict`, function annotations, when to use
- `*args` and `**kwargs` patterns: forwarding, merging dicts, decorator patterns
- Unpacking and spreading: `*` for lists, `**` for dicts, in function calls
- Closures: functions inside functions, enclosing scope
- Useful stdlib tools: `functools` (`partial`, `reduce`, `lru_cache`), `itertools` (`chain`, `product`, `groupby`), `collections` (`Counter`, `defaultdict`, `namedtuple`, `OrderedDict`), `dataclasses`
- Shell vs Python comparison
- Summary table

### 14-python-vs-shell-reference.md (~465 lines)

- Pure side-by-side comparison tables with `Shell` | `Python` columns
- Sections: Variables and Data, String Operations, Conditions and Logic, Loops, Functions, File Operations, Directory Operations, Process and System, Input/Output, Error Handling, Arrays/Lists/Dicts, Text Processing, Date and Time, Networking and HTTP, JSON/YAML/Config, Script Organization, Common One-Liners
- **No prose** — only tables with `Operation | Shell | Python` columns
- This is meant as a quick-lookup cheat sheet, not a teaching file
- Footer says "This is the final concept file" with link back to index

---

## Key differentiators

- **Shell-first perspective** — every concept is introduced by comparing it to what the reader already knows in bash
- **Comparison tables in every file** — `| Concept | Shell | Python |` format
- **Direct tone with no filler** — no "Welcome!", no "In this section we will explore..."
- **~400–800 lines per file** of real teaching content with runnable code examples
- **Total output: ~8,400 lines across 14 files**
