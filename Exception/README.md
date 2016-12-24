# 异常处理

我们在编写程序的时候，经常需要对异常情况做处理。比如，当一个数试图除以 0 时，我们需要捕获这个异常情况并做处理。你可能会使用类似 if/else 的条件语句来对异常情况做判断，比如，判断除法的分母是否为零，如果为零，则打印错误信息。

这在某些简单的情况下是可以的，但是，在大多数时候，我们应该使用 Python 的**异常处理**机制。这主要有两方面的好处：

- 一方面，你可以选择忽略某些不重要的异常事件，或在需要的时候自己引发异常；
- 另一方面，异常处理不会搞乱原来的代码逻辑，但如果使用一大堆 if/else 语句，不仅会没效率和不灵活，而且会让代码相当难读；

# 异常对象

Python 用**异常对象（exception object）**来表示异常情况。当程序在运行过程中遇到错误时，会引发异常。如果异常对象未被处理或捕捉，程序就会用所谓的回溯（Traceback，一种错误信息）终止执行。

比如：

```python
>>> 1/0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: integer division or modulo by zero
```

上面的 `ZeroDivisionError` 就是一个异常类，相应的异常对象就是该类的实例。Python 中所有的异常类都是从 `BaseException` 类派生的，常见的异常类型可以在[这里][1]查看。

# 使用 try/except 捕捉异常

在编写程序的时候，如果我们知道某段代码可能会导致某种异常，而又不希望程序以堆栈跟踪的形式终止，这时我们可以根据需要添加 `try/except` 或者 `try/finally` 语句（或者它们的组合）进行处理。一般来说，有以下使用形式：

```
try...except...
try...except...else...
try...except...else...finally...
try...except...except...else...finally...
try...finally...
```

## 基本形式

捕捉异常的基本形式是 `try...except`。

让我们看看第一个例子：

```python
try:
    x = input('Enter x: ')
    y = input('Enter y: ')
    print x / y
except ZeroDivisionError as e:
    print 'Error:',e

print 'hello world'
```

当 y = 0 时，看看执行结果：

```python
Enter x: 3
Enter y: 0
Error: integer division or modulo by zero
hello world
```

可以看到，我们的程序正确捕获了`除以零`的异常，而且程序没有以堆栈跟踪的形式终止，而是继续执行后面的代码，打印出 'hello world'。

## 多个 except 子句

有时，我们的程序可能会出现多个异常，这时可以用多个 except 子句来处理这种情况。

让我们继续看第一个例子，如果 y 输入的是一个非数字的值，就会产生另外一个异常：

```python
Enter x: 2
Enter y: 'a'        # y 的输入是一个字符
----------------------------------------------------------------------
TypeError                            Traceback (most recent call last)
<ipython-input-209-d4666cfaefb4> in <module>()
      2     x = input('Enter x: ')
      3     y = input('Enter y: ')
----> 4     print x / y
      5 except ZeroDivisionError as e:
      6     print e

TypeError: unsupported operand type(s) for /: 'int' and 'str'
```

可以看到，当 y 输入一个字符 'a' 之后，程序产生了一个 `TypeError` 异常，并且终止，这是因为我们的 except 子句只是捕获了 `ZeroDivisionError` 异常，为了能捕获`TypeError` 异常，我们可以再加一个 except 子句，完整代码如下：

```python
try:
    x = input('Enter x: ')
    y = input('Enter y: ')
    print x / y
except ZeroDivisionError as e:      # 处理 ZeroDivisionError 异常
    print 'ZeroDivisionError:',e
except TypeError as e:              # 处理 TypeError 异常
    print 'TypeError:',e

print 'hello world'
```

当 y 输入 'a' 时，看看执行结果：

```python
Enter x: 3
Enter y: 'a'
TypeError: unsupported operand type(s) for /: 'int' and 'str'
hello world
```

## 捕捉未知异常

事实上，在编写程序的时候，我们很难预料到程序的所有异常情况。比如，对于第一个例子，我们可以预料到一个 `ZeroDivisionError` 异常，如果细心一点，也会预料到一个 `TypeError` 异常，可是，还是有一些其他情况我们没有考虑到，比如在输入 x 的时候，我们直接按回车键，这时又会引发一个异常，程序也会随之挂掉：

```python
Enter x:      # 这里输入回车键
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
  File "<string>", line 0

    ^
SyntaxError: unexpected EOF while parsing
```

那么，我们应该怎么在程序捕获某些难以预料的异常呢？我们在上文说过，Python 中所有的异常类都是从 `BaseException` 类派生的，也就是说，`ZeroDivisionError`、`SyntaxError` 等都是它的子类，因此，对于某些难以预料的异常，我们就可以使用 `BaseException` 来捕获，在大部分情况下，我们也可以使用 `Exception` 来捕获，因为 `Exception` 是大部分异常的父类，可以到[这里][1]查看所有异常类的继承关系。

因此，对于第一个例子，我们可以把程序做一些修改，使其更加健壮：

```python
try:
    x = input('Enter x: ')
    y = input('Enter y: ')
    print x / y
except ZeroDivisionError as e:      # 捕获 ZeroDivisionError 异常
    print 'ZeroDivisionError:',e
except TypeError as e:              # 捕获 TypeError 异常
    print 'TypeError:',e
except BaseException as e:          # 捕获其他异常
    print 'BaseException:',e

print 'hello world'
```

注意到，我们把 `BaseException` 写在了最后一个 except 子句。如果你把它写在了第一个 except 子句，由于 `BaseException` 是所有异常的父类，那么程序的所有异常都会被第一个 except 子句捕获。

## else 子句

我们可以在 except 子句后面加一个 else 子句。当没有异常发生时，会自动执行 else 子句。

对第一个例子，加入 else 子句：

```python
try:
    x = input('Enter x: ')
    y = input('Enter y: ')
    print x / y
except ZeroDivisionError as e:
    print 'ZeroDivisionError:',e
except TypeError as e:
    print 'TypeError:',e
except BaseException as e:
    print 'BaseException:',e
else:
    print 'no error!'

print 'hello world'
```

看看执行结果：

```
Enter x: 6
Enter y: 2
3
no error!
hello world
```

## finally 子句

finally 子句不管有没有出现异常都会被执行。

看看例子：

```python
try:
    x = 1/0
    print x
finally:
    print 'DONE'
```

执行结果：

```
DONE
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
ZeroDivisionError: integer division or modulo by zero
```

再看一个例子：

```python
try:
    x = 1/0
    print x
except ZeroDivisionError as e:
    print 'ZeroDivisionError:',e
finally:
    print 'DONE'
```

执行结果：

```
ZeroDivisionError: integer division or modulo by zero
DONE
```

## 使用 raise 手动引发异常

有时，我们使用 except 捕获了异常，又想把异常抛出去，这时可以使用 raise 语句。

看看例子：

```python
try:
    x = input('Enter x: ')
    y = input('Enter y: ')
    print x / y
except ZeroDivisionError as e:
    print 'ZeroDivisionError:',e
except TypeError as e:
    print 'TypeError:',e
except BaseException as e:
    print 'BaseException:',e
    raise                           # 使用 raise 抛出异常
else:
    print 'no error!'

print 'hello world'
```

运行上面代码，当 x 输入一个回车键时，错误会被打印出来，并被抛出：

```python
Enter x:    # 这里输入回车键
BaseException: unexpected EOF while parsing (<string>, line 0)
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
  File "<string>", line 0

    ^
SyntaxError: unexpected EOF while parsing
```

上面的 raise 语句是不带参数的，它会把当前错误原样抛出。事实上，我们也创建自己的异常类，并抛出自定义的异常。

**创建自定义的异常类需要从 Exception 类继承**，可以间接继承或直接继承，也就是可以继承其他的内建异常类。比如：

```python
# 自定义异常类
class SomeError(Exception):
    pass

try:
    x = input('Enter x: ')
    y = input('Enter y: ')
    print x / y
except ZeroDivisionError as e:
    print 'ZeroDivisionError:',e
except TypeError as e:
    print 'TypeError:',e
except BaseException as e:
    print 'BaseException:',e
    raise SomeError('invalid value')    # 抛出自定义的异常
else:
    print 'no error!'

print 'hello world'
```

运行上面代码，当 x 输入一个回车键时，错误被打印出来，并抛出我们自定义的异常：

```
Enter x: 
BaseException: unexpected EOF while parsing (<string>, line 0)
----------------------------------------------------------------------
SomeError                            Traceback (most recent call last)
<ipython-input-20-66060b472f91> in <module>()
     12 except BaseException as e:
     13     print 'BaseException:',e
---> 14     raise SomeError('invalid value')
     15 else:
     16     print 'no error!'

SomeError: invalid value
```

# 小结

- Python 中所有的异常类都是从 `BaseException` 类派生的。
- 通过 try/except 来捕捉异常，可以使用多个 except 子句来分别处理不同的异常。
- else 子句在主 try 块没有引发异常的情况下被执行。
- finally 子句不管是否发生异常都会被执行。
- 通过继承 Exception 类可以创建自己的异常类。

# 参考资料

- 《Python 基础教程》
- [Python 异常处理](http://www.runoob.com/python/python-exceptions.html)


[1]: https://docs.python.org/2/library/exceptions.html#exception-hierarchy


