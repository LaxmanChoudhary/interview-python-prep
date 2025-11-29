# Most Asked Python Interview Questions & Answers

## 1. Python Basics & Core Concepts

### Q1: What are the key features of Python?
**Answer:**
- **Interpreted Language**: Code executed line by line
- **Dynamically Typed**: No need to declare variable types
- **Object-Oriented**: Everything is an object
- **High-level**: Automatic memory management
- **Easy to Learn**: Simple, readable syntax
- **Rich Standard Library**: Extensive built-in modules
- **Cross-platform**: Works on Windows, Linux, macOS
- **Open Source**: Free and community-driven

---

### Q2: What is the difference between list and tuple?
**Answer:**

```python
# List - Mutable (can be changed)
my_list = [1, 2, 3, 4]
my_list[0] = 10  # Works fine
my_list.append(5)  # Can add items
# Lists use more memory
# Slower than tuples

# Tuple - Immutable (cannot be changed)
my_tuple = (1, 2, 3, 4)
my_tuple[0] = 10  # TypeError: object doesn't support item assignment
# Tuples use less memory
# Faster than lists
# Can be used as dictionary keys

# When to use what:
# List: When data needs to change
# Tuple: When data should remain constant (coordinates, RGB values, etc.)
```

**Key Differences:**
| Feature | List | Tuple |
|---------|------|-------|
| Mutability | Mutable | Immutable |
| Syntax | `[1, 2, 3]` | `(1, 2, 3)` |
| Performance | Slower | Faster |
| Memory | More | Less |
| Methods | Many (append, remove, etc.) | Few (count, index) |
| Use Case | Dynamic data | Fixed data |

---

### Q3: Explain mutable vs immutable objects in Python
**Answer:**

```python
# IMMUTABLE (cannot be changed after creation)
# int, float, bool, string, tuple, frozenset

# Strings are immutable
s = "hello"
s[0] = "H"  # TypeError: 'str' object does not support item assignment
s = "Hello"  # Creates NEW string object

# Tuples are immutable
t = (1, 2, 3)
t[0] = 10  # TypeError

# MUTABLE (can be changed after creation)
# list, dict, set, bytearray

# Lists are mutable
lst = [1, 2, 3]
lst[0] = 10  # Works fine, same object modified

# Dictionaries are mutable
d = {'a': 1}
d['a'] = 2  # Same dict object, value changed

# Important implication for function arguments
def modify_list(lst):
    lst.append(4)  # Modifies original list

my_list = [1, 2, 3]
modify_list(my_list)
print(my_list)  # [1, 2, 3, 4] - Original list changed!

def modify_string(s):
    s = s + " world"  # Creates new string, doesn't modify original

my_str = "hello"
modify_string(my_str)
print(my_str)  # "hello" - Original string unchanged
```

---

### Q4: What is the difference between `==` and `is`?
**Answer:**

```python
# == checks VALUE equality
# is checks IDENTITY (same object in memory)

# Example 1: Small integers (cached by Python)
a = 256
b = 256
print(a == b)  # True (same value)
print(a is b)  # True (Python caches small integers -5 to 256)

# Example 2: Large integers
a = 1000
b = 1000
print(a == b)  # True (same value)
print(a is b)  # False (different objects in memory)

# Example 3: Lists
list1 = [1, 2, 3]
list2 = [1, 2, 3]
print(list1 == list2)  # True (same values)
print(list1 is list2)  # False (different objects)

list3 = list1
print(list1 is list3)  # True (same object)

# Example 4: None comparison (always use 'is')
x = None
if x is None:  # Correct
    print("x is None")

if x == None:  # Works but not recommended
    print("x equals None")
```

**Best Practice:** Use `is` only for singleton objects like `None`, `True`, `False`

---

## 2. Data Structures

### Q5: Explain dictionary vs set in Python
**Answer:**

```python
# Dictionary - Key-Value pairs
person = {
    'name': 'John',
    'age': 30,
    'city': 'New York'
}
print(person['name'])  # Access by key
person['email'] = 'john@example.com'  # Add new key-value

# Set - Unordered collection of unique elements
numbers = {1, 2, 3, 4, 4, 5}  # Duplicates removed automatically
print(numbers)  # {1, 2, 3, 4, 5}

# Set operations
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

print(set1 | set2)  # Union: {1, 2, 3, 4, 5, 6}
print(set1 & set2)  # Intersection: {3, 4}
print(set1 - set2)  # Difference: {1, 2}
print(set1 ^ set2)  # Symmetric difference: {1, 2, 5, 6}

# Common use cases
# Dictionary: Storing mappings, caching, counting
# Set: Removing duplicates, membership testing, mathematical operations
```

---

### Q6: What are list comprehensions and when should you use them?
**Answer:**

```python
# Basic list comprehension
# [expression for item in iterable if condition]

# Traditional way
squares = []
for i in range(10):
    squares.append(i ** 2)

# List comprehension way (more Pythonic)
squares = [i ** 2 for i in range(10)]

# With condition
even_squares = [i ** 2 for i in range(10) if i % 2 == 0]
# Result: [0, 4, 16, 36, 64]

# Nested comprehension
matrix = [[i * j for j in range(3)] for i in range(3)]
# Result: [[0, 0, 0], [0, 1, 2], [0, 2, 4]]

# Dictionary comprehension
squares_dict = {i: i ** 2 for i in range(5)}
# Result: {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Set comprehension
unique_lengths = {len(word) for word in ['hello', 'world', 'hi', 'hey']}
# Result: {2, 3, 5}

# When to use:
# ✓ Simple transformations
# ✓ Filtering data
# ✗ Complex logic (use regular loops for readability)
# ✗ Multiple operations per item
```

---

### Q7: Explain `*args` and `**kwargs`
**Answer:**

```python
# *args - Variable number of positional arguments (tuple)
def sum_numbers(*args):
    print(type(args))  # <class 'tuple'>
    return sum(args)

print(sum_numbers(1, 2, 3))  # 6
print(sum_numbers(1, 2, 3, 4, 5))  # 15

# **kwargs - Variable number of keyword arguments (dict)
def print_info(**kwargs):
    print(type(kwargs))  # <class 'dict'>
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="John", age=30, city="NYC")
# name: John
# age: 30
# city: NYC

# Combined usage (order matters: regular, *args, **kwargs)
def func(a, b, *args, **kwargs):
    print(f"a: {a}")
    print(f"b: {b}")
    print(f"args: {args}")
    print(f"kwargs: {kwargs}")

func(1, 2, 3, 4, 5, x=10, y=20)
# a: 1
# b: 2
# args: (3, 4, 5)
# kwargs: {'x': 10, 'y': 20}

# Unpacking arguments
def add(a, b, c):
    return a + b + c

numbers = [1, 2, 3]
print(add(*numbers))  # 6

person = {'name': 'John', 'age': 30}
def greet(name, age):
    print(f"Hello {name}, you are {age}")

greet(**person)  # Hello John, you are 30
```

---

## 3. Object-Oriented Programming

### Q8: Explain the four pillars of OOP in Python
**Answer:**

**1. Encapsulation** - Bundling data and methods
```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance  # Private attribute
    
    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount
    
    def get_balance(self):
        return self.__balance

account = BankAccount(1000)
# account.__balance  # AttributeError: no attribute '__balance'
print(account.get_balance())  # 1000 - Controlled access
```

**2. Inheritance** - Deriving from parent class
```python
class Animal:
    def __init__(self, name):
        self.name = name
    
    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        return f"{self.name} says Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} says Meow!"

dog = Dog("Buddy")
print(dog.speak())  # Buddy says Woof!
```

**3. Polymorphism** - Same interface, different implementations
```python
def animal_sound(animal):
    print(animal.speak())  # Works for any animal

animal_sound(Dog("Buddy"))  # Buddy says Woof!
animal_sound(Cat("Whiskers"))  # Whiskers says Meow!
```

**4. Abstraction** - Hiding complex implementation
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass
    
    @abstractmethod
    def perimeter(self):
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def area(self):
        return self.width * self.height
    
    def perimeter(self):
        return 2 * (self.width + self.height)

# shape = Shape()  # TypeError: Can't instantiate abstract class
rect = Rectangle(5, 3)
print(rect.area())  # 15
```

---

### Q9: What are `@classmethod`, `@staticmethod`, and instance methods?
**Answer:**

```python
class MyClass:
    class_variable = 0
    
    def __init__(self, value):
        self.instance_variable = value
    
    # Instance method - Operates on instance (self)
    def instance_method(self):
        return f"Instance: {self.instance_variable}"
    
    # Class method - Operates on class (cls)
    @classmethod
    def class_method(cls):
        cls.class_variable += 1
        return f"Class variable: {cls.class_variable}"
    
    # Static method - Doesn't operate on instance or class
    @staticmethod
    def static_method(x, y):
        return x + y

# Usage
obj1 = MyClass(10)
obj2 = MyClass(20)

# Instance method
print(obj1.instance_method())  # Instance: 10

# Class method
print(MyClass.class_method())  # Class variable: 1
print(obj1.class_method())  # Class variable: 2 (shared across instances)

# Static method
print(MyClass.static_method(5, 3))  # 8
print(obj1.static_method(5, 3))  # 8

# Real-world example: Alternative constructors
class Date:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day
    
    @classmethod
    def from_string(cls, date_string):
        year, month, day = map(int, date_string.split('-'))
        return cls(year, month, day)  # Create instance
    
    @staticmethod
    def is_valid_date(year, month, day):
        return 1 <= month <= 12 and 1 <= day <= 31

# Usage
date1 = Date(2024, 11, 29)
date2 = Date.from_string('2024-11-29')  # Alternative constructor
print(Date.is_valid_date(2024, 13, 1))  # False
```

---

### Q10: Explain `__init__` vs `__new__`
**Answer:**

```python
# __new__ - Creates the instance (constructor)
# __init__ - Initializes the instance (initializer)

class Person:
    def __new__(cls, *args, **kwargs):
        print("__new__ called - Creating instance")
        instance = super().__new__(cls)
        return instance
    
    def __init__(self, name):
        print("__init__ called - Initializing instance")
        self.name = name

person = Person("John")
# Output:
# __new__ called - Creating instance
# __init__ called - Initializing instance

# Practical use case: Singleton pattern
class Singleton:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
    
    def __init__(self):
        self.value = 0

s1 = Singleton()
s2 = Singleton()
print(s1 is s2)  # True - Same instance

s1.value = 10
print(s2.value)  # 10 - Shared state
```

---

## 4. Functions & Decorators

### Q11: What are decorators and how do you create them?
**Answer:**

```python
# Decorator is a function that takes another function and extends it

# Simple decorator
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Before function call")
        result = func(*args, **kwargs)
        print("After function call")
        return result
    return wrapper

@my_decorator
def say_hello(name):
    print(f"Hello {name}!")

say_hello("John")
# Output:
# Before function call
# Hello John!
# After function call

# Decorator with arguments
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def greet(name):
    print(f"Hello {name}")

greet("John")
# Hello John
# Hello John
# Hello John

# Real-world examples

# 1. Timing decorator
import time

def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.4f} seconds")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)
    return "Done"

# 2. Authentication decorator
def require_auth(func):
    def wrapper(*args, **kwargs):
        if not user_is_authenticated():
            raise Exception("Not authenticated")
        return func(*args, **kwargs)
    return wrapper

@require_auth
def delete_account():
    print("Account deleted")

# 3. Cache decorator (memoization)
def cache(func):
    cached_results = {}
    def wrapper(*args):
        if args not in cached_results:
            cached_results[args] = func(*args)
        return cached_results[args]
    return wrapper

@cache
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
```

---

### Q12: Explain closures in Python
**Answer:**

```python
# Closure: Inner function that remembers variables from outer function

def outer_function(x):
    # x is in outer function's scope
    
    def inner_function(y):
        # inner function can access x
        return x + y
    
    return inner_function

# Create closure
add_5 = outer_function(5)  # x = 5 is "remembered"
add_10 = outer_function(10)  # x = 10 is "remembered"

print(add_5(3))   # 8 (5 + 3)
print(add_10(3))  # 13 (10 + 3)

# Practical example: Counter
def make_counter():
    count = 0
    
    def counter():
        nonlocal count  # Modify outer variable
        count += 1
        return count
    
    return counter

counter1 = make_counter()
counter2 = make_counter()

print(counter1())  # 1
print(counter1())  # 2
print(counter2())  # 1 (separate closure)

# Real-world: Creating multipliers
def multiplier(factor):
    def multiply(number):
        return number * factor
    return multiply

double = multiplier(2)
triple = multiplier(3)

print(double(5))  # 10
print(triple(5))  # 15
```

---

### Q13: What is the difference between `lambda` and regular functions?
**Answer:**

```python
# Regular function
def square(x):
    return x ** 2

# Lambda function (anonymous function)
square_lambda = lambda x: x ** 2

print(square(5))  # 25
print(square_lambda(5))  # 25

# Key differences:
# 1. Lambda is single expression only
# 2. No name required (anonymous)
# 3. Return is implicit

# Common use cases

# 1. With map()
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x ** 2, numbers))
print(squared)  # [1, 4, 9, 16, 25]

# 2. With filter()
even_numbers = list(filter(lambda x: x % 2 == 0, numbers))
print(even_numbers)  # [2, 4]

# 3. With sorted()
students = [
    {'name': 'John', 'grade': 85},
    {'name': 'Jane', 'grade': 92},
    {'name': 'Bob', 'grade': 78}
]
sorted_students = sorted(students, key=lambda x: x['grade'], reverse=True)
print(sorted_students)
# [{'name': 'Jane', 'grade': 92}, {'name': 'John', 'grade': 85}, ...]

# 4. With reduce()
from functools import reduce
sum_all = reduce(lambda x, y: x + y, numbers)
print(sum_all)  # 15

# When NOT to use lambda:
# ✗ Complex logic (use regular function)
# ✗ Multiple statements
# ✗ Need docstrings
# ✓ Simple, one-line operations
# ✓ As arguments to higher-order functions
```

---

## 5. Generators & Iterators

### Q14: What are generators and why use them?
**Answer:**

```python
# Generator: Function that yields values one at a time (lazy evaluation)

# Regular function (creates entire list in memory)
def get_numbers():
    result = []
    for i in range(1000000):
        result.append(i ** 2)
    return result

# Generator (yields one value at a time)
def get_numbers_generator():
    for i in range(1000000):
        yield i ** 2

# Usage
gen = get_numbers_generator()
print(next(gen))  # 0
print(next(gen))  # 1
print(next(gen))  # 4

# Benefits:
# 1. Memory efficient (doesn't store all values)
# 2. Can represent infinite sequences
# 3. Faster for large datasets

# Generator expression (like list comprehension)
squares_gen = (x ** 2 for x in range(10))
squares_list = [x ** 2 for x in range(10)]

print(type(squares_gen))  # <class 'generator'>
print(type(squares_list))  # <class 'list'>

# Practical examples

# 1. Reading large files
def read_large_file(file_path):
    with open(file_path, 'r') as file:
        for line in file:
            yield line.strip()

# 2. Infinite sequence
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

fib = fibonacci()
print([next(fib) for _ in range(10)])
# [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]

# 3. Pipeline processing
def numbers():
    for i in range(10):
        yield i

def square(nums):
    for n in nums:
        yield n ** 2

def even_only(nums):
    for n in nums:
        if n % 2 == 0:
            yield n

# Chain generators
pipeline = even_only(square(numbers()))
print(list(pipeline))  # [0, 4, 16, 36, 64]
```

---

### Q15: Explain the difference between `yield` and `return`
**Answer:**

```python
# return - Returns value and terminates function
def with_return():
    return 1
    return 2  # Never executed
    return 3

result = with_return()
print(result)  # 1

# yield - Returns value and suspends function
def with_yield():
    yield 1
    yield 2
    yield 3

gen = with_yield()
print(next(gen))  # 1
print(next(gen))  # 2
print(next(gen))  # 3
# next(gen)  # StopIteration

# Practical comparison
def return_example():
    result = []
    for i in range(5):
        result.append(i)
    return result  # Returns all at once

def yield_example():
    for i in range(5):
        yield i  # Returns one at a time

# return: Creates list in memory
numbers_list = return_example()
print(numbers_list)  # [0, 1, 2, 3, 4]

# yield: Creates generator (lazy)
numbers_gen = yield_example()
for num in numbers_gen:
    print(num)  # Prints one at a time

# State preservation with yield
def counter():
    count = 0
    while True:
        count += 1
        received = yield count  # Can receive values
        if received:
            count = received

c = counter()
print(next(c))  # 1
print(next(c))  # 2
print(c.send(100))  # 101 (reset to 100)
print(next(c))  # 102
```

---

## 6. Exception Handling

### Q16: How do you handle exceptions in Python?
**Answer:**

```python
# Basic try-except
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")

# Multiple exceptions
try:
    num = int(input("Enter number: "))
    result = 10 / num
except ValueError:
    print("Invalid number")
except ZeroDivisionError:
    print("Cannot divide by zero")
except Exception as e:
    print(f"Unexpected error: {e}")

# try-except-else-finally
try:
    file = open('data.txt', 'r')
    content = file.read()
except FileNotFoundError:
    print("File not found")
else:
    # Executed if no exception occurred
    print("File read successfully")
finally:
    # Always executed (cleanup code)
    if 'file' in locals():
        file.close()

# Raising exceptions
def validate_age(age):
    if age < 0:
        raise ValueError("Age cannot be negative")
    if age > 150:
        raise ValueError("Invalid age")
    return age

# Custom exceptions
class InsufficientFundsError(Exception):
    def __init__(self, balance, amount):
        self.balance = balance
        self.amount = amount
        super().__init__(f"Insufficient funds: ${balance} < ${amount}")

class BankAccount:
    def __init__(self, balance):
        self.balance = balance
    
    def withdraw(self, amount):
        if amount > self.balance:
            raise InsufficientFundsError(self.balance, amount)
        self.balance -= amount

# Usage
account = BankAccount(100)
try:
    account.withdraw(150)
except InsufficientFundsError as e:
    print(e)  # Insufficient funds: $100 < $150

# Context managers for automatic cleanup
with open('data.txt', 'r') as file:
    content = file.read()
    # File automatically closed, even if exception occurs
```

---

## 7. Advanced Concepts

### Q17: What is the Global Interpreter Lock (GIL)?
**Answer:**

The GIL is a mutex that protects access to Python objects, preventing multiple threads from executing Python bytecode simultaneously.

```python
import threading
import time

# CPU-bound task (affected by GIL)
def cpu_bound_task():
    count = 0
    for i in range(50000000):
        count += 1

# Single threaded
start = time.time()
cpu_bound_task()
cpu_bound_task()
end = time.time()
print(f"Single threaded: {end - start:.2f}s")

# Multi-threaded (no speedup due to GIL)
start = time.time()
thread1 = threading.Thread(target=cpu_bound_task)
thread2 = threading.Thread(target=cpu_bound_task)
thread1.start()
thread2.start()
thread1.join()
thread2.join()
end = time.time()
print(f"Multi-threaded: {end - start:.2f}s")

# Solutions to GIL limitation:

# 1. Multiprocessing (separate processes, no GIL)
from multiprocessing import Process

start = time.time()
process1 = Process(target=cpu_bound_task)
process2 = Process(target=cpu_bound_task)
process1.start()
process2.start()
process1.join()
process2.join()
end = time.time()
print(f"Multi-process: {end - start:.2f}s")  # Actual speedup!

# 2. asyncio for I/O-bound tasks
import asyncio

async def io_bound_task():
    await asyncio.sleep(1)  # I/O operation
    return "Done"

# 3. Use C extensions (NumPy, etc.) that release GIL
```

**Key Points:**
- GIL only affects **multi-threading**, not **multi-processing**
- **I/O-bound tasks**: Use threading (GIL released during I/O)
- **CPU-bound tasks**: Use multiprocessing or asyncio
- Libraries like NumPy release GIL during operations

---

### Q18: Explain shallow copy vs deep copy
**Answer:**

```python
import copy

# Original list with nested structure
original = [[1, 2, 3], [4, 5, 6]]

# Assignment (not a copy - same reference)
assigned = original
assigned[0][0] = 999
print(original)  # [[999, 2, 3], [4, 5, 6]] - Original changed!

# Shallow copy (copies outer list, references inner lists)
original = [[1, 2, 3], [4, 5, 6]]
shallow = copy.copy(original)
# or shallow = original.copy()
# or shallow = original[:]

shallow[0][0] = 999
print(original)  # [[999, 2, 3], [4, 5, 6]] - Original changed!
print(shallow)   # [[999, 2, 3], [4, 5, 6]]

# Deep copy (copies everything recursively)
original = [[1, 2, 3], [4, 5, 6]]
deep = copy.deepcopy(original)

deep[0][0] = 999
print(original)  # [[1, 2, 3], [4, 5, 6]] - Original unchanged!
print(deep)      # [[999, 2, 3], [4, 5, 6]]

# Visual representation
original = [1, [2, 3]]

# Assignment: same object
assigned = original  # original -> [1, [2, 3]] <- assigned

# Shallow copy: new outer list, shared inner list
shallow = original.copy()
# original -> [1, [2, 3]] <- shared
# shallow -> [1, ↑]

# Deep copy: everything new
deep = copy.deepcopy(original)
# original -> [1, [2, 3]]
# deep -> [1, [2, 3]]  (completely separate)

# Practical example
class Person:
    def __init__(self, name, address):
        self.name = name
        self.address = address

address = {'city': 'NYC', 'zip': '10001'}
person1 = Person('John', address)

# Shallow copy
person2 = copy.copy(person1)
person2.address['city'] = 'LA'
print(person1.address)  # {'city': 'LA', 'zip': '10001'} - Changed!

# Deep copy
person3 = copy.deepcopy(person1)
person3.address['city'] = 'Boston'
print(person1.address)  # {'city': 'LA', 'zip': '10001'} - Unchanged
```

---

### Q19: What are magic methods (dunder methods)?
**Answer:**

```python
# Magic methods allow customizing class behavior

class Book:
    def __init__(self, title, pages):
        self.title = title
        self.pages = pages
    
    # String representation
    def __str__(self):
        # For print() and str()
        return f"{self.title} ({self.pages} pages)"
    
    def __repr__(self):
        # For debugging, should be unambiguous
        return f"Book('{self.title}', {self.pages})"
    
    # Comparison operators
    def __eq__(self, other):
        return self.pages == other.pages
    
    def __lt__(self, other):
        return self.pages < other.pages
    
    def __le__(self, other):
        return self.pages <= other.pages
    
    # Arithmetic operators
    def __add__(self, other):
        return Book(f"{self.title} & {other.title}", 
                   self.pages + other.pages)
    
    # Length
    def __len__(self):
        return self.pages
    
    # Container behavior
    def __getitem__(self, key):
        return f"Page {