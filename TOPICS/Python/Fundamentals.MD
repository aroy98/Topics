# Python Mastery Series

## **Part I – Fundamentals**

### **1. Python Architecture & Interpreter**

* Python is an **interpreted, high-level, dynamically typed** language.
* Source code → **Bytecode (.pyc)** → executed by **Python Virtual Machine (PVM)**.
* **CPython** is the standard implementation (written in C).
* Other implementations: **PyPy (JIT-compiled)**, **Jython (Java)**, **IronPython (.NET)**.

#### **Execution Flow:**

1. `.py` → Python parser → Bytecode
2. Bytecode interpreted by **PVM**

#### **Check Python version:**

```bash
python --version
```

---

### **2. Data Types, Variables, and Memory**

* Everything in Python is an **object**.
* Variables are references to objects in memory.

#### **Core Data Types:**

| Type  | Example            | Description             |
| ----- | ------------------ | ----------------------- |
| int   | `x = 10`           | Whole numbers           |
| float | `pi = 3.14`        | Decimal numbers         |
| str   | `name = 'Alice'`   | Text                    |
| bool  | `flag = True`      | Boolean values          |
| list  | `[1, 2, 3]`        | Mutable sequence        |
| tuple | `(1, 2, 3)`        | Immutable sequence      |
| dict  | `{'a': 1, 'b': 2}` | Key-value pairs         |
| set   | `{1, 2, 3}`        | Unique unordered values |

#### **Type Conversion:**

```python
x = int('10')
y = str(20)
```

#### **Check Object Type:**

```python
print(type(x))
```

---

### **3. Operators & Expressions**

* **Arithmetic:** `+ - * / // % **`
* **Comparison:** `== != > < >= <=`
* **Logical:** `and or not`
* **Identity:** `is, is not`
* **Membership:** `in, not in`

#### **Example:**

```python
a, b = 5, 10
print(a + b)   # 15
print(a ** 2)  # 25
print(a < b and b < 20)  # True
```

---

### **4. Control Flow Statements**

#### **if-elif-else:**

```python
x = 10
if x > 0:
    print('Positive')
elif x == 0:
    print('Zero')
else:
    print('Negative')
```

#### **for Loop:**

```python
for i in range(5):
    print(i)
```

#### **while Loop:**

```python
count = 0
while count < 3:
    print('Count:', count)
    count += 1
```

#### **match-case (Python 3.10+):**

```python
match status:
    case 200:
        print('OK')
    case 404:
        print('Not Found')
    case _:
        print('Unknown')
```

---

### **5. Functions, Scope & Decorators**

Functions in Python are **first-class citizens**.

#### **Basic Function:**

```python
def greet(name):
    return f'Hello, {name}'
```

#### **Default & Keyword Arguments:**

```python
def add(a, b=5):
    return a + b
print(add(2))  # 7
```

#### **Lambda Function:**

```python
square = lambda x: x ** 2
print(square(4))
```

#### **Variable Scope:**

* `LEGB` Rule: Local → Enclosing → Global → Built-in

#### **Decorators:**

```python
def logger(func):
    def wrapper(*args, **kwargs):
        print(f'Running {func.__name__}')
        return func(*args, **kwargs)
    return wrapper

@logger
def hello():
    print('Hello Python!')

hello()
```

---

### **6. Modules & Packages**

* A **module** is any `.py` file.
* A **package** is a folder with an `__init__.py` file.

#### **Importing Modules:**

```python
import math
print(math.sqrt(16))

from datetime import datetime
print(datetime.now())
```

#### **Creating a Module:**

`my_module.py`

```python
def add(a, b):
    return a + b
```

Usage:

```python
import my_module
print(my_module.add(2, 3))
```

---

### **7. File Handling (I/O)**

#### **Reading a File:**

```python
with open('data.txt', 'r') as file:
    content = file.read()
    print(content)
```

#### **Writing to a File:**

```python
with open('output.txt', 'w') as file:
    file.write('Hello, File!')
```

#### **Appending:**

```python
with open('output.txt', 'a') as file:
    file.write('\nMore data')
```

---

### **8. Exception Handling**

Handle runtime errors gracefully.

#### **Example:**

```python
try:
    x = 10 / 0
except ZeroDivisionError:
    print('Cannot divide by zero')
except Exception as e:
    print('Error:', e)
else:
    print('No error occurred')
finally:
    print('Execution complete')
```

---

### **9. Virtual Environments**

Used to isolate project dependencies.

#### **Create Environment:**

```bash
python -m venv venv
```

#### **Activate Environment:**

* Windows: `venv\Scripts\activate`
* macOS/Linux: `source venv/bin/activate`

#### **Install Packages:**

```bash
pip install requests
```

#### **Freeze Requirements:**

```bash
pip freeze > requirements.txt
```

---

### **10. Python REPL & Scripts**

* **REPL:** Interactive shell (`python` command)
* **Script Execution:** Run `.py` files using:

```bash
python script.py
```

#### **Command Line Arguments:**

```python
import sys
print(sys.argv)
```

---

### **11. Code Style & Linters**

Follow **PEP 8** (Python Enhancement Proposal 8).

#### **Example Tools:**

* `flake8` – Linting
* `black` – Auto-formatting
* `isort` – Import ordering

```bash
pip install black flake8 isort
black .
```

---

### **12. Summary**

✅ Python is dynamically typed and interpreted.
✅ Follows the **LEGB** scope rule.
✅ Supports both functional and OOP paradigms.
✅ File handling, modules, and error management are integral.
✅ Virtual environments ensure isolated dependency management.

---

## **Next Up: Part II – Object-Oriented Programming (OOP)**
