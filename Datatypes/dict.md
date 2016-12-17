# 字典

字典是 Python 中唯一的映射类型，每个元素由键（key）和值（value）构成，键必须是不可变类型，比如数字、字符串和元组。

# 字典基本操作

这里先介绍字典的几个基本操作，后文再介绍字典的常用方法。

- 创建字典
- 遍历字典
- 判断键是否在字典里面

## 创建字典

字典可以通过下面的方式创建：

```python
>>> d0 = {}    # 空字典
>>> d0
{}
>>> d1 = {'name': 'ethan', 'age': 20}
>>> d1
{'age': 20, 'name': 'ethan'}
>>> d1['age'] = 21          # 更新字典
>>> d1
{'age': 21, 'name': 'ethan'}
>>> d2 = dict(name='ethan', age=20)    # 使用 dict 函数
>>> d2
{'age': 20, 'name': 'ethan'}
>>> item = [('name', 'ethan'), ('age', 20)]
>>> d3 = dict(item)
>>> d3
{'age': 20, 'name': 'ethan'}
```

## 遍历字典

遍历字典有多种方式，这里先介绍一些基本的方式，后文会介绍一些高效的遍历方式。

```python
>>> d = {'name': 'ethan', 'age': 20}
>>> for key in d:
...     print '%s: %s' % (key, d[key])
...
age: 20
name: ethan
>>> d['name']
'ethan'
>>> d['age']
20
>>> for key in d:
...     if key == 'name':
...         del d[key]         # 要删除字典的某一项
...
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
RuntimeError: dictionary changed size during iteration
>>>
>>> for key in d.keys():  # python2 应该使用这种方式, python3 使用 list(d.keys())
...     if key == 'name':
...         del d[key]
...
>>> d
{'age': 20}
```

在上面，我们介绍了两种遍历方式：`for key in d` 和 `for key in d.keys()`，如果在遍历的时候，要删除键为 key 的某项，使用第一种方式会抛出 RuntimeError，使用第二种方式则不会。

## 判断键是否在字典里面

有时，我们需要判断某个键是否在字典里面，这时可以用 `in` 进行判断，如下：

```python
>>> d = {'name': 'ethan', 'age': 20}
>>> 'name' in d
True
>>> d['score']             # 访问不存在的键，会抛出 KeyError
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'score'
>>> 'score' in d           # 使用 in 判断 key 是否在字典里面
False
```

# 字典常用方法

字典有自己的一些操作方法，这里只介绍部分常用的方法：

- clear
- copy
- get
- setdefault
- update
- pop
- popitem
- keys/iterkeys
- values/itervalues
- items/iteritems
- fromkeys

## clear

clear 方法用于清空字典中的所有项，这是个原地操作，所以无返回值（或者说是 None）。

看看例子：

```python
>>> d = {'name': 'ethan', 'age': 20}
>>> rv = d.clear()
>>> d
{}
>>> print rv
None
```

再看看一个例子：

```python
>>> d1 = {}
>>> d2 = d1
>>> d2['name'] = 'ethan'
>>> d1
{'name': 'ethan'}
>>> d2
{'name': 'ethan'}
>>> d1 = {}         # d1 变为空字典
>>> d2
{'name': 'ethan'}   # d2 不受影响
```

在上面，d1 和 d2 最初对应同一个字典，而后我们使用 `d1 = {}` 使其变成一个空字典，但此时 d2 不受影响。如果希望 d1 变成空字典之后，d2 也变成空字典，则可以使用 clear 方法：

```python
>>> d1 = {}
>>> d2 = d1
>>> d2['name'] = 'ethan'
>>> d1
{'name': 'ethan'}
>>> d2
{'name': 'ethan'}
>>> d1.clear()      # d1 清空之后，d2 也为空
>>> d1
{}
>>> d2
{}
```

## copy

copy 方法实现的是浅复制（shallow copy）。它具有以下特点：

- 对可变对象的修改保持同步；
- 对不可变对象的修改保持独立；

看看例子：

```python
# name 的值是不可变对象，books 的值是可变对象
>>> d1 = {'name': 'ethan', 'books': ['book1', 'book2', 'book3']}
>>> d2 = d1.copy()
>>> d2['name'] = 'peter'         # d2 对不可变对象的修改不会改变 d1
>>> d2
{'books': ['book1', 'book2', 'book3'], 'name': 'peter'}
>>> d1
{'books': ['book1', 'book2', 'book3'], 'name': 'ethan'}
>>> d2['books'].remove('book2')  # d2 对可变对象的修改会影响 d1
>>> d2
{'books': ['book1', 'book3'], 'name': 'peter'}
>>> d1
{'books': ['book1', 'book3'], 'name': 'ethan'}
>>> d1['books'].remove('book3')  # d1 对可变对象的修改会影响 d2
>>> d1
{'books': ['book1'], 'name': 'ethan'}
>>> d2
{'books': ['book1'], 'name': 'peter'}
```

和浅复制对应的是深复制（deep copy），它会创造出一个副本，跟原来的对象没有关系，可以通过 copy 模块的 deepcopy 函数来实现：

```python
>>> from copy import deepcopy
>>> d1 = {'name': 'ethan', 'books': ['book1', 'book2', 'book3']}
>>> d2 = deepcopy(d1)              # 创造出一个副本
>>>
>>> d2['books'].remove('book2')    # 对 d2 的任何修改不会影响到 d1
>>> d2
{'books': ['book1', 'book3'], 'name': 'ethan'}
>>> d1
{'books': ['book1', 'book2', 'book3'], 'name': 'ethan'}
>>>
>>> d1['books'].remove('book3')    # 对 d1 的任何修改也不会影响到 d2
>>> d1
{'books': ['book1', 'book2'], 'name': 'ethan'}
>>> d2
{'books': ['book1', 'book3'], 'name': 'ethan'}
```

## get

当我们试图访问字典中不存在的项时会出现 KeyError，但使用 get 就可以避免这个问题。

看看例子：

```python
>>> d = {}
>>> d['name']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'name'
>>> print d.get('name')
None
>>> d.get('name', 'ethan')   # 'name' 不存在，使用默认值 'ethan'
'ethan'
>>> d
{}
```

## setdefault

setdefault 方法用于对字典设定键值。使用形式如下：

```
dict.setdefault(key, default=None)
```

看看例子：

```python
>>> d = {}
>>> d.setdefault('name', 'ethan')   # 返回设定的默认值 'ethan'
'ethan'
>>> d                               # d 被更新
{'name': 'ethan'}
>>> d['age'] = 20
>>> d
{'age': 20, 'name': 'ethan'}
>>> d.setdefault('age', 18)         # age 已存在，返回已有的值，不会更新字典
20
>>> d
{'age': 20, 'name': 'ethan'}
```

可以看到，当键不存在的时候，setdefault 返回设定的默认值并且更新字典。当键存在的时候，会返回已有的值，但不会更新字典。

## update

update 方法用于将一个字典添加到原字典，如果存在相同的键则会进行覆盖。

看看例子：

```python
>>> d = {}
>>> d1 = {'name': 'ethan'}
>>> d.update(d1)               # 将字典 d1 添加到 d
>>> d
{'name': 'ethan'}
>>> d2 = {'age': 20}
>>> d.update(d2)               # 将字典 d2 添加到 d
>>> d
{'age': 20, 'name': 'ethan'}
>>> d3 = {'name': 'michael'}   # 将字典 d3 添加到 d，存在相同的 key，则覆盖
>>> d.update(d3)
>>> d
{'age': 20, 'name': 'michael'}
```
## items/iteritems

items 方法将所有的字典项以列表形式返回，这些列表项的每一项都来自于（键，值）。我们也经常使用这个方法来对字典进行遍历。

看看例子：

```python
>>> d = {'name': 'ethan', 'age': 20}
>>> d.items()
[('age', 20), ('name', 'ethan')]
>>> for k, v in d.items():
... 	print '%s: %s' % (k, v)
...
age: 20
name: ethan
```

iteritems 的作用大致相同，但会返回一个迭代器对象而不是列表，同样，我们也可以使用这个方法来对字典进行遍历，而且这也是推荐的做法：

```python
>>> d = {'name': 'ethan', 'age': 20}
>>> d.iteritems()
<dictionary-itemiterator object at 0x109cf2d60>
>>> for k, v in d.iteritems():
... 	print '%s: %s' % (k, v)
...
age: 20
name: ethan
```

## keys/iterkeys

keys 方法将字典的键以列表形式返回，iterkeys 则返回针对键的迭代器。

看看例子：

```python
>>> d = {'name': 'ethan', 'age': 20}
>>> d.keys()
['age', 'name']
>>> d.iterkeys()
<dictionary-keyiterator object at 0x1077fad08>
```

## values/itervalues

values 方法将字典的值以列表形式返回，itervalues 则返回针对值的迭代器。

看看例子：

```python
>>> d = {'name': 'ethan', 'age': 20}
>>> d.values()
[20, 'ethan']
>>> d.itervalues()
<dictionary-valueiterator object at 0x10477dd08>
```

## pop

pop 方法用于将某个键值对从字典移除，并返回给定键的值。

看看例子：

```python
>>> d = {'name': 'ethan', 'age': 20}
>>> d.pop('name')
'ethan'
>>> d
{'age': 20}
```

## popitem

popitem 用于随机移除字典中的某个键值对。

看看例子：

```python
>>> d = {'id': 10, 'name': 'ethan', 'age': 20}
>>> d.popitem()
('age', 20)
>>> d
{'id': 10, 'name': 'ethan'}
>>> d.popitem()
('id', 10)
>>> d
{'name': 'ethan'}
```

# 对元素为字典的列表排序

事实上，我们很少直接对字典进行排序，而是对元素为字典的列表进行排序。

比如，存在下面的 students 列表，它的元素是字典：

```python
students = [
    {'name': 'john', 'score': 'B', 'age': 15},
    {'name': 'jane', 'score': 'A', 'age': 12},
    {'name': 'dave', 'score': 'B', 'age': 10},
    {'name': 'ethan', 'score': 'C', 'age': 20},
    {'name': 'peter', 'score': 'B', 'age': 20},
    {'name': 'mike', 'score': 'C', 'age': 16}
]
```

- 按 score 从小到大排序

```python
>>> sorted(students, key=lambda stu: stu['score'])
[{'age': 12, 'name': 'jane', 'score': 'A'},
 {'age': 15, 'name': 'john', 'score': 'B'},
 {'age': 10, 'name': 'dave', 'score': 'B'},
 {'age': 20, 'name': 'peter', 'score': 'B'},
 {'age': 20, 'name': 'ethan', 'score': 'C'},
 {'age': 16, 'name': 'mike', 'score': 'C'}]
```

需要注意的是，这里是按照字母的 ascii 大小排序的，所以 score 从小到大，即从 'A' 到 'C'。


- 按 score 从大到小排序

```python
>>> sorted(students, key=lambda stu: stu['score'], reverse=True)  # reverse 参数
[{'age': 20, 'name': 'ethan', 'score': 'C'},
 {'age': 16, 'name': 'mike', 'score': 'C'},
 {'age': 15, 'name': 'john', 'score': 'B'},
 {'age': 10, 'name': 'dave', 'score': 'B'},
 {'age': 20, 'name': 'peter', 'score': 'B'},
 {'age': 12, 'name': 'jane', 'score': 'A'}]
```

- 按 score 从小到大，再按 age 从小到大

```python
>>> sorted(students, key=lambda stu: (stu['score'], stu['age']))
[{'age': 12, 'name': 'jane', 'score': 'A'},
 {'age': 10, 'name': 'dave', 'score': 'B'},
 {'age': 15, 'name': 'john', 'score': 'B'},
 {'age': 20, 'name': 'peter', 'score': 'B'},
 {'age': 16, 'name': 'mike', 'score': 'C'},
 {'age': 20, 'name': 'ethan', 'score': 'C'}]
```

- 按 score 从小到大，再按 age 从大到小

```python
>>> sorted(students, key=lambda stu: (stu['score'], -stu['age']))
[{'age': 12, 'name': 'jane', 'score': 'A'},
 {'age': 20, 'name': 'peter', 'score': 'B'},
 {'age': 15, 'name': 'john', 'score': 'B'},
 {'age': 10, 'name': 'dave', 'score': 'B'},
 {'age': 20, 'name': 'ethan', 'score': 'C'},
 {'age': 16, 'name': 'mike', 'score': 'C'}]
```

# 参考资料

- 《Python 基础教程》
- [python字典和集合 | Alex's Blog](http://codingnow.cn/language/353.html)
- [Python中实现多属性排序 | 酷 壳 - CoolShell.cn](http://coolshell.cn/articles/435.html)
- [HowTo/Sorting - Python Wiki](https://wiki.python.org/moin/HowTo/Sorting)
- [How do I sort a list of dictionaries by values of the dictionary in Python? - Stack Overflow](http://stackoverflow.com/questions/72899/how-do-i-sort-a-list-of-dictionaries-by-values-of-the-dictionary-in-python)


