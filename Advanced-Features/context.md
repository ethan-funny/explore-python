# 上下文管理器

什么是上下文？其实我们可以简单地把它理解成环境。从一篇文章中抽出一句话，让你来理解，我们会说这是断章取义。为什么？因为我们压根就没考虑到这句话的上下文是什么。编程中的上下文也与此类似，比如『进程上下文』，指的是一个进程在执行的时候，CPU 的所有寄存器中的值、进程的状态以及堆栈上的内容等，当系统需要切换到其他进程时，系统会保留当前进程的上下文，也就是运行时的环境，以便再次执行该进程。

迭代器有[迭代器协议（Iterator Protocol）](./iterator.md)，上下文管理器（Context manager）也有上下文管理协议（Context Management Protocol）。

 - **上下文管理器协议**，是指要实现对象的 `__enter__()` 和 `__exit__()` 方法。
 - **上下文管理器**也就是支持上下文管理器协议的对象，也就是实现了 `__enter__()` 和 `__exit__()` 方法。

这里先构造一个简单的上下文管理器的例子，以理解 `__enter__()` 和 `__exit__()` 方法。

```python
from math import sqrt, pow

class Point(object):
    def __init__(self, x, y):
        print 'initialize x and y'
        self.x, self.y = x, y

    def __enter__(self):
        print "Entering context"
        return self

    def __exit__(self, type, value, traceback):
        print "Exiting context"

    def get_distance(self):
        distance = sqrt(pow(self.x, 2) + pow(self.y, 2))
        return distance
```

上面的代码定义了一个 `Point` 类，并实现了 `__enter__()` 和 `__exit__()` 方法，我们还定义了 `get_distance` 方法，用于返回点到原点的距离。

通常，我们使用 `with` 语句调用上下文管理器：

```python
with Point(3, 4) as pt:
    print 'distance: ', pt.get_distance()

# output
initialize x and y   # 调用了 __init__ 方法
Entering context     # 调用了 __enter__ 方法
distance:  5.0       # 调用了 get_distance 方法
Exiting context      # 调用了 __exit__ 方法
```

上面的 `with` 语句执行过程如下：

- Point(3, 4) 生成了一个上下文管理器；
- 调用上下文管理器的 `__enter__()` 方法，并将 `__enter__()` 方法的返回值赋给 as 字句中的变量 pt;
- 执行**语句体**（指 with 语句包裹起来的代码块）内容，输出 distance；
- 不管执行过程中是否发生异常，都执行上下文管理器的 `__exit__()` 方法。`__exit__()` 方法负责执行『清理』工作，如释放资源，关闭文件等。如果执行过程没有出现异常，或者语句体中执行了语句 break/continue/return，则以 None 作为参数调用 `__exit__(None, None, None) `；如果执行过程中出现异常，则使用 `sys.exc_info` 得到的异常信息为参数调用 `__exit__(exc_type, exc_value, exc_traceback)`；
- 出现异常时，如果 `__exit__(type, value, traceback)` 返回 False 或 None，则会重新抛出异常，让 with 之外的语句逻辑来处理异常；如果返回 True，则忽略异常，不再对异常进行处理；

上面的 with 语句执行过程没有出现异常，我们再来看出现异常的情形：

```python
with Point(3, 4) as pt:
    pt.get_length()        # 访问了对象不存在的方法
    
# output
initialize x and y
Entering context
Exiting context
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-216-ab4a0e6b6b4a> in <module>()
      1 with Point(3, 4) as pt:
----> 2     pt.get_length()

AttributeError: 'Point' object has no attribute 'get_length'
```

在我们的例子中，`__exit__` 方法返回的是 None（如果没有 return 语句那么方法会返回 None）。因此，with 语句抛出了那个异常。我们对 `__exit__` 方法做一些改动，让它返回 True。

```python
from math import sqrt, pow

class Point(object):
    def __init__(self, x, y):
        print 'initialize x and y'
        self.x, self.y = x, y

    def __enter__(self):
        print "Entering context"
        return self

    def __exit__(self, type, value, traceback):
        print "Exception has been handled"
        print "Exiting context"
        return True

    def get_distance(self):
        distance = sqrt(pow(self.x, 2) + pow(self.y,2 ))
        return distance

with Point(3, 4) as pt:
    pt.get_length()      # 访问了对象不存在的方法

# output
initialize x and y
Entering context
Exception has been handled
Exiting context
```

可以看到，由于 `__exit__` 方法返回了 True，因此没有异常会被 with 语句抛出。

# 内建对象使用 with 语句

除了自定义上下文管理器，Python 中也提供了一些内置对象，可直接用于 with 语句中，比如最常见的文件操作。

传统的文件操作经常使用 `try/finally` 的方式，比如：

```python
file = open('somefile', 'r')
try:
    for line in file:
        print line
finally:
    file.close()     # 确保关闭文件
```

将上面的代码改用 with 语句：

```python
with open('somefile', 'r') as file:
    for line in file:
        print line
```

可以看到，通过使用 with，代码变得很简洁，而且即使处理过程发生异常，with 语句也会确保我们的文件被关闭。

# contextlib 模块

除了在类中定义 `__enter__` 和 `__exit__` 方法来实现上下文管理器，我们还可以通过生成器函数（也就是带有 yield 的函数）结合装饰器来实现上下文管理器，Python 中自带的 contextlib 模块就是做这个的。

contextlib 模块提供了三个对象：装饰器 contextmanager、函数 nested 和上下文管理器 closing。其中，contextmanager 是一个装饰器，用于装饰生成器函数，并返回一个上下文管理器。需要注意的是，被装饰的生成器函数只能产生一个值，否则会产生 RuntimeError 异常。

下面我们看一个简单的例子：

```python
from contextlib import contextmanager

@contextmanager
def point(x, y):
    print 'before yield'
    yield x * x + y * y
    print 'after yield'

with point(3, 4) as value:
    print 'value is: %s' % value
    
# output
before yield
value is: 25
after yield
```

可以看到，yield 产生的值赋给了 as 子句中的 value 变量。

另外，需要强调的是，虽然通过使用 contextmanager 装饰器，我们可以不必再编写 `__enter__` 和 `__exit__` 方法，但是『获取』和『清理』资源的操作仍需要我们自己编写：『获取』资源的操作定义在 yield 语句之前，『释放』资源的操作定义在 yield 语句之后。

# 小结

- 上下文管理器是支持上下文管理协议的对象，也就是实现了 `__enter__` 和 `__exit__` 方法。
- 通常，我们使用 `with` 语句调用上下文管理器。with 语句尤其适用于对资源进行访问的场景，确保执行过程中出现异常情况时也可以对资源进行回收，比如自动关闭文件等。
- `__enter__` 方法在 with 语句体执行前调用，with 语句将该方法的返回值赋给 as 字句中的变量，如果有 as 字句的话。
- `__exit__` 方法在退出`运行时上下文`时被调用，它负责执行『清理』工作，比如关闭文件，释放资源等。如果退出时没有发生异常，则 `__exit__` 的三个参数，即 type, value 和 traceback 都为 None。如果发生异常，返回 True 表示不处理异常，否则会在退出该方法后重新抛出异常以由 with 语句之外的代码逻辑进行处理。

# 参考资料

- [编程中什么是「Context(上下文)」？ - 知乎](https://www.zhihu.com/question/26387327)
- [浅谈 Python 的 with 语句](https://www.ibm.com/developerworks/cn/opensource/os-cn-pythonwith/)
- [上下文管理器 · Python进阶](https://eastlakeside.gitbooks.io/interpy-zh/content/context_managers/)


