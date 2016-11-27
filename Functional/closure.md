# 闭包

在 Python 中，函数也是一个对象。因此，我们在定义函数时，可以再嵌套定义一个函数，并将该嵌套函数返回，比如：

```python
from math import pow

def make_pow(n):
    def inner_func(x):     # 嵌套定义了 inner_func
        return pow(x, n)   # 注意这里引用了外部函数的 n
    return inner_func      # 返回 inner_func
```

上面的代码中，函数 `make_pow` 里面又定义了一个内部函数 `inner_func`，然后将该函数返回。因此，我们可以使用 `make_pow` 来生成另一个函数：

```
>>> pow2 = make_pow(2)  # pow2 是一个函数，参数 2 是一个自由变量
>>> pow2
<function inner_func at 0x10271faa0>
>>> pow2(6)
36.0
```

我们还注意到，内部函数 `inner_func` 引用了外部函数 `make_pow` 的自由变量 `n`，这也就意味着，当函数 `make_pow` 的生命周期结束之后，`n` 这个变量依然会保存在 `inner_func` 中，它被 `inner_func` 所引用。

```
>>> del make_pow         # 删除 make_pow
>>> pow3 = make_pow(3)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'make_pow' is not defined
>>> pow2(9)     # pow2 仍可正常调用，自由变量 2 仍保存在 pow2 中
81.0
```

像上面这种情况，一个函数返回了一个内部函数，该内部函数引用了外部函数的相关参数和变量，我们把该返回的内部函数称为**闭包（Closure）**。

在上面的例子中，`inner_func` 就是一个闭包，它引用了自由变量 `n`。

# 闭包的作用

 - 闭包的最大特点就是引用了自由变量，即使生成闭包的环境已经释放，闭包仍然存在。
 - 闭包在运行时可以有多个实例，即使传入的参数相同。

```
>>> pow_a = make_pow(2)
>>> pow_b = make_pow(2)
>>> pow_a == pow_b
False
```
 - 利用闭包，我们还可以模拟类的实例。

这里构造一个类，用于求一个点到另一个点的距离：

```python
from math import sqrt

class Point(object):
    def __init__(self, x, y):
        self.x, self.y = x, y

    def get_distance(self, u, v):
        distance = sqrt((self.x - u) ** 2 + (self.y - v) ** 2)
        return distance

>>> pt = Point(7, 2)        # 创建一个点
>>> pt.get_distance(10, 6)  # 求到另一个点的距离
5.0
```

用闭包来实现：

```python
def point(x, y):
    def get_distance(u, v):
        return sqrt((x - u) ** 2 + (y - v) ** 2)

    return get_distance

>>> pt = point(7, 2)
>>> pt(10, 6)
5.0
```

可以看到，结果是一样的，但使用闭包实现比使用类更加简洁。

# 常见误区

闭包的概念很简单，但实现起来却容易出现一些误区，比如下面的例子：

```python
def count():
    funcs = []
    for i in [1, 2, 3]:
        def f():
            return i
        funcs.append(f)
    return funcs
```

在该例子中，我们在每次 `for` 循环中创建了一个函数，并将它存到 `funcs` 中。现在，调用上面的函数，你可能认为返回结果是 1, 2, 3，事实上却不是：

```
>>> f1, f2, f3 = count()
>>> f1()
3
>>> f2()
3
>>> f3()
3
```

为什么呢？原因在于上面的函数 `f` 引用了变量 `i`，但函数 `f` 并非立刻执行，当 `for` 循环结束时，此时变量 `i` 的值是3，`funcs` 里面的函数引用的变量都是 3，最终结果也就全为 3。

因此，我们应**尽量避免在闭包中引用循环变量，或者后续会发生变化的变量**。

那上面这种情况应该怎么解决呢？我们可以再创建一个函数，并将循环变量的值传给该函数，如下：

```python
def count():
    funcs = []
    for i in [1, 2, 3]:
        def g(param):
            f = lambda : param    # 这里创建了一个匿名函数
            return f
        funcs.append(g(i))        # 将循环变量的值传给 g
    return funcs

>>> f1, f2, f3 = count()
>>> f1()
1
>>> f2()
2
>>> f3()
3
```

# 小结

- 闭包是携带自由变量的函数，即使创建闭包的外部函数的生命周期结束了，闭包所引用的自由变量仍会存在。
- 闭包在运行可以有多个实例。
- 尽量不要在闭包中引用循环变量，或者后续会发生变化的变量。

# 参考资料

- [返回函数 - 廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/0014186131194415d50558b7a1c424f9fb52b84dc9c965c000)
- [Why aren't python nested functions called closures? - Stack Overflow](http://stackoverflow.com/questions/4020419/why-arent-python-nested-functions-called-closures)


