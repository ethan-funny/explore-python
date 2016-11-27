# 高阶函数

在函数式编程中，我们可以将函数当作变量一样自由使用。一个函数接收另一个函数作为参数，这种函数称之为**高阶函数（Higher-order Functions）**。

看一个简单的例子：

```python
def func(g, arr):
    return [g(x) for x in arr]
```

上面的代码中，`func` 是一个高阶函数，它接收两个参数，第 1 个参数是函数，第 2 个参数是数组，`func` 的功能是将函数 g 逐个作用于数组 arr 上，并返回一个新的数组，比如，我们可以这样用：

```
def double(x):
    return 2 * x

def square(x):
    return x * x

arr1 = func(double, [1, 2, 3, 4])
arr2 = func(square, [1, 2, 3, 4])
```

不难判断出，arr1 是 [2, 4, 6, 8]，arr2 是 [1, 4, 9, 16]。

# 小结

- 可接收其他函数作为参数的函数称为高阶函数。


