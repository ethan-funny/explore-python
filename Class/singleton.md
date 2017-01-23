# 单例模式

**单例模式（Singleton Pattern）**是一种常用的软件设计模式，该模式的主要目的是确保**某一个类只有一个实例存在**。当你希望在整个系统中，某个类只能出现一个实例时，单例对象就能派上用场。

比如，某个服务器程序的配置信息存放在一个文件中，客户端通过一个 AppConfig 的类来读取配置文件的信息。如果在程序运行期间，有很多地方都需要使用配置文件的内容，也就是说，很多地方都需要创建 AppConfig 对象的实例，这就导致系统中存在多个 AppConfig 的实例对象，而这样会严重浪费内存资源，尤其是在配置文件内容很多的情况下。事实上，类似 AppConfig 这样的类，我们希望在程序运行期间只存在一个实例对象。

在 Python 中，我们可以用多种方法来实现单例模式：

- 使用模块
- 使用 `__new__`
- 使用装饰器（decorator）
- 使用元类（metaclass）

# 使用模块

其实，**Python 的模块就是天然的单例模式**，因为模块在第一次导入时，会生成 `.pyc` 文件，当第二次导入时，就会直接加载 `.pyc` 文件，而不会再次执行模块代码。因此，我们只需把相关的函数和数据定义在一个模块中，就可以获得一个单例对象了。如果我们真的想要一个单例类，可以考虑这样做：

```python
# mysingleton.py
class My_Singleton(object):
    def foo(self):
        pass

my_singleton = My_Singleton()
```

将上面的代码保存在文件 `mysingleton.py` 中，然后这样使用：

```
from mysingleton import my_singleton

my_singleton.foo()
```

# 使用 `__new__`

为了使类只能出现一个实例，我们可以使用 `__new__` 来控制实例的创建过程，代码如下：

```python
class Singleton(object):
    _instance = None
    def __new__(cls, *args, **kw):
        if not cls._instance:
            cls._instance = super(Singleton, cls).__new__(cls, *args, **kw)  
        return cls._instance  

class MyClass(Singleton):  
    a = 1
```

在上面的代码中，我们将类的实例和一个类变量 `_instance` 关联起来，如果 `cls._instance` 为 None 则创建实例，否则直接返回 `cls._instance`。

执行情况如下：

```
>>> one = MyClass()
>>> two = MyClass()
>>> one == two
True
>>> one is two
True
>>> id(one), id(two)
(4303862608, 4303862608)
```

# 使用装饰器

我们知道，装饰器（decorator）可以动态地修改一个类或函数的功能。这里，我们也可以使用装饰器来装饰某个类，使其只能生成一个实例，代码如下：

```
from functools import wraps

def singleton(cls):
    instances = {}
    @wraps(cls)
    def getinstance(*args, **kw):
        if cls not in instances:
            instances[cls] = cls(*args, **kw)
        return instances[cls]
    return getinstance

@singleton
class MyClass(object):
    a = 1
```

在上面，我们定义了一个装饰器 `singleton`，它返回了一个内部函数 `getinstance`，该函数会判断某个类是否在字典 `instances` 中，如果不存在，则会将 `cls` 作为 key，`cls(*args, **kw)` 作为 value 存到 `instances` 中，否则，直接返回 `instances[cls]`。

# 使用 metaclass

元类（metaclass）可以控制类的创建过程，它主要做三件事：

- 拦截类的创建
- 修改类的定义
- 返回修改后的类

使用元类实现单例模式的代码如下：

```python
class Singleton(type):
    _instances = {}
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super(Singleton, cls).__call__(*args, **kwargs)
        return cls._instances[cls]

# Python2
class MyClass(object):
    __metaclass__ = Singleton

# Python3
# class MyClass(metaclass=Singleton):
#    pass
```

# 小结

- Python 的模块是天然的单例模式，这在大部分情况下应该是够用的，当然，我们也可以使用装饰器、元类等方法

# 参考资料

- [Creating a singleton in Python - Stack Overflow](http://stackoverflow.com/questions/6760685/creating-a-singleton-in-python)
- [深入浅出单实例Singleton设计模式 | 酷 壳](http://coolshell.cn/articles/265.html)
- [design patterns - Python's use of __new__ and __init__? - Stack Overflow](http://stackoverflow.com/questions/674304/pythons-use-of-new-and-init)


