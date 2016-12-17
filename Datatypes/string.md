# 字符串

字符串也是一种序列，因此，通用的序列操作，比如索引，分片，加法，乘法等对它同样适用。比如：

```python
>>> s = 'hello, '
>>> s[0]            # 索引
'h'
>>> s[1:3]          # 分片
'el'
>>> s + 'world'     # 加法
'hello, world'
>>> s * 2           # 乘法
'hello, hello, '
```

但需要注意的是，字符串和元组一样，也是不可变的，所以你不能对它进行赋值等操作：

```python
>>> s = 'hello'
>>> s[1] = 'ab'    # 不能对它进行赋值
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
```

除了通用的序列操作，字符串还有自己的方法，比如 join, lower, upper 等。字符串的方法特别多，这里只介绍一些常用的方法，如下：

- find
- split
- join
- strip
- replace
- translate
- lower/upper

# find

find 方法用于在一个字符串中查找子串，它返回子串所在位置的最左端索引，如果没有找到，则返回 -1。

看看例子：

```python
>>> motto = "to be or not to be, that is a question"
>>> motto.find('be')            # 返回 'b' 所在的位置，即 3
3
>>> motto.find('be', 4)         # 指定从起始位置开始找，找到的是第 2 个 'be'
16
>>> motto.find('be', 4, 7)      # 指定起始位置和终点位置，没有找到，返回 -1
-1
```

# split

split 方法用于将字符串分割成序列。

看看例子：

```python
>>> '/user/bin/ssh'.split('/')         # 使用 '/' 作为分隔符
['', 'user', 'bin', 'ssh']
>>> '1+2+3+4+5'.split('+')             # 使用 '+' 作为分隔符
['1', '2', '3', '4', '5']
>>> 'that    is a   question'.split()  # 没有提供分割符，默认使用所有空格作为分隔符
['that', 'is', 'a', 'question']
```

需要注意的是，如果不提供分隔符，则默认会使用所有空格作为分隔符（空格、制表符、换行等）。

# join

join 方法可以说是 split 的逆方法，它用于将序列中的元素连接起来。

看看例子：

```python
>>> '/'.join(['', 'user', 'bin', 'ssh'])
'/user/bin/ssh'
>>>
>>> '+'.join(['1', '2', '3', '4', '5'])
'1+2+3+4+5'
>>> ' '.join(['that', 'is', 'a', 'question'])
'that is a question'
>>> ''.join(['h', 'e', 'll', 'o'])
'hello'
>>> '+'.join([1, 2, 3, 4, 5])          # 不能是数字
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: sequence item 0: expected string, int found
```

# strip

strip 方法用于移除字符串左右两侧的空格，但不包括内部，当然也可以指定需要移除的字符串。

看看例子：

```python
>>> '  hello world!   '.strip()                # 移除左右两侧空格
'hello world!'
>>> '%%%   hello world!!!  ####'.strip('%#')   # 移除左右两侧的 '%' 或 '#'
'   hello world!!!  '
>>> '%%%   hello world!!!  ####'.strip('%# ')  # 移除左右两侧的 '%' 或 '#' 或空格
'hello world!!!'
```

# replace

replace 方法用于替换字符串中的**所有**匹配项。

看看例子：

```python
>>> motto = 'To be or not To be, that is a question'
>>> motto.replace('To', 'to')        # 用 'to' 替换所有的 'To'，返回了一个新的字符串
'to be or not to be, that is a question'
>>> motto                            # 原字符串保持不变
'To be or not To be, that is a question'
```

# translate

translate 方法和 replace 方法类似，也可以用于替换字符串中的某些部分，但 **translate 方法只处理单个字符**。

translate 方法的使用形式如下：

```python
str.translate(table[, deletechars]);
```

其中，table 是一个包含 256 个字符的转换表，可通过 maketrans 方法转换而来，deletechars 是字符串中要过滤的字符集。

看看例子：

```python
>>> from string import maketrans
>>> table = maketrans('aeiou', '12345')
>>> motto = 'to be or not to be, that is a question'
>>> motto.translate(table)
't4 b2 4r n4t t4 b2, th1t 3s 1 q52st34n'
>>> motto
'to be or not to be, that is a question'
>>> motto.translate(table, 'rqu')        # 移除所有的 'r', 'q', 'u'
't4 b2 4 n4t t4 b2, th1t 3s 1 2st34n'
```

可以看到，maketrans 接收两个参数：两个等长的字符串，表示第一个字符串的每个字符用第二个字符串对应位置的字符替代，在上面的例子中，就是 'a' 用 '1' 替代，'e' 用 '2' 替代，等等，注意，是单个字符的代替，而不是整个字符串的替代。因此，motto 中的 o 都被替换为 4，e 都被替换为 2，等等。

# lower/upper

lower/upper 用于返回字符串的大写或小写形式。

看看例子：

```python
>>> x = 'PYTHON'
>>> x.lower()
'python'
>>> x
'PYTHON'
>>>
>>> y = 'python'
>>> y.upper()
'PYTHON'
>>> y
'python'
```

# 小结

- 字符串是不可变对象，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回。
- translate 针对单个字符进行替换。

# 参考资料

- 《python 基础教程》


