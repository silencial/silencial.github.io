---
title: Python Tricks
date: 2021-04-26
lastmod: 2021-05-03
categories:
- Tech
tags:
- CS
- Tool
---

关于 Python 的一些知识收集

<!--more-->

---

# 语法

## 迭代器

可以被 `for` 循环的对象统称为可迭代对象。而迭代器除了可以使用 `for` 循环，还可以使用 `next()` 函数调用并返回下一个值。比如生成器同时也是一个迭代器。

```python
class FibIter():
    def __init__(self, num):
        self.num = num
        self.a, self.b = 0, 1
        self.idx = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.idx < self.num:
            self.a, self.b = self.b, self.a + self.b
            self.idx += 1
            return self.a
        raise StopIteration()

fib = FibIter(10)
print(next(fib))
for i in fib:
    print(fib)
```

## 生成器

将列表生成式中的 `[]` 更改成 `()` 便可以得到一个生成器。区别在于列表在生成时已经完成了所有计算并将结果全部储存，而生成器只有在循环时才会计算当前需要的值。

```python
lis = [m + n for m in 'ABCDEFG' for n in '12345']
gen = (m + n for m in 'ABCDEFG' for n in '12345')
```

函数中使用 `yield` 关键词也会产生生成器：

```python
def fib(num):
    n, a, b, = 0, 1, 1
    while n < num:
        yield b
        a, b = a+b, a
        n += 1

for i in fib(10):
    print(i)
```

函数生成器中单独的 `yield` 可以用来接收参数，即将生成器作为协程使用：

```python
def f():
    while True:
        x, y = yield
        print(x, y)

a = f()
a.send(None)  # first must be None
a.send([1, 2])
a.send([3, 4])
```

## 集合

```python
set1 = {1, 2, 3, 3, 3, 2}
set2 = set(range(2, 11, 2))
print(set1 & set2)  # intersection
print(set1 | set2)  # union
print(set1 - set2)  # difference
print(set1 ^ set2)  # symmetric difference
print(set2 <= set1)  # subset
print(set1 >= set2)  # upperset
```

## 装饰器

本质上，装饰器是一个返回函数的高阶函数。Python 中可以使用 `@` 来很方便地定义装饰器。下面的代码等价于 `say=my_decorator(say)`

```python
def my_decorator(func):
    def wrapper():
        print("Before the function is called.")
        func()
        print("After the function is called.")
    return wrapper

@my_decorator
def say():
    print("hhh")
```

带参数+返回值的装饰器：

```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Before the function is called.")
        val = func(*args, **kwargs)
        print("After the function is called.")
        return val
    return wrapper

@my_decorator
def add(x, y):
    return x + y
```

### 内置装饰器

1. `@property`: 使得调用类中的成员函数像调用成员变量一样
2. `@staticmethod`: 可以由类名直接调用，表明改函数不需要访问该类
3. `@classmethod`: 可以由类名直接调用，只能访问类变量。默认第一个参数是类本身。常用来提供额外的构造器

```python
class Date：
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def today(cls):
        t = time.localtime()
        return cls(t.tm_year, t.tm_mon, t.tm_mday)

    @property
    def year(self):
        return self.year

    @staticmethod
    def subtract(x, y):
        return x - y

a = Date(2021, 4, 24)
b = Date.today()  # 使用 classmethod 实现另一种初始化方法
b.year - a.year  # 而非 b.year()
Date.subtract(2, 1)  # 类名直接调用
b.subtract(2, 1)  # 实例调用
```

## 多重继承

下方的代码 `D` 类同时继承了两个父类，即同时获得了两个父类的所有功能。注意多重继承时父类的顺序。

`super` 的工作原理如下：

```python
def super(cls, inst):
    mro = inst.__class__.mro()
    return mro[mro.index(cls) + 1]
```

即从 `inst` 的 mro 列表中寻找 `cls` 的下一个类。因此下方的代码 `super(B, self).foo()` 会调用 `C` 的函数：

```python
class A(object):
    def foo(self):
        print('foo of A')

class B(A):
    pass

class C(A):
    def foo(self):
        print('foo of C')

class D(B, C):
    pass

class E(D):
    def foo(self):
        print('foo in E')
        super().foo()
        super(B, self).foo()
        super(C, self).foo()

d = D()
d.foo()
e = E()
e.foo()
```

## 上下文管理

实现了上下文管理器的对象可以使用 `with` 语句。在执行时，会依次执行对象的 `__enter__` 函数，块内的代码，对象的 `__exit__` 函数。因此可以很方便实现自己的上下文管理：

```python
class My_Context_Manager():
    def __init__(self, name):
        self.name = name

    def __enter__(self):
        print('Begin')
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print('Exit')

    def func(self):
        print('Simple function')

with My_Context_Manager('test') as c:
    c.func()
```

注意 `__exit__` 中的参数是用来做异常处理的，不能省略。

Python 的 `contextmanager` 装饰器提供了更简单的写法：

```python
from contextlib import contextmanager
import time
from time import perf_counter

@contextmanager
def count(sec):
    start = perf_counter()
    yield sec
    end = perf_counter()
    print(f'{end - start} s')

with count(1) as c:
    time.sleep(c)
```

在执行时，遇到 `yield` 语句后会执行 `with` 语句内的内容，最后执行 `yield` 之后的部分。

## 魔术方法

### 比较

```python
class Student():
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __eq__(self, other):  # ==
        return self.name == other.name and self.age == other.age
    def __ne__(self, other):  # !=
        return self.name != other.name or self.age != other.age
    def __gt__(self, other):  # >
        if self.age > other.age:
            return True
        elif self.age < other.age:
            return False
        else:
            return self.name > other.name
    def __lt__(self, other):  # <
        return not self.__gt__(other)

a = Student('a', 1)
b = Student('b', 2)
c = Student('c', 2)
print(a < b, b > c)
```

### 表示

- 如果想要将自定义类放入 `set` 或作为 `dict` 的键，则必须重写 `__hash_` 和 `__eq__` 两个魔术方法
- `__str__` 在使用 `print` 函数时被调用，`__repr__` 在 `print` 一个 list/set/dict 时被调用

```python
class Student():
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __hash__(self):
        return hash(self.name) + hash(self.age)

    def __eq__(self, other):
        return self.name == other.name and self.age == other.age

    def __str__(self):
        return f'{self.name}: {self.age}'

    def __repr__(self):
        return self.__str__()

stu = set()
stu.add(Student('a', 1))
stu.add(Student('a', 1))
stu.add(Student('b', 2))
print(stu)
```

## 文档

在文件开头或函数开头的 string 会被赋值到 `__doc__` 这一变量中

```python
'''脚本介绍'''

def main():
    '''函数介绍'''
    print(__doc__)
    print(main.__doc__)

if __name__ == '__main__':
    main()
```

# 并发编程

- 并发：一个时间段内，有多个程序在同一个 CPU 上运行，但是任意时刻只有一个程序在 CPU 上运行。
- 并行：在任意时刻，有多个程序运行在多个 CPU 上

## 多线程

- 由于 Python 全局锁 GIL 的存在，多线程无法利用多核优势，不适合计算密集型任务。
- 线程之间可以使用全局变量进行通信。

以下情况考虑多线程：

- 程序需要维护很多共享状态时（list/dict/set）
- 程序的大量时间花费在 I/O 操作上，如爬虫

```python
import threading
import time

def sleeper(n, name):
    print(f'{name} start')
    time.sleep(n)
    print(f'{name} finished')

t = threading.Thread(target=sleeper, args=(2, 'thread1'))
# t.daemon = True  # 主程序停止则线程停止，默认为 False
t.start()
print('All done')
```

```python
import threading
import time

def sleeper(n, name):
    print(f'{name} start')
    time.sleep(n)
    print(f'{name} finished')

threads = []
for i in range(5):
    t = threading.Thread(target=sleeper, args=(2, f'thread-{i}'))
    threads.append(t)
    t.start()
for t in threads:
    t.join()  # 等待该线程执行完毕

print('All done')
```

多个线程竞争一个资源：使用锁

```python
import threading
from concurrent.futures import ThreadPoolExecutor

class Counter():
    def __init__(self):
        self.lock = threading.Lock()
        self.count = 0

    def increment(self):
        with self.lock:  # 加锁
            self.count += 1

def worker(c):
    for _ in range(100000):
        c.increment()

c = Counter()
with ThreadPoolExecutor() as pool:
    for _ in range(5):
        pool.submit(worker, c)
print(c.count)
```

多个线程的调度：使用 `Condition` 来暂停/唤醒线程

```python
from concurrent.futures import ThreadPoolExecutor
from random import randint
from time import sleep
import threading

class Account():
    def __init__(self, balance=0):
        self.balance = balance
        lock = threading.Lock()
        self.condition = threading.Condition(lock)

    def withdraw(self, money):
        with self.condition:
            while money > self.balance:
                self.condition.wait()  # 等待 notify 解锁
            self.balance -= money

    def deposit(self, money):
        with self.condition:
            self.balance += money
            self.condition.notify_all()

def add_money(account):
    while True:
        money = randint(5, 10)
        account.deposit(money)
        print(threading.current_thread().name, ':', money, '====>', account.balance)
        sleep(0.5)

def sub_money(account):
    while True:
        money = randint(10, 30)
        account.withdraw(money)
        print(threading.current_thread().name, ':', money, '<====', account.balance)
        sleep(1)

account = Account()
with ThreadPoolExecutor(max_workers=10) as pool:
    for _ in range(5):
        pool.submit(add_money, account)
        pool.submit(sub_money, account)
```

## 多进程

进程间的数据是完全隔离的

以下情况考虑多进程：

- 计算密集型任务
- 程序的输入可以并行的分块，并且运算结果可合并

```python
from multiprocessing import Pool, cpu_count

def f(x):
    for _ in range(10000):
        x = 1 / (1 + x)
    return x

if __name__ == '__main__':
    with Pool(cpu_count()) as p:
        res = p.map(f, range(cpu_count()*1000))
```

## 异步 I/O

异步 I/O 是一种单线程、单进程设计，但仍能用作多任务处理（[aiohttp](https://docs.aiohttp.org/en/stable/) 异步 HTTP 网络访问）

- `async def`: 定义一个协程函数，内部可以使用 `await` 语句
- `await`: 暂停该协程的执行，同时让其它协程运行，直到 `await` 后面的语句执行完毕

```python
import asyncio
import random

async def makerandom(i, threshold):
    print(f'{i} start')
    k = random.randint(0, 10)
    while k <= threshold:
        print(f'{k} too low')
        await asyncio.sleep(i + 1)
        k = random.randint(0, 10)
    print(f'---> Finished with {k}')
    return k

async def main():
    res = await asyncio.gather(*(makerandom(i, 10 - i - 1) for i in range(3)))
    return res

if __name__ == "__main__":
    r1, r2, r3 = asyncio.run(main())
```

# 密码学

- AES：对称加密算法
- RSA：非对称加密算法
- md5：加密哈希函数。可以将字符串转换为 32 字节字符串
- base64：基于 64 个可打印字符编码二进制数据。常用在网页中传递少量二进制数据。

```python
import base64
from hashlib import md5
from Crypto.Cipher import AES, PKCS1_OAEP
from Crypto import Random
from Crypto.PublicKey import RSA

# AES
message = '中文'
# 生成密钥
key = md5(b'1qaz2wsx').digest()
iv = Random.new().read(AES.block_size)
# 加密
cipher = AES.new(key, AES.MODE_CFB, iv)
data = cipher.encrypt(message.encode())
print(data)
# 解密
cipher = AES.new(key, AES.MODE_CFB, iv)
message = cipher.decrypt(data)
print(message.decode())

# RSA
message = 'hello, world!'
# 生成密钥
key_pair = RSA.generate(1024)
pub_key = RSA.importKey(key_pair.publickey().exportKey())
pri_key = RSA.importKey(key_pair.exportKey())
# 加密
encryptor = PKCS1_OAEP.new(pub_key)
data = encryptor.encrypt(message.encode())
print(data)
# base64 编码
print(base64.b64encode(data))
# 解密
decryptor = PKCS1_OAEP.new(pri_key)
message = decryptor.decrypt(data)
print(message.decode())
```

# 测试

## 单元测试

使用 [pytest](https://docs.pytest.org/en/6.2.x/) 来做单元测试。pytest 会自动寻找当前目录及子目录下所有 `test_*.py` 文件，并运行其中所有以 `test_` 开头的函数。

```python
# 使用 assert 语句
def test_example():
    assert 1 == 2

# 如果没有抛出指定错误则 fail
with pytest.raise(ExpectedError):
    ...
```

## 覆盖率测试

使用 [coverge](https://coverage.readthedocs.io/en/coverage-5.5/) 来做代码覆盖率测试

- 命令行使用 `coverage run -m pytest [args]`，会在当前目录下生成 `.coverage` 文件

- `coverage report -m`：显示生成的报告
- `coverage html`：以网页的形式展示报告，可以互动

# Python 包

一般的 python 包的结构如下：

```text
pkg_name
├── pkg_name
│   ├── module1.py
│   └── module2.py
├── README.md
└── setup.py
```

## 打包

使用 [setuptools](https://setuptools.readthedocs.io/en/latest/) 进行打包，创建 `setup.py`：

```python
from setuptools import setup

setup(
    name='<name>',
    version='<version>',
    author='<author>',
    packages=['<dir1>', ...]
    install_requires=[
        '<package1>',
        ...
    ]，
    entry_points={
        'console_scripts': [
            '<name> = <package>.<module>:<function>',
            ...
        ]
    })
```

- `packages`：需要被打包的代码所在的文件夹
- `install_requires`：项目依赖项
- `console_scripts`：将项目中的某个函数注册为可执行命令

### 版本编号

一般使用三位数字的编号，例如 `0.1.2`。分别对应大版本、小版本和补丁。增加版本号的规则如下：

- 增加大版本编号：存在与之前版本不兼容的情况
- 增加小版本编号：增加新功能，但不影响之前的版本
- 增加补丁编号：修改 bug

## 安装

`pip install -e .`：在当前目录下寻找 `setup.py` 文件并以编辑模式安装包，即修改代码后不需要重新安装包。

简单来说，当安装包时，文件内容会被复制到一个会被 python 搜索的文件夹中。大部分情况是复制到 python 安装目录下的 `site-packages` 文件夹。可以使用如下方法查看 python 的搜索路径：

```python
import sys
sys.path
```

# 坑

## 默认参数

默认参数语句总在 `def` 定义函数时被求值，且仅执行一次。因此在将可变对象作为函数的默认参数时需要注意：

```python
def func(data=[]):
    data.append(1)
    return data

func()
func()
```

上述代码返回值分别为 `[1]`，`[1, 1]`。
