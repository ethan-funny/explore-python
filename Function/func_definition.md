# 定义函数

在 Python 中，定义函数使用 `def` 语句。一个函数主要由三部分构成：

- 函数名
- 函数参数
- 函数返回值

让我们看一个简单的例子：

```python
def hello(name):
    return name

>>> r = hello('ethan')
>>> r
'ethan'
```

在上面，我们定义了一个函数。函数名是 `hello`；函数有一个参数，参数名是 name；函数有一个返回值，name。

我们也可以定义一个没有参数和返回值的函数：

```python
def greet():              # 没有参数
    print 'hello world'   # 没有 return，会自动 return None

>>> r = greet()
hello world
>>> r == None
```

这里，函数 `greet` 没有参数，它也没有返回值（或者说是 None）。

我们还可以定义返回多个值的函数：

```python
>>> def add_one(x, y, z):
... 	return x+1, y+1, z+1       # 有 3 个返回值
...
>>>
>>> result = add_one(1, 5, 9)
>>> result      # result 实际上是一个 tuple
(2, 6, 10)
>>> type(result)
<type 'tuple'>
```

# 小结

- 如果函数没有 `return` 语句，则自动 `return None`。


