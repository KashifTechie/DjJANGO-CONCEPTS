# Object-Oriented Programming in Python
### A Complete, Detail-First Guide

---

## What is OOP?

Before the pillars — understand *why* OOP exists.

Without OOP, you write code as a sequence of instructions. Data and logic float around separately. As your project grows, it becomes a mess — functions referencing variables from everywhere, no structure, no ownership.

OOP says: **group related data and behavior together into objects.**

An **object** is a self-contained unit that holds:
- **Data** (called attributes)
- **Behavior** (called methods)

Everything in Python is an object. Even an integer.

```python
x = 5
print(type(x))   # <class 'int'>
print(dir(x))    # Shows all methods an int has
```

---

## Class vs Object — The Blueprint Analogy

A **class** is a blueprint. An **object** is a real thing built from that blueprint.

```python
class Dog:
    def __init__(self, name, breed):
        self.name = name       # attribute
        self.breed = breed     # attribute

    def bark(self):            # method
        print(f"{self.name} says: Woof!")
```

```python
dog1 = Dog("Bruno", "Labrador")   # object 1
dog2 = Dog("Tommy", "Pug")        # object 2

dog1.bark()   # Bruno says: Woof!
dog2.bark()   # Tommy says: Woof!
```

`Dog` is the class (blueprint).  
`dog1` and `dog2` are objects (real instances built from the blueprint).  
Both share the same structure but hold different data.

---

## `__init__` — The Constructor

`__init__` is called automatically when you create an object. It initializes the object's data.

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

When you write `Person("Alice", 30)`:
1. Python creates a new empty object
2. Calls `__init__` on it
3. `self` refers to that new object
4. `self.name = "Alice"` and `self.age = 30` get stored on it

`self` is not a keyword — it's just a convention. It's the object itself being passed as the first argument.

---

## The 4 Pillars of OOP

1. Encapsulation
2. Inheritance
3. Polymorphism
4. Abstraction

---

---

# Pillar 1 — Encapsulation

## The Idea

Encapsulation means **bundling data and methods together**, and **controlling access to that data**.

You don't let outsiders directly touch the internal data of an object. You give them controlled access through methods.

**Real world:** Your bank account has a balance. You can't directly edit that number — you go through deposit/withdraw functions. Those functions validate, log, and then change the balance. That's encapsulation.

---

## Public, Protected, Private

Python uses naming conventions to signal access levels.

### Public (default)
Accessible from anywhere.

```python
class Car:
    def __init__(self):
        self.speed = 100   # public
```

```python
c = Car()
print(c.speed)   # 100 — works fine
```

---

### Protected — Single Underscore `_`

Convention saying: *"internal use — don't touch from outside unless you know what you're doing."*  
Python does NOT enforce this. It's a gentleman's agreement.

```python
class Car:
    def __init__(self):
        self._engine_temp = 90   # protected
```

```python
c = Car()
print(c._engine_temp)   # Works, but you're breaking convention
```

---

### Private — Double Underscore `__`

Python **actively hides** this using name mangling.  
`__balance` becomes `_ClassName__balance` internally.

```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance   # private

    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount

    def get_balance(self):
        return self.__balance
```

```python
acc = BankAccount(1000)
acc.deposit(500)
print(acc.get_balance())   # 1500

print(acc.__balance)       # AttributeError — hidden
print(acc._BankAccount__balance)  # 1500 — accessible via mangled name (but don't do this)
```

---

## Getters and Setters

Instead of exposing attributes directly, you expose controlled methods.

```python
class Student:
    def __init__(self, name, grade):
        self.__name = name
        self.__grade = grade

    def get_grade(self):          # getter
        return self.__grade

    def set_grade(self, grade):   # setter
        if 0 <= grade <= 100:
            self.__grade = grade
        else:
            print("Invalid grade")
```

```python
s = Student("Ravi", 85)
s.set_grade(95)
print(s.get_grade())   # 95

s.set_grade(150)       # Invalid grade — protected by setter
```

---

## The Pythonic Way — `@property`

Python has a cleaner way to write getters/setters using decorators.

```python
class Student:
    def __init__(self, name, grade):
        self.__name = name
        self.__grade = grade

    @property
    def grade(self):              # getter — accessed like an attribute
        return self.__grade

    @grade.setter
    def grade(self, value):       # setter — triggered on assignment
        if 0 <= value <= 100:
            self.__grade = value
        else:
            print("Invalid grade")
```

```python
s = Student("Ravi", 85)
print(s.grade)     # 85 — looks like attribute access, actually calls getter
s.grade = 95       # looks like assignment, actually calls setter
s.grade = 150      # Invalid grade
```

This is preferred over explicit `get_` and `set_` methods in Python.

---

## Why Encapsulation Matters

- Prevents accidental corruption of data
- Lets you validate before setting values
- Lets you change internal implementation without breaking external code
- Makes objects self-responsible for their own data

---

---

# Pillar 2 — Inheritance

## The Idea

Inheritance means **a class can take everything from another class and build on top of it.**

You don't rewrite shared logic. You inherit it.

**Real world:** A `SavingsAccount` is a type of `BankAccount`. It has everything a bank account has, plus extra rules like interest rates.

---

## Basic Inheritance

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def breathe(self):
        print(f"{self.name} is breathing")

    def eat(self):
        print(f"{self.name} is eating")
```

```python
class Dog(Animal):    # Dog inherits from Animal
    def bark(self):
        print(f"{self.name} says: Woof!")
```

```python
d = Dog("Bruno")
d.breathe()    # Bruno is breathing  — inherited
d.eat()        # Bruno is eating     — inherited
d.bark()       # Bruno says: Woof!   — Dog's own method
```

`Dog` got `breathe` and `eat` for free. You wrote them once in `Animal`.

---

## Constructor Chaining — `super()`

This is where many beginners get confused. Pay close attention.

When a child class has its own `__init__`, it **overrides** the parent's `__init__`. If you don't explicitly call the parent's constructor, the parent's setup never happens.

### Problem — forgetting to call parent constructor

```python
class Animal:
    def __init__(self, name):
        self.name = name
        print(f"Animal __init__ called for {name}")

class Dog(Animal):
    def __init__(self, name, breed):
        # self.name is never set because Animal's __init__ was never called
        self.breed = breed
        print(f"Dog __init__ called")
```

```python
d = Dog("Bruno", "Labrador")
print(d.breed)   # Labrador — works
print(d.name)    # AttributeError — self.name was never set!
```

---

### Solution — `super().__init__()`

`super()` gives you a reference to the parent class. You use it to call the parent's constructor.

```python
class Animal:
    def __init__(self, name):
        self.name = name
        print(f"Animal __init__ called → name set to {name}")

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)   # calls Animal's __init__ first
        self.breed = breed
        print(f"Dog __init__ called → breed set to {breed}")
```

```python
d = Dog("Bruno", "Labrador")
# Animal __init__ called → name set to Bruno
# Dog __init__ called → breed set to Labrador

print(d.name)    # Bruno   — set by Animal's __init__
print(d.breed)   # Labrador — set by Dog's __init__
```

**Order of execution:**
1. `Dog.__init__` starts
2. `super().__init__(name)` → jumps to `Animal.__init__` → sets `self.name`
3. Returns to `Dog.__init__` → sets `self.breed`

This is **constructor chaining**.

---

## Multi-level Inheritance

A chain: `C` inherits from `B`, `B` inherits from `A`.

```python
class Vehicle:
    def __init__(self, brand):
        self.brand = brand
        print(f"Vehicle init — brand: {brand}")

    def start(self):
        print("Vehicle starting...")

class Car(Vehicle):
    def __init__(self, brand, model):
        super().__init__(brand)   # calls Vehicle.__init__
        self.model = model
        print(f"Car init — model: {model}")

    def drive(self):
        print("Car is driving")

class ElectricCar(Car):
    def __init__(self, brand, model, battery):
        super().__init__(brand, model)   # calls Car.__init__
        self.battery = battery
        print(f"ElectricCar init — battery: {battery}kWh")

    def charge(self):
        print(f"Charging {self.battery}kWh battery")
```

```python
ec = ElectricCar("Tesla", "Model 3", 75)
# Vehicle init — brand: Tesla
# Car init — model: Model 3
# ElectricCar init — battery: 75kWh

ec.start()    # Vehicle starting...  — from Vehicle
ec.drive()    # Car is driving       — from Car
ec.charge()   # Charging 75kWh battery — from ElectricCar

print(ec.brand)    # Tesla
print(ec.model)    # Model 3
print(ec.battery)  # 75
```

Each `super()` call chains up one level. The whole chain of constructors fires.

---

## Method Overriding

A child class can **redefine** a method it inherited.

```python
class Animal:
    def speak(self):
        print("Some generic animal sound")

class Dog(Animal):
    def speak(self):          # overrides Animal's speak
        print("Woof!")

class Cat(Animal):
    def speak(self):          # overrides Animal's speak
        print("Meow!")
```

```python
a = Animal()
a.speak()    # Some generic animal sound

d = Dog()
d.speak()    # Woof!  — Dog's version, not Animal's

c = Cat()
c.speak()    # Meow!  — Cat's version
```

---

## Calling Parent Method + Adding to It

Sometimes you want to keep the parent's behavior AND add something.

```python
class Animal:
    def describe(self):
        print(f"I am an animal named {self.name}")

class Dog(Animal):
    def describe(self):
        super().describe()             # run Animal's describe first
        print(f"My breed is {self.breed}")   # then add Dog-specific info
```

```python
d = Dog("Bruno", "Labrador")
d.describe()
# I am an animal named Bruno
# My breed is Labrador
```

---

## Multiple Inheritance

A class can inherit from more than one parent.

```python
class Flyable:
    def fly(self):
        print("I can fly")

class Swimmable:
    def swim(self):
        print("I can swim")

class Duck(Flyable, Swimmable):
    def quack(self):
        print("Quack!")
```

```python
d = Duck()
d.fly()     # I can fly
d.swim()    # I can swim
d.quack()   # Quack!
```

---

## MRO — Method Resolution Order

When multiple parents have the same method, which one gets called?  
Python uses **MRO** — it searches left to right, then up.

```python
class A:
    def hello(self):
        print("Hello from A")

class B(A):
    def hello(self):
        print("Hello from B")

class C(A):
    def hello(self):
        print("Hello from C")

class D(B, C):   # inherits from B first, then C
    pass
```

```python
d = D()
d.hello()   # Hello from B  — B comes before C in D's definition

print(D.__mro__)
# (<class 'D'>, <class 'B'>, <class 'C'>, <class 'A'>, <class 'object'>)
```

Python searches: `D → B → C → A → object`  
First match wins.

---

## Hierarchical Inheritance

One parent class, **multiple child classes** all inheriting from it.  
Each child gets everything from the parent but adds its own behavior independently.

```
        Animal
       /   |   \
     Dog  Cat  Bird
```

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def breathe(self):
        print(f"{self.name} is breathing")

    def eat(self):
        print(f"{self.name} is eating")


class Dog(Animal):
    def speak(self):
        print(f"{self.name} says: Woof!")

    def fetch(self):
        print(f"{self.name} fetches the ball")


class Cat(Animal):
    def speak(self):
        print(f"{self.name} says: Meow!")

    def purr(self):
        print(f"{self.name} is purring")


class Bird(Animal):
    def speak(self):
        print(f"{self.name} says: Tweet!")

    def fly(self):
        print(f"{self.name} is flying")
```

```python
d = Dog("Bruno")
c = Cat("Whiskers")
b = Bird("Tweety")

d.breathe()   # Bruno is breathing     — from Animal
d.eat()       # Bruno is eating        — from Animal
d.speak()     # Bruno says: Woof!      — Dog's own
d.fetch()     # Bruno fetches the ball — Dog's own

c.breathe()   # Whiskers is breathing  — from Animal
c.speak()     # Whiskers says: Meow!   — Cat's own
c.purr()      # Whiskers is purring    — Cat's own

b.eat()       # Tweety is eating       — from Animal
b.speak()     # Tweety says: Tweet!    — Bird's own
b.fly()       # Tweety is flying       — Bird's own
```

All three share `breathe` and `eat` from `Animal` — written once, inherited by all.  
Each child is completely independent of the other children.

**Dog knows nothing about Cat. Cat knows nothing about Bird. They only share the parent.**

---

## Hybrid Inheritance

Hybrid inheritance is a **combination of two or more types of inheritance** in a single hierarchy.  
Most commonly it mixes multi-level + multiple inheritance.

```
        Animal
       /      \
     Dog      Robot
       \      /
       RoboDog
```

`RoboDog` inherits from both `Dog` and `Robot`.  
`Dog` inherits from `Animal`.  
This creates a diamond — the classic challenge of hybrid inheritance.

```python
class Animal:
    def __init__(self, name):
        self.name = name
        print(f"Animal init: {name}")

    def breathe(self):
        print(f"{self.name} breathes")


class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)       # chains to Animal
        self.breed = breed
        print(f"Dog init: breed={breed}")

    def bark(self):
        print(f"{self.name} barks!")


class Robot:
    def __init__(self, model):
        self.model = model
        print(f"Robot init: model={model}")

    def compute(self):
        print(f"{self.model} is computing")


class RoboDog(Dog, Robot):
    def __init__(self, name, breed, model):
        Dog.__init__(self, name, breed)    # explicitly call Dog's init
        Robot.__init__(self, model)        # explicitly call Robot's init
        print(f"RoboDog init complete")

    def perform(self):
        print(f"{self.name} ({self.model}) barks and computes!")
```

```python
rd = RoboDog("Rex", "Cyber-Lab", "RX-9")
# Animal init: Rex
# Dog init: breed=Cyber-Lab
# Robot init: model=RX-9
# RoboDog init complete

rd.breathe()    # Rex breathes       — from Animal (via Dog)
rd.bark()       # Rex barks!         — from Dog
rd.compute()    # RX-9 is computing  — from Robot
rd.perform()    # Rex (RX-9) barks and computes! — RoboDog's own

print(rd.name)    # Rex
print(rd.breed)   # Cyber-Lab
print(rd.model)   # RX-9
```

---

### The Diamond Problem in Hybrid Inheritance

When two parents share a common ancestor, Python can call that ancestor's `__init__` **twice** if you're not careful.

```
       A
      / \
     B   C
      \ /
       D
```

```python
class A:
    def __init__(self):
        print("A init")

class B(A):
    def __init__(self):
        super().__init__()
        print("B init")

class C(A):
    def __init__(self):
        super().__init__()
        print("C init")

class D(B, C):
    def __init__(self):
        super().__init__()   # Python's MRO handles this correctly
        print("D init")
```

```python
d = D()
# A init   — called only ONCE, MRO prevents double call
# C init
# B init
# D init
```

Python's MRO (C3 linearization) ensures `A.__init__` fires **exactly once**, even though both `B` and `C` call `super()`.

```python
print(D.__mro__)
# (<class 'D'>, <class 'B'>, <class 'C'>, <class 'A'>, <class 'object'>)
```

The MRO chain is: `D → B → C → A → object`  
Each `super()` call passes the baton to the **next in the MRO chain**, not to the direct parent.  
This is why Python's `super()` works correctly in diamond/hybrid situations.

---

### Summary of All Inheritance Types

| Type | Structure | Description |
|---|---|---|
| **Single** | `A → B` | One parent, one child |
| **Multi-level** | `A → B → C` | Chain of inheritance |
| **Multiple** | `B, C → D` | One child, many parents |
| **Hierarchical** | `A → B, C, D` | One parent, many children |
| **Hybrid** | Mix of the above | Combination in one hierarchy |

---

## `isinstance()` and `issubclass()`

```python
class Animal: pass
class Dog(Animal): pass

d = Dog()

print(isinstance(d, Dog))      # True  — d is a Dog
print(isinstance(d, Animal))   # True  — d is also an Animal (inherited)
print(issubclass(Dog, Animal)) # True  — Dog is a subclass of Animal
```

---

---

# Pillar 3 — Polymorphism

## The Idea

Poly = many. Morph = forms.  
**Same interface, different behavior.**

One function, one call — but different things happen depending on what object it's working with.

---

## Method Polymorphism (via Overriding)

```python
class Shape:
    def area(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14 * self.radius ** 2

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height
```

```python
shapes = [Circle(5), Rectangle(4, 6), Circle(3)]

for shape in shapes:
    print(shape.area())   # same call — different result each time
# 78.5
# 24
# 28.26
```

The loop doesn't care what shape it is. It just calls `.area()`.  
Each object responds differently. That's polymorphism.

---

## Duck Typing

Python's flavor of polymorphism. Very powerful.

> "If it walks like a duck and quacks like a duck, it's a duck."

You don't need inheritance. If an object has the method you're calling — it works.

```python
class Dog:
    def speak(self):
        print("Woof!")

class Cat:
    def speak(self):
        print("Meow!")

class Robot:
    def speak(self):
        print("Beep boop.")
```

```python
def make_it_speak(thing):   # no type restriction
    thing.speak()

make_it_speak(Dog())    # Woof!
make_it_speak(Cat())    # Meow!
make_it_speak(Robot())  # Beep boop.
```

`Dog`, `Cat`, and `Robot` have zero relationship to each other. No shared parent. But they all have `.speak()` — so the function works with all of them.

---

## Built-in Polymorphism

Python's built-ins are full of polymorphism.

```python
print(len("hello"))       # 5  — length of string
print(len([1, 2, 3]))     # 3  — length of list
print(len({"a": 1}))      # 1  — length of dict
```

Same `len()` call — different types — different internal behavior.

```python
print(5 + 3)         # 8     — integer addition
print("Hello" + " World")  # Hello World — string concatenation
```

`+` means different things depending on the type.

---

## Operator Overloading — `__dunder__` methods

You can define what operators mean for your own classes using special (dunder) methods.

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):        # defines +
        return Vector(self.x + other.x, self.y + other.y)

    def __str__(self):               # defines how it prints
        return f"Vector({self.x}, {self.y})"

    def __len__(self):               # defines len()
        return int((self.x**2 + self.y**2) ** 0.5)
```

```python
v1 = Vector(2, 3)
v2 = Vector(1, 4)

v3 = v1 + v2         # calls __add__
print(v3)            # Vector(3, 7)  — calls __str__
print(len(v1))       # 3             — calls __len__
```

---

## Key Dunder Methods

| Method | Triggered by |
|---|---|
| `__init__` | `ClassName()` |
| `__str__` | `print(obj)` or `str(obj)` |
| `__repr__` | `repr(obj)`, debugging |
| `__add__` | `obj1 + obj2` |
| `__sub__` | `obj1 - obj2` |
| `__mul__` | `obj1 * obj2` |
| `__eq__` | `obj1 == obj2` |
| `__lt__` | `obj1 < obj2` |
| `__len__` | `len(obj)` |
| `__getitem__` | `obj[index]` |

---

---

# Pillar 4 — Abstraction

## The Idea

Abstraction means **showing what an object can do, hiding how it does it.**

You define a **contract** — a set of methods that every subclass MUST implement.  
The contract says *what*, not *how*.

---

## Abstract Base Class (ABC)

Python provides the `abc` module for this.

```python
from abc import ABC, abstractmethod

class Notifier(ABC):

    @abstractmethod
    def send(self, user, message):
        pass   # No implementation. Just declaring the contract.
```

`Notifier` cannot be instantiated directly:

```python
n = Notifier()   # TypeError: Can't instantiate abstract class
```

It exists only to be inherited from.

---

## Implementing the Contract

```python
class EmailNotifier(Notifier):
    def send(self, user, message):
        # HOW email works — hidden inside here
        print(f"[EMAIL] Connecting to SMTP...")
        print(f"[EMAIL] To: {user.email} | Message: {message}")


class SMSNotifier(Notifier):
    def send(self, user, message):
        # HOW SMS works — hidden inside here
        print(f"[SMS] Calling Twilio API...")
        print(f"[SMS] To: {user.phone} | Message: {message}")


class SlackNotifier(Notifier):
    def send(self, user, message):
        # HOW Slack works — hidden inside here
        print(f"[SLACK] Posting to channel...")
        print(f"[SLACK] Message: {message}")
```

---

## The Clean Business Logic

```python
def notify_user(notifier: Notifier, user, message):
    notifier.send(user, message)   # only sees the WHAT
```

This function will work for every notifier, present and future, without ever being modified.

```python
notify_user(EmailNotifier(), user, "Order confirmed")
notify_user(SMSNotifier(), user, "OTP: 4521")
notify_user(SlackNotifier(), user, "Deployment failed")
```

---

## Forgetting to Implement a Method

```python
class BrokenNotifier(Notifier):
    pass   # forgot to implement send()
```

```python
b = BrokenNotifier()
# TypeError: Can't instantiate abstract class BrokenNotifier
# with abstract method send
```

Python enforces the contract. You cannot create an object that breaks it.

---

## Multiple Abstract Methods

An abstract class can enforce multiple methods.

```python
from abc import ABC, abstractmethod

class PaymentGateway(ABC):

    @abstractmethod
    def charge(self, amount):
        pass

    @abstractmethod
    def refund(self, amount):
        pass

    @abstractmethod
    def get_status(self, transaction_id):
        pass
```

```python
class StripeGateway(PaymentGateway):
    def charge(self, amount):
        print(f"Stripe: charging ₹{amount}")

    def refund(self, amount):
        print(f"Stripe: refunding ₹{amount}")

    def get_status(self, transaction_id):
        print(f"Stripe: checking status of {transaction_id}")


class RazorpayGateway(PaymentGateway):
    def charge(self, amount):
        print(f"Razorpay: charging ₹{amount}")

    def refund(self, amount):
        print(f"Razorpay: refunding ₹{amount}")

    def get_status(self, transaction_id):
        print(f"Razorpay: checking status of {transaction_id}")
```

```python
def process_payment(gateway: PaymentGateway, amount):
    gateway.charge(amount)

process_payment(StripeGateway(), 500)    # Stripe: charging ₹500
process_payment(RazorpayGateway(), 500)  # Razorpay: charging ₹500
```

Tomorrow if you add `PaytmGateway` — `process_payment` never changes.

---

## Abstract Class Can Have Non-Abstract Methods Too

Not everything has to be abstract. You can mix.

```python
class Notifier(ABC):

    @abstractmethod
    def send(self, user, message):
        pass   # must be overridden

    def log(self, message):             # regular method — shared by all
        print(f"[LOG] Notification sent: {message}")
```

```python
class EmailNotifier(Notifier):
    def send(self, user, message):
        print(f"Email to {user.email}: {message}")
        self.log(message)   # inherits log() from Notifier
```

---

---

# Putting It All Together

A real example using all 4 pillars at once.

```python
from abc import ABC, abstractmethod

# ABSTRACTION — defines the contract
class Employee(ABC):

    def __init__(self, name, employee_id):
        self.__name = name                # ENCAPSULATION — private
        self.__employee_id = employee_id  # ENCAPSULATION — private

    @property
    def name(self):                       # ENCAPSULATION — controlled access
        return self.__name

    @property
    def employee_id(self):
        return self.__employee_id

    @abstractmethod
    def calculate_salary(self):           # ABSTRACTION — contract
        pass

    def display(self):
        print(f"ID: {self.employee_id} | Name: {self.name} | Salary: ₹{self.calculate_salary()}")


# INHERITANCE — FullTimeEmployee inherits Employee
class FullTimeEmployee(Employee):
    def __init__(self, name, employee_id, monthly_salary):
        super().__init__(name, employee_id)       # constructor chaining
        self.__monthly_salary = monthly_salary

    def calculate_salary(self):                   # POLYMORPHISM — own implementation
        return self.__monthly_salary


# INHERITANCE — FreelanceEmployee inherits Employee
class FreelanceEmployee(Employee):
    def __init__(self, name, employee_id, hours, rate_per_hour):
        super().__init__(name, employee_id)       # constructor chaining
        self.__hours = hours
        self.__rate_per_hour = rate_per_hour

    def calculate_salary(self):                   # POLYMORPHISM — own implementation
        return self.__hours * self.__rate_per_hour
```

```python
employees = [
    FullTimeEmployee("Ravi", "E001", 85000),
    FreelanceEmployee("Priya", "E002", 120, 500),
    FullTimeEmployee("Arjun", "E003", 72000),
]

for emp in employees:
    emp.display()   # POLYMORPHISM — same call, different salary logic

# ID: E001 | Name: Ravi   | Salary: ₹85000
# ID: E002 | Name: Priya  | Salary: ₹60000
# ID: E003 | Name: Arjun  | Salary: ₹72000
```

---

## Summary Table

| Pillar | Core Idea | Key Tool |
|---|---|---|
| **Encapsulation** | Hide data, control access | `__private`, `@property` |
| **Inheritance** | Reuse and extend existing classes | `class Child(Parent)`, `super()` |
| **Polymorphism** | Same call, different behavior | Method overriding, duck typing |
| **Abstraction** | Define contract, hide implementation | `ABC`, `@abstractmethod` |

---

## Quick Reference — Most Used Patterns

```python
# Class with private data + property
class MyClass:
    def __init__(self, value):
        self.__value = value

    @property
    def value(self):
        return self.__value

    @value.setter
    def value(self, v):
        self.__value = v


# Inheritance with constructor chaining
class Child(Parent):
    def __init__(self, x, y):
        super().__init__(x)
        self.y = y


# Abstract class
from abc import ABC, abstractmethod

class Interface(ABC):
    @abstractmethod
    def do_something(self):
        pass


# Polymorphism
for obj in list_of_objects:
    obj.do_something()   # works on all, each behaves differently
```

---

---

# Magic Methods (Dunder Methods) — Complete Guide

Magic methods are special methods with double underscores on both sides — `__method__`.  
Python calls them **automatically** in response to specific operations. You never call them directly.

```python
obj + other      # Python calls obj.__add__(other)
print(obj)       # Python calls obj.__str__()
len(obj)         # Python calls obj.__len__()
obj()            # Python calls obj.__call__()
del obj          # Python calls obj.__del__()
```

You define them to make your objects behave like built-in Python types.

---

## 1. Object Lifecycle Methods

These control **creation and destruction** of objects.

---

### `__new__` — Object Creation (before `__init__`)

`__new__` is called **before** `__init__`. It actually creates and returns the object.  
`__init__` just fills it with data.

In 99% of cases you don't touch `__new__`. But it matters for singletons and immutable types.

```python
class MyClass:
    def __new__(cls, *args, **kwargs):
        print("__new__ called — object is being CREATED")
        instance = super().__new__(cls)   # actually creates the object
        return instance

    def __init__(self, value):
        print("__init__ called — object is being INITIALIZED")
        self.value = value
```

```python
obj = MyClass(10)
# __new__ called — object is being CREATED
# __init__ called — object is being INITIALIZED
```

**Real use case — Singleton (only one instance ever exists):**

```python
class Singleton:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance   # always returns the same object
```

```python
a = Singleton()
b = Singleton()
print(a is b)   # True — exact same object
```

---

### `__init__` — Initialization

Already covered in Pillars. Included here for completeness.

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

Called right after `__new__`. Sets up the object's initial state.

---

### `__del__` — Destructor

Called when an object is **about to be garbage collected** — when there are no more references to it.

```python
class DatabaseConnection:
    def __init__(self, db_name):
        self.db_name = db_name
        print(f"[OPEN] Connection to '{db_name}' opened")

    def __del__(self):
        print(f"[CLOSE] Connection to '{self.db_name}' closed")
```

```python
conn = DatabaseConnection("users_db")
# [OPEN] Connection to 'users_db' opened

del conn
# [CLOSE] Connection to 'users_db' closed
```

**When does `__del__` fire without explicit `del`?**

```python
def connect():
    conn = DatabaseConnection("orders_db")
    # conn only lives inside this function
    # when function ends, conn goes out of scope → __del__ fires

connect()
# [OPEN] Connection to 'orders_db' opened
# [CLOSE] Connection to 'orders_db' closed
```

**Important warnings about `__del__`:**

```python
# __del__ is NOT guaranteed to fire at a specific time
# Python's garbage collector decides when — don't rely on it for critical cleanup
# Use context managers (__enter__ / __exit__) for guaranteed cleanup instead
```

---

## 2. String Representation Methods

These control **how your object looks** when printed or inspected.

---

### `__str__` — Human-Readable String

Called by `print()` and `str()`. For the end user.

```python
class Book:
    def __init__(self, title, author):
        self.title = title
        self.author = author

    def __str__(self):
        return f"'{self.title}' by {self.author}"
```

```python
b = Book("1984", "George Orwell")
print(b)       # '1984' by George Orwell
print(str(b))  # '1984' by George Orwell
```

---

### `__repr__` — Developer/Debug Representation

Called by `repr()`, in the Python shell, and in logs.  
Should ideally return a string that could recreate the object.

```python
class Book:
    def __init__(self, title, author):
        self.title = title
        self.author = author

    def __repr__(self):
        return f"Book(title='{self.title}', author='{self.author}')"
```

```python
b = Book("1984", "George Orwell")
repr(b)   # Book(title='1984', author='George Orwell')

# In a list, __repr__ is used (not __str__)
books = [Book("1984", "Orwell"), Book("Dune", "Herbert")]
print(books)
# [Book(title='1984', author='Orwell'), Book(title='Dune', author='Herbert')]
```

**Rule of thumb:**
- `__str__` → for users → readable
- `__repr__` → for developers → unambiguous

If only `__repr__` is defined, Python uses it for both.

---

### `__format__` — Custom `format()` Support

```python
class Temperature:
    def __init__(self, celsius):
        self.celsius = celsius

    def __format__(self, spec):
        if spec == "F":
            return f"{self.celsius * 9/5 + 32:.1f}°F"
        elif spec == "K":
            return f"{self.celsius + 273.15:.1f}K"
        return f"{self.celsius}°C"
```

```python
t = Temperature(100)
print(format(t))      # 100°C
print(format(t, "F")) # 212.0°F
print(format(t, "K")) # 373.1K
print(f"Boiling point: {t:F}")  # Boiling point: 212.0°F
```

---

## 3. Arithmetic Operator Methods

These let your objects support `+`, `-`, `*`, `/` etc.

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):         # self + other
        return Vector(self.x + other.x, self.y + other.y)

    def __sub__(self, other):         # self - other
        return Vector(self.x - other.x, self.y - other.y)

    def __mul__(self, scalar):        # self * scalar
        return Vector(self.x * scalar, self.y * scalar)

    def __truediv__(self, scalar):    # self / scalar
        return Vector(self.x / scalar, self.y / scalar)

    def __neg__(self):                # -self (unary minus)
        return Vector(-self.x, -self.y)

    def __abs__(self):                # abs(self)
        return (self.x**2 + self.y**2) ** 0.5

    def __str__(self):
        return f"Vector({self.x}, {self.y})"
```

```python
v1 = Vector(3, 4)
v2 = Vector(1, 2)

print(v1 + v2)    # Vector(4, 6)
print(v1 - v2)    # Vector(2, 2)
print(v1 * 3)     # Vector(9, 12)
print(v1 / 2)     # Vector(1.5, 2.0)
print(-v1)        # Vector(-3, -4)
print(abs(v1))    # 5.0
```

---

### Reflected Operators — `__radd__`, `__rmul__` etc.

What if the left operand doesn't know how to handle the operation?

```python
v1 * 3   # calls v1.__mul__(3)  — works
3 * v1   # calls int.__mul__(v1) — int doesn't know Vector → tries v1.__rmul__(3)
```

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)

    def __rmul__(self, scalar):      # handles scalar * vector
        return self.__mul__(scalar)

    def __str__(self):
        return f"Vector({self.x}, {self.y})"
```

```python
v = Vector(2, 3)
print(v * 4)   # Vector(8, 12)   — __mul__
print(4 * v)   # Vector(8, 12)   — __rmul__
```

---

### In-place Operators — `__iadd__`, `__imul__` etc.

For `+=`, `*=` etc.

```python
class Counter:
    def __init__(self, value=0):
        self.value = value

    def __iadd__(self, amount):    # +=
        self.value += amount
        return self                # must return self

    def __str__(self):
        return str(self.value)
```

```python
c = Counter(10)
c += 5
print(c)   # 15
```

---

## 4. Comparison Operator Methods

```python
class Student:
    def __init__(self, name, gpa):
        self.name = name
        self.gpa = gpa

    def __eq__(self, other):    # ==
        return self.gpa == other.gpa

    def __ne__(self, other):    # !=
        return self.gpa != other.gpa

    def __lt__(self, other):    # <
        return self.gpa < other.gpa

    def __le__(self, other):    # <=
        return self.gpa <= other.gpa

    def __gt__(self, other):    # >
        return self.gpa > other.gpa

    def __ge__(self, other):    # >=
        return self.gpa >= other.gpa

    def __str__(self):
        return f"{self.name}({self.gpa})"
```

```python
s1 = Student("Ravi", 8.5)
s2 = Student("Priya", 9.2)

print(s1 < s2)    # True
print(s1 == s2)   # False
print(s2 > s1)    # True

students = [Student("Ravi", 8.5), Student("Priya", 9.2), Student("Arjun", 7.8)]
students.sort()   # works because __lt__ is defined
for s in students:
    print(s)
# Arjun(7.8)
# Ravi(8.5)
# Priya(9.2)
```

**Shortcut — `@functools.total_ordering`**

Define only `__eq__` and one of `__lt__` / `__le__` / `__gt__` / `__ge__`.  
Python fills in the rest automatically.

```python
from functools import total_ordering

@total_ordering
class Student:
    def __init__(self, name, gpa):
        self.name = name
        self.gpa = gpa

    def __eq__(self, other):
        return self.gpa == other.gpa

    def __lt__(self, other):         # define just this one
        return self.gpa < other.gpa

# __le__, __gt__, __ge__ are auto-generated
```

---

## 5. Container / Collection Methods

These make your object behave like a list, dict, or set.

```python
class Playlist:
    def __init__(self):
        self.__songs = []

    def add(self, song):
        self.__songs.append(song)

    def __len__(self):                    # len(playlist)
        return len(self.__songs)

    def __getitem__(self, index):         # playlist[0]
        return self.__songs[index]

    def __setitem__(self, index, value):  # playlist[0] = "new song"
        self.__songs[index] = value

    def __delitem__(self, index):         # del playlist[0]
        del self.__songs[index]

    def __contains__(self, song):         # "song" in playlist
        return song in self.__songs

    def __iter__(self):                   # for song in playlist
        return iter(self.__songs)

    def __reversed__(self):               # reversed(playlist)
        return reversed(self.__songs)
```

```python
p = Playlist()
p.add("Blinding Lights")
p.add("Levitating")
p.add("Peaches")

print(len(p))               # 3
print(p[0])                 # Blinding Lights
print("Levitating" in p)    # True

p[1] = "Save Your Tears"
del p[2]

for song in p:              # __iter__ enables this
    print(song)
# Blinding Lights
# Save Your Tears
```

---

### `__missing__` — Default Value for Missing Dict Keys

```python
class DefaultDict:
    def __init__(self, default):
        self.__data = {}
        self.__default = default

    def __getitem__(self, key):
        return self.__data[key]

    def __setitem__(self, key, value):
        self.__data[key] = value

    def __missing__(self, key):               # called when key not found
        self.__data[key] = self.__default     # auto-insert default
        return self.__default
```

```python
d = DefaultDict(0)
d["a"] = 5
print(d["a"])   # 5
print(d["b"])   # 0  — key didn't exist, __missing__ fired, inserted 0
```

---

## 6. `__call__` — Make an Object Callable

`__call__` lets you call an **object like a function**.

```python
class Multiplier:
    def __init__(self, factor):
        self.factor = factor

    def __call__(self, value):          # triggered by obj(value)
        return value * self.factor
```

```python
double = Multiplier(2)
triple = Multiplier(3)

print(double(10))    # 20  — calling the object like a function
print(triple(10))    # 30

print(callable(double))  # True — has __call__, so it's callable
```

**Real use case — Stateful function:**

```python
class Counter:
    def __init__(self):
        self.__count = 0

    def __call__(self):
        self.__count += 1
        return self.__count
```

```python
count = Counter()
print(count())   # 1
print(count())   # 2
print(count())   # 3
```

**Real use case — Validators as callable objects:**

```python
class RangeValidator:
    def __init__(self, min_val, max_val):
        self.min_val = min_val
        self.max_val = max_val

    def __call__(self, value):
        if not (self.min_val <= value <= self.max_val):
            raise ValueError(f"{value} must be between {self.min_val} and {self.max_val}")
        return True
```

```python
validate_age = RangeValidator(0, 120)
validate_score = RangeValidator(0, 100)

validate_age(25)     # True
validate_score(85)   # True
validate_age(200)    # ValueError: 200 must be between 0 and 120
```

---

## 7. Attribute Access Methods

These intercept getting, setting, and deleting attributes.

---

### `__getattr__` — Called when attribute is NOT found normally

```python
class FlexObject:
    def __init__(self):
        self.real = "I exist"

    def __getattr__(self, name):         # only fires if normal lookup fails
        return f"'{name}' doesn't exist, but I'm handling it"
```

```python
obj = FlexObject()
print(obj.real)           # I exist  — normal attribute, __getattr__ NOT called
print(obj.fake_attr)      # 'fake_attr' doesn't exist, but I'm handling it
```

---

### `__getattribute__` — Called on EVERY attribute access

This is more powerful (and dangerous) — fires on every access, even existing ones.

```python
class Logged:
    def __init__(self, value):
        self.value = value

    def __getattribute__(self, name):
        print(f"[ACCESS] Accessing attribute: {name}")
        return super().__getattribute__(name)   # must call super — else infinite loop
```

```python
obj = Logged(42)
print(obj.value)
# [ACCESS] Accessing attribute: value
# 42
```

---

### `__setattr__` — Called on every attribute assignment

```python
class Validated:
    def __setattr__(self, name, value):
        if name == "age" and not isinstance(value, int):
            raise TypeError("age must be an integer")
        super().__setattr__(name, value)   # must call super to actually set it
```

```python
obj = Validated()
obj.age = 25      # fine
obj.age = "old"   # TypeError: age must be an integer
```

---

### `__delattr__` — Called when an attribute is deleted

```python
class Protected:
    def __init__(self):
        self.data = "important"
        self.temp = "can delete"

    def __delattr__(self, name):
        if name == "data":
            raise AttributeError("Cannot delete 'data'")
        super().__delattr__(name)
```

```python
obj = Protected()
del obj.temp    # fine
del obj.data    # AttributeError: Cannot delete 'data'
```

---

## 8. Context Manager Methods — `__enter__` and `__exit__`

These power the `with` statement. Guarantee cleanup even if errors occur.

```python
class FileManager:
    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode
        self.file = None

    def __enter__(self):                    # called at start of `with` block
        print(f"Opening {self.filename}")
        self.file = open(self.filename, self.mode)
        return self.file                    # this becomes the `as` variable

    def __exit__(self, exc_type, exc_val, exc_tb):   # called at end, always
        print(f"Closing {self.filename}")
        if self.file:
            self.file.close()
        return False                        # False = don't suppress exceptions
```

```python
with FileManager("test.txt", "w") as f:
    f.write("Hello!")
# Opening test.txt
# Closing test.txt  — guaranteed, even if write() had raised an error
```

**`__exit__` parameters:**

```python
def __exit__(self, exc_type, exc_val, exc_tb):
    # exc_type  → type of exception (None if no error)
    # exc_val   → exception value/message
    # exc_tb    → traceback object
    # return True  → suppress the exception
    # return False → let it propagate
```

**Real use case — Database transaction:**

```python
class Transaction:
    def __init__(self, connection):
        self.conn = connection

    def __enter__(self):
        print("BEGIN TRANSACTION")
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type is None:
            print("COMMIT")       # no error → commit
        else:
            print("ROLLBACK")     # error occurred → rollback
        return False
```

```python
with Transaction(db_conn) as tx:
    # do database operations
    pass
# If all good: COMMIT
# If exception: ROLLBACK
```

---

## 9. Hashing and Boolean

---

### `__hash__` — Makes object usable in sets and dict keys

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

    def __hash__(self):              # must define if you define __eq__
        return hash((self.x, self.y))
```

```python
p1 = Point(1, 2)
p2 = Point(1, 2)
p3 = Point(3, 4)

print(p1 == p2)   # True
print(hash(p1) == hash(p2))   # True

point_set = {p1, p2, p3}
print(len(point_set))   # 2  — p1 and p2 are equal, only stored once
```

> If you define `__eq__` without `__hash__`, Python sets `__hash__` to `None`, making objects unhashable. Always define both together.

---

### `__bool__` — Truth value of an object

```python
class BankAccount:
    def __init__(self, balance):
        self.balance = balance

    def __bool__(self):
        return self.balance > 0    # account is "truthy" only if has money
```

```python
acc1 = BankAccount(1000)
acc2 = BankAccount(0)

if acc1:
    print("acc1 has funds")    # acc1 has funds

if not acc2:
    print("acc2 is empty")     # acc2 is empty

print(bool(acc1))   # True
print(bool(acc2))   # False
```

If `__bool__` is not defined, Python falls back to `__len__`. If neither exists, the object is always `True`.

---

## 10. Iteration Methods — `__iter__` and `__next__`

These let your object work in `for` loops and with `next()`.

```python
class Countdown:
    def __init__(self, start):
        self.start = start
        self.current = start

    def __iter__(self):          # returns the iterator object (self here)
        self.current = self.start
        return self

    def __next__(self):          # returns next value, raises StopIteration when done
        if self.current < 0:
            raise StopIteration
        value = self.current
        self.current -= 1
        return value
```

```python
for n in Countdown(5):
    print(n)
# 5  4  3  2  1  0

# Manual iteration
c = Countdown(3)
it = iter(c)
print(next(it))   # 3
print(next(it))   # 2
print(next(it))   # 1
print(next(it))   # 0
print(next(it))   # StopIteration
```

---

## 11. `__slots__` — Memory Optimization

By default Python stores object attributes in a dict (`__dict__`). For thousands of objects, this wastes memory. `__slots__` replaces that dict with a fixed tuple.

```python
class WithoutSlots:
    def __init__(self, x, y):
        self.x = x
        self.y = y
# Each instance has a __dict__ — flexible but memory-heavy


class WithSlots:
    __slots__ = ['x', 'y']     # only these attributes allowed

    def __init__(self, x, y):
        self.x = x
        self.y = y
# No __dict__ — faster and lighter
```

```python
obj = WithSlots(1, 2)
print(obj.x)         # 1
obj.z = 3            # AttributeError — z not in __slots__
```

---

## Complete Magic Methods Reference

### Lifecycle
| Method | Triggered by |
|---|---|
| `__new__(cls)` | Object creation (before init) |
| `__init__(self)` | Object initialization |
| `__del__(self)` | Object destruction / garbage collection |

### Representation
| Method | Triggered by |
|---|---|
| `__str__` | `print(obj)`, `str(obj)` |
| `__repr__` | `repr(obj)`, shell display, lists |
| `__format__` | `format(obj, spec)`, f-strings with spec |
| `__bytes__` | `bytes(obj)` |

### Arithmetic
| Method | Triggered by |
|---|---|
| `__add__` | `obj + other` |
| `__sub__` | `obj - other` |
| `__mul__` | `obj * other` |
| `__truediv__` | `obj / other` |
| `__floordiv__` | `obj // other` |
| `__mod__` | `obj % other` |
| `__pow__` | `obj ** other` |
| `__neg__` | `-obj` |
| `__pos__` | `+obj` |
| `__abs__` | `abs(obj)` |
| `__radd__` | `other + obj` (reflected) |
| `__iadd__` | `obj += other` (in-place) |

### Comparison
| Method | Triggered by |
|---|---|
| `__eq__` | `obj == other` |
| `__ne__` | `obj != other` |
| `__lt__` | `obj < other` |
| `__le__` | `obj <= other` |
| `__gt__` | `obj > other` |
| `__ge__` | `obj >= other` |

### Container
| Method | Triggered by |
|---|---|
| `__len__` | `len(obj)` |
| `__getitem__` | `obj[key]` |
| `__setitem__` | `obj[key] = value` |
| `__delitem__` | `del obj[key]` |
| `__contains__` | `item in obj` |
| `__iter__` | `for x in obj`, `iter(obj)` |
| `__next__` | `next(obj)` |
| `__reversed__` | `reversed(obj)` |
| `__missing__` | `obj[key]` when key not found (dict subclass) |

### Attribute Access
| Method | Triggered by |
|---|---|
| `__getattr__` | `obj.attr` (only when not found normally) |
| `__getattribute__` | `obj.attr` (every access) |
| `__setattr__` | `obj.attr = value` |
| `__delattr__` | `del obj.attr` |
| `__dir__` | `dir(obj)` |

### Callable & Context
| Method | Triggered by |
|---|---|
| `__call__` | `obj(args)` |
| `__enter__` | `with obj as x:` start |
| `__exit__` | `with` block end |

### Type & Identity
| Method | Triggered by |
|---|---|
| `__bool__` | `bool(obj)`, `if obj:` |
| `__hash__` | `hash(obj)`, used in sets/dicts |
| `__sizeof__` | `sys.getsizeof(obj)` |
| `__class__` | `obj.__class__` |

### Bitwise
| Method | Triggered by |
|---|---|
| `__and__` | `obj & other` |
| `__or__` | `obj \| other` |
| `__xor__` | `obj ^ other` |
| `__lshift__` | `obj << n` |
| `__rshift__` | `obj >> n` |
| `__invert__` | `~obj` |

---

*End of Document*
