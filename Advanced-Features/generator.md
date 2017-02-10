生成器
====

生成器（generator）也是一种迭代器，在每次迭代时返回一个值，直到抛出 `StopIteration` 异常。它有两种构造方式：

- 生成器表达式

和列表推导式的定义类似，生成器表达式使用 `()` 而不是 `[]`，比如：

```
numbers = (x for x in range(5))   # 注意是()，而不是[]
for num in numbers:
    print num
```

- 生成器函数

含有 `yield` 关键字的函数，调用该函数时会返回一个生成器。

本文主要讲**生成器函数**。

# 生成器函数

先来看一个简单的例子：

```python
>>> def generator_function():
...     print 'hello 1'
...     yield 1
...     print 'hello 2'
...     yield 2
...     print 'hello 3'
>>>
>>> g = generator_function()  # 函数没有立即执行，而是返回了一个生成器，当然也是一个迭代器
>>> g.next()  # 当使用 next() 方法，或使用 next(g) 的时候开始执行，遇到 yield 暂停
hello 1
1
>>> g.next()    # 从原来暂停的地方继续执行
hello 2
2
>>> g.next()    # 从原来暂停的地方继续执行，没有 yield，抛出异常
hello 3
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

可以看到，上面的函数没有使用 `return` 语句返回值，而是使用了 `yield`『生出』一个值。一个带有 `yield` 的函数就是一个生成器函数，当我们使用 `yield` 时，它帮我们自动创建了 `__iter__()` 和 `next()` 方法，而且在没有数据时，也会抛出 `StopIteration` 异常，也就是我们不费吹灰之力就获得了一个迭代器，非常简洁和高效。

带有 `yield` 的函数执行过程比较特别：

- 调用该函数的时候不会立即执行代码，而是返回了一个生成器对象；
- 当使用 `next()` (在 for 循环中会自动调用 `next()`) 作用于返回的生成器对象时，函数开始执行，在遇到 `yield` 的时候会『暂停』，并返回当前的迭代值；
- 当再次使用 `next()` 的时候，函数会从原来『暂停』的地方继续执行，直到遇到 `yield` 语句，如果没有 `yield` 语句，则抛出异常；

整个过程看起来就是不断地 `执行->中断->执行->中断` 的过程。一开始，调用生成器函数的时候，函数不会立即执行，而是返回一个生成器对象；然后，当我们使用 `next()` 作用于它的时候，它开始执行，遇到 `yield` 语句的时候，执行被中断，并返回当前的迭代值，要注意的是，此刻会记住中断的位置和所有的变量值，也就是执行时的上下文环境被保留起来；当再次使用 `next()` 的时候，从原来中断的地方继续执行，直至遇到 `yield`，如果没有 `yield`，则抛出异常。

**简而言之，就是 `next` 使函数执行，`yield` 使函数暂停。**

# 例子

看一个 Fibonacci 数列的例子，如果使用自定义迭代器的方法，是这样的：

```python
>>> class Fib(object):
...     def __init__(self):
...         self.a, self.b = 0, 1
...     def __iter__(self):
...         return self
...     def next(self):
...         self.a, self.b = self.b, self.a + self.b
...         return self.a
...
>>> f = Fib()
>>> for item in f:
...     if item > 10:
...         break
...     print item
...
1
1
2
3
5
8
```

而使用生成器的方法，是这样的：

```python
>>> def fib():
...     a, b = 0, 1
...     while True:
...         a, b = b, a + b
...         yield a
...
>>> f = fib()
>>> for item in f:
...     if item > 10:
...         break
...     print item
...
1
1
2
3
5
8
```

可以看到，使用生成器的方法非常简洁，不用自定义 `__iter__()` 和 `next()` 方法。

另外，在处理大文件的时候，我们可能无法一次性将其载入内存，这时可以通过构造固定长度的缓冲区，来不断读取文件内容。有了 `yield`，我们就不用自己实现读文件的迭代器了，比如下面的实现：

```python
def read_in_chunks(file_object, chunk_size=1024):
    """Lazy function (generator) to read a file piece by piece.
    Default chunk size: 1k."""
    while True:
        data = file_object.read(chunk_size)
        if not data:
            break
        yield data

f = open('really_big_file.dat')
for piece in read_in_chunks(f):
    process_data(piece)
```

# 进阶使用

我们除了能对生成器进行迭代使它返回值外，还能：

- 使用 `send()` 方法给它发送消息；
- 使用 `throw()` 方法给它发送异常；
- 使用 `close()` 方法关闭生成器；

## send() 方法

看一个简单的例子：

```
>>> def generator_function():
...     value1 = yield 0
...     print 'value1 is ', value1
...     value2 = yield 1
...     print 'value2 is ', value2
...     value3 = yield 2
...     print 'value3 is ', value3
...
>>> g = generator_function()
>>> g.next()   # 调用 next() 方法开始执行，返回 0
0
>>> g.send(2)
value1 is  2
1
>>> g.send(3)
value2 is  3
2
>>> g.send(4)
value3 is  4
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

在上面的代码中，我们先调用 `next()` 方法，使函数开始执行，代码执行到 `yield 0` 的时候暂停，返回了 0；接着，我们执行了 `send()` 方法，它会恢复生成器的运行，并将发送的值赋给上次中断时 yield 表达式的执行结果，也就是 value1，这时控制台打印出 value1 的值，并继续执行，直到遇到 yield 后暂停，此时返回 1；类似地，再次执行 `send()` 方法，将值赋给 value2。

简单地说，`send()` 方法就是 `next()` 的功能，加上传值给 `yield`。

## throw() 方法

除了可以给生成器传值，我们还可以给它传异常，比如：

```python
>>> def generator_function():
...     try:
...         yield 'Normal'
...     except ValueError:
...         yield 'Error'
...     finally:
...         print 'Finally'
...
>>> g = generator_function()
>>> g.next()
'Normal'
>>> g.throw(ValueError)
'Error'
>>> g.next()
Finally
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

可以看到，`throw()` 方法向生成器函数传递了 `ValueError` 异常，此时代码进入 except ValueError 语句，遇到 yield 'Error'，暂停并返回 Error 字符串。

简单的说，`throw()` 就是 `next()` 的功能，加上传异常给 `yield`。

## close() 方法

我们可以使用 `close()` 方法来关闭一个生成器。生成器被关闭后，再次调用 next() 方法，不管能否遇到 yield 关键字，都会抛出 StopIteration 异常，比如：

```python
>>> def generator_function():
...     yield 1
...     yield 2
...     yield 3
...
>>> g = generator_function()
>>>
>>> g.next()
1
>>> g.close()  # 关闭生成器
>>> g.next()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

# 小结

- yield 把函数变成了一个生成器。
- 生成器函数的执行过程看起来就是不断地 `执行->中断->执行->中断` 的过程。
    - 一开始，调用生成器函数的时候，函数不会立即执行，而是返回一个生成器对象；
    - 然后，当我们使用 `next()` 作用于它的时候，它开始执行，遇到 `yield` 语句的时候，执行被中断，并返回当前的迭代值，要注意的是，此刻会记住中断的位置和所有的数据，也就是执行时的上下文环境被保留起来；
    - 当再次使用 `next()` 的时候，从原来中断的地方继续执行，直至遇到 `yield`，如果没有 `yield`，则抛出异常。


# 参考资料

- [Python yield 使用浅析](https://www.ibm.com/developerworks/cn/opensource/os-cn-python-yield/)
- [谈谈Python的生成器 – 思诚之道](http://www.bjhee.com/python-yield.html)
- [Function vs Generator in Python](https://code-maven.com/function-vs-generator-in-python)
- [Lazy Method for Reading Big File in Python? - Stack Overflow](http://stackoverflow.com/questions/519633/lazy-method-for-reading-big-file-in-python)


