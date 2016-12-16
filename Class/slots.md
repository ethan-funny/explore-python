# slots 魔法

在 Python 中，我们在定义类的时候可以定义属性和方法。当我们创建了一个类的实例后，我们还可以给该实例绑定任意新的属性和方法。

看下面一个简单的例子：

```python
class Point(object):    
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

>>> p = Point(3, 4)
>>> p.z = 5    # 绑定了一个新的属性
>>> p.z
5
>>> p.__dict__
{'x': 3, 'y': 4, 'z': 5}
```

在上面，我们创建了实例 p 之后，给它绑定了一个新的属性 z，这种动态绑定的功能虽然很有用，但它的代价是消耗了更多的内存。

因此，为了不浪费内存，可以使用 `__slots__` 来告诉 Python 只给一个固定集合的属性分配空间，对上面的代码做一点改进，如下：

```python
class Point(object):
    __slots__ = ('x', 'y')       # 只允许使用 x 和 y

    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y
```

上面，我们给 `__slots__` 设置了一个元组，来限制类能添加的属性。现在，如果我们想绑定一个新的属性，比如 z，就会出错了，如下：

```python
>>> p = Point(3, 4)
>>> p.z = 5
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-648-625ed954d865> in <module>()
----> 1 p.z = 5

AttributeError: 'Point' object has no attribute 'z'
```

使用 `__slots__` 有一点需要注意的是，`__slots__` 设置的属性仅对当前类有效，对继承的子类不起效，除非子类也定义了 `__slots__`，这样，子类允许定义的属性就是自身的 slots 加上父类的 slots。

# 小结

- __slots__ 魔法：限定允许绑定的属性.
- `__slots__` 设置的属性仅对当前类有效，对继承的子类不起效，除非子类也定义了 slots，这样，子类允许定义的属性就是自身的 slots 加上父类的 slots。

# 参考资料

- [slots魔法 · Python进阶](https://eastlakeside.gitbooks.io/interpy-zh/content/slots_magic/)


