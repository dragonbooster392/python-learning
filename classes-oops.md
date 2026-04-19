# 12 — Classes and OOP

How to group related data and behavior together using classes (Object-Oriented Programming).

---

## Table of Contents

- [What Is a Class?](#what-is-a-class)
- [Creating a Class](#creating-a-class)
- [\_\_init\_\_ — The Constructor](#__init__-the-constructor)
- [self — The Instance Reference](#self-the-instance-reference)
- [Attributes and Methods](#attributes-and-methods)
- [Class vs Instance Attributes](#class-vs-instance-attributes)
- [Practical Example: API Client](#practical-example-api-client)
- [Practical Example: Config Manager](#practical-example-config-manager)
- [String Representation](#string-representation)
- [Properties — Controlled Access](#properties-controlled-access)
- [Inheritance — Reusing Code](#inheritance-reusing-code)
- [@staticmethod and @classmethod](#staticmethod-and-classmethod)
- [Private and Protected by Convention](#private-and-protected-by-convention)
- [Dataclasses — Less Boilerplate](#dataclasses-less-boilerplate)
- [When to Use Classes vs Plain Functions](#when-to-use-classes-vs-plain-functions)
- [Classes in spectro-cli](#classes-in-spectro-cli)
- [Shell vs Python Comparison](#shell-vs-python-comparison)

---

## What Is a Class?

A **class** is a blueprint for creating objects that bundle **data** (attributes) and **behavior** (methods) together.

Think of it like this:

| Concept | Shell Equivalent | Python |
|---------|-----------------|--------|
| Data storage | Variables, files | Attributes |
| Operations | Functions | Methods |
| Grouping | A script file | A class |
| Instance | Running the script | Creating an object |

```python
# Without classes - loose data + functions:
cluster_name = "prod-east"
cluster_status = "active"

def get_cluster_info(name, status):
    return f"{name}: {status}"

# With a class - data + behavior together:
class Cluster:
    def __init__(self, name, status):
        self.name = name
        self.status = status

    def get_info(self):
        return f"{self.name}: {self.status}"

c = Cluster("prod-east", "active")
print(c.get_info())                # "prod-east: active"
```

---

## Creating a Class

```python
class Cluster:
    pass                           # empty class (placeholder)

# Create instances (objects):
c1 = Cluster()
c2 = Cluster()

# They're separate objects:
print(c1 is c2)                    # False
```

### Naming Convention

- Classes use **PascalCase**: `Cluster`, `APIClient`, `NodeProfile`
- Functions/methods use **snake_case**: `get_cluster`, `create_profile`
- This is a Python-wide convention (PEP 8)

---

## \_\_init\_\_ — The Constructor

`__init__` runs automatically when you create a new instance:

```python
class Cluster:
    def __init__(self, name, cloud, region="us-east-1"):
        self.name = name           # store as instance attribute
        self.cloud = cloud
        self.region = region
        self.status = "pending"    # default attribute

# Creating instances:
c1 = Cluster("prod", "aws")
c2 = Cluster("dev", "azure", region="westus2")

print(c1.name)                    # "prod"
print(c1.region)                  # "us-east-1" (default)
print(c2.region)                  # "westus2"
```

### Key Points

- `__init__` is NOT a "constructor" technically — it's an **initializer**
- First parameter is always `self` (the new instance)
- You don't call `__init__` directly — Python calls it when you do `Cluster(...)`
- Set defaults using default parameter values or assignments in the body

---

## self — The Instance Reference

`self` is a reference to the current instance. It's how the object refers to its own data:

```python
class Cluster:
    def __init__(self, name):
        self.name = name           # self.name = the instance's 'name' attribute

    def describe(self):
        return f"Cluster: {self.name}"
                                   # uses self.name to access its own data

    def rename(self, new_name):
        self.name = new_name       # modifies its own data

c = Cluster("prod")
c.describe()                       # "Cluster: prod"
c.rename("production")
c.describe()                       # "Cluster: production"
```

### Rules

- **Every** method's first parameter must be `self`
- When calling, you **don't** pass `self` — Python does it automatically
- `self` is just a convention name — you *could* use `this` or any name, but **always use `self`**

```python
class Example:
    def method(self):              # define with self
        print(self.data)

obj = Example()
obj.method()                       # call without self — Python injects it
```

---

## Attributes and Methods

### Instance Attributes — Data

```python
class Server:
    def __init__(self, host, port=8080):
        self.host = host           # instance attribute
        self.port = port           # instance attribute
        self.connections = 0       # instance attribute with default

s = Server("192.168.1.1")
print(s.host)                      # "192.168.1.1"
print(s.port)                      # 8080

# You can add attributes dynamically (but generally shouldn't):
s.name = "web-01"                  # works, but not recommended
```

### Methods — Behavior

```python
class Server:
    def __init__(self, host, port=8080):
        self.host = host
        self.port = port
        self.connections = 0

    def connect(self):
        """Open a connection."""
        self.connections += 1
        return f"Connected to {self.host}:{self.port}"

    def disconnect(self):
        """Close a connection."""
        if self.connections > 0:
            self.connections -= 1

    def status(self):
        """Return server status."""
        return {
            "host": self.host,
            "port": self.port,
            "active_connections": self.connections,
        }

s = Server("10.0.0.1")
print(s.connect())                 # "Connected to 10.0.0.1:8080"
print(s.status())                  # {"host": "10.0.0.1", "port": 8080, "active_connections": 1}
```

---

## Class vs Instance Attributes

```python
class Cluster:
    # Class attribute — shared by ALL instances:
    api_version = "v1"
    all_clusters = []

    def __init__(self, name):
        # Instance attribute — unique to each instance:
        self.name = name
        Cluster.all_clusters.append(self)

c1 = Cluster("prod")
c2 = Cluster("dev")

print(c1.api_version)             # "v1" (reads from class)
print(c2.api_version)             # "v1" (same value)
print(Cluster.api_version)        # "v1" (accessed via class directly)

print(len(Cluster.all_clusters))  # 2 (shared across instances)
```

### When to Use Each

| Use | For |
|-----|-----|
| Class attributes | Constants, counters, shared state, defaults |
| Instance attributes | Data unique to each object |

### Gotcha: Mutable Class Attributes

```python
# DANGEROUS — mutable class attribute shared by all:
class Bad:
    items = []                     # shared list!

    def add(self, item):
        self.items.append(item)    # modifies the SHARED list

b1 = Bad()
b2 = Bad()
b1.add("hello")
print(b2.items)                    # ["hello"] — b2 sees b1's data!

# CORRECT — put mutable data in __init__:
class Good:
    def __init__(self):
        self.items = []            # each instance gets its own list

    def add(self, item):
        self.items.append(item)
```

---

## Practical Example: API Client

This is the pattern used in spectro-cli's `PaletteClient`:

```python
import requests

class APIClient:
    """Simple REST API client."""

    def __init__(self, base_url, api_key=None, timeout=30):
        self.base_url = base_url.rstrip("/")
        self.timeout = timeout
        self.session = requests.Session()
        if api_key:
            self.session.headers["Authorization"] = f"Bearer {api_key}"

    def _request(self, method, endpoint, **kwargs):
        """Make an HTTP request (internal method)."""
        url = f"{self.base_url}/{endpoint.lstrip('/')}"
        response = self.session.request(
            method, url, timeout=self.timeout, **kwargs
        )
        response.raise_for_status()
        return response.json()

    def get(self, endpoint, params=None):
        """GET request."""
        return self._request("GET", endpoint, params=params)

    def post(self, endpoint, data=None):
        """POST request."""
        return self._request("POST", endpoint, json=data)

    def delete(self, endpoint):
        """DELETE request."""
        return self._request("DELETE", endpoint)

# Usage:
client = APIClient("https://api.example.com", api_key="abc123")
clusters = client.get("/v1/clusters")
client.post("/v1/clusters", data={"name": "new-cluster"})
```

---

## Practical Example: Config Manager

```python
import json
import os

class ConfigManager:
    """Manage app configuration with file persistence."""

    def __init__(self, config_path="~/.myapp/config.json"):
        self.path = os.path.expanduser(config_path)
        self.data = {}
        self._load()

    def _load(self):
        """Load config from file."""
        if os.path.exists(self.path):
            with open(self.path) as f:
                self.data = json.load(f)

    def _save(self):
        """Save config to file."""
        os.makedirs(os.path.dirname(self.path), exist_ok=True)
        with open(self.path, "w") as f:
            json.dump(self.data, f, indent=2)

    def get(self, key, default=None):
        """Get a config value."""
        return self.data.get(key, default)

    def set(self, key, value):
        """Set a config value and save."""
        self.data[key] = value
        self._save()

    def delete(self, key):
        """Remove a config key."""
        self.data.pop(key, None)
        self._save()

# Usage:
config = ConfigManager()
config.set("api_url", "https://api.example.com")
print(config.get("api_url"))
```

---

## String Representation

### \_\_str\_\_ — Human-readable

```python
class Cluster:
    def __init__(self, name, status):
        self.name = name
        self.status = status

    def __str__(self):
        return f"{self.name} ({self.status})"

c = Cluster("prod", "active")
print(c)                           # "prod (active)"
print(f"Cluster: {c}")            # "Cluster: prod (active)"
```

### \_\_repr\_\_ — Developer-readable

```python
class Cluster:
    def __init__(self, name, status):
        self.name = name
        self.status = status

    def __repr__(self):
        return f"Cluster(name='{self.name}', status='{self.status}')"

c = Cluster("prod", "active")
print(repr(c))                     # Cluster(name='prod', status='active')
print([c])                         # [Cluster(name='prod', status='active')]
```

### Both Together

```python
class Cluster:
    def __init__(self, name, status):
        self.name = name
        self.status = status

    def __str__(self):             # for print() and f-strings
        return f"{self.name} ({self.status})"

    def __repr__(self):            # for debugging, lists, dicts
        return f"Cluster('{self.name}', '{self.status}')"
```

---

## Properties — Controlled Access

Properties let you add logic to attribute access without changing the interface:

```python
class Cluster:
    def __init__(self, name):
        self._name = name          # "private" storage (convention)

    @property
    def name(self):
        """Get the cluster name."""
        return self._name

    @name.setter
    def name(self, value):
        """Set the cluster name with validation."""
        if not value or not value.strip():
            raise ValueError("Cluster name cannot be empty")
        self._name = value.strip().lower()

c = Cluster("  Prod  ")
print(c.name)                      # "  Prod  " (stored as-is in __init__)
c.name = "  Dev  "                 # setter runs: strips and lowercases
print(c.name)                      # "dev"
c.name = ""                        # raises ValueError
```

### Read-only Property

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        """Computed on access, read-only."""
        return 3.14159 * self.radius ** 2

c = Circle(5)
print(c.area)                      # 78.53975 (no parentheses!)
c.area = 100                       # AttributeError: can't set attribute
```

---

## Inheritance — Reusing Code

A class can inherit from another, getting all its attributes and methods:

```python
# Parent (base) class:
class Resource:
    def __init__(self, name, kind):
        self.name = name
        self.kind = kind
        self.status = "pending"

    def describe(self):
        return f"{self.kind}/{self.name} [{self.status}]"

# Child classes (inherit from Resource):
class Cluster(Resource):
    def __init__(self, name, cloud):
        super().__init__(name, kind="Cluster")    # call parent's __init__
        self.cloud = cloud

    def deploy(self):
        self.status = "deploying"

class Profile(Resource):
    def __init__(self, name, layers):
        super().__init__(name, kind="Profile")
        self.layers = layers

# Usage:
c = Cluster("prod", "aws")
p = Profile("k8s-base", ["os", "k8s", "cni"])

print(c.describe())               # "Cluster/prod [pending]" (inherited method)
c.deploy()
print(c.describe())               # "Cluster/prod [deploying]"
print(c.cloud)                    # "aws" (own attribute)

print(p.describe())               # "Profile/k8s-base [pending]"
```

### Method Overriding

```python
class Cluster(Resource):
    def describe(self):
        # Override parent method:
        return f"☁ {self.cloud} {super().describe()}"

c = Cluster("prod", "aws")
c.kind = "Cluster"
print(c.describe())               # "☁ aws Cluster/prod [pending]"
```

### isinstance() Check

```python
c = Cluster("prod", "aws")
print(isinstance(c, Cluster))     # True
print(isinstance(c, Resource))    # True  (parent)
print(isinstance(c, Profile))     # False
```

### Exception Inheritance (Real-world Use)

```python
class SpectroError(Exception):
    """Base for all spectro errors."""
    pass

class AuthError(SpectroError):
    pass

class NotFoundError(SpectroError):
    pass

# Catch all spectro errors:
try:
    api_call()
except SpectroError as e:         # catches AuthError, NotFoundError, etc.
    print(f"Error: {e}")
```

---

## @staticmethod and @classmethod

### @staticmethod — No Access to Instance or Class

```python
class Cluster:
    @staticmethod
    def validate_name(name):
        """Validate cluster name format (no instance needed)."""
        if not name:
            return False
        if not name[0].isalpha():
            return False
        return all(c.isalnum() or c == "-" for c in name)

# Call without creating an instance:
Cluster.validate_name("prod-01")   # True
Cluster.validate_name("")          # False
```

### @classmethod — Access to the Class (not Instance)

```python
class Cluster:
    all_clusters = []

    def __init__(self, name):
        self.name = name
        Cluster.all_clusters.append(self)

    @classmethod
    def from_dict(cls, data):
        """Alternative constructor from a dictionary."""
        return cls(data["name"])   # cls is the Cluster class

    @classmethod
    def count(cls):
        return len(cls.all_clusters)

# Usage:
c = Cluster.from_dict({"name": "prod"})
print(Cluster.count())            # 1
```

### When to Use

| Decorator | Has `self`? | Has `cls`? | Use For |
|-----------|-----------|-----------|---------|
| (none) | Yes | No | Normal method — needs instance data |
| `@staticmethod` | No | No | Utility — doesn't need instance or class |
| `@classmethod` | No | Yes | Alternative constructors, class-level operations |

---

## Private and Protected by Convention

Python has **no real private** attributes — everything is accessible. Privacy is by **convention**:

```python
class APIClient:
    def __init__(self, api_key):
        self.base_url = "https://api.example.com"    # public
        self._session = requests.Session()            # "protected" (convention)
        self.__api_key = api_key                      # "private" (name mangled)

    def _build_headers(self):           # "protected" method
        return {"Authorization": f"Bearer {self.__api_key}"}

    def __internal_method(self):        # "private" method (name mangled)
        pass
```

| Prefix | Convention | Meaning |
|--------|------------|---------|
| `name` | Public | Use freely |
| `_name` | Protected | Internal use — don't access from outside |
| `__name` | Name-mangled | Strongly private — becomes `_ClassName__name` |
| `__name__` | Dunder/magic | Python special methods — don't invent these |

### In Practice

- Use `_name` for "internal" attributes and methods
- Rarely need `__name` (name mangling)
- `_` prefix is a signal: "don't depend on this, it may change"

---

## Dataclasses — Less Boilerplate

Python 3.7+ `@dataclass` auto-generates `__init__`, `__repr__`, and more:

```python
from dataclasses import dataclass, field

@dataclass
class Cluster:
    name: str
    cloud: str
    region: str = "us-east-1"
    tags: list = field(default_factory=list)

# Auto-generated __init__, __repr__, __eq__:
c = Cluster("prod", "aws")
print(c)                           # Cluster(name='prod', cloud='aws', region='us-east-1', tags=[])
print(c.name)                     # "prod"

# Equality works:
c2 = Cluster("prod", "aws")
print(c == c2)                    # True
```

### With Methods

```python
@dataclass
class Cluster:
    name: str
    cloud: str
    status: str = "pending"

    def deploy(self):
        self.status = "deploying"

    def is_active(self):
        return self.status == "active"
```

### Frozen (Immutable)

```python
@dataclass(frozen=True)
class Point:
    x: float
    y: float

p = Point(1.0, 2.0)
p.x = 5.0                         # FrozenInstanceError!
```

---

## When to Use Classes vs Plain Functions

### Use Functions When

- You have **standalone operations** that don't share state
- Simple utility/helper logic
- You just need to transform data in → data out
- One-off scripts

```python
# Functions are fine for:
def load_config(path):
    with open(path) as f:
        return json.load(f)

def validate_name(name):
    return bool(name and name[0].isalpha())
```

### Use Classes When

- You have **related data + operations** that go together
- You need to maintain **state** across multiple calls
- You want to **group** an API (client, manager, handler)
- You need **multiple instances** of the same thing

```python
# Classes make sense for:
class PaletteClient:       # state: session, auth, base_url
class ConfigManager:       # state: config data, file path
class Cluster:             # data: name, status, cloud, nodes
```

### Don't Over-Use Classes

```python
# BAD — unnecessary class:
class Calculator:
    def add(self, a, b):
        return a + b

# GOOD — just use a function:
def add(a, b):
    return a + b
```

---

## Classes in spectro-cli

The project uses classes in several places:

### PaletteClient (client.py)

An API client class with session management, authentication, and API methods:

```python
class PaletteClient:
    def __init__(self, config):
        self.config = config
        self.session = requests.Session()
        # setup auth headers...

    def get_clusters(self):
        return self._get("/v1/spectroclusters")

    def get_cluster(self, name):
        # ...
```

### Custom Exceptions (exceptions.py)

```python
class SpectroError(Exception): pass
class AuthError(SpectroError): pass
class NotFoundError(SpectroError): pass
```

### Models (models.py)

Data classes for API response objects.

### Config (config.py)

Configuration management with persistence.

---

## Shell vs Python Comparison

| Concept | Shell | Python |
|---------|-------|--------|
| Group related code | Script file | Class |
| Store data | Variables | Attributes (`self.x`) |
| Operations | Functions | Methods |
| Multiple instances | Run script multiple times | Create objects |
| Inheritance | Source another script | `class Child(Parent)` |
| Private data | N/A | `_name` convention |
| Constructor | N/A (script just runs) | `__init__` |
| Access data | `$variable` | `self.variable` |

---

> **Practice:** Chapter `04-files-errors-classes.py` section 7+
> **Next:** [13 - Decorators, Generators, and Advanced Topics](13-decorators-generators-advanced.md)
