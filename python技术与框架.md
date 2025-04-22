# python技术和框架

## Peotry

```bash
poetry run python ...
```

## Python

### 函数传参

python是是对象引用传递，具体了解之后感觉还是值传递

1. 可选参数：函数定义有默认值的参数，为可选参数
   
   ```python
   def func(a, b=4, c=88)
   ```

2. 不定量位置参数：带有*号的参数其实是多个元素为一个元组
   
   ```python
   def minimum(*n):
       ....
       pass
   minimum(1,2,3...)
   ```

3. 不定量关键字参数：带有双**号的参数其实是多个元素一个字典
   
   ```python
   def func(**kwargs):
       ....
       pass
   func(a=1, b=42)
   ```
   
   

### 面向对象

- 类和对象：
  
  ```python
  class Dog:
      pass
  my_dog = Dog()
  ```

- 属性和方法
  
  ```python
  class Dog:
      # 初始化方法（构造函数）
      def __init__(self, name, age):
          self.name = name  # 实例属性
          self.age = age
  
      # 实例方法
      def bark(self):
          print(f"{self.name} says: Woof!")
  
  # 创建对象并调用方法
  my_dog = Dog("Buddy", 3)
  my_dog.bark()  # 输出: Buddy says: Woof!
  ```

- 封装与访问控制
  
  - _var：约定为保护属性（外部可访问）
  
  - __var：私有属性（外部无法直接访问）
  
  ```python
  class BankAccount:
      def __init__(self, balance):
          self.__balance = balance  # 私有属性
  
      def deposit(self, amount):
          if amount > 0:
              self.__balance += amount
  
      def get_balance(self):  # 提供访问接口
          return self.__balance
  
  account = BankAccount(1000)
  # print(account.__balance)  # 报错：AttributeError
  print(account.get_balance())  # 正确方式
  ```

- 继承和多态
  
  ```python
  class Animal:
      def __init__(self, name):
          self.name = name
  
      def speak(self):
          raise NotImplementedError("子类必须实现此方法")
  
  class Cat(Animal):
      # 重写父类方法
      def speak(self):
          print(f"{self.name} says: Meow!")
  
  class Duck(Animal):
      def speak(self):
          print(f"{self.name} says: Quack!")
  
  # 多态示例
  animals = [Cat("Kitty"), Duck("Donald")]
  for animal in animals:
      animal.speak()  # 自动调用对应子类的方法
  ```

- 特殊方法
  
  ```python
  class Vector:
      def __init__(self, x, y):
          self.x = x
          self.y = y
  
      def __add__(self, other):
          return Vector(self.x + other.x, self.y + other.y)
  
      def __str__(self):
          return f"Vector({self.x}, {self.y})"
  
  v1 = Vector(2, 3)
  v2 = Vector(4, 5)
  v3 = v1 + v2  # 自动调用 __add__
  print(v3)      # 输出: Vector(6, 8)
  ```

- 类方法和静态方法
  
  - 类方法：第一个参数为cls，操作类的属性
  
  - 静态方法：没有cls和self参数，静态方法就是一个独立的、单独的函数，只是托管到这个类中
  
  ```python
  class Student:
      school = "清华大学"  # 类属性
  
      def __init__(self, name):
          self.name = name
  
      @classmethod
      def set_school(cls, name):  # 操作类属性
          cls.school = name
  
      @staticmethod
      def is_adult(age):         # 与类和实例无关的工具方法
          return age >= 18
  
  # 调用示例
  Student.set_school("北京大学")
  print(Student.school)          # 输出: 北京大学
  print(Student.is_adult(20))    # 输出: True
  ```

- 接口
  python没有显示定义接口，可以使用抽象基类（ABC）来模拟接口能力
  接口的好处：降低代码耦合，调用方不需要知道我调用的是谁，只需要知道一系列接口列表可以实现功能
  
  ```python
  from abc import ABC, abstractmethod
  
  # 定义一个接口，抽象基类
  class Shape(ABC):
      """Animal iinterface"""
  
      @abstractmethod
      def area():
          pass
      @abstractmethod
      def perimeter(self):
          pass
  # 定义一个具体的类实现接口
  class Circle(Shape):
      def __init__(self, radius):
          self.radius = radius
      def area(self):
          return 3.14 * self.radius * self.radius
      def permitter(self):
          return 2 * 3.14 * self.radius
  # 定义另一个具体类继承接口
  class Rectangle(Shape):
      def __init__(self, width, height):
          self.width = width
          self.height = height
      def area(self):
          return self.width * self.height
      def perimeter(self):
          return 2 * (self.width + self.height)
  
  ```

### 异步

核心概念：

- Event Loop：事件循环，异步程序的调度中心

- Coroutine：协程，使用async def定义的异步函数

- Task：任务，协程的包装，由时间循环调度

- Future：异步操作的最终结果容器

```bash
import asyncio

# 简单示例
async def hello_world():
    print("Hello")
    await asyncio.sleep(1) # 非阻塞等待
    print("World")

def run():
    asyncio.run(hello_world())

# 并发执行多任务
async def task(name, delay):
    print(f"{name} started")
    await asyncio.sleep(delay=delay)
    print("World")
async def run():
    taskA = asyncio.create_task(task("A",1))
    taskB = asyncio.create_task(task("B",2))
    await taskA
    await taskB
# 收集结果
async def get_data(x):
    await asyncio.sleep(1)
    return x * 2
async def run():
    results = await asyncio.gather(
        get_data(1),
        get_data(2),
        get_data(3),
    )
    print(results)
# 超时控制
async def long_operation():
    await asyncio.sleep(10)
    return "Done"
async def run():
    try:
        result = await asyncio.wait_for(long_operation(), timeout=2)
    except asyncio.TimeoutError:
        print("Operation timed out")
    print(result)
# 生产者消费者模式
async def producer(queue, n):
    for x in range(n):
        await queue.put(x)
        await asyncio.sleep(0.1)
    await queue.put(None) # 结束信号
async def consumer(queue):
    while True:
        item = await queue.get()
        if item is None:
            break
        print(f"Processing item {item}")
async def run():
    queue = asyncio.Queue()
    await asyncio.gather(
        producer(queue,10),
        consumer(queue)
    )
# 异步上下文管理器
class AsyncConnection:
    async def __aenter__(self):
        print("Connecting...")
        await asyncio.sleep(0.5)
        return self
    async def __aexit__(self):
        print("Closing connection...")
        await asyncio.sleep(0.5)
async def run():
    async with AsyncConnection() as conn:
        print("Using connection...")
# 将同步函数抛出到线程池中运行
async def block():
    import time
    await asyncio.to_thread(time.sleep, 2)
async def run():
    await block()

if __name__=="__main__":
    asyncio.run(run())
```



### 并发



### typing



### 数据结构

## Celery

[Celery - Distributed Task Queue &#8212; Celery 5.5.1 documentation](https://docs.celeryq.dev/en/stable/)

[Celery中文文档](http://celery.xgqyq.com/Celery%E8%B5%B7%E6%AD%A5.html)





## FastAPI

## Redis

## Nginx

## Postgresql

## MongoDB

## Elasticsearch

## Docker
