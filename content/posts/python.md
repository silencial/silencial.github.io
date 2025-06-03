---
title: Python
date: 2021-04-26
lastmod: 2025-06-02
categories:
- Tech
tags:
- CS
- Tool"
---

Python 知识点梳理

<!--more-->

---

# 语法

## 数据类（dataclasses）

Python 3.7 引入了 `dataclasses` 模块，提供了一个装饰器和一些函数，用于自动为数据类添加特殊方法，如 `__init__()`、`__repr__()`、`__eq__()`、`__lt__()` 等，从而简化代码编写。

```python
from dataclasses import dataclass, field
import uuid # 用于生成唯一ID

# 辅助函数，用于 field 的 default_factory
def generate_id():
    return str(uuid.uuid4()).split('-')[0]

@dataclass(order=True, frozen=False) # order=True 会自动生成 __lt__, __le__, __gt__, __ge__
                                   # frozen=True 会使实例不可变（生成 __hash__ 但禁止赋值）
class InventoryItem:
    name: str
    unit_price: float
    quantity_on_hand: int = 0 # 可以为字段提供默认值
    # item_id 字段在构造时不提供，而是通过 default_factory 自动生成
    item_id: str = field(default_factory=generate_id, init=False, repr=True)

    # 仍然可以自定义方法
    def total_cost(self) -> float:
        return self.unit_price * self.quantity_on_hand

# 创建实例
item1 = InventoryItem("笔记本电脑", 7999.90, 5)
item2 = InventoryItem("无线鼠标", 199.00, 25)
item3 = InventoryItem("笔记本电脑", 7999.90, 5) # item_id 会不同于 item1
item4 = InventoryItem("键盘", 499.50) # quantity_on_hand 使用默认值 0

print(item1) # 自动生成的 __repr__
print(f"{item1.name} 的总价值: {item1.total_cost():.2f}")
```

## 赋值表达式（Assignment Expression `:=`）

Python 3.8 引入了赋值表达式，也称为「海象运算符」。它允许在表达式内部为变量赋值。

```python
# 1. 在 if 语句中使用
user_input = "example_value_123"
if ((match_result := user_input).startswith("example_")): # 赋值并判断
    print(f"输入以 'example_' 开头，匹配结果: {match_result}, 原值: {user_input}")
else:
    print(f"输入不以 'example_' 开头 (原值: {user_input})")

# 2. 在列表推导式中使用
def process(x):
    return x * x

data = [1, 2, 3, 4, 5, -1, -2]
processed_data = [p_val for x in data if x > 0 and (p_val := process(x)) > 10]
print(f"处理后大于10的数据: {processed_data}")

```

## 字典的合并和更新运算符（`|` 和 `|=`）

Python 3.9 引入了新的字典合并运算符 `|` 和 `|=`，作用与在集合中类似。

```python
dict_a = {'name': 'Alice', 'age': 30}
dict_b = {'age': 31, 'city': 'New York', 'email': 'alice@example.com'}

# 使用 | 合并字典（如果键冲突，右侧字典的值优先）
merged_dict = dict_a | dict_b
print(f"合并后的字典 (dict_a | dict_b): {merged_dict}")

# 使用 |= 原地更新字典
dict_c = {'name': 'Bob', 'occupation': 'Engineer'}
dict_c |= {'occupation': 'Data Scientist', 'country': 'Canada'}
print(f"原地更新后的字典 (dict_c): {dict_c}")
```

## 结构化模式匹配（match-case）

Python 3.10 引入了结构化模式匹配，通过 `match` 和 `case` 语句实现。它允许你将一个值与一系列模式进行比较。如果找到匹配的模式，则执行相应的代码块。相当于其他语言中的 `switch-case` 语句的加强版。

```python
def describe_point(point):
    match point:
        case (0, 0): # 字面量模式：匹配元组 (0, 0)
            print("点在原点")
        case (0, y_coord): # 捕获模式：捕获 y 坐标
            print(f"点在 Y 轴上，y={y_coord}")
        case (x_coord, 0): # 捕获模式：捕获 x 坐标
            print(f"点在 X 轴上，x={x_coord}")
        case (x_coord, y_coord) if x_coord == y_coord: # 捕获模式带守卫（guard）
            print(f"点在 y=x 直线上, x=y={x_coord}")
        case (x_coord, y_coord): # 捕获模式：匹配任意二元组
            print(f"点坐标: ({x_coord}, {y_coord})")
        case _: # 通配符模式：匹配任何其他情况（可选，作为默认分支）
            print("输入不是一个有效的二维点")

describe_point((0, 0))
describe_point((0, 5))
describe_point((3, 0))
describe_point((2, 2))
describe_point((1, 10))
describe_point("not a point")
```

## 集合（Set）

集合是无序且不包含重复元素的容器。

```python
set1 = {1, 2, 3, 3, 3, 2} # {1, 2, 3}
set2 = set(range(2, 11, 2)) # {2, 4, 6, 8, 10}
print(f"set1: {set1}")
print(f"set2: {set2}")
print(f"Intersection (set1 & set2): {set1 & set2}")  # 交集
print(f"Union (set1 | set2): {set1 | set2}")  # 并集
print(f"Difference (set1 - set2): {set1 - set2}")  # 差集（set1 中有但 set2 中没有的）
print(f"Symmetric Difference (set1 ^ set2): {set1 ^ set2}")  # 对称差集（存在于 set1 或 set2 中，但不同时存在于两者中的元素）
print(f"Is set2 subset of set1? (set2 <= set1): {set2 <= set1}")  # 子集判断
print(f"Is set1 superset of set2? (set1 >= set2): {set1 >= set2}")  # 超集判断
```

## 迭代器（Iterator）

可以被 `for` 循环遍历的对象称为可迭代对象。迭代器不仅是可迭代对象，它还实现了 `__next__()` 方法，可以通过 `next()` 内置函数逐个获取元素，并在没有更多元素时引发 `StopIteration` 异常。例如，生成器就是一种迭代器。

```python
class FibIter():
    def __init__(self, num):
        self.num = num
        self.a, self.b = 0, 1
        self.idx = 0

    def __iter__(self):
        # 迭代器对象自身就是迭代器，因此返回 self
        return self

    def __next__(self):
        if self.idx < self.num:
            self.a, self.b = self.b, self.a + self.b
            self.idx += 1
            return self.a
        raise StopIteration()

fib = FibIter(10)
print(next(fib)) # 输出第一个 Fibonacci number (1)
for i in fib:    # 输出剩下的 9 个 Fibonacci number
    print(i)
```

## 生成器（Generator）

将 list comprehension 中的 `[]` 替换为 `()` 即可创建生成器。主要区别在于：list comprehension 会立即计算并存储所有元素，而生成器在迭代时才逐个计算并生成值，实现了惰性求值，从而在处理大量数据时能有效节省内存。

```python
lis = [m + n for m in 'ABCDEFG' for n in '12345']
gen = (m + n for m in 'ABCDEFG' for n in '12345')
```

在函数中使用 `yield` 关键字，该函数就会变成一个生成器函数。调用该函数会返回一个生成器对象。

```python
def fib_generator(num): # Renamed for clarity
    n, current, next_val = 0, 0, 1
    while n < num:
        yield current
        current, next_val = next_val, current + next_val
        n += 1

for i in fib_generator(10):
    print(i)
```

在生成器函数中，`yield` 语句不仅可以返回值，还可以接收通过 `send()` 方法传递的值，这使得生成器可以作为协程使用：

```python
def f():
    print("Coroutine started")
    while True:
        x, y = yield # 'yield' can receive a value here
        print(f"Received: x={x}, y={y}")

a = f()
a.send(None)  # 必须先发送 None 来启动生成器（执行到第一个 yield 语句）
a.send([1, 2])
a.send([3, 4])
# a.close() # 可以关闭生成器
```

## 装饰器（Decorator）

装饰器本质上是一个高阶函数，它接收函数作为参数并返回一个新的函数。Python 提供了 `@` 语法糖来简化装饰器的应用。例如，`@my_decorator` 应用到 `say` 函数，等价于执行 `say = my_decorator(say)`。

```python
def my_decorator(func):
    def wrapper():
        print(f"Before calling {func.__name__}")
        func()
        print(f"After calling {func.__name__}")
    return wrapper

@my_decorator
def say():
    print("Hello")

say()
```

带参数和返回值的装饰器：

```python
def verbose_decorator(func):
    def wrapper(*args, **kwargs):
        print(f"Before calling {func.__name__}, args={args}, kwargs={kwargs}")
        val = func(*args, **kwargs)
        print(f"After calling {func.__name__}, return value={val}")
        return val
    return wrapper

@verbose_decorator
def add(x, y):
    return x + y

print(add(5, 3))
```

### 内置装饰器

1. `@property`: 将类中的方法转换为只读属性，使其可以像访问成员变量一样被调用，无需使用圆括号。通常用于实现 getter 方法。可以配合 `@<attribute_name>.setter` 和 `@<attribute_name>.deleter` 实现 setter 和 deleter。
2. `@staticmethod`: 将方法标记为静态方法。静态方法不接收隐式的第一个参数（如 `self` 或 `cls`），因此不能直接访问类属性/方法。它们通常是与类相关的工具函数，可以通过类名或实例直接调用。
3. `@classmethod`: 将方法标记为类方法。类方法的第一个参数是类本身（通常命名为 `cls`），可以通过它访问类变量或调用其他类方法。常用于实现备选构造函数或操作类级别的属性。

```python
import time

class Date:
    def __init__(self, year, month, day):
        self._year = year # 使用下划线开头的属性名以避免与 property 名称冲突
        self.month = month
        self.day = day

    @classmethod
    def today(cls):
        t = time.localtime()
        return cls(t.tm_year, t.tm_mon, t.tm_mday) # 使用 cls 调用构造函数

    @property
    def year(self): # year 的 getter 方法
        print("Accessing year via @property getter")
        return self._year

    @year.setter
    def year(self, value): # year 的 setter 方法
        print(f"Setting year to {value} via @year.setter")
        if value < 0:
            raise ValueError("Year cannot be negative")
        self._year = value

    @staticmethod
    def subtract(x, y):
        return x - y

a = Date(2021, 4, 24)
b = Date.today()  # 使用 classmethod 实现另一种初始化方法

print(f"Year of b: {b.year}") # 调用 @property getter，而非 b.year()
b.year = 2012 # 调用 @year.setter
print(f"New year of b: {b.year}")

print(f"Subtraction (static method via class): {Date.subtract(10, 3)}")
print(f"Subtraction (static method via instance): {b.subtract(5, 2)}")
```

## 多重继承（Multiple Inheritance）

下方的代码中，`D` 类通过多重继承同时继承了 `B` 和 `C` 两个父类，从而获得了它们的功能。在多重继承中，父类的声明顺序非常重要，因为它决定了方法解析顺序（Method Resolution Order, MRO）。

`super()` 的工作原理如下（简化说明）：

```python
# 这是一个概念性的 super 实现，实际的 super() 更复杂
def conceptual_super(cls, inst):
    mro = inst.__class__.mro()
    return mro[mro.index(cls) + 1]
```

即从 `inst` 的 MRO 列表中寻找 `cls` 的下一个类。因此，在下方 `E` 类的 `foo` 方法中：

- `super().foo()`（等价于 `super(E, self).foo()`）会调用 `D` 的 `foo`（如果 `D` 没有 `foo`，则继续沿 MRO 寻找）。
- `super(B, self).foo()` 会从 `self`（类型为 `E`）的 MRO（`[E, D, B, C, A, object]`）中找到 `B` 之后的 `C`，所以调用 `C.foo()`。
- `super(C, self).foo()` 会从 `self` 的 MRO 中找到 `C` 之后的 `A`，所以调用 `A.foo()`。
- 注意这里的 `self` 永远指代 `E` 的实例，因此使用的 MRO 是相同的。

```python
class A(object):
    def foo(self):
        print('foo of A')

class B(A):
    def foo(self):
        print('foo of B')
        super().foo()

class C(A):
    def foo(self):
        print('foo of C')
        super().foo()

class D(B, C): # MRO: D, B, C, A, object
    def foo(self):
        print('foo of D')
        super().foo()

class E(D): # MRO: E, D, B, C, A, object
    def foo(self):
        print('foo in E')
        super().foo()
        print('--- Calling super(B, self).foo() from E ---')
        super(B, self).foo()
        print('--- Calling super(C, self).foo() from E ---')
        super(C, self).foo()

print("--- d.foo() ---")
d = D()
d.foo()

print("\n--- e.foo() ---")
e = E()
e.foo()
```

## 上下文管理（Context Management）

实现了特定协议（`__enter__` 和 `__exit__` 方法）的对象称为上下文管理器，可以使用 `with` 语句进行管理。进入 `with` 块时，对象的 `__enter__` 方法被调用，其返回值赋给 `as` 子句的变量；退出 `with` 块时（无论正常退出还是因异常退出），`__exit__` 方法被调用。这使得自定义资源管理逻辑（如自动获取和释放文件、锁、网络连接等资源）变得方便。

```python
class MyContextManager():
    def __init__(self, name):
        self.name = name
        print(f"MyContextManager('{self.name}'): __init__ called")

    def __enter__(self):
        print(f"MyContextManager('{self.name}'): __enter__ called, resource setup")
        return self # 这个返回值会赋给 'as' 后面的变量

    def __exit__(self, exc_type, exc_value, traceback):
        # exc_type: 异常类型
        # exc_value: 异常实例
        # traceback: traceback 对象
        print(f"MyContextManager('{self.name}'): __exit__ called, resource cleanup")
        if exc_type:
            print(f"  Exception occurred: {exc_type}, {exc_value}")
        # 如果 __exit__ 返回 True，则表示异常已被处理，不会向外传播
        # 如果返回 False 或 None（默认），异常会继续向外传播
        return False

    def do_something(self):
        print(f"MyContextManager('{self.name}'): do_something() called")

with MyContextManager('test_context') as cm:
    cm.do_something()
    print("  Inside 'with' block.")
    # 取消注释来测试异常处理:
    # raise ValueError("Test exception inside with block")

print("After 'with' block.")
```

注意：`__exit__` 方法的三个参数（`exc_type`, `exc_value`, `traceback`）用于处理 `with` 块内部可能发生的异常。即使不显式处理异常，这三个参数也不能省略。如果 `with` 块正常结束，这三个参数将是 `None`。

Python 的 `contextlib` 模块中的 `contextmanager` 装饰器提供了一种更简洁的方式来创建上下文管理器：

```python
from contextlib import contextmanager
import time
from time import perf_counter

@contextmanager
def timed_block(second):
    """A context manager to measure the execution time of a block."""
    print(f"Starting timer with {second} second...")
    start_time = perf_counter()
    try:
        yield second # yield 语句是 __enter__ 和 __exit__ 的分界点
                     # yield 后面可以带一个值，赋给 'as' 子句的变量
    finally:
        end_time = perf_counter()
        print(f"Finished. Elapsed time: {end_time - start_time:.4f} seconds")

with timed_block(1) as s:
    print(f"  Sleeping for {s} second...")
    time.sleep(s)
    print("  Slept.")
```

使用 `@contextmanager` 装饰的生成器函数，`yield` 语句之前的部分相当于 `__enter__` 方法的逻辑，`yield` 语句本身返回的值会赋给 `as` 子句中的变量，`yield` 之后的部分（通常在 `finally` 块中以确保执行）则相当于 `__exit__` 方法的逻辑，无论 `with` 块内部是否发生异常，`finally` 块中的代码都会执行。

## 魔术方法（Magic Method）

魔术方法是 Python 中一些具有特殊名称（前后双下划线）的方法，它们允许自定义类响应特定的内置操作或语法结构。

### 比较（Comparison）

通过实现比较相关的魔术方法，可以自定义对象的比较行为。

```python
import functools

@functools.total_ordering # 自动生成缺失的比较方法
class Student():
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __eq__(self, other):  # ==
        if not isinstance(other, Student):
            return NotImplemented # 表示无法比较此类型
        return self.name == other.name and self.age == other.age

    # 只需定义 __eq__ 和一个其他的比较方法（如 __lt__），
    # @functools.total_ordering 就能自动生成剩下的（__le__, __gt__, __ge__, __ne__）
    def __lt__(self, other):  # <
        if not isinstance(other, Student):
            return NotImplemented
        if self.age < other.age:
            return True
        elif self.age == other.age:
            return self.name < other.name
        return False

a = Student('Alice', 20)
b = Student('Bob', 21)
c = Student('Charlie', 21)
d = Student('Alice', 20)

print(f"a < b: {a < b}")  # True (Alice 20 < Bob 21)
print(f"b > c: {b > c}")  # False (Bob 21 not > Charlie 21 by name; Bob < Charlie)
print(f"c > b: {c > b}")  # True (Charlie 21 > Bob 21 by name)
print(f"a == d: {a == d}") # True
print(f"a != b: {a != b}") # True (auto-generated by total_ordering)
```

注意：为了使 `@functools.total_ordering` 生效，必须至少定义 `__eq__` 和以下之一：`__lt__`，`__le__`，`__gt__`，`__ge__`。

### 表示（Representation）

- 若希望自定义类的实例能够作为 `set` 的元素或 `dict` 的键，必须同时正确实现 `__hash__` 和 `__eq__` 这两个魔术方法。如果实现了 `__eq__` 但未实现 `__hash__`，对象默认是不可哈希的（除非继承自可哈希的不可变基类）。
- `__str__` 方法返回对象的「非正式」或「用户友好」的字符串表示，通常由 `print()` 函数或 `str()` 内置函数调用。其目标是可读性。若 `__str__` 未定义，`print()` 和 `str()` 会回退使用 `__repr__`。
- `__repr__` 方法返回对象的「官方」字符串表示，目标是无歧义，理想情况下 `eval(repr(obj)) == obj`。容器（如列表、字典）在显示其包含的元素时，通常调用元素的 `__repr__` 方法。

```python
class StudentWithHash():
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __hash__(self):
        # 哈希值应基于不可变属性计算
        # 确保 __eq__ 为 True 的对象具有相同的哈希值
        print(f"Calculating hash for {self.name}")
        return hash((self.name, self.age)) # 使用元组确保属性顺序和不可变性

    def __eq__(self, other):
        if not isinstance(other, StudentWithHash):
            return NotImplemented
        print(f"Comparing {self.name} with {other.name} for equality")
        return self.name == other.name and self.age == other.age

    def __str__(self):
        # 用户友好的输出
        return f"Student Name: {self.name}, Age: {self.age}"

    def __repr__(self):
        # 明确的、可用于重新创建对象的表示
        return f"StudentWithHash('{self.name}', {self.age})"

s1 = StudentWithHash('Alice', 20)
s2 = StudentWithHash('Alice', 20) # Same as s1
s3 = StudentWithHash('Bob', 22)

print(f"s1 (str): {str(s1)}")
print(f"s1 (repr): {repr(s1)}")

student_set = set()
student_set.add(s1)
student_set.add(s2) # s2 与 s1 相等且哈希值相同，不会重复添加
student_set.add(s3)
print(f"Set of students: {student_set}") # 打印集合时，元素会用 __repr__

student_dict = {s1: "Grade A"}
print(f"Dict with student key: {student_dict}")
print(f"Value for s2 in dict: {student_dict.get(s2)}") # s2 与 s1 等价，可以查到值
```

## 文档字符串（Docstring）

在模块、类、函数或方法定义的起始处（紧随定义语句之后）的第一个未赋值的字符串字面量，会被自动赋值给该对象的 `__doc__` 特殊属性，用作文档字符串。

```python
'''这是一个模块级别的文档字符串，用于介绍整个脚本的功能。'''

class MyClass:
    '''这是一个类级别的文档字符串，描述这个类的作用。'''
    pass

def main_function():
    '''这是一个函数级别的文档字符串，解释这个函数做什么。
    它可以是多行的。
    '''
    print(f"Module's docstring: {__doc__[:30]}...") # 打印模块文档字符串的前30个字符
    print(f"MyClass's docstring: {MyClass.__doc__}")
    print(f"main_function's docstring: {main_function.__doc__}")


if __name__ == '__main__':
    main_function()
```

# 并发编程（Concurrent Programming）

并发编程是一种程序设计方法，并发和并行是实现手段。并行编程是并发编程的子集，并且一定通过并行实现。

- 并发（Concurrency）：指在单个 CPU 上（或单核场景下），通过时间片轮转调度等方式快速切换多个任务，使得这些任务在一段时间内看起来像是同时在执行。但在任何一个精确的时间点，CPU 仅执行一个任务。
- 并行（Parallelism）：指在多个 CPU 核心上，多个任务真正地在同一时刻同时执行。

## 多线程（Multithreading）

- 由于 CPython 解释器中全局解释器锁（Global Interpreter Lock, GIL）的存在，同一 Python 进程内的多线程并不能真正利用多核 CPU 实现计算密集型任务的并行执行。GIL 保证了任何时候只有一个线程在执行 Python 字节码。
- 因此，Python 多线程不适合 CPU 密集型任务，但对于 I/O 密集型任务（如网络请求、文件读写），多线程仍能通过并发提高效率，因为线程在等待 I/O 时可以释放 GIL，允许其他线程运行。
- 线程之间共享同一进程的内存空间，因此可以通过共享全局变量、对象属性等方式进行通信。但这同时也带来了数据竞争的风险，需要注意线程安全问题，适时使用锁（Lock）、条件变量（Condition）、信号量（Semaphore）等同步机制。

考虑使用多线程的场景：

- 程序中存在大量 I/O 密集型操作。在等待 I/O 操作完成时，线程可以释放 GIL，让其他线程执行，从而提高程序整体的响应速度和吞吐量。
- 需要进行并发执行，且任务间共享数据较为频繁或逻辑上紧密（尽管需要谨慎处理同步）。

启动多个线程并等待它们完成：

```python
import threading
import time

def worker_task(duration, task_name):
    print(f"任务 {task_name}: 开始，预计耗时 {duration} 秒。")
    time.sleep(duration)
    print(f"任务 {task_name}: 完成。")

threads_list = []
for i in range(3): # 创建3个工作线程
    thread = threading.Thread(target=worker_task, args=(i + 1, f'Task-{i+1}'))
    threads_list.append(thread)
    thread.start() # 启动线程

print("主线程：所有工作线程已启动。")

for t in threads_list:
    t.join()  # 等待该线程执行完毕

print("主线程：所有工作线程均已执行完毕。")
```

多线程访问共享资源时的同步：使用锁

```python
import threading
import time
from concurrent.futures import ThreadPoolExecutor

class Counter:
    def __init__(self):
        self.lock = threading.Lock() # 创建一个锁对象
        self.count = 0

    def increment(self):
        # 'with' 语句可以自动获取和释放锁，确保即使发生异常也能正确解锁
        with self.lock:
            current_val = self.count # 读取共享资源
            time.sleep(0.001) # 模拟一些耗时操作，增加竞争的可能性
            self.count = current_val + 1 # 修改共享资源

def increment_worker(counter_obj, iterations):
    try:
        for _ in range(iterations):
            counter_obj.increment()
    except Exception as e:
        # print(f"[线程异常] {e}")
        # traceback.print_exc()
        raise  # 可选：将异常重新抛出供主线程捕获

shared_counter = Counter()
num_iterations_per_thread = 100
num_threads = 5

# 使用 ThreadPoolExecutor 管理线程
futures = []
with ThreadPoolExecutor(max_workers=num_threads) as executor:
    for _ in range(num_threads):
        futures.append(executor.submit(increment_worker, shared_counter, num_iterations_per_thread))
    for i, f in enumerate(futures):
        try:
            f.result()  # 等待线程完成并捕获异常
        except Exception as e:
            print(f"[主线程捕获异常] 线程 {i} 发生异常: {e}")

print(f"预期计数值: {num_threads * num_iterations_per_thread}")
print(f"实际计数值（有锁保护）: {shared_counter.count}")
```

更复杂的线程同步与调度：使用条件变量。条件变量允许一个或多个线程等待某个条件成立，当条件满足时，由另一个线程通知它们。

```python
from concurrent.futures import ThreadPoolExecutor
from random import randint
from time import sleep
import threading

class BankAccount:
    def __init__(self, initial_balance=0):
        self.balance = initial_balance
        # Condition 内部会自动创建一个 Lock，也可以传入一个已有的 Lock
        self.condition = threading.Condition()

    def withdraw(self, amount):
        with self.condition: # 获取与条件变量关联的锁
            while amount > self.balance:
                print(f"{threading.current_thread().name}: 想取款 ${amount}, 但余额 ${self.balance} 不足，等待存款...")
                self.condition.wait()  # 释放内部锁并等待，直到被 notify/notify_all 唤醒并重新获取锁
            self.balance -= amount
            print(f"{threading.current_thread().name}: 取款 ${amount} 成功, 剩余余额: ${self.balance}")

    def deposit(self, amount):
        with self.condition: # 获取与条件变量关联的锁
            self.balance += amount
            print(f"{threading.current_thread().name}: 存款 ${amount} 成功, 当前余额: ${self.balance}")
            # self.condition.notify() # 唤醒一个等待的线程
            self.condition.notify_all() # 唤醒所有等待的线程

def depositor(account):
    for _ in range(5): # 模拟几次存款
        money = randint(50, 150)
        account.deposit(money)
        sleep(randint(0, 2) * 0.1) # 随机等待一段时间

def withdrawer(account):
    for _ in range(3): # 模拟几次取款
        money = randint(80, 200)
        account.withdraw(money)
        sleep(randint(1, 3) * 0.1) # 随机等待一段时间

account = BankAccount(100)
with ThreadPoolExecutor(max_workers=5, thread_name_prefix="BankThread") as pool:
    for _ in range(2): # 2个存款者
        pool.submit(depositor, account)
    for _ in range(3): # 3个取款者
        pool.submit(withdrawer, account)

print("所有银行操作任务已提交。")
# ThreadPoolExecutor 在退出 with 块时会自动调用 shutdown(wait=True)
```

## 多进程（Multiprocessing）

- 进程拥有独立的内存空间，因此数据默认是隔离的。这避免了多线程中常见的 GIL 和数据竞争问题，能够充分利用多核 CPU 进行并行计算。
- 进程间通信（Inter-Process Communication, IPC）需要通过特定机制，如管道（Pipe）、队列（Queue）、共享内存（Shared memory）、管理器（Manager）等。

考虑使用多进程的场景：

- CPU 密集型任务：可以充分利用多核 CPU 的并行计算能力，显著提高计算效率。
- 任务的输入数据可以被清晰地分块处理，并且各子任务的结果可以方便地合并，或者子任务间不需要大量通信。

```python
from multiprocessing import Pool, cpu_count
import time

def complex_computation(x):
    # print(f"Process starting for input {x}") # 频繁打印会影响性能观察
    result = x
    for _ in range(100000): # 增加计算量
        result = (result + x**2) / (1 + result + x) if (1 + result + x) != 0 else 0
    # print(f"Process finished for input {x}")
    return result

if __name__ == '__main__':
    # 在 Windows 等平台上创建子进程时，__main__ 保护是必需的，
    # 确保子进程不会重复执行父进程的模块加载和初始化代码。
    num_tasks = cpu_count() * 10
    inputs = range(num_tasks)

    print(f"将使用 {cpu_count()} 个CPU核心处理 {num_tasks} 个任务。")

    start_time = time.perf_counter()
    # Pool 会自动管理进程池
    with Pool(processes=cpu_count()) as p:
        # map 方法会阻塞直到所有结果返回，并保持结果顺序与输入一致
        results = p.map(complex_computation, inputs)
    end_time = time.perf_counter()

    # print(f"部分结果: {results[:5]}...")
    print(f"多进程计算耗时: {end_time - start_time:.4f} 秒")

    # 对比单线程执行时间（近似）
    start_time_single = time.perf_counter()
    results_single = [complex_computation(i) for i in inputs]
    end_time_single = time.perf_counter()
    print(f"单线程计算耗时（近似）: {end_time_single - start_time_single:.4f} 秒")
```

## 异步 I/O（Asynchronous I/O）

异步 I/O 通常基于单线程事件循环（Event Loop）模型，通过协程实现并发。它特别适合高并发的 I/O 密集型场景（如大量网络连接、文件操作）。异步允许在等待 I/O 操作（如网络请求发送后等待响应）完成时，程序可以切换执行其他任务，而不是阻塞整个线程。`asyncio` 是 Python 标准库中用于异步编程的核心模块。

- `async def`: 用于定义一个协程函数。调用协程函数会返回一个协程对象，但函数体内的代码此时并不会立即执行，需要被事件循环调度执行。
- `await`: 用于暂停当前协程的执行，等待 `await` 后面的可等待对象（通常是另一个协程、Future 或 Task）完成。在等待期间，事件循环可以运行其他已准备好的任务。

```python
import asyncio
import random
import time

async def make_random_value(task_id, threshold):
    print(f"任务 {task_id}: 开始执行，目标阈值: {threshold}")
    value = random.randint(0, 10)
    while value <= threshold:
        print(f"任务 {task_id}: 当前值 {value} 过低（阈值 {threshold}），等待1秒后重试...")
        await asyncio.sleep(1) # 模拟 I/O 等待，释放控制权给事件循环
        value = random.randint(0, 10)
    print(f"任务 {task_id}: ---> 完成，最终值 {value}（阈值 {threshold}）")
    return value

async def main_async_task(): # Renamed from main
    # asyncio.gather 用于并发运行多个协程
    tasks = [make_random_value(i, 7 - i) for i in range(3)] # thresholds: 7, 6, 5
    print("并发启动所有 make_random_value 任务...")
    results = await asyncio.gather(*tasks)
    return results

if __name__ == "__main__":
    start_time = time.perf_counter()
    # asyncio.run() 是运行顶级入口点协程（main_async_task）的推荐方式
    # 它会自动管理事件循环的创建和销毁
    final_results = asyncio.run(main_async_task())
    end_time = time.perf_counter()
    print(f"\n所有异步任务完成。结果: {final_results}")
    print(f"异步执行总耗时: {end_time - start_time:.4f} 秒")
```

# 密码学（Cryptography）

- AES (Advanced Encryption Standard)：对称加密算法，加密和解密使用相同的密钥。速度快，适合加密大量数据。需要安全地管理和分发密钥。
- RSA (Rivest-Shamir-Adleman)：非对称加密算法，使用一对密钥：公钥和私钥。公钥加密的数据只能用对应的私钥解密，私钥签名的数据可以用公钥验证。常用于密钥交换、数字签名。
- SHA-256：一种安全哈希算法（Secure Hash Algorithm），产生一个 256 位的哈希值，通常表示为 64 个十六进制字符。广泛应用于密码存储（通常结合盐值）、数字签名、区块链等安全敏感场景，也可用于校验文件完整性。鉴于 MD5 已被证实存在严重的安全漏洞（如易于碰撞），SHA-256 是其更安全的替代方案。
- Base64：一种将二进制数据编码为 ASCII 字符串的方法，使用 64 个可打印字符（A-Z, a-z, 0-9, +, /）。常用于在文本环境中（如 URL、HTTP 头部、电子邮件正文）传递少量二进制数据，本身不提供加密。

```python
import base64
from hashlib import sha256
from Crypto.Cipher import AES, PKCS1_OAEP
from Crypto.Random import get_random_bytes
from Crypto.PublicKey import RSA

# --- AES 示例 ---
message_aes = '这是一条需要 AES 加密的中文消息!'
# 密钥生成：实际应用中应使用更安全的密钥派生函数（KDF）或随机生成密钥
# 此处仅为演示，直接使用 SHA-256 哈希作为密钥不安全
aes_key = sha256(b'my_super_secret_password_123').digest() # 32字节（256位）密钥
iv = get_random_bytes(AES.block_size) # 生成随机的初始化向量 IV

# 加密
cipher_aes = AES.new(aes_key, AES.MODE_CFB, iv) # CFB 模式需要 IV
data_encrypted_aes = iv + cipher_aes.encrypt(message_aes.encode('utf-8')) # 将 IV 和密文一起存储/传输
print(f"AES 加密后（原始字节）：{data_encrypted_aes}")
print(f"AES 加密后（Base64 编码）：{base64.b64encode(data_encrypted_aes).decode()}")

# 解密
received_iv = data_encrypted_aes[:AES.block_size]
received_ciphertext = data_encrypted_aes[AES.block_size:]
decipher_aes = AES.new(aes_key, AES.MODE_CFB, received_iv) # 使用相同的密钥和 IV
message_decrypted_aes = decipher_aes.decrypt(received_ciphertext).decode('utf-8')
print(f"AES 解密后：{message_decrypted_aes}")
assert message_aes == message_decrypted_aes

# --- RSA 示例 ---
message_rsa = b'This is a secret message for RSA!' # RSA 通常处理字节串

# 生成密钥对（建议至少2048位）
rsa_key_pair = RSA.generate(2048)
public_key = rsa_key_pair.publickey()
private_key = rsa_key_pair

# print(f"Public Key:\n{public_key.export_key().decode()}")
# print(f"Private Key:\n{private_key.export_key().decode()}")

# 加密（使用公钥）
encryptor_rsa = PKCS1_OAEP.new(public_key)
data_encrypted_rsa = encryptor_rsa.encrypt(message_rsa)
print(f"RSA 加密后（原始字节）：{data_encrypted_rsa[:20]}...") # 只显示一部分
print(f"RSA 加密后（Base64 编码）：{base64.b64encode(data_encrypted_rsa).decode()}")

# 解密（使用私钥）
decryptor_rsa = PKCS1_OAEP.new(private_key)
message_decrypted_rsa = decryptor_rsa.decrypt(data_encrypted_rsa)
print(f"RSA 解密后：{message_decrypted_rsa.decode()}")
assert message_rsa == message_decrypted_rsa
```

# 测试（Testing）

## 单元测试（Unit Testing）

使用 [pytest](https://docs.pytest.org/en/stable/) 框架进行单元测试。pytest 会自动发现并执行当前目录及其子目录下所有符合特定命名约定的测试文件（如 `test_*.py` 或 `*_test.py`）和其中的测试函数/方法（以 `test_` 开头的函数，或 `Test` 类中以 `test_` 开头的方法）。

```python
# test_example.py（示例文件）
import pytest

# 一个简单的函数待测试
def add_numbers(x, y):
    return x + y

def to_uppercase(s):
    if not isinstance(s, str):
        raise TypeError("Input must be a string")
    return s.upper()

# --- 测试用例 ---

# 使用 assert 语句进行断言
def test_add_numbers():
    assert add_numbers(1, 2) == 3, "Test Case Failed: Basic addition"

# 测试是否抛出特定异常
def test_to_uppercase_raises_type_error_for_int():
    # pytest.raises 作为上下文管理器，检查块内代码是否抛出指定异常
    with pytest.raises(TypeError, match="Input must be a string"):
        to_uppercase(123) # 传入非字符串，应引发 TypeError

def test_to_uppercase_valid_string():
    assert to_uppercase("hello") == "HELLO", "Test Case Failed: Uppercase conversion"

# 可以在类中组织测试
class TestStringOperations:
    def test_concatenation(self):
        assert "a" + "b" == "ab"

    def test_string_length(self):
        assert len("hello") == 5
```

pytest 提供了丰富的特性，如 fixtures、参数化测试、插件等。

## 代码覆盖率测试（Code Coverage Testing）

使用 [Coverage.py](https://coverage.readthedocs.io/en/stable/) 工具进行代码覆盖率测试，它可以衡量测试用例执行了多少比例的产品代码。

- 命令行运行 `coverage run -m pytest [pytest 参数]`，执行测试并通过 coverage 收集数据，结果默认保存在当前目录下的 `.coverage` 文件中。
- `coverage report -m`：在命令行中显示覆盖率报告，包括各文件及未覆盖的行号。
- `coverage html`：生成 HTML 格式的覆盖率报告（通常在 `htmlcov/` 目录下），可在浏览器中查看，并进行交互式分析，高亮显示已覆盖和未覆盖的代码行。

# Python 包（Python Package）

一个典型的 Python 包项目结构（采用 `src` 布局）如下：

```
my_project_root/
├── src/
│   └── my_package_name/  # 包的实际代码目录（可导入的包名）
│       ├── __init__.py   # 标记此目录为一个包，可以为空或包含包级别代码
│       ├── module1.py
│       └── module2.py
├── tests/                # 测试代码目录
│   ├── __init__.py
│   ├── test_module1.py
│   └── ...
├── README.md             # 项目说明
├── LICENSE               # 项目许可证文件
├── pyproject.toml        #（现代）项目元数据和构建系统配置
└── ...                   # 其他如 .gitignore 等
```

## 打包（Packaging）

[Python Packaging User Guide](https://packaging.python.org/en/latest/)

现代 Python 项目推荐使用 `pyproject.toml` 文件作为项目构建和元数据的统一配置文件。该文件允许开发者声明项目构建所需的工具（构建后端，如 `setuptools`、`hatchling`、`flit`、`poetry-core` 等）以及项目的核心元数据（如包名、版本、作者、依赖项等）。

`pyproject.toml` 示例：

```toml
[build-system]
# 指定构建包所需的依赖，例如 setuptools 和 wheel
requires = [
    "setuptools>=61.0", # setuptools 作为构建后端的一个常见选择
    "wheel"
]
# 指定用于构建包的 Python 对象（构建后端）
build-backend = "setuptools.build_meta"

[project]
# 项目的核心元数据（遵循 PEP 621）
name = "my_package_name"
version = "0.1.0" # 通常可以从文件或 SCM 读取，或动态生成
description = "A short description of my awesome package."
readme = "README.md" # 指向包含详细描述的文件
requires-python = ">=3.10" # 项目兼容的 Python 版本

# 作者信息
authors = [
    { name = "Your Name", email = "your.email@example.com" },
]

license = { file = "LICENSE" } # 或者 { text = "MIT License" }

keywords = ["example", "packaging", "python"]

classifiers = [
    "Development Status :: 3 - Alpha",
    "Intended Audience :: Developers",
    "License :: OSI Approved :: MIT License",
    "Programming Language :: Python :: 3",
    "Programming Language :: Python :: 3.11",
    "Operating System :: OS Independent",
    "Topic :: Software Development :: Libraries :: Python Modules",
]

# 项目运行所需的依赖
dependencies = [
    "requests>=2.25.0", # 可以指定版本约束
    "numpy<2.0,>=1.20",
    'importlib-metadata; python_version<"3.10"', # 条件依赖
]

# 项目链接，例如文档、源码仓库等
[project.urls]
Homepage = "https://example.com"
Documentation = "https://readthedocs.org"
Repository = "https://github.com/me/example.git"

# 定义命令行脚本入口点
[project.scripts]
my_command = "my_package_name.module1:main_function"

# 定义可选依赖（用于 "extras"）
[project.optional-dependencies]
plotting = ["matplotlib>=3.0"]
ui = ["PyQt5"]
dev = ["pytest", "coverage", "black", "ruff"] # 开发环境依赖
```

### 版本编号

通常遵循语义化版本号（Semantic Versioning）规范，采用 `主版本号.次版本号.修订号`（如 `0.1.2`）的格式。版本号递增规则如下：

- 主版本号（Major）：当 API 发生不兼容的重大更改时递增（例如 `1.x.x` -> `2.0.0`）。
- 次版本号（Minor）：当以向后兼容的方式添加功能时递增（例如 `1.1.x` -> `1.2.0`）。
- 修订号（Patch）：当以向后兼容的方式修复错误时递增（例如 `1.1.2` -> `1.1.3`）。

## 安装

- `pip install .`：在当前项目目录下（必须包含 `pyproject.toml` 文件），以标准模式安装包。`pip` 会读取 `pyproject.toml` 来确定构建系统，然后调用构建后端来构建包并将其安装到 Python 环境的 `site-packages` 目录。
- `pip install -e .`：同样在包含 `pyproject.toml` 的项目目录下，以「可编辑」模式安装包。这意味着对项目源代码的修改会立即反映到已安装的包中（通常通过在 `site-packages` 中创建指向项目实际位置的链接实现），无需重新安装，非常适合开发阶段。

可以使用以下代码查看 Python 的模块搜索路径：

```python
import sys
print(sys.path)
```

# 常见陷阱

## 可变默认参数（Mutable Default Arguments）

Python 函数的默认参数值在函数定义（`def` 语句执行）时被计算一次，并且后续所有调用都共享这同一个对象。因此，当使用列表、字典等可变对象作为默认参数时，若在函数内部修改了该对象，这种修改会累积并影响到后续调用。

```python
def append_to_list_buggy(item, data=[]): # 'data=[]' 只在函数定义时创建一次
    data.append(item)
    return data

print(f"第一次调用（buggy）: {append_to_list_buggy(1)}") # 输出: [1]
print(f"第二次调用（buggy）: {append_to_list_buggy(2)}") # 输出: [1, 2]（不是期望的 [2]）
print(f"第三次调用（buggy）指定data: {append_to_list_buggy(3, ['a'])}") # 输出: ['a', 3]（正常）
print(f"第四次调用（buggy）依赖默认: {append_to_list_buggy(4)}") # 输出: [1, 2, 4]

# 正确的推荐做法：使用 None 作为默认值，在函数内部创建新对象
def append_to_list_correct(item, data=None):
    if data is None:
        data = [] # 每次调用时，如果 data 未提供，则创建新的列表
    data.append (item)
    return data

print(f"第一次调用（correct）: {append_to_list_correct (1)}") # 输出: [1]
print(f"第二次调用（correct）: {append_to_list_correct (2)}") # 输出: [2]
```
