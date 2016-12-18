# 读写文本文件

读写文件是最常见的 IO 操作。通常，我们使用 `input` 从控制台读取输入，使用 `print` 将内容输出到控制台。实际上，我们也经常从文件读取输入，将内容写到文件。

# 读文件

在 Python 中，读文件主要分为三个步骤：

- 打开文件
- 读取内容
- 关闭文件

一般使用形式如下：

```python
try:
    f = open('/path/to/file', 'r')    # 打开文件
    data = f.read()                   # 读取文件内容
finally:
    if f:
        f.close()                     # 确保文件被关闭
```

注意到，我们在代码中加了 `try...finally`，这是因为，如果打开和读取文件时出现错误，文件就没有被关闭。为了确保在任何情况下，文件都能被关闭，我们加了 `try...finally`。

上面的代码中，'r' 模式表示读模式，`open` 函数的常用模式主要有：

| ‘r' | 读模式 |
| :-: | :-: |
| ‘w' | 写模式 |
| ‘a' | 追加模式 |
| ‘b' | 二进制模式（可添加到其他模式中使用） |
| ‘+' | 读/写模式（可添加到其他模式中使用） |

上面的读文件做法很繁琐，我们可以使用 Python 的 `with` 语句来帮我们自动调用 `close` 方法：

```python
with open('/path/to/file', 'r') as f:
    data = f.read()
```

可以看到，这种方式很简洁，而且还能在出现异常的情况下自动关闭文件。

通常而言，读取文件有以下几种方式：

- 一次性读取所有内容，使用 `read()` 或 `readlines()`；
- 按字节读取，使用 `read(size)`；
- 按行读取，使用 `readline()`；

## 读取所有内容

读取所有内容可以使用 `read()` 或 `readlines()`。我们在上面已经介绍过 `read()` 了，现在，让我们看看 `readlines()`。

`readlines()` 方法会把文件读入一个字符串列表，在列表中每个字符串就是一行。

假设有一个文件 data.txt，它的文件内容如下（数字之间的间隔符是'\t'）：

```python
10  1   9   9
6   3   2   8
20  10  3   23
1   4   1   10
10  8   6   3
10  2   1   6
```

我们使用 `readlines()` 将文件读入一个字符串列表：

```python
with open('data.txt', 'r') as f:
    lines = f.readlines()
    line_num = len(lines)
    print lines
    print line_num
```

执行结果：

```
['10\t1\t9\t9\n', '6\t3\t2\t8\n', '20\t10\t3\t23\n', '1\t4\t1\t10\n', '10\t8\t6\t3\n', '10\t2\t1\t6']
6
```

可以看到，列表中的每个元素都是一个字符串，刚好对应文件中的每一行。

## 按字节读取

如果文件较小，一次性读取所有内容确实比较方便。但是，如果文件很大，比如有 100G，那就不能一次性读取所有内容了。这时，我们构造一个固定长度的缓冲区，来不断读取文件内容。

看看例子：

```python
with open('path/to/file', 'r') as f:
    while True:
        piece = f.read(1024)        # 每次读取 1024 个字节（即 1 KB）的内容
        if not piece:
            break
        print piece
```

在上面，我们使用 `f.read(1024)` 来每次读取 1024 个字节（1KB） 的文件内容，将其存到 piece，再对 piece 进行处理。

事实上，我们还可以结合 yield 来使用：

```python
def read_in_chunks(file_object, chunk_size=1024):
    """Lazy function (generator) to read a file piece by piece.
    Default chunk size: 1k."""
    while True:
        data = file_object.read(chunk_size)
        if not data:
            break
        yield data

with open('path/to/file', 'r') as f:
    for piece in read_in_chunks(f):
        print piece
```

## 逐行读取

在某些情况下，我们希望逐行读取文件，这时可以使用 `readline()` 方法。

看看例子：

```python
with open('data.txt', 'r') as f:
    while True:
        line = f.readline()     # 逐行读取
        if not line:
            break
        print line,             # 这里加了 ',' 是为了避免 print 自动换行
```

执行结果：

```
10  1   9   9
6   3   2   8
20  10  3   23
1   4   1   10
10  8   6   3
10  2   1   6
```

## 文件迭代器

在 Python 中，**文件对象是可迭代的**，这意味着我们可以直接在 for 循环中使用它们，而且是逐行迭代的，也就是说，效果和 `readline()` 是一样的，而且更简洁。

看看例子：

```python
with open('data.txt', 'r') as f:
    for line in f:
        print line,
```

在上面的代码中，f 就是一个文件迭代器，因此我们可以直接使用 `for line in f`，它是逐行迭代的。

看看执行结果：

```python
10  1   9   9
6   3   2   8
20  10  3   23
1   4   1   10
10  8   6   3
10  2   1   6
```

再看一个例子：

```python
with open(file_path, 'r') as f:
    lines = list(f)
    print lines
```

执行结果：

```python
['10\t1\t9\t9\n', '6\t3\t2\t8\n', '20\t10\t3\t23\n', '1\t4\t1\t10\n', '10\t8\t6\t3\n', '10\t2\t1\t6']
```

可以看到，我们可以对文件迭代器执行和普通迭代器相同的操作，比如上面使用 `list(open(filename))` 将 f 转为一个字符串列表，这样所达到的效果和使用 `readlines` 是一样的。

# 写文件

写文件使用 `write` 方法，如下：

```python
with open('/Users/ethan/data2.txt', 'w') as f:
    f.write('one\n')
    f.write('two')
```

- 如果上述文件已存在，则会清空原内容并覆盖掉；
- 如果上述路径是正确的（比如存在 /Users/ethan 的路径），但是文件不存在（data2.txt 不存在），则会新建一个文件，并写入上述内容；
- 如果上述路径是不正确的（比如将路径写成 /Users/eth ），这时会抛出 IOError；

如果我们想往已存在的文件追加内容，可以使用 'a' 模式，如下：

```python
with open('/Users/ethan/data2.txt', 'a') as f:
    f.write('three\n')
    f.write('four')
```

# 小结

- 推荐使用 with 语句操作文件 IO。
- 如果文件较大，可以按字节读取或按行读取。
- 使用文件迭代器进行逐行迭代。

# 参考资料

- 《Python 基础教程》
- [读写文本数据 — python3-cookbook 2.0.0 文档](http://python3-cookbook.readthedocs.io/zh_CN/latest/c05/p01_read_write_text_data.html)


