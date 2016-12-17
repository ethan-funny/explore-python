# 集合

集合（set）和字典（dict）类似，它是一组 key 的集合，但不存储 value。集合的特性就是：key 不能重复。

# 集合常用操作

## 创建集合

set 的创建可以使用 `{}` 也可以使用 set 函数：

```python
>>> s1 = {'a', 'b', 'c', 'a', 'd', 'b'}   # 使用 {}
>>> s1
set(['a', 'c', 'b', 'd'])
>>>
>>> s2 = set('helloworld')                # 使用 set()，接收一个字符串
>>> s2
set(['e', 'd', 'h', 'l', 'o', 'r', 'w'])
>>>
>>> s3 = set(['.mp3', '.mp4', '.rmvb', '.mkv', '.mp3'])   # 使用 set()，接收一个列表
>>> s3
set(['.mp3', '.mkv', '.rmvb', '.mp4'])
```

## 遍历集合

```python
>>> s = {'a', 'b', 'c', 'a', 'd', 'b'}
>>> for e in s:
... 	print e
...
a
c
b
d
```

## 添加元素

`add()` 方法可以将元素添加到 set 中，可以重复添加，但没有效果。

```python
>>> s = {'a', 'b', 'c', 'a', 'd', 'b'}
>>> s
set(['a', 'c', 'b', 'd'])
>>> s.add('e')
>>> s
set(['a', 'c', 'b', 'e', 'd'])
>>> s.add('a')
>>> s
set(['a', 'c', 'b', 'e', 'd'])
>>> s.add(4)
>>> s
set(['a', 'c', 'b', 4, 'd', 'e'])
```

## 删除元素

`remove()` 方法可以删除集合中的元素。

看看例子：

```python
>>> s = {'a', 'b', 'c', 'a', 'd', 'b'}
>>> s
set(['a', 'c', 'b', 'd'])
>>> s.remove('a')           # 删除元素 'a'
>>> s
set(['c', 'b', 'd'])
>>> s.remove('e')           # 删除不存在的元素，会抛出 KeyErro
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'e'
```

## 交集/并集/差集

Python 中的集合也可以看成是数学意义上的无序和无重复元素的集合，因此，我们可以对两个集合作交集、并集等。

看看例子：

```python
>>> s1 = {1, 2, 3, 4, 5, 6}
>>> s2 = {3, 6, 9, 10, 12}
>>> s3 = {2, 3, 4}
>>> s1 & s2            # 交集
set([3, 6])
>>> s1 | s2            # 并集
set([1, 2, 3, 4, 5, 6, 9, 10, 12])
>>> s1 - s2            # 差集
set([1, 2, 4, 5])
>>> s3.issubset(s1)    # s3 是否是 s1 的子集
True
>>> s3.issubset(s2)    # s3 是否是 s2 的子集
False
>>> s1.issuperset(s3)  # s1 是否是 s3 的超集
True
>>> s1.issuperset(s2)  # s1 是否是 s2 的超集
False
```

# 参考资料

- [使用dict和set - 廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/0013868193482529754158abf734c00bba97c87f89a263b000)
- [python字典和集合 | Alex's Blog](http://codingnow.cn/language/353.html)
- [python - Why is it possible to replace set() with {}? - Stack Overflow](http://stackoverflow.com/questions/36674083/why-is-it-possible-to-replace-set-with)


