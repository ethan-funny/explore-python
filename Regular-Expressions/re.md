# re 模块

在 Python 中，我们可以使用内置的 re 模块来使用正则表达式。

有一点需要特别注意的是，正则表达式使用 `\` 对特殊字符进行转义，比如，为了匹配字符串 'python.org'，我们需要使用正则表达式 `'python\.org'`，而 Python 的字符串本身也用 `\` 转义，所以上面的正则表达式在 Python 中应该写成 `'python\\.org'`，这会很容易陷入 `\` 的困扰中，因此，我们建议使用 Python 的原始字符串，只需加一个 r 前缀，上面的正则表达式可以写成：

```
r'python\.org'
```

re 模块提供了不少有用的函数，用以匹配字符串，比如：

- compile 函数
- match 函数
- search 函数
- findall 函数
- finditer 函数
- split 函数
- sub 函数
- subn 函数

re 模块的一般使用步骤如下：

- 使用 compile 函数将正则表达式的字符串形式编译为一个 Pattern 对象
- 通过 Pattern 对象提供的一系列方法对文本进行匹配查找，获得匹配结果（一个 Match 对象）
- 最后使用 Match 对象提供的属性和方法获得信息，根据需要进行其他的操作

# compile 函数

**compile 函数用于编译正则表达式，生成一个 Pattern 对象**，它的一般使用形式如下：

```
re.compile(pattern[, flag])
```

其中，pattern 是一个字符串形式的正则表达式，flag 是一个可选参数，表示匹配模式，比如忽略大小写，多行模式等。

下面，让我们看看例子。

```python
import re

# 将正则表达式编译成 Pattern 对象 
pattern = re.compile(r'\d+')
```

在上面，我们已将一个正则表达式编译成 Pattern 对象，接下来，我们就可以利用 pattern 的一系列方法对文本进行匹配查找了。Pattern 对象的一些常用方法主要有：

- match 方法
- search 方法
- findall 方法
- finditer 方法
- split 方法
- sub 方法
- subn 方法

## match 方法

match 方法用于查找字符串的头部（也可以指定起始位置），它是一次匹配，只要找到了一个匹配的结果就返回，而不是查找所有匹配的结果。它的一般使用形式如下：

```
match(string[, pos[, endpos]])
```

其中，string 是待匹配的字符串，pos 和 endpos 是可选参数，指定字符串的起始和终点位置，默认值分别是 0 和 len (字符串长度)。因此，**当你不指定 pos 和 endpos 时，match 方法默认匹配字符串的头部**。

当匹配成功时，返回一个 Match 对象，如果没有匹配上，则返回 None。

看看例子。

```python
>>> import re
>>> pattern = re.compile(r'\d+')                    # 用于匹配至少一个数字
>>> m = pattern.match('one12twothree34four')        # 查找头部，没有匹配
>>> print m
None
>>> m = pattern.match('one12twothree34four', 2, 10) # 从'e'的位置开始匹配，没有匹配
>>> print m
None
>>> m = pattern.match('one12twothree34four', 3, 10) # 从'1'的位置开始匹配，正好匹配
>>> print m                                         # 返回一个 Match 对象
<_sre.SRE_Match object at 0x10a42aac0>
>>> m.group(0)   # 可省略 0
'12'
>>> m.start(0)   # 可省略 0
3
>>> m.end(0)     # 可省略 0
5
>>> m.span(0)    # 可省略 0
(3, 5)
```

在上面，当匹配成功时返回一个 Match 对象，其中：

- `group([group1, …])` 方法用于获得一个或多个分组匹配的字符串，当要获得整个匹配的子串时，可直接使用 `group()` 或 `group(0)`；
- `start([group])` 方法用于获取分组匹配的子串在整个字符串中的起始位置（子串第一个字符的索引），参数默认值为 0；
- `end([group])` 方法用于获取分组匹配的子串在整个字符串中的结束位置（子串最后一个字符的索引+1），参数默认值为 0；
- `span([group])` 方法返回 `(start(group), end(group))`。

再看看一个例子：

```python
>>> import re
>>> pattern = re.compile(r'([a-z]+) ([a-z]+)', re.I)   # re.I 表示忽略大小写
>>> m = pattern.match('Hello World Wide Web')
>>> print m                               # 匹配成功，返回一个 Match 对象
<_sre.SRE_Match object at 0x10bea83e8>
>>> m.group(0)                            # 返回匹配成功的整个子串
'Hello World'
>>> m.span(0)                             # 返回匹配成功的整个子串的索引
(0, 11)
>>> m.group(1)                            # 返回第一个分组匹配成功的子串
'Hello'
>>> m.span(1)                             # 返回第一个分组匹配成功的子串的索引
(0, 5)
>>> m.group(2)                            # 返回第二个分组匹配成功的子串
'World'
>>> m.span(2)                             # 返回第二个分组匹配成功的子串
(6, 11)
>>> m.groups()                            # 等价于 (m.group(1), m.group(2), ...)
('Hello', 'World')
>>> m.group(3)                            # 不存在第三个分组
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: no such group
```

## search 方法

search 方法用于查找字符串的任何位置，它也是一次匹配，只要找到了一个匹配的结果就返回，而不是查找所有匹配的结果，它的一般使用形式如下：

```
search(string[, pos[, endpos]])
```

其中，string 是待匹配的字符串，pos 和 endpos 是可选参数，指定字符串的起始和终点位置，默认值分别是 0 和 len (字符串长度)。

当匹配成功时，返回一个 Match 对象，如果没有匹配上，则返回 None。

让我们看看例子：

```python
>>> import re
>>> pattern = re.compile('\d+')
>>> m = pattern.search('one12twothree34four')  # 这里如果使用 match 方法则不匹配
>>> m
<_sre.SRE_Match object at 0x10cc03ac0>
>>> m.group()
'12'
>>> m = pattern.search('one12twothree34four', 10, 30)  # 指定字符串区间
>>> m
<_sre.SRE_Match object at 0x10cc03b28>
>>> m.group()
'34'
>>> m.span()
(13, 15)
```

再来看一个例子：

```python
# -*- coding: utf-8 -*-

import re
 
# 将正则表达式编译成 Pattern 对象
pattern = re.compile(r'\d+') 
 
# 使用 search() 查找匹配的子串，不存在匹配的子串时将返回 None 
# 这里使用 match() 无法成功匹配 
m = pattern.search('hello 123456 789') 
 
if m: 
    # 使用 Match 获得分组信息 
    print 'matching string:',m.group()
    print 'position:',m.span()
```

执行结果：

```
matching string: 123456
position: (6, 12)
```

## findall 方法

上面的 match 和 search 方法都是一次匹配，只要找到了一个匹配的结果就返回。然而，在大多数时候，我们需要搜索整个字符串，获得所有匹配的结果。

findall 方法的使用形式如下：

```
findall(string[, pos[, endpos]])
```

其中，string 是待匹配的字符串，pos 和 endpos 是可选参数，指定字符串的起始和终点位置，默认值分别是 0 和 len (字符串长度)。

findall 以列表形式返回全部能匹配的子串，如果没有匹配，则返回一个空列表。

看看例子：

```python
import re
 
pattern = re.compile(r'\d+')   # 查找数字
result1 = pattern.findall('hello 123456 789')
result2 = pattern.findall('one1two2three3four4', 0, 10)
 
print result1
print result2
```

执行结果：

```
['123456', '789']
['1', '2']
```

## finditer 方法

finditer 方法的行为跟 findall 的行为类似，也是搜索整个字符串，获得所有匹配的结果。但它返回一个顺序访问每一个匹配结果（Match 对象）的迭代器。

看看例子：

```python
# -*- coding: utf-8 -*-

import re
 
pattern = re.compile(r'\d+')

result_iter1 = pattern.finditer('hello 123456 789')
result_iter2 = pattern.finditer('one1two2three3four4', 0, 10)

print type(result_iter1)
print type(result_iter2)

print 'result1...'
for m1 in result_iter1:   # m1 是 Match 对象
    print 'matching string: {}, position: {}'.format(m1.group(), m1.span())

print 'result2...'
for m2 in result_iter2:
    print 'matching string: {}, position: {}'.format(m2.group(), m2.span())
```

执行结果：

```
<type 'callable-iterator'>
<type 'callable-iterator'>
result1...
matching string: 123456, position: (6, 12)
matching string: 789, position: (13, 16)
result2...
matching string: 1, position: (3, 4)
matching string: 2, position: (7, 8)
```

## split 方法

split 方法按照能够匹配的子串将字符串分割后返回列表，它的使用形式如下：

```
split(string[, maxsplit])
```

其中，maxsplit 用于指定最大分割次数，不指定将全部分割。 

看看例子：

```python
import re
 
p = re.compile(r'[\s\,\;]+')
print p.split('a,b;; c   d')
```

执行结果：

```
['a', 'b', 'c', 'd']
```

## sub 方法

sub 方法用于替换。它的使用形式如下：

```
sub(repl, string[, count])
```

其中，repl 可以是字符串也可以是一个函数：

- 如果 repl 是字符串，则会使用 repl 去替换字符串每一个匹配的子串，并返回替换后的字符串，另外，repl 还可以使用 `\id` 的形式来引用分组，但不能使用编号 0；
- 如果 repl 是函数，这个方法应当只接受一个参数（Match 对象），并返回一个字符串用于替换（返回的字符串中不能再引用分组）。 

count 用于指定最多替换次数，不指定时全部替换。 

看看例子：

```python
import re
 
p = re.compile(r'(\w+) (\w+)')
s = 'hello 123, hello 456'

def func(m):
    return 'hi' + ' ' + m.group(2)

print p.sub(r'hello world', s)  # 使用 'hello world' 替换 'hello 123' 和 'hello 456'
print p.sub(r'\2 \1', s)        # 引用分组
print p.sub(func, s)
print p.sub(func, s, 1)         # 最多替换一次
```

执行结果：

```
hello world, hello world
123 hello, 456 hello
hi 123, hi 456
hi 123, hello 456
```

## subn 方法

subn 方法跟 sub 方法的行为类似，也用于替换。它的使用形式如下：

```
subn(repl, string[, count])
```

它返回一个元组：

```
(sub(repl, string[, count]), 替换次数)
```

元组有两个元素，第一个元素是使用 sub 方法的结果，第二个元素返回原字符串被替换的次数。

看看例子：

```
import re
 
p = re.compile(r'(\w+) (\w+)')
s = 'hello 123, hello 456'

def func(m):
    return 'hi' + ' ' + m.group(2)

print p.subn(r'hello world', s)
print p.subn(r'\2 \1', s)
print p.subn(func, s)
print p.subn(func, s, 1)
```

执行结果：

```
('hello world, hello world', 2)
('123 hello, 456 hello', 2)
('hi 123, hi 456', 2)
('hi 123, hello 456', 1)
```

# 其他函数

事实上，使用 compile 函数生成的 Pattern 对象的一系列方法跟 re 模块的多数函数是对应的，但在使用上有细微差别。

## match 函数

match 函数的使用形式如下：

```
re.match(pattern, string[, flags]):
```

其中，pattern 是正则表达式的字符串形式，比如 `\d+`, `[a-z]+`。

而 Pattern 对象的 match 方法使用形式是：

```
match(string[, pos[, endpos]])
```

可以看到，match 函数不能指定字符串的区间，它只能搜索头部，看看例子：

```python
import re

m1 = re.match(r'\d+', 'One12twothree34four')
if m1:
    print 'matching string:',m1.group()
else:
    print 'm1 is:',m1
    
m2 = re.match(r'\d+', '12twothree34four')
if m2:
    print 'matching string:', m2.group()
else:
    print 'm2 is:',m2
```

执行结果：

```
m1 is: None
matching string: 12
```

## search 函数

search 函数的使用形式如下：

```
re.search(pattern, string[, flags])
```

search 函数不能指定字符串的搜索区间，用法跟 Pattern 对象的 search 方法类似。

## findall 函数

findall 函数的使用形式如下：

```
re.findall(pattern, string[, flags])
```

findall 函数不能指定字符串的搜索区间，用法跟 Pattern 对象的 findall 方法类似。

看看例子：

```python
import re

print re.findall(r'\d+', 'hello 12345 789')

# 输出
['12345', '789']
```

## finditer 函数

finditer 函数的使用方法跟 Pattern 的 finditer 方法类似，形式如下：

```
re.finditer(pattern, string[, flags])
```

## split 函数

split 函数的使用形式如下：

```
re.split(pattern, string[, maxsplit])
```

## sub 函数

sub 函数的使用形式如下：

```
re.sub(pattern, repl, string[, count])
```

## subn 函数

subn 函数的使用形式如下：

```
re.subn(pattern, repl, string[, count])
```

# 到底用哪种方式

从上文可以看到，使用 re 模块有两种方式：

- 使用 re.compile 函数生成一个 Pattern 对象，然后使用 Pattern 对象的一系列方法对文本进行匹配查找；
- 直接使用 re.match, re.search 和 re.findall 等函数直接对文本匹配查找；

下面，我们用一个例子展示这两种方法。

先看第 1 种用法：

```python
import re

# 将正则表达式先编译成 Pattern 对象
pattern = re.compile(r'\d+')

print pattern.match('123, 123')
print pattern.search('234, 234')
print pattern.findall('345, 345')
```

再看第 2 种用法：

```python
import re

print re.match(r'\d+', '123, 123')
print re.search(r'\d+', '234, 234')
print re.findall(r'\d+', '345, 345')
```

如果一个正则表达式需要用到多次（比如上面的 `\d+`），在多种场合经常需要被用到，出于效率的考虑，我们应该预先编译该正则表达式，生成一个 Pattern 对象，再使用该对象的一系列方法对需要匹配的文件进行匹配；而如果直接使用 re.match, re.search 等函数，每次传入一个正则表达式，它都会被编译一次，效率就会大打折扣。

因此，我们推荐使用第 1 种用法。

# 匹配中文

在某些情况下，我们想匹配文本中的汉字，有一点需要注意的是，[中文的 unicode 编码范围](http://blog.oasisfeng.com/2006/10/19/full-cjk-unicode-range/) 主要在 `[\u4e00-\u9fa5]`，这里说主要是因为这个范围并不完整，比如没有包括全角（中文）标点，不过，在大部分情况下，应该是够用的。

假设现在想把字符串 `title = u'你好，hello，世界'` 中的中文提取出来，可以这么做：

```python
# -*- coding: utf-8 -*-

import re

title = u'你好，hello，世界'
pattern = re.compile(ur'[\u4e00-\u9fa5]+')
result = pattern.findall(title)

print result
```

注意到，我们在正则表达式前面加上了两个前缀 `ur`，其中 `r` 表示使用原始字符串，`u` 表示是 unicode 字符串。

执行结果:

```
[u'\u4f60\u597d', u'\u4e16\u754c']
```

# 贪婪匹配

在 Python 中，正则匹配默认是**贪婪匹配**（在少数语言中可能是非贪婪），也就是**匹配尽可能多的字符**。

比如，我们想找出字符串中的所有 `div` 块：

```python
import re

content = 'aa<div>test1</div>bb<div>test2</div>cc'
pattern = re.compile(r'<div>.*</div>')
result = pattern.findall(content)

print result
```

执行结果：

```
['<div>test1</div>bb<div>test2</div>']
```

由于正则匹配是贪婪匹配，也就是尽可能多的匹配，因此，在成功匹配到第一个 `</div>` 时，它还会向右尝试匹配，查看是否还有更长的可以成功匹配的子串。

如果我们想非贪婪匹配，可以加一个 `?`，如下：

```python
import re

content = 'aa<div>test1</div>bb<div>test2</div>cc'
pattern = re.compile(r'<div>.*?</div>')    # 加上 ?
result = pattern.findall(content)

print result
```

结果：

```
['<div>test1</div>', '<div>test2</div>']
```

# 小结

- re 模块的一般使用步骤如下：
    - 使用 compile 函数将正则表达式的字符串形式编译为一个 Pattern 对象；
    - 通过 Pattern 对象提供的一系列方法对文本进行匹配查找，获得匹配结果（一个 Match 对象）；
    - 最后使用 Match 对象提供的属性和方法获得信息，根据需要进行其他的操作；
- Python 的正则匹配默认是贪婪匹配。

# 参考资料

- [Python正则表达式指南](http://www.cnblogs.com/huxi/archive/2010/07/04/1771073.html)
- [正则表达式 - 廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386832260566c26442c671fa489ebc6fe85badda25cd000)


