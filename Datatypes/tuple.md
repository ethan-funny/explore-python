# 元组

在 Python 中，**元组是一种不可变序列**，它使用圆括号来表示：

```python
>>> a = (1, 2, 3)    # a 是一个元组
>>> a
(1, 2, 3)
>>> a[0] = 6         # 元组是不可变的，不能对它进行赋值操作
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
```

# 空元组

创建一个空元组可以用没有包含内容的圆括号来表示：

```python
>>> a = ()
>>> a
()
```

# 一个值的元组

创建一个值的元组需要在值后面再加一个逗号，这个比较特殊，需要牢牢记住：

```python
>>> a = (12,)   # 在值后面再加一个逗号
>>> a
(12,)
>>> type(a)
<type 'tuple'>
>>>
>>> b = (12)    # 只是使用括号括起来，而没有加逗号，不是元组，本质上是 b = 12
>>> b
12
>>> type(b)
<type 'int'>
```

# 元组操作

元组也是一种序列，因此也可以对它进行索引、分片等。由于它是不可变的，因此就没有类似列表的 append, extend, sort 等方法。

# 小结

- 元组是不可变的。
- 创建一个值的元组需要在值后面再加一个逗号。

