迭代器 (Iterator)
====

# 迭代和可迭代

迭代器这个概念在很多语言中（比如 C++，Java）都是存在的，但是不同语言实现迭代器的方式各不相同。**在 Python 中，迭代器是指遵循迭代器协议（iterator protocol）的对象。**至于什么是迭代器协议，稍后自然会说明。为了更好地理解迭代器，我先介绍和迭代器相关的两个概念：

- 迭代（Iteration）
- 可迭代对象（Iterable）

你可能会觉得这是在玩文字游戏，但这确实是要搞清楚的。

> 当我们用一个循环（比如 for 循环）来遍历容器（比如列表，元组）中的元素时，这种遍历的过程就叫***迭代***。

在 Python 中，我们使用 `for...in...` 进行迭代。比如，遍历一个 list:

```python
numbers = [1, 2, 3, 4]
for num in numbers:
    print num
```

像上面这种可以使用 `for` 循环进行迭代的对象，就是可迭代对象，它的定义如下：

> 含有 `__iter__()` 方法或 `__getitem__()` 方法的对象称之为***可迭代对象***。

我们可以使用 Python 内置的 `hasattr()` 函数来判断一个对象是不是可迭代的：

```python
>>> hasattr((), '__iter__')
True
>>> hasattr([], '__iter__')
True
>>> hasattr({}, '__iter__')
True
>>> hasattr(123, '__iter__')
False
>>> hasattr('abc', '__iter__')
False
>>> hasattr('abc', '__getitem__')
True
```

另外，我们也可使用 `isinstance()` 进行判断：

```python
>>> from collections import Iterable

>>> isinstance((), Iterable)        # 元组
True
>>> isinstance([], Iterable)        # 列表
True
>>> isinstance({}, Iterable)        # 字典
True
>>> isinstance('abc', Iterable)     # 字符串
True
>>> isinstance(100, Iterable)       # 数字
False
```

可见，我们熟知的字典（dict）、元组（tuple）、集合（set）和字符串对象都是可迭代的。

# 迭代器

现在，让我们看看什么是迭代器（Iterator）。上文说过，**迭代器是指遵循迭代器协议（iterator protocol）的对象。**从这句话我们可以知道，迭代器是一个对象，但比较特别，它需要遵循迭代器协议，那什么是迭代器协议呢？

> ***迭代器协议（iterator protocol）***是指要实现对象的 `__iter()__` 和 `next()` 方法（注意：Python3 要实现 `__next__()` 方法），其中，`__iter()__` 方法返回迭代器对象本身，`next()` 方法返回容器的下一个元素，在没有后续元素时抛出 `StopIteration` 异常。

接下来讲讲迭代器的例子，有什么常见的迭代器呢？列表是迭代器吗？字典是迭代器吗？我们使用 `hasattr()` 进行判断：

```
>>> hasattr((1, 2, 3), '__iter__')
True
>>> hasattr((1, 2, 3), 'next')  # 有 __iter__ 方法但是没有 next 方法，不是迭代器
False
>>>
>>> hasattr([1, 2, 3], '__iter__')
True
>>> hasattr([1, 2, 3], 'next')
False
>>>
>>> hasattr({'a': 1, 'b': 2}, '__iter__')
True
>>> hasattr({'a': 1, 'b': 2}, 'next')
False
```

同样，我们也可以使用 `isinstance()` 进行判断：

```
>>> from collections import Iterator
>>>
>>> isinstance((), Iterator)
False
>>> isinstance([], Iterator)
False
>>> isinstance({}, Iterator)
False
>>> isinstance('', Iterator)
False
>>> isinstance(123, Iterator)
False
```

可见，**虽然元组、列表和字典等对象是可迭代的，但它们却不是迭代器！**对于这些可迭代对象，可以使用 Python 内置的 `iter()` 函数获得它们的迭代器对象，看下面的使用：

```
>>> from collections import Iterator
>>> isinstance(iter([1, 2, 3]), Iterator)  # 使用 iter() 函数，获得迭代器对象
True
>>> isinstance(iter('abc'), Iterator)
True
>>>
>>> my_str = 'abc'
>>> next(my_str)      # my_str 不是迭代器，不能使用 next()，因此出错
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-15-5f369cd8082f> in <module>()
----> 1 next(my_str)

TypeError: str object is not an iterator
>>>
>>> my_iter = iter(my_str)   # 获得迭代器对象
>>> isinstance(my_iter, Iterator)
True
>>> next(my_iter)   # 可使用内置的 next() 函数获得下一个元素
'a'
```

事实上，Python 的 `for` 循环就是先通过内置函数 `iter()` 获得一个迭代器，然后再不断调用 `next()` 函数实现的，比如：

```
for x in [1, 2, 3]:
    print i
```

等价于

```python
# 获得 Iterator 对象
it = iter([1, 2, 3])

# 循环
while True:
    try:
        # 获得下一个值
        x = next(it)
        print x
    except StopIteration:
        # 没有后续元素，退出循环
        break
```

# 斐波那契数列迭代器

现在，让我们来自定义一个迭代器：斐波那契（Fibonacci）数列迭代器。根据迭代器的定义，我们需要实现 `__iter()__` 和 `next()` 方法（在 Python3 中是 `__next__()` 方法）。先看代码：

```python
# -*- coding: utf-8 -*-

from collections import Iterator

class Fib(object):
    def __init__(self):
        self.a, self.b = 0, 1
    
    # 返回迭代器对象本身
    def __iter__(self):
        return self
    
    # 返回容器下一个元素
    def next(self):
        self.a, self.b = self.b, self.a + self.b
        return self.a

def main():
    fib = Fib()    # fib 是一个迭代器
    print 'isinstance(fib, Iterator): ', isinstance(fib, Iterator)

    for i in fib:
        if i > 10:
            break
        print i

if __name__ == '__main__':
    main()
```

在上面的代码中，我们定义了一个 Fib 类，用于生成 Fibonacci 数列。在类的实现中，我们定义了 `__iter__` 方法，它返回对象本身，这个方法会在遍历时被 Python 内置的 `iter()` 函数调用，返回一个迭代器。类中的 `next()` 方法用于返回容器的下一个元素，当使用 `for` 循环进行遍历的时候，就会使用 Python 内置的 `next()` 函数调用对象的 `next` 方法（在 Python3 中是 `__next__` 方法）对迭代器进行遍历。

运行上面的代码，可得到如下结果：

```
isinstance(fib, Iterator):  True
1
1
2
3
5
8
```

# 小结

- 元组、列表、字典和字符串对象是可迭代的，但不是迭代器，不过我们可以通过 `iter()` 函数获得一个迭代器对象；
- Python 的 `for` 循环实质上是先通过内置函数 `iter()` 获得一个迭代器，然后再不断调用 `next()` 函数实现的；
- 自定义迭代器需要实现对象的 `__iter()__` 和 `next()` 方法（注意：Python3 要实现 `__next__()` 方法），其中，`__iter()__` 方法返回迭代器对象本身，`next()` 方法返回容器的下一个元素，在没有后续元素时抛出 `StopIteration` 异常。

# 参考资料

- [Callback or Iterator in Python](https://code-maven.com/callback-or-iterator-in-python)
- [迭代器 - 廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143178254193589df9c612d2449618ea460e7a672a366000)


