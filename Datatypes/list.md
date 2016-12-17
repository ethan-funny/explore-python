# 列表

字符串和元组是不可变的，而列表是可变（mutable）的，可以对它进行随意修改。我们还可以将字符串和元组转换成一个列表，只需使用 `list` 函数，比如：

```python
>>> s = 'hello'
>>> list(s)
['h', 'e', 'l', 'l', 'o']
>>> a = (1, 2, 3)
>>> list(a)
[1, 2, 3]
```

本文主要介绍常用的列表方法：

- index
- count
- append
- extend
- insert
- pop
- remove
- reverse
- sort

# index

index 方法用于从列表中找出某个元素的位置，如果有多个相同的元素，则返回第一个元素的位置。

看看例子：

```python
>>> numbers = [1, 2, 3, 4, 5, 5, 7, 8]
>>> numbers.index(5)        # 列表有两个 5，返回第一个元素的位置
4
>>> numbers.index(2)
1
>>> words = ['hello', 'world', 'you', 'me', 'he']
>>> words.index('me')
3
>>> words.index('her')       # 如果没找到元素，则会抛出异常
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: 'her' is not in list
```

# count

count 方法用于统计某个元素在列表中出现的次数。

看看例子：

```python
>>> numbers = [1, 2, 3, 4, 5, 5, 6, 7]
>>> numbers.count(2)   # 出现一次
1
>>> numbers.count(5)   # 出现了两次
2
>>> numbers.count(9)   # 没有该元素，返回 0
0
```

# append

append 方法用于在列表末尾增加新的元素。

看看例子：

```python
>>> numbers = [1, 2, 3, 4, 5, 5, 6, 7]
>>> numbers.append(8)         # 增加 8 这个元素
>>> numbers
[1, 2, 3, 4, 5, 5, 6, 7, 8]
>>> numbers.append([9, 10])   # 增加 [9, 10] 这个元素
>>> numbers
[1, 2, 3, 4, 5, 5, 6, 7, 8, [9, 10]]
```

# extend

extend 方法将一个新列表的元素添加到原列表中。

看看例子：

```python
>>> a = [1, 2, 3]
>>> b = [4, 5, 6]
>>> a.extend(b)
>>> a
[1, 2, 3, 4, 5, 6]
>>>
>>> a.extend(3)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'int' object is not iterable
>>> a.extend([3])
>>> a
[1, 2, 3, 4, 5, 6, 3]
```

注意到，虽然 append 和 extend 可接收一个列表作为参数，但是 append 方法是将其作为一个元素添加到列表中，而 extend 则是将新列表的元素逐个添加到原列表中。

# insert

insert 方法用于将某个元素添加到某个位置。

看看例子：

```python
>>> numbers = [1, 2, 3, 4, 5, 6]
>>> numbers.insert(3, 9)
>>> numbers
[1, 2, 3, 9, 4, 5, 6]
```

# pop

pop 方法用于移除列表中的一个元素（默认是最后一个），并且返回该元素的值。

看看例子：

```python
>>> numbers = [1, 2, 3, 4, 5, 6]
>>> numbers.pop()
6
>>> numbers
[1, 2, 3, 4, 5]
>>> numbers.pop(3)
4
>>> numbers
[1, 2, 3, 5]
```

# remove

remove 方法用于移除列表中的某个匹配元素，如果有多个匹配，则移除第一个。

看看例子：

```python
>>> numbers = [1, 2, 3, 5, 6, 7, 5, 8]
>>> numbers.remove(5)          # 有两个 5，移除第 1 个
>>> numbers
[1, 2, 3, 6, 7, 5, 8]
>>> numbers.remove(9)          # 没有匹配的元素，抛出异常
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: list.remove(x): x not in list
```

# reverse

reverse 方法用于将列表中的元素进行反转。

看看例子：

```python
>>> numbers = [1, 2, 3, 5, 6, 7, 5, 8]
>>> numbers.reverse()
>>> numbers
[8, 5, 7, 6, 5, 3, 2, 1]
```

# sort

sort 方法用于对列表进行排序，注意该方法会改变原来的列表，而不是返回新的排序列表，另外，sort 方法的返回值是空。

看看例子：

```python
>>> a = [4, 3, 6, 8, 9, 1]
>>> b = a.sort()
>>> b == None         # 返回值为空
True
>>> a
[1, 3, 4, 6, 8, 9]    # 原列表已经发生改变
```

如果我们不想改变原列表，而是希望返回一个排序后的列表，可以使用 sorted 函数，如下：

```python
>>> a = [4, 3, 6, 8, 9, 1]
>>> b = sorted(a)          # 返回一个排序后的列表
>>> a
[4, 3, 6, 8, 9, 1]         # 原列表没有改变
>>> b
[1, 3, 4, 6, 8, 9]         # 这是对原列表排序后的列表
```

注意到，不管是 sort 方法还是 sorted 函数，默认排序都是升序排序。如果你想要降序排序，就需要指定排序参数了。比如，对 sort 方法，可以添加一个 reverse 关键字参数，如下：

```python
>>> a = [4, 3, 6, 8, 9, 1]
>>> a.sort(reverse=True)    # 反向排序
>>> a
[9, 8, 6, 4, 3, 1]
```

该参数对 sorted 函数同样适用：

```python
>>> a = [4, 3, 6, 8, 9, 1]
>>> sorted(a, reverse=True)
[9, 8, 6, 4, 3, 1]
```

除了 reverse 关键字参数，还可以指定 key 关键字参数，它为每个元素创建一个键，然后所有元素按照这个键来排序，比如我们想根据元素的长度来排序：

```python
>>> s = ['ccc', 'a', 'bb', 'dddd']
>>> s.sort(key=len)          # 使用 len 作为键函数，根据元素长度排序
>>> s
['a', 'bb', 'ccc', 'dddd']
```

另外，我们还可以使用 sorted 进行多列（属性）排序。

看看例子：

```python
>>> students = [
        ('john', 'B', 15),
        ('jane', 'A', 12),
        ('dave', 'B', 10),
        ('ethan', 'C', 20),
        ('peter', 'B', 20),
        ('mike', 'C', 16)
    ]
>>>
# 对第 3 列排序 (从小到大)
>>> sorted(students, key=lambda student: student[2]) 
[('dave', 'B', 10),
 ('jane', 'A', 12),
 ('john', 'B', 15),
 ('mike', 'C', 16),
 ('ethan', 'C', 20),
 ('peter', 'B', 20)]

# 对第 2 列排序（从小到大），再对第 3 列从大到小排序
>>> sorted(students, key=lambda student: (student[1], -student[2]))
[('jane', 'A', 12),
 ('peter', 'B', 20),
 ('john', 'B', 15),
 ('dave', 'B', 10),
 ('ethan', 'C', 20),
 ('mike', 'C', 16)]
```

如果你想了解更多关于排序的知识，可以参考[此文](https://wiki.python.org/moin/HowTo/Sorting)。

# 小结

- 列表是可变的。
- 列表常用的方法有 index, count, append, extend 等。

# 参考资料

- 《Python 基础教程》
- [HowTo/Sorting - Python Wiki](https://wiki.python.org/moin/HowTo/Sorting)


