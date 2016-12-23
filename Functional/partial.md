# partial 函数

Python 提供了一个 functools 的模块，该模块为高阶函数提供支持，partial 就是其中的一个函数，该函数的形式如下：

```python
functools.partial(func[,*args][, **kwargs])
```

这里先举个例子，看看它是怎么用的。

假设有如下函数：

```python
def multiply(x, y):
    return x * y
```

现在，我们想返回某个数的双倍，即：

```
>>> multiply(3, y=2)
6
>>> multiply(4, y=2)
8
>>> multiply(5, y=2)
10
```

上面的调用有点繁琐，每次都要传入 `y=2`，我们想到可以定义一个新的函数，把 `y=2` 作为默认值，即：

```python
def double(x, y=2):
    return multiply(x, y)
```

现在，我们可以这样调用了：

```
>>> double(3)
6
>>> double(4)
8
>>> double(5)
10
```

事实上，我们可以不用自己定义 `double`，利用 `partial`，我们可以这样：

```python
from functools import partial

double = partial(multiply, y=2)
```

`partial` 接收函数 `multiply` 作为参数，固定 `multiply` 的参数 `y=2`，并返回一个新的函数给 `double`，这跟我们自己定义 `double` 函数的效果是一样的。

所以，简单而言，`partial` 函数的功能就是：把一个函数的某些参数给固定住，返回一个新的函数。

需要注意的是，我们上面是固定了 `multiply` 的关键字参数 `y=2`，如果直接使用：

```python
double = partial(multiply, 2)
```

则 `2` 是赋给了 `multiply` 最左边的参数 `x`，不信？我们可以验证一下：

```python
from functools import partial

def subtraction(x, y):
    return x - y

f = partial(subtraction, 4)  # 4 赋给了 x
>>> f(10)   # 4 - 10
-6
```

# 小结

- partial 的功能：固定函数参数，返回一个新的函数。
- 当函数参数太多，需要固定某些参数时，可以使用 `functools.partial` 创建一个新的函数。

# 参考资料

- [偏函数 - 廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386819893624a7edc0e3e3df4d5d852a352b037c93ec000)


