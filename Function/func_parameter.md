# 函数参数

在 Python 中，定义函数和调用函数都很简单，但如何定义函数参数和传递函数参数，则涉及到一些套路了。总的来说，Python 的函数参数主要分为以下几种：

- 必选参数
- 默认参数
- 可变参数
- 关键字参数

# 必选参数

必选参数可以说是最常见的了，顾名思义，必选参数就是在调用函数的时候要传入数量一致的参数，比如：

```python
>>> def add(x, y):        # x, y 是必选参数
...     print x + y
...
>>> add()                 # 啥都没传，不行
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: add() takes exactly 2 arguments (0 given)
>>> add(1)                # 只传了一个，也不行
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: add() takes exactly 2 arguments (1 given)
>>> add(1, 2)             # 数量一致，通过
3
```

# 默认参数

默认参数是指在定义函数的时候提供一些默认值，如果在调用函数的时候没有传递该参数，则自动使用默认值，否则使用传递时该参数的值。

看看例子就明白了：

```python
>>> def add(x, y, z=1):     # x, y 是必选参数，z 是默认参数，默认值是 1
...     print x + y + z
...
>>> add(1, 2, 3)            # 1+2+3
6
>>> add(1, 2)               # 没有传递 z，自动使用 z=1，即 1+2+1
4
```

可以看到，默认参数使用起来也很简单，但有两点需要注意的是：

- 默认参数要放在所有必选参数的后面
- 默认参数应该使用不可变对象

比如，下面对默认参数的使用是错误的：

```python
>>> def add(x=1, y, z):      # x 是默认参数，必须放在所有必选参数的后面
...     return x + y + z
...
  File "<stdin>", line 1
SyntaxError: non-default argument follows default argument
>>>
>>> def add(x, y=1, z):      # y 是默认参数，必须放在所有必选参数的后面
...     return x + y + z
...
  File "<stdin>", line 1
SyntaxError: non-default argument follows default argument
```

再来看看为什么默认参数应该使用不可变对象。

我们看一个例子：

```python
>>> def add_to_list(L=[]):
...     L.append('END')
...     return L
```

在上面的函数中，L 是一个默认参数，默认值是 `[]`，表示空列表。

我们来看看使用：

```python
>>> add_to_list([1, 2, 3])          # 没啥问题
[1, 2, 3, 'END']
>>> add_to_list(['a', 'b', 'c'])    # 没啥问题
['a', 'b', 'c', 'END']
>>> add_to_list()                   # 没有传递参数，使用默认值，也没啥问题
['END']
>>> add_to_list()                   # 没有传递参数，使用默认值，竟出现两个 'END'
['END', 'END']
>>> add_to_list()                   # 糟糕了，三个 'END'
['END', 'END', 'END']
```

为啥呢？我们在调用函数的时候没有传递参数，那么就默认使用 `L=[]`，经过处理，L 应该只有一个元素，怎么会出现调用函数两次，L 就有两个元素呢？

原来，L 指向了可变对象 `[]`，当你调用函数时，L 的内容发生了改变，默认参数的内容也会跟着变，也就是，当你第一次调用时，L 的初始值是 `[]`，当你第二次调用时，L 的初始值是 `['END']`，等等。

所以，为了避免不必要的错误，我们应该使用不可变对象作为函数的默认参数。

# 可变参数

在某些情况下，我们在定义函数的时候，无法预估函数应该制定多少个参数，这时我们就可以使用**可变参数**了，也就是，函数的参数个数是不确定的。

看看例子：

```python
>>> def add(*numbers):
...     sum = 0
...     for i in numbers:
...         sum += i
...     print 'numbers:', numbers
...     return sum
```

在上面的代码中，numbers 就是一个可变参数，参数前面有一个 `*` 号，表示是可变的。在函数内部，参数 numbers 接收到的是一个 tuple。

在调用函数时，我们可以给该函数传递任意个参数，包括 0 个参数：

```python
>>> add()           # 传递 0 个参数
numbers: ()
0
>>> add(1)          # 传递 1 个参数
numbers: (1,)
1
>>> add(1, 2)       # 传递 2 个参数
numbers: (1, 2)
3
>>> add(1, 2, 3)    # 传递 3 个参数
numbers: (1, 2, 3)
6
```

上面的 `*` 表示任意参数，实际上，它还有另外一个用法：用来给函数传递参数。

看看例子：

```python
>>> def add(x, y, z):        # 有 3 个必选参数
... 	return x + y + z
...
>>> a = [1, 2, 3]
>>> add(a[0], a[1], a[2])    # 这样传递参数很累赘
6
>>> add(*a)                  # 使用 *a，相当于上面的做法
6
>>> b = (4, 5, 6)
>>> add(*b)                  # 对元组一样适用
15
```

再看一个例子：

```python
>>> def add(*numbers):       # 函数参数是可变参数
... 	sum = 0
... 	for i in numbers:
... 	    sum += i
... 	return sum
...
>>> a = [1, 2]
>>> add(*a)                  # 使用 *a 给函数传递参数
3
>>> a = [1, 2, 3, 4]
>>> add(*a)
10
```

# 关键字参数

可变参数允许你将不定数量的参数传递给函数，而**关键字参数**则允许你将不定长度的**键值对**, 作为参数传递给一个函数。

让我们看看例子：

```python
>>> def add(**kwargs):
    return kwargs
>>> add()            # 没有参数，kwargs 为空字典
{}
>>> add(x=1)         # x=1 => kwargs={'x': 1}
{'x': 1}
>>> add(x=1, y=2)    # x=1, y=2 => kwargs={'y': 2, 'x': 1}
{'y': 2, 'x': 1}
```

在上面的代码中，kwargs 就是一个关键字参数，它前面有两个 `*` 号。kwargs 可以接收不定长度的键值对，在函数内部，它会表示成一个 dict。

和可变参数类似，我们也可以使用 `**kwargs` 的形式来调用函数，比如：

```python
>>> def add(x, y, z):
... 	return x + y + z
...
>>> dict1 = {'z': 3, 'x': 1, 'y': 6}
>>> add(dict1['x'], dict1['y'], dict1['z'])    # 这样传参很累赘
10
>>> add(**dict1)        # 使用 **dict1 来传参，等价于上面的做法
10
```

再看一个例子：

```python
>>> def sum(**kwargs):               # 函数参数是关键字参数
... 	sum = 0
... 	for k, v in kwargs.items():
... 	    sum += v
... 	return sum
>>> sum()                            # 没有参数
0
>>> dict1 = {'x': 1}
>>> sum(**dict1)                     # 相当于 sum(x=1)
1
>>> dict2 = {'x': 2, 'y': 6} 
>>> sum(**dict2)                     # 相当于 sum(x=2, y=6)
8
```

# 参数组合

在实际的使用中，我们经常会同时用到必选参数、默认参数、可变参数和关键字参数或其中的某些。但是，需要注意的是，**它们在使用的时候是有顺序的，依次是必选参数、默认参数、可变参数和关键字参数**。

比如，定义一个包含上述四种参数的函数：

```python
>>> def func(x, y, z=0, *args, **kwargs):
    print 'x =', x
    print 'y =', y
    print 'z =', z
    print 'args =', args
    print 'kwargs =', kwargs
```

在调用函数的时候，Python 会自动按照参数位置和参数名把对应的参数传进去。让我们看看：

```python
>>> func(1, 2)                     # 至少提供两个参数，因为 x, y 是必选参数
x = 1
y = 2
z = 0
args = ()
kwargs = {}
>>> func(1, 2, 3)                  # x=1, y=2, z=3
x = 1
y = 2
z = 3
args = ()
kwargs = {}
>>> func(1, 2, 3, 4, 5, 6)         # x=1, y=2, z=3, args=(4, 5, 6), kwargs={}
x = 1
y = 2
z = 3
args = (4, 5, 6)
kwargs = {}
>>> func(1, 2, 4, u=6, v=7)        # args = (), kwargs = {'u': 6, 'v': 7}
x = 1
y = 2
z = 4
args = ()
kwargs = {'u': 6, 'v': 7}
>>> func(1, 2, 3, 4, 5, u=6, v=7)   # args = (4, 5), kwargs = {'u': 6, 'v': 7}
x = 1
y = 2
z = 3
args = (4, 5)
kwargs = {'u': 6, 'v': 7}
```

我们还可以通过下面的形式来传递参数：

```python
>>> a = (1, 2, 3)
>>> b = {'u': 6, 'v': 7}
>>> func(*a, **b)
x = 1
y = 2
z = 3
args = ()
kwargs = {'u': 6, 'v': 7}
```

# 小结

- 默认参数要放在所有必选参数的后面。
- 应该使用不可变对象作为函数的默认参数。
- `*args` 表示可变参数，`**kwargs` 表示关键字参数。
- 参数组合在使用的时候是有顺序的，依次是必选参数、默认参数、可变参数和关键字参数。
- `*args` 和 `**kwargs` 是 Python 的惯用写法。

# 参考资料

- [args 和 *kwargs · Python进阶](https://eastlakeside.gitbooks.io/interpy-zh/content/args_kwargs/)
- [函数的参数 - 廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001374738449338c8a122a7f2e047899fc162f4a7205ea3000)


