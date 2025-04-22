# AI大模型开发

## Python学习

### pip命令学习

```bash
pip install -y python-dotenv
pip list
```

### conda命令学习

### 安装anaconda

通过清华源镜像来安装[Anaconda](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive)

示例

```bash
conda env list
conda create --name chatgpt python=3.9 -y
conda activate chatgpt
conda deactivate
conda list
```

### 导入用法和注意点

**使用方式**

1. **导入整个模块**

```python
import math  # 导入标准库模块
result = math.sqrt(16)  # 通过模块名访问函数
```

2. **导入特定内容**

```python
from math import sqrt, pi  # 仅导入需要的函数或变量
print(sqrt(9))  # 直接使用
```

3. **使用别名（避免命名冲突）**

```python
import numpy as np  # 常用别名
array = np.array([1, 2, 3])

from pandas import DataFrame as DF  # 类或函数别名
```

4. **导入包中的模块**

```python
# 假设有包结构：my_package/__init__.py, my_package/module.py
import my_package.module  # 绝对导入
from my_package import module
```

5. **相对导入（在包内部使用）**

```python
# 在 my_package/submodule.py 中导入同级模块
from . import sibling_module  # 导入当前包的子模块
from ..parent_module import some_function  # 上级包
```

**注意点**

1. **循环导入（Circular Imports）**
   - **问题**：模块 A 导入模块 B，模块 B 又导入模块 A，导致初始化失败。
   - **解决**：重构代码，解耦依赖。将导入语句移到函数或方法内部（延迟导入）。使用 `import` 语句而非 `from ... import`

2. **路径问题**

- **模块搜索路径**：Python 按 `sys.path` 顺序查找模块（包含当前目录、环境变量等）。

- **自定义路径**：

  ```python
  import sys
  import os
  sys.path.append("/path/to/your/module")  # 临时添加路径
  sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__)))) # 添加上级路径
  ```

3. **命名空间污染**

   避免 `from module import *`：

   ```python
   # 不推荐！可能导致命名冲突
   from math import *
   print(sqrt(16) + sin(0))
   ```

   明确导入所需内容，或用模块名前缀。

### 项目设置环境变量

使用python-dotenv库来解析

这是最简洁的方式，`python-dotenv` 库能直接解析 `.env` 文件并加载到环境变量。

1. **安装库**：

   ```bash
   pip install python-dotenv
   ```

2. **创建 `.env` 文件**：

```
# .env 文件示例
DATABASE_HOST=localhost
DATABASE_PORT=5432
API_KEY="your_api_key_here"
```

3. **Python 代码加载环境变量**：

```python
import os
from dotenv import load_dotenv

# 加载 .env 文件中的变量到环境变量
load_dotenv()  # 默认加载当前目录下的 .env 文件

# 使用环境变量
db_host = os.getenv("DATABASE_HOST")
api_key = os.getenv("API_KEY")

print(f"Database Host: {db_host}, API Key: {api_key}")
```



### python特殊函数

在 Python 中，**特殊函数（Magic/Dunder Methods）** 是以双下划线 `__` 开头和结尾的函数，用于自定义类的行为。它们使得对象可以支持内置操作（如算术运算、迭代、上下文管理等）。以下是常见分类及示例：

---

#### 一、基础对象生命周期
##### 1. **`__init__(self, ...)`**
- **作用**：对象初始化（构造函数），**不是真正的构造**。
- **调用时机**：对象创建后自动调用。
- **示例**：
  ```python
  class User:
      def __init__(self, name):
          self.name = name
  
  user = User("Alice")
  ```

##### 2. **`__new__(cls, ...)`**
- **作用**：实际创建并返回对象实例（真正的构造方法）。
- **用途**：单例模式、继承不可变类型（如 `str`）。
- **示例**：
  ```python
  class Singleton:
      _instance = None
      def __new__(cls):
          if not cls._instance:
              cls._instance = super().__new__(cls)
          return cls._instance
  
  a = Singleton()
  b = Singleton()
  print(a is b)  # True
  ```

##### 3. **`__del__(self)`**
- **作用**：析构函数，对象销毁前调用（不可靠，慎用）。
- **注意**：Python 的垃圾回收机制不保证及时调用。

---

#### 二、字符串表示
##### 1. **`__str__(self)`**
- **作用**：返回用户友好的字符串表示（`str(obj)` 或 `print(obj)` 时调用）。
- **示例**：
  ```python
  class User:
      def __str__(self):
          return f"User: {self.name}"
  
  print(User())  # User: Alice
  ```

##### 2. **`__repr__(self)`**
- **作用**：返回官方的、无歧义的字符串表示（`repr(obj)` 或直接输入对象时调用）。
- **要求**：应尽量返回可用于重建对象的表达式。
- **示例**：
  ```python
  class Point:
      def __repr__(self):
          return f"Point(x={self.x}, y={self.y})"
  ```

---

#### 三、容器类行为
##### 1. **`__len__(self)`**
- **作用**：定义容器的长度（`len(obj)` 时调用）。
- **示例**：
  
  ```python
  class Bucket:
      def __len__(self):
          return 10
  
  print(len(Bucket()))  # 10
  ```

##### 2. **`__getitem__(self, key)`**
- **作用**：实现索引访问（`obj[key]`）。
- **示例**：
  ```python
  class MyList:
      def __getitem__(self, index):
          return index * 2
  
  lst = MyList()
  print(lst[3])  # 6
  ```

##### 3. **`__iter__(self)` 和 `__next__(self)`**
- **作用**：实现迭代器协议（`for item in obj`）。
- **示例**：
  ```python
  class CountDown:
      def __init__(self, start):
          self.current = start
      def __iter__(self):
          return self
      def __next__(self):
          if self.current <= 0:
              raise StopIteration
          num = self.current
          self.current -= 1
          return num
  
  for num in CountDown(3):
      print(num)  # 3, 2, 1
  ```

---

#### 四、运算符重载
##### 1. **算术运算符**
- `__add__(self, other)`: `obj + other`
- `__sub__(self, other)`: `obj - other`
- **示例**：
  ```python
  class Vector:
      def __add__(self, other):
          return Vector(x=self.x + other.x, y=self.y + other.y)
  ```

##### 2. **比较运算符**
- `__eq__(self, other)`: `obj == other`
- `__lt__(self, other)`: `obj < other`
- **示例**：
  ```python
  class Money:
      def __eq__(self, other):
          return self.amount == other.amount
  ```

##### 3. **其他运算符**
- `__call__(self, ...)`: 允许实例像函数一样调用。
  
  ```python
  class Adder:
      def __call__(self, a, b):
          return a + b
  
  add = Adder()
  print(add(3, 5))  # 8
  ```

---

#### 五、属性访问控制
##### 1. **`__getattr__(self, name)`**
- **作用**：当访问不存在的属性时触发。
- **示例**：
  ```python
  class LazyLoader:
      def __getattr__(self, name):
          print(f"加载属性 {name}")
          value = ...  # 动态加载逻辑
          setattr(self, name, value)
          return value
  ```

##### 2. **`__setattr__(self, name, value)`**
- **作用**：设置属性时触发（包括初始化中的赋值）。
- **注意**：需避免递归调用（用 `super().__setattr__`）。
  
  ```python
  class ValidatedUser:
      def __setattr__(self, name, value):
          if name == "age" and value < 0:
              raise ValueError("年龄不能为负")
          super().__setattr__(name, value)
  ```

##### 3. **`__getattribute__(self, name)`**
- **作用**：访问任何属性时触发（优先级高于 `__getattr__`）。
- **慎用**：容易导致无限递归。

---

#### 六、上下文管理
##### **`__enter__(self)` 和 `__exit__(self, exc_type, exc_val, exc_tb)`**
- **作用**：实现 `with` 语句的上下文管理（如文件操作）。
- **示例**：
  
  ```python
  class ManagedFile:
      def __enter__(self):
          self.file = open("file.txt", "r")
          return self.file
      def __exit__(self, exc_type, exc_val, exc_tb):
          self.file.close()
  
  with ManagedFile() as f:
      content = f.read()
  ```

---

#### 七、高级特性
##### 1. **`__slots__`**
- **作用**：显式声明类允许的属性，节省内存。
- **示例**：
  ```python
  class User:
      __slots__ = ["name", "age"]  # 禁止动态添加属性
  ```

##### 2. **`__hash__` 和 `__eq__`**
- **作用**：定义对象的哈希值和相等性（用于 `dict` 键或 `set` 元素）。
- **规则**：若 `__eq__` 被重写，`__hash__` 必须同时重写（或设为 `None`）。
  ```python
  class Key:
      def __hash__(self):
          return hash(self.id)
      def __eq__(self, other):
          return self.id == other.id
  ```

##### 3. **描述符协议（`__get__`, `__set__`, `__delete__`）**
- **作用**：实现属性描述符（如 `@property` 的底层机制）。
- **示例**：
  ```python
  class PositiveNumber:
      def __set__(self, instance, value):
          if value <= 0:
              raise ValueError("必须为正数")
          instance.__dict__[self.name] = value
  
  class Order:
      quantity = PositiveNumber()
  ```

---

#### 八、完整示例：自定义序列
```python
class CustomRange:
    def __init__(self, start, end):
        self.start = start
        self.end = end

    def __len__(self):
        return self.end - self.start + 1

    def __getitem__(self, index):
        if index < 0 or index >= len(self):
            raise IndexError("索引超出范围")
        return self.start + index

    def __contains__(self, value):
        return self.start <= value <= self.end

# 使用示例
cr = CustomRange(5, 10)
print(len(cr))      # 6
print(cr[3])        # 8
print(7 in cr)      # True
```

---

#### **关键注意点**
1. **避免滥用**：仅在需要定制类行为时使用特殊方法。
2. **性能考量**：如 `__slots__` 可优化内存，但会限制灵活性。
3. **兼容性**：重写 `__eq__` 时需同时定义 `__hash__`。
4. **递归陷阱**：在 `__setattr__` 或 `__getattribute__` 中谨慎操作属性。

掌握这些特殊函数，可以让你自定义的类更符合 Python 风格，并支持丰富的内置操作！



### abc模块（接口）

Python 没有显式的 `interface` 关键字，但可以通过 `abc` 模块（Abstract Base Classes）实现接口的抽象约束。接口定义了一组方法，要求子类必须实现。

示例代码：

```python
from abc import ABC, abstractmethod

# 定义接口（抽象基类）
class Animal(ABC):
    @abstractmethod
    def speak(self):
        pass

# 子类必须实现接口方法
class Dog(Animal):
    def speak(self):
        return "Woof!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

# 使用接口
dog = Dog()
print(dog.speak())  # 输出: Woof!

# 未实现抽象方法会报错
class Duck(Animal):
    pass

# duck = Duck()  # 报错: TypeError
```

关键点：

1. **抽象基类**：通过继承 `ABC` 定义接口。
2. **抽象方法**：用 `@abstractmethod` 装饰器标记必须实现的方法。
3. **强制实现**：子类未实现抽象方法时，实例化会抛出 `TypeError`。

python接口类是可以重写特殊函数的

### operator模块

Python 的 `operator` 模块提供了一系列与内置操作符对应的高效函数，可以替代常规的 lambda 表达式，使代码更简洁且性能更优。以下是学习 `operator` 模块的核心内容：

---

#### **1. 基础功能**
`operator` 模块的函数对应 Python 操作符，例如：
- `operator.add(a, b)` 对应 `a + b`
- `operator.mul(a, b)` 对应 `a * b`
- `operator.eq(a, b)` 对应 `a == b`

**示例**：
```python
import operator

print(operator.add(3, 5))    # 输出 8
print(operator.eq(3, 5))     # 输出 False
```

---

#### **2. 常用函数分类**
**(1) 算术运算符**

- `add`, `sub`, `mul`, `truediv`（除法）, `floordiv`（整除）
- `mod`, `pow`, `neg`（取负数）

**示例**：
```python
operator.sub(10, 3)  # 7 (即 10 - 3)
operator.pow(2, 3)   # 8 (即 2**3)
```

**(2) 比较运算符**

- `lt`（<）, `le`（<=）, `eq`（==）
- `ne`（!=）, `ge`（>=）, `gt`（>）

**示例**：
```python
operator.lt(3, 5)  # True
operator.ne(3, 3)  # False
```

**(3) 序列操作**

- `concat`（拼接序列）, `contains`（检查元素存在性）
- `countOf`（统计元素出现次数）, `indexOf`（查找元素索引）

**示例**：
```python
operator.concat([1, 2], [3])  # [1, 2, 3]
operator.contains([1, 2], 2)  # True
```

---

#### **3. 高级工具**
#### **(1) `itemgetter`：返回提取元素的函数**
用于获取可迭代对象的元素（如列表索引、字典键值）：
```python
from operator import itemgetter

data = [("Alice", 25), ("Bob", 30), ("Charlie", 20)]
# 按年龄排序
sorted_data = sorted(data, key=itemgetter(1))  
# 输出 [('Charlie', 20), ('Alice', 25), ('Bob', 30)]

get_name = itemgetter(0)
print(get_name(data[0]))  # 输出 'Alice'
```

#### **(2) `attrgetter`：返回提取对象属性的函数**
用于获取对象的属性：
```python
from operator import attrgetter

class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

people = [Person("Alice", 25), Person("Bob", 30)]
# 按年龄排序
sorted_people = sorted(people, key=attrgetter('age'))  
get_age = attrgetter('age')
print(get_age(people[0]))  # 25
```

#### **(3) `methodcaller`：调用方法**
动态调用对象的方法：
```python
from operator import methodcaller

words = ["apple", "Banana", "cherry"]
upper_words = list(map(methodcaller('upper'), words))
# 输出 ['APPLE', 'BANANA', 'CHERRY']
```

---

#### **4. 实际应用场景**
- **替代 lambda**：简化代码，例如 `sorted(data, key=lambda x: x[1])` → `sorted(data, key=itemgetter(1))`
- **函数式编程**：与 `map`、`filter`、`reduce` 结合使用：
  ```python
  from functools import reduce
  product = reduce(operator.mul, [1, 2, 3, 4])  # 计算 1*2*3*4=24
  ```
- **高性能计算**：`operator` 函数由 C 实现，比 lambda 更快。

---

#### **5. 注意事项**
- **参数顺序**：例如 `operator.sub(a, b)` 对应 `a - b`，而非 `b - a`。
- **与内置函数对比**：`operator` 函数更适用于需要传递函数参数的场景（如 `sorted` 的 `key`）。

---

通过掌握 `operator` 模块，可以写出更简洁、高效且易读的 Python 代码。建议结合具体需求选择合适函数，避免冗余的 lambda 定义。



### typing与typing_extensions

学习Python的`typing`和`typing_extensions`模块可以显著提升代码的可读性和健壮性。以下是关键知识点和示例的总结：

---

#### **1. `typing` 模块**
Python标准库中的类型提示支持，适用于Python 3.5+。

**基础类型注解**

- **变量、参数、返回值注解**：
  ```python
  def greet(name: str) -> str:
      return f"Hello, {name}"
  ```

- **容器类型**（Python 3.9+可直接用`list[int]`，旧版本需从`typing`导入）：
  ```python
  from typing import List, Dict
  
  def get_data() -> List[int]:
      return [1, 2, 3]
  
  def process_dict() -> Dict[str, int]:
      return {"age": 30}
  ```

**联合类型与可选类型**

- **`Union`**（Python 3.10+可用`|`替代）：
  ```python
  from typing import Union
  
  def square(num: Union[int, float]) -> Union[int, float]:
      return num ** 2
  ```

- **`Optional`**（等价于`Union[T, None]`）：
  ```python
  from typing import Optional
  
  def find_user(id: int) -> Optional[str]:
      return "Alice" if id == 1 else None
  ```

**泛型与类型变量**

- **`TypeVar` 定义泛型**：
  ```python
  from typing import TypeVar, Sequence
  
  T = TypeVar('T')
  
  def first_element(seq: Sequence[T]) -> T:
      return seq[0]
  ```

**回调函数类型**

- **`Callable`**：
  ```python
  from typing import Callable
  
  def apply(func: Callable[[int], str], value: int) -> str:
      return func(value)
  ```

**高级类型**

- **`Literal`**（指定具体值）：
  
  ```python
  from typing import Literal
  
  def set_direction(direction: Literal["left", "right"]) -> None:
      pass
  ```
  
- **`TypedDict`**（定义字典结构）：
  ```python
  from typing import TypedDict
  
  class User(TypedDict):
      name: str
      age: int
  
  user: User = {"name": "Alice", "age": 30}
  ```

- **`Protocol`**（鸭子类型）：
  ```python
  from typing import Protocol
  
  class Closable(Protocol):
      def close(self) -> None: ...
  
  def close_resource(resource: Closable) -> None:
      resource.close()
  ```

---

#### **2. `typing_extensions` 模块**
为旧版Python提供新类型或实验性功能。

**向后兼容的新特性**

- **`TypeAlias`**（明确类型别名）：
  ```python
  from typing_extensions import TypeAlias
  
  IntList: TypeAlias = list[int]
  ```

- **`ParamSpec`**（装饰器保留参数类型）：
  ```python
  from typing_extensions import ParamSpec, Callable, TypeVar
  
  P = ParamSpec("P")
  R = TypeVar("R")
  
  def decorator(func: Callable[P, R]) -> Callable[P, R]:
      def wrapper(*args: P.args, **kwargs: P.kwargs) -> R:
          return func(*args, **kwargs)
      return wrapper
  ```

- **`TypeGuard`**（类型谓词）：
  ```python
  from typing_extensions import TypeGuard
  
  def is_str_list(val: list[object]) -> TypeGuard[list[str]]:
      return all(isinstance(x, str) for x in val)
  ```

- **`Self`**（返回自身类型）：
  ```python
  from typing_extensions import Self
  
  class MyClass:
      def method(self) -> Self:
          return self
  ```

---

#### **3. 实践建议**
- **版本适配**：使用`typing_extensions`在旧版本中访问新特性。
- **类型检查工具**：配合`mypy`或`pyright`检查类型错误。
- **避免`Any`**：尽量使用具体类型以发挥类型检查的优势。
- **学习资源**：
  - [Python `typing` 官方文档](https://docs.python.org/3/library/typing.html)
  - [`typing_extensions` PyPI页面](https://pypi.org/project/typing-extensions/)
  - [Mypy类型检查器](https://mypy-lang.org/)

---

通过掌握这些类型提示技巧，你的代码将更易于维护和协作，同时减少运行时错误。

### Annotated类型

`Annotated` 是 Python 类型提示中的一个特殊类型，**允许为类型附加额外的元数据**（metadata），这些元数据不会影响类型检查，但可以被静态分析工具、框架或运行时库（如 Pydantic、FastAPI 等）用来实现更复杂的逻辑（如数据验证、文档生成等）。

---

#### **核心概念**
- **语法**：`Annotated[类型, 元数据1, 元数据2, ...]`
- **用途**：将类型与额外信息绑定（如单位、约束条件、文档描述等）。
- **特性**：
  - 元数据对类型检查器（如 `mypy`）是透明的，仅用于工具或框架。
  - 元数据可以是任意对象（字符串、类实例、字典等）。

---

#### **使用场景示例**

##### **1. 添加文档或描述信息**
```python
from typing import Annotated

# 定义一个带有单位描述的浮点数类型
Temperature = Annotated[float, "单位：摄氏度"]

def get_temperature() -> Temperature:
    return 25.5
```
- 静态类型检查器会将其视为 `float`，但框架可以提取元数据生成文档。

---

##### **2. 数据验证约束（如 Pydantic/FastAPI）**
```python
from typing import Annotated
from pydantic import BaseModel, Field

# 使用 Annotated 定义字段的验证规则
Age = Annotated[int, Field(ge=0, le=150)]

class User(BaseModel):
    name: str
    age: Age

# 自动验证 age 是否在 0~150 之间
user = User(name="Alice", age=30)  # 合法
user = User(name="Bob", age=200)   # 抛出 ValidationError
```

---

##### **3. 自定义元数据（供工具解析）**
```python
from typing import Annotated

# 自定义元数据类
class ColumnInfo:
    def __init__(self, name: str, is_primary_key: bool):
        self.name = name
        self.is_primary_key = is_primary_key

# 定义数据库模型字段的元数据
UserID = Annotated[int, ColumnInfo("user_id", is_primary_key=True)]
UserName = Annotated[str, ColumnInfo("user_name", is_primary_key=False)]

def save_to_db(user_id: UserID, name: UserName) -> None:
    # 框架可通过元数据生成 SQL 语句
    pass
```

---

##### **4. 联合元数据与类型别名**
```python
from typing import Annotated, Union

# 允许整数或字符串类型，并附加格式描述
IDType = Annotated[
    Union[int, str], 
    {"format": "可以是整数或 UUID 字符串"}
]

def get_by_id(id: IDType) -> dict:
    # 框架可能根据元数据自动处理不同格式的 ID
    return {"id": id}
```

---

#### **技术细节**
1. **Python 版本兼容性**：
   
   - `Annotated` 在 **Python 3.9+** 的 `typing` 模块中内置。
   - 旧版本（Python 3.8-）需从 `typing_extensions` 导入：
     ```python
     from typing_extensions import Annotated
     ```
   
2. **元数据访问**：
   - 需要依赖工具或框架主动解析元数据。例如：
     ```python
     from typing import get_type_hints, Annotated
     
     def example(param: Annotated[int, "示例元数据"]) -> None:
         pass
     
     # 获取参数的类型提示
     hints = get_type_hints(example, include_extras=True)
     print(hints["param"])  # 输出: typing.Annotated[int, '示例元数据']
     ```

---

#### **与其他工具结合**
- **Pydantic/FastAPI**：通过 `Field` 或自定义元数据实现字段验证和文档生成。
- **SQLAlchemy**：可结合元数据定义数据库模型字段的约束。
- **自定义工具**：开发静态分析工具时，解析元数据实现特定逻辑。

---

#### **总结**
- `Annotated` 的核心价值是**将类型与业务逻辑的元数据解耦**，同时保持类型提示的简洁性。
- 在框架或工具链中使用时，能显著提升代码的可维护性和扩展性（如自动生成文档、验证数据）。
- 普通业务代码中可能较少直接使用，但在库或框架开发中非常有用。

## LangChain指南

https://www.cnblogs.com/fanzhidongyzby/p/18075179/langchain

### 安装LangChain

```bash
pip install langchain langchain-openai
```

### Hello World

langchain的hello world

```python
from langchain_openai import ChatOpenAI
import sys
import os
sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from config import BAILIAN_MODEL, BAILIAN_API_KEY, BAILIAN_BASE_URL

llm = ChatOpenAI(
        model=BAILIAN_MODEL,
        api_key=BAILIAN_API_KEY,
        base_url=BAILIAN_BASE_URL
    )
response = llm.invoke('很高兴认识你')
print(response.text())
```



### Prompt

提示词，就是输入格式化

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
import sys
import os
sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from config import BAILIAN_MODEL, BAILIAN_API_KEY, BAILIAN_BASE_URL

llm = ChatOpenAI(
        model=BAILIAN_MODEL,
        api_key=BAILIAN_API_KEY,
        base_url=BAILIAN_BASE_URL
    )

# 创建prompt
prompt = ChatPromptTemplate.from_template("{question}")
# 创建输出解释器
output_parser = StrOutputParser()

# 调用llm
promptValue = prompt.invoke({'question': '很高兴认识你'})
print("\n问题\n", promptValue.to_string())
response = llm.invoke(promptValue)
answer = output_parser.invoke(response)
print("\n回答\n", answer)
```



### OutputParser

输出格式化

### Chain

chain的类型是一个RunnableSerializable

#### 语法

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
import sys
import os
sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from config import BAILIAN_API_KEY,BAILIAN_BASE_URL,BAILIAN_MODEL

# 创建LLM
llm = ChatOpenAI(base_url=BAILIAN_BASE_URL,api_key=BAILIAN_API_KEY,model=BAILIAN_MODEL)
# 创建prompt
prompt = ChatPromptTemplate.from_template('{question}')
# 创建output_parser
output_parser = StrOutputParser()

# 创建chain
chain = prompt | llm | output_parser # __or__特殊函数实现的
# 执行链
answer = chain.invoke({'question': '很高兴认识你'})

print(answer)

```

#### RunnablePassthrough

`RunnablePassthrough` 主要用在链中传递数据。一般在链中的第一个位置，用来处理用户的输入，处在中间的位置就是接收上一个的输出。

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough
import sys
import os
sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from config import BAILIAN_API_KEY,BAILIAN_BASE_URL,BAILIAN_MODEL

# 创建LLM
llm = ChatOpenAI(base_url=BAILIAN_BASE_URL,api_key=BAILIAN_API_KEY,model=BAILIAN_MODEL)
# 创建prompt
prompt = ChatPromptTemplate.from_template('{question}')
# 创建output_parser
output_parser = StrOutputParser()

# 创建chain
chain = {"question": RunnablePassthrough()} | prompt | llm | output_parser # __or__特殊函数实现的
# 执行链
answer = chain.invoke('很高兴认识你')

print(answer)

```

#### RunnablePassthrough.assgin

RunnablePassthrough.assign 的作用是在保留原始输入数据的基础上，动态添加或修改字段，生成一个包含新旧字段的字典

```python
from langchain.schema.runnable import RunnablePassthrough
from langchain.chains import classify_chain, summary_chain

chain = (
    RunnablePassthrough.assign(
        topic=classify_chain,  # 假设 classify_chain 返回 "science"
        length=lambda x: len(x["question"])  # 计算长度：9
    )
    | summary_chain
)

{
    "question": "如何学习量子计算？",  # 原始字段保留
    "topic": "science",            # 新增字段
    "length": 9                    # 新增字段
}
```

#### RunnableParallel

将多个链并发执行

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough, RunnableParallel
import sys
import os
sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from config import BAILIAN_API_KEY,BAILIAN_BASE_URL,BAILIAN_MODEL
"""
并行执行组件
"""

# 创建LLM
llm = ChatOpenAI(base_url=BAILIAN_BASE_URL,api_key=BAILIAN_API_KEY,model=BAILIAN_MODEL)
# 创建两个prompt
template1 = "列出{city}这座城市的主要旅游景点，仅列出名称，最多5个"
template2 = "列出{city}这座城市的历史名人名字，仅列出名字，最多5个"
prompt1 = ChatPromptTemplate.from_template(template1)
prompt2 = ChatPromptTemplate.from_template(template2)
# 创建output_parser
output_parser = StrOutputParser()
# 创建两个链
chain1 = {'city': RunnablePassthrough()} | prompt1 | llm | output_parser
chain2 = {'city': RunnablePassthrough()} | prompt2 | llm | output_parser
# 并发执行
parallel_chain = RunnableParallel(chain1=chain1,chain2=chain2)
parallel_chain.get_graph().print_ascii()
response = parallel_chain.invoke('南京')
print(response['chain1'], '\n',response['chain2'])

```

#### RunnableBranch

`RunnableBranch`主要用于多分支子链的场景，为链的调用提供了路由功能，这个有点类似于LangChain的`路由链`。我们可以创建多个子链，然后根据条件选择执行某一个子链。

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate, SystemMessagePromptTemplate, HumanMessagePromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnableBranch, RunnableLambda
import sys
import os
sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from config import BAILIAN_API_KEY,BAILIAN_BASE_URL,BAILIAN_MODEL

"""
链选择一个分支执行
"""

# 创建LLM
llm = ChatOpenAI(base_url=BAILIAN_BASE_URL,api_key=BAILIAN_API_KEY,model=BAILIAN_MODEL)


# 创建三个prompt
math_prompt = ChatPromptTemplate.from_messages([
    SystemMessagePromptTemplate.from_template('你是一个数学老师，擅长回答数学问题。'),
    HumanMessagePromptTemplate.from_template('具体问题如下：\n{question}')
])
english_prompt = ChatPromptTemplate.from_messages([
    SystemMessagePromptTemplate.from_template("你是一个英语老师，擅长回答英语问题"),
    HumanMessagePromptTemplate.from_template('具体问题如下：\n{question}')
])
other_prompt = ChatPromptTemplate.from_messages([
    SystemMessagePromptTemplate.from_template("你是一位家长，为了维护在孩子心中伟大形象，你什么问题都会"),
    HumanMessagePromptTemplate.from_template('具体问题如下：\n{question}')
])
category_prompt = ChatPromptTemplate.from_template('这个问题：\n{question}\n属于数学、英语还是其他，只需要输出数学、英语、其他这三个中的一个')
# 创建output_parser
output_parser = StrOutputParser()
# 创建链：分类链、数学链、英语链、家长链
category_chain = category_prompt | llm | output_parser
math_chain = math_prompt | llm | output_parser
english_chain = english_prompt | llm | output_parser
other_chain = other_prompt | llm | output_parser
# 选择哪个链回答问题
answer_chain = RunnableBranch(
        (lambda x: '数学' in x['category'], math_chain), 
        (lambda x: '英语' in x['category'], english_chain), 
        other_chain, 
    )

def print_info(info):
    print(info)
    return info

full_chain = {'category': category_chain, 'question': lambda x: x['question']} | RunnableLambda(print_info) | answer_chain
full_chain.get_graph().print_ascii()
response = full_chain.invoke({'question': '1+1等于几?'})
print(response)


```



#### RunnableLambda

它可以将Python 函数转换为 `Runnable`对象。这种转换使得任何函数都可以被看作 LCEL 链的一部分，我们把自己需要的功能通过自定义函数 + `RunnableLambda`的方式包装一下，集成到 LCEL 链中，这样算是***可以跟任何外部系统打通***了。

在RunnableBranch章节已经演示过



#### DAG（有向无环图）

Chain也可以分叉、合并，组合出更复杂的DAG计算图结构

通过调用`chain.get_graph().print_ascii()`可以查看Chain的计算图结构。

```bash
pip install grandalf
```

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough
from langchain_openai import ChatOpenAI
from operator import itemgetter
import sys
import os
sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from config import BAILIAN_API_KEY,BAILIAN_BASE_URL,BAILIAN_MODEL

# 创建LLM
llm = ChatOpenAI(base_url=BAILIAN_BASE_URL,api_key=BAILIAN_API_KEY,model=BAILIAN_MODEL)
# 创建output_parser
output_parser = StrOutputParser()
# 创建prompt
topic_prompt = ChatPromptTemplate.from_template('生成一种{input}的名称')
good_prompt = ChatPromptTemplate.from_template('列举{topic}的好处')
bad_prompt = ChatPromptTemplate.from_template('列举{topic}的坏处')
summary_prompt = ChatPromptTemplate.from_messages(
        [('ai', '{topic}'), ('human', '好处\n{good}\n\n坏处\n{bad}'), ('system', '生成最终结论')]
    )
# 创建组合chain
topic_chain = topic_prompt | llm | output_parser | {'topic': RunnablePassthrough()}
good_chain = good_prompt | llm | output_parser | {'good': RunnablePassthrough()}
bad_chain = bad_prompt | llm | output_parser | {'bad': RunnablePassthrough()}
summary_chain = summary_prompt | llm | output_parser

chain = topic_chain | {'good': good_chain, 'bad': bad_chain, 'topic': itemgetter('topic')} | summary_chain
chain.get_graph().print_ascii()
# 调用chain
answer = chain.invoke({'input': '常见水果'})
print(answer)

```



#### 深入LangChain表达语言

**概述**：LangChain的核心是Chain，即对多个组件的组合和一系列调用，LCEL是LangChain的表达式语言，是一种高效简介的调用一系列组件的方式。

**使用方式**：LCEL的使用方式跟Linux中的管道类似，例如：`chain = prompt_tpl | model | output_parser`

**实现原理**：

1. Runable接口定义了`invoke`方法，并重写了`__or__`特殊函数
2. 所有的组件都实现了Runnable接口，例如LLM、prompts、output_parser

所以两个组件进行或运算的时候，就是前一个组件invoke的输出作为后一个组件invoke的输入

**隐式适配**：LangChain会自动将字符串、dict等原始类型包装为`RunnableLambda`，使类似`"Hello" | llm`的写法合法。

1. 字符串 -> RunnableLambda
2. 字典 -> RunnableParallel
3. 列表 -> RunnableSequence（就是一个Chain）
4. 函数 -> RunnableLambda





### Memory

已经过时了，以下是示例

```python
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder, HumanMessagePromptTemplate
from langchain_openai import ChatOpenAI
from langchain.memory import ConversationBufferMemory
from langchain.chains import LLMChain
import sys
import os
sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
from config import BAILIAN_API_KEY,BAILIAN_BASE_URL,BAILIAN_MODEL

# 创建llm
llm = ChatOpenAI(base_url=BAILIAN_BASE_URL,api_key=BAILIAN_API_KEY,model=BAILIAN_MODEL)
# 创建memory
memory = ConversationBufferMemory(memory_key='chat_history',return_messages=True)
memory.chat_memory.add_user_message('你的名字叫什么?')
memory.chat_memory.add_ai_message('我的名字叫小爱')
print("历史消息: ", memory.load_memory_variables({}))

# 创建prompt
prompt = ChatPromptTemplate.from_messages([
        MessagesPlaceholder(variable_name='chat_history'),
        HumanMessagePromptTemplate.from_template('用户输入问题: \n"{question}')
    ])
# 创建LLMChain
llm_chain = LLMChain(llm=llm, memory=memory, prompt=prompt)
# 调用llm_chain
print(llm_chain.predict(question='你刚刚说你叫啥？'))
```



### 文档加载

以下是部分文档类型加载示例

```python
# pip install langchain-communty


# 加载pdf文档
def loadPDF():
    # pip install pypdf
    from langchain_community.document_loaders import PyPDFLoader
    loader = PyPDFLoader(file_path='example/static/金院字[2022]68（关于修订印发《金陵科技学院本科生第二课堂成绩认证办法》的通知）.pdf')
    pages = loader.load()
    # 先答应第一个页的信息和内容
    page = pages[0]
    print(page.metadata, '\n',page.page_content)

# 加载biliBili视频
def loadBilibili():
    # pip install bilibili-api-python
    from langchain_community.document_loaders import BiliBiliLoader
    # cookie认证信息必须有，否则只能总结视频metadata
    loader = BiliBiliLoader(
        ['https://www.bilibili.com/video/BV1CiQwYmE81'],
        sessdata='dd4eea85%2C1743052289%2C82b50%2A92CjDYVZjuu9BrDHBgUYaN_VLX5BB7x9hO_gcWckg-FF3-mpvsBAStVfdvurhiluBBe3cSVllIZWQzQkF4RnU5UU9NT2xWc0FNdUNJZUVyblpjTk5reV9OSlRUSVY3cjUxaTJvbDBrMVZ1c2RoazhwaU51TnRsYVNydmJ0SVV3Y1pkSHNyYzFxdldBIIEC',
        bili_jct='4986bb540bfd405e7145242a1fc6690e',
        buvid3='C2540544-0F89-A727-013C-6C895B6D018576749infoc',
    )
    pages = loader.load()
    print(len(pages))
    for page in pages:
        print(page.page_content)

# 加载网页
def loadWeb():
    # pip install beautifulsoup4
    from langchain_community.document_loaders import WebBaseLoader
    loader = WebBaseLoader(web_path='https://docs.python.org/zh-cn/3.13/installing/index.html')
    pages = loader.load()
    print("长度: ", len(pages))
    for i,page in enumerate(pages):
        print(f"打印第{i}页内容: {page.page_content}")

    # 还可以更加精确到标签或者class
    from bs4 import SoupStrainer
    loader = WebBaseLoader(
        web_path='https://docs.python.org/zh-cn/3.13/installing/index.html',
        bs_kwargs={
            "parse_only": SoupStrainer(class_="body")
        },
        bs_get_text_kwargs={"separator": " | ", "strip": True}
    )
    pages = loader.load()
    print("长度: ", len(pages))
    for i,page in enumerate(pages):
        print(f"打印第{i}页内容: {page.page_content}")
    
    

# 加载非结构化文档（pdf、图片、网页都属于非结构化）
def loadUnstructured():
    # pip install langchain-unstructured unstructured
    from langchain_unstructured import UnstructuredLoader
    loader = UnstructuredLoader(web_url='https://docs.python.org/zh-cn/3.13/installing/index.html')
    pages = loader.load()
    print("长度: ", len(pages))
    for i,page in enumerate(pages):
        print(f"打印第{i}页内容: {page.page_content}")



if __name__ == '__main__':
    # loadPDF()
    # loadBilibili()
    # loadWeb()
    loadUnstructured()
```



### 文档分割

#### 递归字符分割

这个文本分割器是推荐用于通用文本的。它通过字符列表进行参数化。它尝试按顺序在这些字符上进行分割，直到块的大小足够小。默认列表是 ['\n\n', '\n', ' ', '']。这会尽量保持所有段落（然后是句子，再然后是单词）在一起，因为这些通常被认为是语义上最相关的文本片段。

```python
from langchain_text_splitters import RecursiveCharacterTextSplitter
    text = """
Python 是一门易于学习、功能强大的编程语言。它提供了高效的高级数据结构，还能简单有效地面向对象编程。Python 优雅的语法和动态类型以及解释型语言的本质，使它成为多数平台上写脚本和快速开发应用的理想语言。

Python 官网（https://www.python.org/）上免费提供了 Python 解释器和扩展的标准库，包括源码和适用于各操作系统的机器码形式，并可自由地分发。Python 官网还包含许多免费的第三方 Python 模块、程序和工具发布包及文档链接。
"""
    text_splitter = RecursiveCharacterTextSplitter(separators=['\n\n','\n','。','，',' ',''],chunk_size=100,chunk_overlap=20,length_function=len,is_separator_regex=False)
    pages = text_splitter.create_documents(texts=[text])
    print(f'一共分割了{len(pages)}块')
    for i,page in enumerate(pages):
        print(f'打印第{i}块内容：{page.page_content}')
```

#### 按照HTML标题分割

分割出的一段文本会在metadata部分设置属于的一级标题、二级标题等

```python
from langchain_text_splitters import HTMLHeaderTextSplitter
    html = """
<!DOCTYPE html>
<html>
<body>
    <div>
        <h1>Foo</h1>
        <p>Some intro text about Foo.</p>
        <div>
            <h2>Bar main section</h2>
            <p>Some intro text about Bar.</p>
            <h3>Bar subsection 1</h3>
            <p>Some text about the first subtopic of Bar.</p>
            <h3>Bar subsection 2</h3>
            <p>Some text about the second subtopic of Bar.</p>
        </div>
        <div>
            <h2>Baz</h2>
            <p>Some text about Baz</p>
        </div>
        <br>
        <p>Some concluding text about Foo</p>
    </div>
</body>
</html>
"""
    text_splitter = HTMLHeaderTextSplitter([('h1', '一级标题'), ('h2', '二级标题'), ('h3', '三级标题')])
    pages = text_splitter.split_text(html)
    for i,page in enumerate(pages):
        print(f"分割的第{i}块: {page.metadata} {page.page_content}")
```

#### 按照MarkDown标题分割

分割出的一段文本会在metadata部分设置属于的一级标题、二级标题等

```python
from langchain_text_splitters import MarkdownHeaderTextSplitter
    markdown = """
# 第一章
## 第一小节
Python 是一门易于学习、功能强大的编程语言。
## 第二小节
Python 优雅的语法和动态类型以及解释型语言的本质，使它成为多数平台上写脚本和快速开发应用的理想语言。
# 第二章
Python 官网（https://www.python.org/）上免费提供了 Python 解释器和扩展的标准库
"""
    text_splitter = MarkdownHeaderTextSplitter([('#', '一级标题'), ('##', '二级标题')])
    pages = text_splitter.split_text(markdown) 
    for i,page in enumerate(pages):
        print(f"分割的第{i}块: {page.metadata} {page.page_content}")
```



### Embedding

接口调用向量模型都需要收费

使用本地离线模型来embedding

#### modelscope

下载modelscope平台的向量模型

```bash
pip install modelscope
pip install "modelscope[nlp]" -f https://modelscope.oss-cn-beijing.aliyuncs.com/releases/repo.html
# 下载模型
modelscope download --model iic/nlp_gte_sentence-embedding_chinese-large
```

```python
from langchain_community.embeddings import ModelScopeEmbeddings
embedding = ModelScopeEmbeddings(
    model_id='iic/nlp_gte_sentence-embedding_chinese-large',
    model_revision='v1.1.0',
)
doc_embeddings = embedding.embed_documents(['阿猫阿狗','熊大熊二'])
query_embedding = embedding.embed_query('狗熊')
print(query_embedding, doc_embeddings)
```

在apple m系列上不支持mps，仅使用cpu，非常慢



#### huggingface

```bash
pip install sentence-transformers transformers torch
# 下载模型
python -c "from sentence_transformers import SentenceTransformer; model = SentenceTransformer('sentence-transformers/all-mpnet-base-v2')"
# 下载langchain SDK
pip install langchain-huggingface
```

```python
# pip install langchain-huggingface
from langchain_huggingface import HuggingFaceEmbeddings
embedding = HuggingFaceEmbeddings(
    model_name='sentence-transformers/all-mpnet-base-v2',
    model_kwargs = {'device': get_device()}
)
doc_embeddings = embedding.embed_documents(['阿猫阿狗','熊大熊二'])
query_embedding = embedding.embed_query('狗熊')
print(query_embedding, doc_embeddings)
```

看样子，速度差不多



嵌入模型

### Rag



### 向量数据库



### Agents

## LangGraph指南

