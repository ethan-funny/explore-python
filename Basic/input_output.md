# 输入和输出

Python2 提供了 `input`，`raw_input`，`print` 等用于输入输出，但在 Python3 中发生了一些改变，`raw_input` 已经没有了，`input` 的用法发生了变化，`print` 也从原来的语句变成了一个函数。本文将对这两种情况进行介绍。

## 输入

- 首先看 Python2 中的 `raw_input`，它的用法如下：

```
raw_input(prompt)
```

其中，prompt 表示输入提示。`raw_input` 会读取控制台的输入，并返回字符串类型。

让我们看几个例子：

```python
>>> name = raw_input('please enter your name: ')
please enter your name: ethan     # 输入一个字符串
>>> name
'ethan'
>>> type(name)
<type 'str'>
>>>
>>> num = raw_input('please enter your id: ')
please enter your id: 12345       # 输入一个数值
>>> num
'12345'
>>> type(num)
<type 'str'>
>>>
>>> sum = raw_input('please enter a+b: ')
please enter a+b: 3+6             # 输入一个表达式
>>> sum
'3+6'
>>> type(sum)
<type 'str'>
```

可以看到，不管我们输入一个字符串、数值还是表达式，`raw_input` 都直接返回一个字符串。

- 现在看一下 Pythn2 中的 `input`。

`input` 的用法跟 `raw_input` 类似，形式如下：

```python
input(prompt)
```

事实上，`input` 本质上是使用 `raw_input` 实现的，如下：

```python
def input(prompt):
    return (eval(raw_input(prompt)))
```

也就是说，调用 `input` 实际上是通过调用 `raw_input` 再调用 `eval` 函数实现的。

这里的 `eval` 通常用来执行一个字符串表达式，并返回表达式的值，它的基本用法如下：

```
>>> eval('1+2')
3
>>> a = 1
>>> eval('a+9')
10
```

现在，让我们看看 `input` 的用法：

```python
>>> name = input('please input your name: ')
please input your name: ethan         # 输入字符串如果没加引号会出错
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<string>", line 1, in <module>
NameError: name 'ethan' is not defined
>>>
>>> name = input('please input your name: ')
please input your name: 'ethan'       # 添加引号
>>> name
'ethan'
>>>
>>> num = input('please input your id: ')
please input your id: 12345           # 输入数值
>>> num                               # 注意返回的是数值类型，而不是字符串
12345
>>> type(num)
<type 'int'>
>>>
>>> sum = input('please enter a+b: ')  # 输入数字表达式，会对表达式求值
please enter a+b: 3+6
>>> sum
9
>>> type(sum)
<type 'int'>
>>>
>>> sum = input('please enter a+b: ')   # 输入字符串表达式，会字符串进行运算
please enter a+b: '3'+'6'
>>> sum
'36'
```

可以看到，使用 `input` 的时候，如果输入的是字符串，必须使用引号把它们括起来；如果输入的是数值类型，则返回的也是数值类型；如果输入的是表达式，会对表达式进行运算。

- 再来看一下 Python3 中的 `input`。

事实上，Python3 中的 `input` 就是 Python2 中的 `raw_input`，也就是说，原 Python2 中的 `raw_input` 被重命名为 `input` 了。那如果我们想使用原 Python2 的 `input` 功能呢？你可以这样做：

```python
eval(input())
```

也就是说，手动添加 `eval` 函数。

## 输出

Python2 中的 `print` 是一个语句（statement），而 Python3 中的 `print` 是一个函数。

### Python2 中的 print

- 简单输出

使用 `print` 最简单的方式就是直接在 `print` 后面加上数字、字符串、列表等对象，比如：

```python
# Python 2.7.11 (default, Feb 24 2016, 10:48:05)
>>> print 123
123
>>> print 'abc'
abc
>>> x = 10
>>> print x
10
>>> d = {'a': 1, 'b': 2}
>>> print d
{'a': 1, 'b': 2}
>>>
>>> print(123)
123
>>> print('abc')
abc
>>> print(x)
10
>>> print(d)
{'a': 1, 'b': 2}
```

在 Python2 中，使用 `print` 时可以加括号，也可以不加括号。

- 格式化输出

有时，我们需要对输出进行一些格式化，比如限制小数的精度等，直接看几个例子：

```
>>> s = 'hello'
>>> l = len(s)
>>> print('the length of %s is %d' % (s, l))
the length of hello is 5
>>>
>>> pi = 3.14159
>>> print('%10.3f' % pi)     # 字段宽度 10，精度 3
     3.142
>>> print('%010.3f' % pi)    # 用 0 填充空白
000003.142
>>> print('%+f' % pi)        # 显示正负号
+3.141590
```

- 换行输出

print 默认是换行输出的，如果不想换行，可以在末尾加上一个 `,'，比如：

```python
>>> for i in range(0, 3):
...     print i
...
0
1
2
>>> for i in range(0, 3):
...     print i,           # 加了 ,
...
0 1 2                      # 注意会加上一个空格
```

### Python3 中的 print

在 Python3 中使用 print 跟 Python2 差别不大，不过要注意的是在 Python3 中使用 print 必须加括号，否则会抛出 SyntaxError。

另外，如果不想 print 换行输出，可以参考下面的方式：

```python
>>> for i in range(0, 3):
...     print(i)
...
0
1
2
>>> for i in range(0, 3):
...     print(i, end='')    # 加上一个 end 参数
...
012
```

# 小结

- 在 Python2 中，`raw_input` 会读取控制台的输入，并返回字符串类型。
- 在 Python2 中，如无特殊要求建议使用 raw_input() 来与用户交互。
- 在 Python3 中，使用 `input` 处理输入，如有特殊要求，可以考虑加上 `eval`。

# 参考资料

- [python - What's the difference between raw_input() and input() in python3.x? - Stack Overflow](http://stackoverflow.com/questions/4915361/whats-the-difference-between-raw-input-and-input-in-python3-x)

