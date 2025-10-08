# Python Mastery Series

## **Part III – Data Structures & Algorithms**

### **1. Introduction**

Data Structures define how data is stored and accessed. Algorithms define how efficiently that data is processed.

Python provides built-in data structures, along with modules for advanced manipulation and optimization.

---

### **2. Lists**

Lists are **mutable ordered sequences**.

```python
numbers = [1, 2, 3, 4, 5]
print(numbers[0])  # Access
numbers.append(6)  # Add
numbers.remove(3)  # Remove
```

#### **List Comprehension:**

```python
squares = [x**2 for x in range(5)]
print(squares)
```

#### **Useful Methods:**

| Method    | Description        |
| --------- | ------------------ |
| append()  | Add an item        |
| extend()  | Add multiple items |
| pop()     | Remove last item   |
| sort()    | Sort the list      |
| reverse() | Reverse the list   |

---

### **3. Tuples**

Tuples are **immutable ordered sequences**.

```python
coordinates = (10, 20)
print(coordinates[0])
```

#### **Tuple Unpacking:**

```python
x, y = coordinates
print(x, y)
```

Use tuples for fixed data structures and dictionary keys.

---

### **4. Sets**

Sets are **unordered collections of unique elements**.

```python
fruits = {"apple", "banana", "orange"}
fruits.add("kiwi")
fruits.discard("apple")
```

#### **Set Operations:**

```python
a = {1, 2, 3}
b = {3, 4, 5}
print(a | b)  # Union
print(a & b)  # Intersection
print(a - b)  # Difference
```

---

### **5. Dictionaries**

Dictionaries store **key-value pairs**.

```python
user = {"name": "Alice", "age": 25}
print(user["name"])
user["age"] = 26
```

#### **Dictionary Comprehension:**

```python
squares = {x: x**2 for x in range(5)}
print(squares)
```

#### **Useful Methods:**

| Method   | Description            |
| -------- | ---------------------- |
| keys()   | Return all keys        |
| values() | Return all values      |
| items()  | Return key-value pairs |
| get()    | Safe key access        |

---

### **6. Collections Module**

Python’s `collections` module provides optimized containers.

#### **Counter:**

```python
from collections import Counter

text = "banana"
count = Counter(text)
print(count)  # {'a': 3, 'b': 1, 'n': 2}
```

#### **defaultdict:**

```python
from collections import defaultdict

nums = defaultdict(int)
nums['a'] += 1
print(nums['a'], nums['b'])  # 1 0
```

#### **deque:**

Fast append/pop operations on both ends.

```python
from collections import deque

queue = deque([1, 2, 3])
queue.append(4)
queue.popleft()
print(queue)
```

---

### **7. Stacks and Queues**

#### **Stack (LIFO):**

```python
stack = []
stack.append(10)
stack.append(20)
stack.pop()
```

#### **Queue (FIFO):**

```python
from collections import deque
queue = deque()
queue.append(1)
queue.append(2)
queue.popleft()
```

---

### **8. Searching Algorithms**

#### **Linear Search:**

```python
def linear_search(arr, target):
    for i, value in enumerate(arr):
        if value == target:
            return i
    return -1

print(linear_search([1, 3, 5, 7], 5))  # 2
```

#### **Binary Search:**

```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1

print(binary_search([1, 3, 5, 7, 9], 7))  # 3
```

**Complexity:** O(log n)

---

### **9. Sorting Algorithms**

#### **Bubble Sort:**

```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n-1):
        for j in range(n-i-1):
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]
    return arr

print(bubble_sort([5, 2, 9, 1]))
```

**Time Complexity:** O(n²)

#### **Quick Sort:**

```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr)//2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quick_sort(left) + middle + quick_sort(right)

print(quick_sort([3, 6, 8, 10, 1, 2, 1]))
```

**Average Complexity:** O(n log n)

---

### **10. Recursion**

Recursion is a function calling itself until a base case is met.

#### **Factorial Example:**

```python
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)

print(factorial(5))  # 120
```

#### **Fibonacci Example:**

```python
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(6))  # 8
```

---

### **11. Time and Space Complexity**

| Algorithm     | Time Complexity | Space Complexity |
| ------------- | --------------- | ---------------- |
| Linear Search | O(n)            | O(1)             |
| Binary Search | O(log n)        | O(1)             |
| Bubble Sort   | O(n²)           | O(1)             |
| Quick Sort    | O(n log n)      | O(log n)         |
| Merge Sort    | O(n log n)      | O(n)             |

#### **Big O Notation Summary:**

| Complexity | Example             |
| ---------- | ------------------- |
| O(1)       | Accessing list item |
| O(log n)   | Binary search       |
| O(n)       | Loop through list   |
| O(n log n) | Sorting algorithms  |
| O(n²)      | Nested loops        |

---

### **12. Practical Applications**

| Data Structure | Use Case                   |
| -------------- | -------------------------- |
| List           | Dynamic data storage       |
| Tuple          | Fixed, immutable data      |
| Set            | Duplicate elimination      |
| Dict           | Key-value lookup           |
| Queue          | Task scheduling            |
| Stack          | Undo operations            |
| Graph          | Social network, navigation |

---

### **13. Real-World Example: URL Caching System**

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.cache = OrderedDict()
        self.capacity = capacity

    def get(self, key):
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key, value):
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)

cache = LRUCache(2)
cache.put(1, 'A')
cache.put(2, 'B')
print(cache.get(1))
cache.put(3, 'C')  # Evicts key 2
print(cache.get(2))  # -1
```

---

### **14. Summary**

✅ Python provides flexible, dynamic data structures.
✅ Lists, tuples, sets, and dicts cover 90% of real-world use cases.
✅ `collections` and `heapq` offer performance optimizations.
✅ Understanding algorithmic complexity is crucial for scalability.
✅ Recursion and sorting form the basis for problem-solving in Python.

---

## **Next Up: Part IV – Working with Databases**
