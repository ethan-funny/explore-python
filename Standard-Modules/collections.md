# collections

我们知道，Python 的数据类型有 list, tuple, dict, str 等，**collections 模块**提供了额外 5 个高性能的数据类型：

- `Counter`: 计数器
- `OrderedDict`: 有序字典
- `defaultdict`: 带有默认值的字典
- `namedtuple`: 生成可以通过属性访问元素内容的 tuple 子类
- `deque`: 双端队列，能够在队列两端添加或删除元素

# Counter

`Counter` 是一个简单的计数器，可用于统计字符串、列表等的元素个数。

看看例子：

```python
>>> from collections import Counter
>>>
>>> s = 'aaaabbbccd'
>>> c = Counter(s)        # 创建了一个 Counter 对象
>>> c                     
Counter({'a': 4, 'b': 3, 'c': 2, 'd': 1})
>>> isinstance(c, dict)   # c 其实也是一个字典对象
True
>>> c.get('a')
4
>>> c.most_common(2)      # 获取出现次数最多的前两个元素
[('a', 4), ('b', 3)]
```

在上面，我们使用 `Counter()` 创建了一个 `Counter` 对象 `c`，`Counter` 其实是 dict 的一个子类，我们可以使用 `get` 方法来获取某个元素的个数。`Counter` 对象有一个 `most_common` 方法，允许我们获取出现次数最多的前几个元素。

另外，两个 `Counter` 对象还可以做运算：

```python
>>> from collections import Counter
>>>
>>> s1 = 'aaaabbbccd'
>>> c1 = Counter(s1)
>>> c1
Counter({'a': 4, 'b': 3, 'c': 2, 'd': 1})
>>>
>>> s2 = 'aaabbef'
>>> c2 = Counter(s2)
>>> c2
Counter({'a': 3, 'b': 2, 'e': 1, 'f': 1})
>>>
>>> c1 + c2       # 两个计数结果相加
Counter({'a': 7, 'b': 5, 'c': 2, 'e': 1, 'd': 1, 'f': 1})
>>> c1 - c2       # c2 相对于 c1 的差集
Counter({'c': 2, 'a': 1, 'b': 1, 'd': 1})
>>> c1 & c2       # c1 和 c2 的交集
Counter({'a': 3, 'b': 2})
>>> c1 | c2       # c1 和 c2 的并集
Counter({'a': 4, 'b': 3, 'c': 2, 'e': 1, 'd': 1, 'f': 1})
```

# OrderedDict

Python 中的 dict 是无序的：

```python
>>> dict([('a', 10), ('b', 20), ('c', 15)])
{'a': 10, 'c': 15, 'b': 20}
```

有时，我们希望保持 key 的顺序，这时可以用 OrderedDict：

```python
>>> from collections import OrderedDict
>>> OrderedDict([('a', 10), ('b', 20), ('c', 15)])
OrderedDict([('a', 10), ('b', 20), ('c', 15)])
```

# defaultdict

在 Python 中使用 dict 时，如果访问了不存在的 key，会抛出 KeyError 异常，因此，在访问之前，我们经常需要对 key 作判断，比如：

```python
>>> d = dict()
>>> s = 'aaabbc'
>>> for char in s:
...     if char in d:
...         d[char] += 1
...     else:
...         d[char] = 1
...
>>> d
{'a': 3, 'c': 1, 'b': 2}
```

使用 defaultdict，我们可以给字典中的 key 提供一个默认值。访问 defaultdict 中的 key，如果 key 存在，就返回 key 对应的 value，如果 key 不存在，就返回默认值。

```python
>>> from collections import defaultdict
>>> d = defaultdict(int)   # 默认的 value 值是 0
>>> s = 'aaabbc'
>>> for char in s:
...     d[char] += 1
...
>>> d
defaultdict(<type 'int'>, {'a': 3, 'c': 1, 'b': 2})
>>> d.get('a')
3
>>> d['z']
0
```

使用 defaultdict 时，我们可以传入一个工厂方法来指定默认值，比如传入 int，表示默认值是 0，传入 list，表示默认是 `[]`：

```python
>>> from collections import defaultdict
>>>
>>> d1 = defaultdict(int)
>>> d1['a']
0
>>> d2 = defaultdict(list)
>>> d2['b']
[]
>>> d3 = defaultdict(str)
>>> d3['a']
''
```

我们还可以自定义默认值，通过 `lambda` 函数来实现：

```python
>>> from collections import defaultdict
>>>
>>> d = defaultdict(lambda: 10)
>>> d['a']
10
```

# namedtuple

我们经常用 tuple (元组) 来表示一个不可变对象，比如用一个 `(姓名, 学号, 年龄)` 的元组来表示一个学生：

```python
>>> stu = ('ethan', '001', 20)
>>> stu[0]
'ethan'
```

这里使用 tuple 没什么问题，但可读性比较差，我们必须清楚索引代表的含义，比如索引 0 表示姓名，索引 1 表示学号。如果用类来定义，就可以通过设置属性 name, id, age 来表示，但就有些小题大作了。

我们可以通过 namedtuple 为元组的每个索引设置名称，然后通过「属性名」来访问：

```python
>>> from collections import namedtuple
>>> Student = namedtuple('Student', ['name', 'id', 'age'])  # 定义了一个 Student 元组
>>>
>>> stu = Student('ethan', '001', 20)
>>> stu.name
'ethan'
>>> stu.id
'001'
```

# deque

deque 是双端队列，允许我们在队列两端添加或删除元素。

```python
>>> from collections import deque

>>> q = deque(['a', 'b', 'c', 'd'])
>>> q.append('e')         # 添加到尾部
>>> q
deque(['a', 'b', 'c', 'd', 'e'])
>>> q.appendleft('o')     # 添加到头部
>>> q
deque(['o', 'a', 'b', 'c', 'd', 'e'])
>>> q.pop()               # 从尾部弹出元素
'e'
>>> q
deque(['o', 'a', 'b', 'c', 'd'])
>>> q.popleft()           # 从头部弹出元素
'o'
>>> q
deque(['a', 'b', 'c', 'd'])
>>> q.extend('ef')        # 在尾部 extend 元素
>>> q
deque(['a', 'b', 'c', 'd', 'e', 'f'])
>>> q.extendleft('uv')    # 在头部 extend 元素，注意顺序
>>> q
deque(['v', 'u', 'a', 'b', 'c', 'd', 'e', 'f'])
>>>
>>> q.rotate(2)           # 将尾部的两个元素移动到头部
>>> q
deque(['e', 'f', 'v', 'u', 'a', 'b', 'c', 'd'])
>>> q.rotate(-2)          # 将头部的两个元素移动到尾部
>>> q
deque(['v', 'u', 'a', 'b', 'c', 'd', 'e', 'f'])
```

其中，`rotate` 方法用于旋转，如果旋转参数 n 大于 0，表示将队列右端的 n 个元素移动到左端，否则相反。


# 参考资料

- [collections — High-performance container datatypes — Python 2.7.13 documentation](https://docs.python.org/2/library/collections.html#module-collections)


