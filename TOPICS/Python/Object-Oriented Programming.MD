# Python Mastery Series

## **Part II – Object-Oriented Programming (OOP)**

### **1. Introduction to OOP in Python**

Python is an **object-oriented language**, meaning everything is an **object** (functions, classes, modules, etc.).
OOP provides a modular structure to organize and reuse code effectively.

**Core Concepts:**

1. **Class** – Blueprint for creating objects.
2. **Object** – Instance of a class.
3. **Attributes** – Data stored inside an object.
4. **Methods** – Functions that act on object data.

---

### **2. Creating Classes and Objects**

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):
        return f"Hello, my name is {self.name} and I am {self.age} years old."

p1 = Person("Alice", 25)
print(p1.greet())
```

**Key Points:**

* `__init__` is a **constructor** called automatically when an object is created.
* `self` refers to the current object instance.

---

### **3. Instance vs Class Variables**

```python
class Dog:
    species = "Canine"  # Class variable

    def __init__(self, name):
        self.name = name  # Instance variable

buddy = Dog("Buddy")
print(buddy.name, Dog.species)
```

---

### **4. Inheritance**

Inheritance allows one class to **derive properties and methods** from another.

```python
class Animal:
    def speak(self):
        return "Some sound"

class Dog(Animal):
    def speak(self):
        return "Bark!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

dog = Dog()
print(dog.speak())  # Bark!
```

**Types of Inheritance:**

* Single
* Multiple
* Multilevel
* Hierarchical

---

### **5. Method Overriding & super()**

```python
class Parent:
    def show(self):
        print("Parent method")

class Child(Parent):
    def show(self):
        super().show()
        print("Child method")

obj = Child()
obj.show()
```

**Output:**

```
Parent method
Child method
```

---

### **6. Encapsulation (Data Hiding)**

Encapsulation restricts direct access to internal variables.

```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance  # Private variable

    def deposit(self, amount):
        self.__balance += amount

    def get_balance(self):
        return self.__balance

acc = BankAccount(1000)
acc.deposit(500)
print(acc.get_balance())  # 1500
```

Private variables are prefixed with `__`, which triggers **name mangling**.

---

### **7. Polymorphism**

Different classes can define methods with the same name but different behaviors.

```python
class Bird:
    def speak(self):
        return "Chirp"

class Dog:
    def speak(self):
        return "Bark"

def animal_sound(animal):
    print(animal.speak())

animal_sound(Bird())
animal_sound(Dog())
```

**Output:**

```
Chirp
Bark
```

---

### **8. Abstraction**

Use **abstract base classes (ABC)** to define required methods for subclasses.

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14 * self.radius ** 2

circle = Circle(5)
print(circle.area())
```

---

### **9. Magic (Dunder) Methods**

Python classes can override built-in behavior using **double underscore methods**.

| Method     | Description            |
| ---------- | ---------------------- |
| `__init__` | Constructor            |
| `__str__`  | String representation  |
| `__len__`  | Length method override |
| `__add__`  | Operator overloading   |
| `__eq__`   | Equality comparison    |

#### **Example:**

```python
class Vector:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __str__(self):
        return f"({self.x}, {self.y})"

v1 = Vector(2, 3)
v2 = Vector(4, 1)
print(v1 + v2)  # (6, 4)
```

---

### **10. Class & Static Methods**

* **Class methods** operate on class variables.
* **Static methods** don’t depend on class or instance.

```python
class MathOps:
    count = 0

    def __init__(self):
        MathOps.count += 1

    @classmethod
    def total_objects(cls):
        return cls.count

    @staticmethod
    def add(a, b):
        return a + b

print(MathOps.add(3, 5))  # 8
print(MathOps.total_objects())  # 0 (before instantiation)
```

---

### **11. Data Classes & NamedTuples**

**Data classes** simplify class creation for storing data.

```python
from dataclasses import dataclass

@dataclass
class Product:
    name: str
    price: float

item = Product("Laptop", 999.99)
print(item)
```

**NamedTuple Example:**

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
p1 = Point(2, 3)
print(p1.x, p1.y)
```

---

### **12. Type Hints and Annotations**

```python
def add(a: int, b: int) -> int:
    return a + b

def greet(name: str) -> str:
    return f"Hello, {name}"
```

Type hints improve code readability and help tools like **mypy** perform static analysis.

---

### **13. Real-World Example: Employee Management System**

```python
class Employee:
    raise_percent = 1.05

    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def apply_raise(self):
        self.salary = int(self.salary * self.raise_percent)

class Developer(Employee):
    raise_percent = 1.10

emp1 = Employee("Alice", 50000)
emp2 = Developer("Bob", 60000)

emp1.apply_raise()
emp2.apply_raise()

print(emp1.salary, emp2.salary)
```

**Output:**

```
52500 66000
```

---

### **14. Summary**

✅ Classes organize logic and data into reusable blueprints.
✅ Inheritance enables code reuse and specialization.
✅ Encapsulation, Abstraction, and Polymorphism ensure modularity.
✅ Magic methods allow operator overloading and custom behavior.
✅ Data classes simplify data storage models.

---

## **Next Up: Part III – Data Structures & Algorithms**
