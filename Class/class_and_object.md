# 类和实例

类是一个抽象的概念，我们可以把它理解为具有相同**属性**和**方法**的一组对象的集合，而实例则是一个具体的对象。我们还是先来看看在 Python 中怎么定义一个类。

这里以动物（Animal）类为例，Python 提供关键字 `class` 来声明一个类：

```python
class Animal(object):
    pass
```

其中，`Animal` 是类名，通常类名的首字母采用大写（如果有多个单词，则每个单词的首字母大写），后面紧跟着 `(object)`，表示该类是从哪个类继承而来的，所有类最终都会继承自 `object` 类。

类定义好了，接下来我们就可以**创建实例**了：

```python
>>> animal = Animal()  # 创建一个实例对象
>>> animal
<__main__.Animal at 0x1030a44d0>
```

我们在创建实例的时候，还可以传入一些参数，以初始化对象的属性，为此，我们需要添加一个 `__init__` 方法：

```python
class Animal(object):
    def __init__(self, name):
        self.name = name
```

然后，在创建实例的时候，传入参数：

```python
>>> animal = Aniaml('dog1')   # 传入参数 'dog1'
>>> animal.name               # 访问对象的 name 属性
'dog1'
```

我们可以把 `__init__` 理解为对象的初始化方法，它的第一个参数永远是 `self`，指向创建的实例本身。定义了 `__init__` 方法，我们在创建实例的时候，就需要传入与 `__init__` 方法匹配的参数。

接下来，我们再来添加一个方法：

```python
class Animal(object):
    def __init__(self, name):
        self.name = name
    def greet(self):
        print 'Hello, I am %s.' % self.name
```

我们添加了方法 `greet`，看看下面的使用：

```python
>>> dog1 = Animal('dog1')
>>> dog1.name
'dog1'
>>> dog1.greet()
Hello, I am dog1.
```

现在，让我们做一下总结。我们在 Animal 类定义了两个方法：`__init__` 和 `greet`。`__init__` 是 Python 中的**特殊方法（special method）**，它用于对对象进行初始化，类似于 C++ 中的构造函数；`greet` 是我们自定义的方法。

注意到，我们在上面定义的两个**方法**有一个共同点，就是它们的第一个参数都是 `self`，指向实例本身，也就是说它们是和实例绑定的函数，这也是我们称它们为方法而不是函数的原因。

# 访问限制

在某些情况下，我们希望限制用户访问对象的属性或方法，也就是希望它是私有的，对外隐蔽。比如，对于上面的例子，我们希望 `name` 属性在外部不能被访问，我们可以**在属性或方法的名称前面加上两个下划线**，即 `__`，对上面的例子做一点改动：

```python
class Animal(object):
    def __init__(self, name):
        self.__name = name
    def greet(self):
        print 'Hello, I am %s.' % self.__name
```

```python
>>> dog1 = Animal('dog1')
>>> dog1.__name   # 访问不了
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-206-7f6730db631e> in <module>()
----> 1 dog1.__name

AttributeError: 'Animal' object has no attribute '__name'
>>> dog1.greet()   # 可以访问
Hello, I am dog1.
```

可以看到，加了 `__` 的 `__name` 是不能访问的，而原来的 `greet` 仍可以正常访问。

需要注意的是，在 Python 中，以双下划线开头，并且以双下划线结尾（即 `__xxx__`）的变量是特殊变量，特殊变量是可以直接访问的。所以，不要用 `__name__` 这样的变量名。

另外，如果变量名前面只有一个下划线 `_`，表示**不要随意访问这个变量**，虽然它可以直接被访问。

# 获取对象信息

当我们拿到一个对象时，我们往往会考察它的类型和方法等，比如：

```
>>> a = 123
>>> type(a)
int
>>> b = '123'
>>> type(b)
str
```

当我们拿到一个类的对象时，我们用什么去考察它呢？回到前面的例子：

```python
class Animal(object):
    def __init__(self, name):
        self.name = name
    def greet(self):
        print 'Hello, I am %s.' % self.name
```

- 第 1 招：使用 `type`

使用 `type(obj)` 来获取对象的相应类型：

```
>>> dog1 = Animal('dog1')
>>> type(dog1)
__main__.Animal
```

- 第 2 招：使用 `isinstance`

使用 `isinstance(obj, type)` 判断对象是否为指定的 type 类型的实例：

```
>>> isinstance(dog1, Animal)
True
```

- 第 3 招：使用 `hasattr/getattr/setattr`

    - 使用 `hasattr(obj, attr)` 判断对象是否具有指定属性/方法；
    - 使用 `getattr(obj, attr[, default])` 获取属性/方法的值, 要是没有对应的属性则返回 default 值（前提是设置了 default），否则会抛出 AttributeError 异常；
    - 使用 `setattr(obj, attr, value)` 设定该属性/方法的值，类似于 obj.attr=value；

看下面例子：

```python
>>> hasattr(dog1, 'name')
True
>>> hasattr(dog1, 'x')
False
>>> hasattr(dog1, 'greet')
True
>>> getattr(dog1, 'name')
'dog1'
>>> getattr(dog1, 'greet')
<bound method Animal.greet of <__main__.Animal object at 0x10c3564d0>>
>>> getattr(dog1, 'x')
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-241-42f5b7da1012> in <module>()
----> 1 getattr(dog1, 'x')

AttributeError: 'Animal' object has no attribute 'x'
>>> getattr(dog1, 'x', 'xvalue')
'xvalue'
>>> setattr(dog1, 'age', 12)
>>> dog1.age
12
```

- 第 4 招：使用 `dir`

使用 `dir(obj)` 可以获取相应对象的**所有**属性和方法名的列表：

```python
>>> dir(dog1)
['__class__',
 '__delattr__',
 '__dict__',
 '__doc__',
 '__format__',
 '__getattribute__',
 '__hash__',
 '__init__',
 '__module__',
 '__new__',
 '__reduce__',
 '__reduce_ex__',
 '__repr__',
 '__setattr__',
 '__sizeof__',
 '__str__',
 '__subclasshook__',
 '__weakref__',
 'age',
 'greet',
 'name']
```

# 小结

- 类是具有相同**属性**和**方法**的一组对象的集合，实例是一个个具体的对象。
- 方法是与实例绑定的函数。
- 获取对象信息可使用下面方法：
    - `type(obj)`：来获取对象的相应类型；
    - `isinstance(obj, type)`：判断对象是否为指定的 type 类型的实例；
    - `hasattr(obj, attr)`：判断对象是否具有指定属性/方法；
    - `getattr(obj, attr[, default])` 获取属性/方法的值, 要是没有对应的属性则返回 default 值（前提是设置了 default），否则会抛出 AttributeError 异常；
    - `setattr(obj, attr, value)`：设定该属性/方法的值，类似于 obj.attr=value；
    - `dir(obj)`：可以获取相应对象的**所有**属性和方法名的列表：

# 参考资料

- [Python:类和对象object | Hom](http://gohom.win/2015/10/20/pyObject/)


