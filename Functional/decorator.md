# 装饰器

我们知道，在 Python 中，我们可以像使用变量一样使用函数：

- 函数可以被赋值给其他变量
- 函数可以被删除
- 可以在函数里面再定义函数
- 函数可以作为参数传递给另外一个函数
- 函数可以作为另一个函数的返回

**简而言之，函数就是一个对象**。

# 对一个简单的函数进行装饰

为了更好地理解装饰器，我们先从一个简单的例子开始，假设有下面的函数：

```python
def hello():
    return 'hello world'
```

现在我们想增强 `hello()` 函数的功能，希望给返回加上 HTML 标签，比如 `<i>hello world</i>`，但是有一个要求，不改变原来 `hello()` 函数的定义。这里当然有很多种方法，下面给出一种跟本文相关的方法：

```python
def makeitalic(func):
    def wrapped():
        return "<i>" + func() + "</i>"
    return wrapped
```

在上面的代码中，我们定义了一个函数 `makeitalic`，该函数有一个参数 `func`，它是一个函数；在 `makeitalic` 函数里面我们又定义了一个内部函数 `wrapped`，并将该函数作为返回。

现在，我们就可以不改变 `hello()` 函数的定义，给返回加上 HTML 标签了：

```python
>>> hello = makeitalic(hello)  # 将 hello 函数传给 makeitalic
>>> hello()
'<i>hello world</i>'
```

在上面，我们将 `hello` 函数传给 `makeitalic`，再将返回赋给 `hello`，此时调用 `hello()` 就得到了我们想要的结果。

不过要注意的是，由于我们将 `makeitalic` 的返回赋给了 `hello`，此时 `hello()` 函数仍然存在，但是它的函数名不再是 hello 了，而是 wrapped，正是 `makeitalic` 返回函数的名称，可以验证一下：

```python
>>> hello.__name__
'wrapped'
```

对于这个小瑕疵，后文将会给出解决方法。

现在，我们梳理一下上面的例子，为了增强原函数 `hello` 的功能，我们定义了一个函数，它接收原函数作为参数，并返回一个新的函数，完整的代码如下：

```python
def makeitalic(func):
    def wrapped():
        return "<i>" + func() + "</i>"
    return wrapped

def hello():
    return 'hello world'

hello = makeitalic(hello)
```

事实上，`makeitalic` 就是一个**装饰器（decorator）**，它『装饰』了函数 `hello`，并返回一个函数，将其赋给 `hello`。

一般情况下，我们使用装饰器提供的 `@` 语法糖（Syntactic Sugar），来简化上面的写法：

```python
def makeitalic(func):
    def wrapped():
        return "<i>" + func() + "</i>"
    return wrapped

@makeitalic
def hello():
    return 'hello world'
```

像上面的情况，可以动态修改函数（或类）功能的函数就是装饰器。**本质上，它是一个高阶函数，以被装饰的函数（比如上面的 hello）为参数，并返回一个包装后的函数（比如上面的 wrapped）给被装饰函数（hello）**。

# 装饰器的使用形式

- 装饰器的一般使用形式如下：

```python
@decorator
def func():
    pass
```

等价于下面的形式：

```python
def func():
    pass
func = decorator(func)
```

- 装饰器可以定义多个，离函数定义最近的装饰器先被调用，比如：

```python
@decorator_one
@decorator_two
def func():
    pass
```

等价于：

```python
def func():
    pass

func = decorator_one(decorator_two(func))
```

- 装饰器还可以带参数，比如：

```python
@decorator(arg1, arg2)
def func():
    pass
```

等价于：

```python
def func():
    pass

func = decorator(arg1, arg2)(func)
```

下面我们再看一些具体的例子，以加深对它的理解。

# 对带参数的函数进行装饰

前面的例子中，被装饰的函数 `hello()` 是没有带参数的，我们看看被装饰函数带参数的情况。对前面例子中的 `hello()` 函数进行改写，使其带参数，如下：

```python
def makeitalic(func):
    def wrapped(*args, **kwargs):
        ret = func(*args, **kwargs)
        return '<i>' + ret + '</i>'
    return wrapped

@makeitalic
def hello(name):
    return 'hello %s' % name
    
@makeitalic
def hello2(name1, name2):
    return 'hello %s, %s' % (name1, name2)
```

由于函数 `hello` 带参数，因此内嵌包装函数 `wrapped` 也做了一点改变：

- 内嵌包装函数的参数传给了 `func`，即被装饰函数，也就是说内嵌包装函数的参数跟被装饰函数的参数对应，这里使用了 `(*args, **kwargs)`，是为了适应可变参数。

看看使用：

```python
>>> hello('python')
'<i>hello python</i>'
>>> hello2('python', 'java')
'<i>hello python, java</i>'
```

# 带参数的装饰器

上面的例子，我们增强了函数 `hello` 的功能，给它的返回加上了标签 `<i>...</i>`，现在，我们想改用标签 `<b>...</b>` 或 `<p>...</p>`。是不是要像前面一样，再定义一个类似 `makeitalic` 的装饰器呢？其实，我们可以定义一个函数，将标签作为参数，返回一个装饰器，比如：

```python
def wrap_in_tag(tag):
    def decorator(func):
        def wrapped(*args, **kwargs):
            ret = func(*args, **kwargs)
            return '<' + tag + '>' + ret + '</' + tag + '>'
        return wrapped

    return decorator
```

现在，我们可以根据需要生成想要的装饰器了：

```python
makebold = wrap_in_tag('b')  # 根据 'b' 返回 makebold 生成器

@makebold
def hello(name):
    return 'hello %s' % name
    
>>> hello('world')
'<b>hello world</b>'
```

上面的形式也可以写得更加简洁：

```python
@wrap_in_tag('b')
def hello(name):
    return 'hello %s' % name
```

这就是带参数的装饰器，其实就是在装饰器外面多了一层包装，根据不同的参数返回不同的装饰器。

# 多个装饰器

现在，让我们来看看多个装饰器的例子，为了简单起见，下面的例子就不使用带参数的装饰器。

```python
def makebold(func):
    def wrapped():
        return '<b>' + func() + '</b>'

    return wrapped

def makeitalic(func):
    def wrapped():
        return '<i>' + func() + '</i>'

    return wrapped

@makebold
@makeitalic
def hello():
    return 'hello world'
```

上面定义了两个装饰器，对 `hello` 进行装饰，上面的最后几行代码相当于：

```python
def hello():
    return 'hello world'

hello = makebold(makeitalic(hello))
```

调用函数 `hello`：

```python
>>> hello()
'<b><i>hello world</i></b>'
```

# 基于类的装饰器

前面的装饰器都是一个函数，其实也可以基于类定义装饰器，看下面的例子：

```python
class Bold(object):
    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwargs):
        return '<b>' + self.func(*args, **kwargs) + '</b>'

@Bold
def hello(name):
    return 'hello %s' % name

>>> hello('world')
'<b>hello world</b>'
```

可以看到，类 `Bold` 有两个方法：

- `__init__()`：它接收一个函数作为参数，也就是被装饰的函数
- `__call__()`：让类对象可调用，就像函数调用一样，在调用被装饰函数时被调用

还可以让类装饰器带参数：

```python
class Tag(object):
    def __init__(self, tag):
        self.tag = tag

    def __call__(self, func):
        def wrapped(*args, **kwargs):
            return "<{tag}>{res}</{tag}>".format(
                res=func(*args, **kwargs), tag=self.tag
            )
        return wrapped

@Tag('b')
def hello(name):
    return 'hello %s' % name
```

需要注意的是，如果类装饰器有参数，则 `__init__` 接收参数，而 `__call__` 接收 `func`。

# 装饰器的副作用

前面提到，使用装饰器有一个瑕疵，就是被装饰的函数，它的函数名称已经不是原来的名称了，回到最开始的例子：

```python
def makeitalic(func):
    def wrapped():
        return "<i>" + func() + "</i>"
    return wrapped

@makeitalic
def hello():
    return 'hello world'
```

函数 `hello` 被 `makeitalic` 装饰后，它的函数名称已经改变了：

```python
>>> hello.__name__
'wrapped'
```

为了消除这样的副作用，Python 中的 functools 包提供了一个 wraps 的装饰器：

```python
from functools import wraps

def makeitalic(func):
    @wraps(func)       # 加上 wraps 装饰器
    def wrapped():
        return "<i>" + func() + "</i>"
    return wrapped

@makeitalic
def hello():
    return 'hello world'

>>> hello.__name__
'hello'
```

# 小结

- 本质上，装饰器就是一个返回函数的高阶函数。
- 装饰器可以动态地修改一个类或函数的功能，通过在原有的类或者函数上包裹一层修饰类或修饰函数实现。
- 事实上，装饰器就是闭包的一种应用，但它比较特别，接收被装饰函数为参数，并返回一个函数，赋给被装饰函数，闭包则没这种限制。

# 参考资料

- [Python修饰器的函数式编程 - coolshell](http://coolshell.cn/articles/11265.html)
- [How can I make a chain of function decorators in Python? - Stack Overflow](http://stackoverflow.com/questions/739654/how-can-i-make-a-chain-of-function-decorators-in-python)
- [Python中的装饰器介绍 – 思诚之道](http://www.bjhee.com/python-decorator.html)
- [python装饰器入门与提高 | 赖明星](http://mingxinglai.com/cn/2015/08/python-decorator/)


