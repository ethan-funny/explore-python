# 定制类和魔法方法

在 Python 中，我们可以经常看到以双下划线 `__` 包裹起来的方法，比如最常见的 `__init__`，这些方法被称为**魔法方法（magic method）或特殊方法（special method）**。简单地说，这些方法可以给 Python 的类提供特殊功能，方便我们定制一个类，比如 `__init__` 方法可以对实例属性进行初始化。

完整的特殊方法列表可在[这里](https://docs.python.org/2/reference/datamodel.html#special-method-names)查看，本文介绍部分常用的特殊方法：

- `__new__`
- `__str__` , `__repr__`
- `__iter__`
- `__getitem__` , `__setitem__` , `__delitem__`
- `__getattr__` , `__setattr__` , `__delattr__`
- `__call__`

# new

在 Python 中，当我们创建一个类的实例时，类会先调用 `__new__(cls[, ...])` 来创建实例，然后 `__init__` 方法再对该实例（self）进行初始化。

关于 `__new__` 和 `__init__` 有几点需要注意：

- `__new__` 是在 `__init__` 之前被调用的；
- `__new__` 是类方法，`__init__` 是实例方法；
- 重载 `__new__` 方法，需要返回类的实例；

一般情况下，我们不需要重载 `__new__` 方法。但在某些情况下，我们想**控制实例的创建过程**，这时可以通过重载 `__new_` 方法来实现。

让我们看一个例子：

```python
class A(object):
    _dict = dict()

    def __new__(cls):
        if 'key' in A._dict:
            print "EXISTS"
            return A._dict['key']
        else:
            print "NEW"
            return object.__new__(cls)

    def __init__(self):
        print "INIT"
        A._dict['key'] = self
```

在上面，我们定义了一个类 `A`，并重载了 `__new__` 方法：当 `key` 在 `A._dict` 中时，直接返回 `A._dict['key']`，否则创建实例。

执行情况：

```
>>> a1 = A()
NEW
INIT
>>> a2 = A()
EXISTS
INIT
>>> a3 = A()
EXISTS
INIT
```

# str & repr

先看一个简单的例子：

```python
class Foo(object):
    def __init__(self, name):
        self.name = name

>>> print Foo('ethan')
<__main__.Foo object at 0x10c37aa50>
```

在上面，我们使用 print 打印一个实例对象，但如果我们想打印更多信息呢，比如把 name 也打印出来，这时，我们可以在类中加入 `__str__` 方法，如下：

```python
class Foo(object):
    def __init__(self, name):
        self.name = name
    def __str__(self):
        return 'Foo object (name: %s)' % self.name

>>> print Foo('ethan')      # 使用 print
Foo object (name: ethan)
>>>
>>> str(Foo('ethan'))       # 使用 str
'Foo object (name: ethan)'
>>>
>>> Foo('ethan')             # 直接显示
<__main__.Foo at 0x10c37a490>
```

可以看到，使用 print 和 str 输出的是 `__str__` 方法返回的内容，但如果直接显示则不是，那能不能修改它的输出呢？当然可以，我们只需在类中加入 `__repr__` 方法，比如：

```python
class Foo(object):
    def __init__(self, name):
        self.name = name
    def __str__(self):
        return 'Foo object (name: %s)' % self.name
    def __repr__(self):
        return 'Foo object (name: %s)' % self.name

>>> Foo('ethan')
'Foo object (name: ethan)'
```

可以看到，现在直接使用 `Foo('ethan')` 也可以显示我们想要的结果了，然而，我们发现上面的代码中，`__str__` 和 `__repr__` 方法的代码是一样的，能不能精简一点呢，当然可以，如下：

```python
class Foo(object):
    def __init__(self, name):
        self.name = name
    def __str__(self):
        return 'Foo object (name: %s)' % self.name
    __repr__ = __str__
```

# iter

在某些情况下，我们希望实例对象可被用于 `for...in` 循环，这时我们需要在类中定义 `__iter__` 和 `next`（在 Python3 中是 `__next__`）方法，其中，`__iter__` 返回一个迭代对象，`next` 返回容器的下一个元素，在没有后续元素时抛出 `StopIteration` 异常。

看一个斐波那契数列的例子：

```python
class Fib(object):
    def __init__(self):
        self.a, self.b = 0, 1

    def __iter__(self):  # 返回迭代器对象本身
        return self      

    def next(self):      # 返回容器下一个元素
        self.a, self.b = self.b, self.a + self.b
        return self.a    

>>> fib = Fib()
>>> for i in fib:
...     if i > 10:
...         break
...     print i
...
1
1
2
3
5
8
```

# getitem

有时，我们希望可以使用 `obj[n]` 这种方式对实例对象进行取值，比如对斐波那契数列，我们希望可以取出其中的某一项，这时我们需要在类中实现 `__getitem__` 方法，比如下面的例子：

```python
class Fib(object):
    def __getitem__(self, n):
        a, b = 1, 1
        for x in xrange(n):
            a, b = b, a + b
        return a
    
>>> fib = Fib()
>>> fib[0], fib[1], fib[2], fib[3], fib[4], fib[5]
(1, 1, 2, 3, 5, 8)
```

我们还想更进一步，希望支持 `obj[1:3]` 这种切片方法来取值，这时 `__getitem__` 方法传入的参数可能是一个整数，也可能是一个切片对象 slice，因此，我们需要对传入的参数进行判断，可以使用 `isinstance` 进行判断，改后的代码如下：

```python
class Fib(object):
    def __getitem__(self, n):
        if isinstance(n, slice):   # 如果 n 是 slice 对象
            a, b = 1, 1
            start, stop = n.start, n.stop
            L = []
            for i in xrange(stop):
                if i >= start:
                    L.append(a)
                a, b = b, a + b
            return L
        if isinstance(n, int):     # 如果 n 是 int 型
            a, b = 1, 1
            for i in xrange(n):
                a, b = b, a + b
            return a
```

现在，我们试试用切片方法：

```python
>>> fib = Fib()
>>> fib[0:3]
[1, 1, 2]
>>> fib[2:6]
[2, 3, 5, 8]
```

上面，我们只是简单地演示了 getitem 的操作，但是它还很不完善，比如没有对负数处理，不支持带 step 参数的切片操作 `obj[1:2:5]` 等等，读者有兴趣的话可以自己实现看看。

`__geitem__` 用于获取值，类似地，`__setitem__` 用于设置值，`__delitem__` 用于删除值，让我们看下面一个例子：

```python
class Point(object):
    def __init__(self):
        self.coordinate = {}

    def __str__(self):
        return "point(%s)" % self.coordinate

    def __getitem__(self, key):
        return self.coordinate.get(key)

    def __setitem__(self, key, value):
        self.coordinate[key] = value

    def __delitem__(self, key):
        del self.coordinate[key]
        print 'delete %s' % key

    def __len__(self):
        return len(self.coordinate)
        
    __repr__ = __str__
```

在上面，我们定义了一个 Point 类，它有一个属性 coordinate（坐标），是一个字典，让我们看看使用：

```python
>>> p = Point()
>>> p['x'] = 2    # 对应于 p.__setitem__('x', 2)
>>> p['y'] = 5    # 对应于 p.__setitem__('y', 5)
>>> p             # 对应于 __repr__
point({'y': 5, 'x': 2})
>>> len(p)        # 对应于 p.__len__
2
>>> p['x']        # 对应于 p.__getitem__('x')
2
>>> p['y']        # 对应于 p.__getitem__('y')
5
>>> del p['x']    # 对应于 p.__delitem__('x')
delete x
>>> p
point({'y': 5})
>>> len(p)
1
```

# getattr

当我们获取对象的某个属性，如果该属性不存在，会抛出 AttributeError 异常，比如：

```python
class Point(object):
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

>>> p = Point(3, 4)
>>> p.x, p.y
(3, 4)
>>> p.z
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-547-6dce4e43e15c> in <module>()
----> 1 p.z

AttributeError: 'Point' object has no attribute 'z'
```

那有没有办法不让它抛出异常呢？当然有，只需在类的定义中加入 `__getattr__` 方法，比如：

```python
class Point(object):
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y
    def __getattr__(self, attr):
        if attr == 'z':
            return 0

>>> p = Point(3, 4)
>>> p.z
0
```

现在，当我们调用不存在的属性（比如 z）时，解释器就会试图调用 `__getattr__(self, 'z')` 来获取值，但是，上面的实现还有一个问题，当我们调用其他属性，比如 w ，会返回 None，因为 `__getattr__` 默认返回就是 None，只有当 attr 等于 'z' 时才返回 0，如果我们想让 `__getattr__` 只响应几个特定的属性，可以加入异常处理，修改 `__getattr__` 方法，如下：

```python
def __getattr__(self, attr):
    if attr == 'z':
        return 0
    raise AttributeError("Point object has no attribute %s" % attr)
```

这里再强调一点，`__getattr__` 只有在属性不存在的情况下才会被调用，对已存在的属性不会调用 `__getattr__`。

与 `__getattr__` 一起使用的还有 `__setattr__`, `__delattr__`，类似 `obj.attr = value`, `del obj.attr`，看下面一个例子：

```python
class Point(object):
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

    def __getattr__(self, attr):
        if attr == 'z':
            return 0
        raise AttributeError("Point object has no attribute %s" % attr)

    def __setattr__(self, *args, **kwargs):  
        print 'call func set attr (%s, %s)' % (args, kwargs)
        return object.__setattr__(self, *args, **kwargs)

    def __delattr__(self, *args, **kwargs):  
        print 'call func del attr (%s, %s)' % (args, kwargs)
        return object.__delattr__(self, *args, **kwargs)
    
>>> p = Point(3, 4)
call func set attr (('x', 3), {})
call func set attr (('y', 4), {})
>>> p.z
0
>>> p.z = 7
call func set attr (('z', 7), {})
>>> p.z
7
>>> p.w
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 8, in __getattr__
AttributeError: Point object has no attribute w
>>> p.w = 8
call func set attr (('w', 8), {})
>>> p.w
8
>>> del p.w
call func del attr (('w',), {})
>>> p.__dict__
{'y': 4, 'x': 3, 'z': 7}
```

# call

我们一般使用 `obj.method()` 来调用对象的方法，那能不能**直接在实例本身上调用**呢？在 Python 中，只要我们在类中定义 `__call__` 方法，就可以对实例进行调用，比如下面的例子：

```python
class Point(object):
    def __init__(self, x, y):
        self.x, self.y = x, y
    def __call__(self, z):
        return self.x + self.y + z
```

使用如下：

```python
>>> p = Point(3, 4)
>>> callable(p)     # 使用 callable 判断对象是否能被调用
True
>>> p(6)            # 传入参数，对实例进行调用，对应 p.__call__(6)
13                  # 3+4+6
```

可以看到，对实例进行调用就好像对函数调用一样。

# 小结

- `__new__` 在 `__init__` 之前被调用，用来创建实例。
- `__str__` 是用 print 和 str 显示的结果，`__repr__` 是直接显示的结果。
- `__getitem__` 用类似 `obj[key]` 的方式对对象进行取值
- `__getattr__` 用于获取不存在的属性 obj.attr
- `__call__` 使得可以对实例进行调用

# 参考资料

- [design patterns - Python's use of __new__ and __init__? - Stack Overflow](http://stackoverflow.com/questions/674304/pythons-use-of-new-and-init)
- [定制类](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/0013946328809098c1be08a2c7e4319bd60269f62be04fa000)
- [__setitem__ implementation in Python for Point(x,y) class - Stack Overflow](http://stackoverflow.com/questions/15774804/setitem-implementation-in-python-for-pointx-y-class)
- [Python对象的特殊属性和方法 | Hom](http://gohom.win/2015/10/09/pySpecialObjMethod/)
- [介绍Python的魔术方法 - Magic Method - 旺酱的专栏 - SegmentFault](https://segmentfault.com/a/1190000007256392)


