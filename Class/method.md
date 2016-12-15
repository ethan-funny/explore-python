# 类方法和静态方法

在讲类方法和静态方法之前，先来看一个简单的例子：

```python
class A(object):
    def foo(self):
        print 'Hello ', self

>>> a = A()
>>> a.foo()
Hello,  <__main__.A object at 0x10c37a450>
```

在上面，我们定义了一个类 A，它有一个方法 foo，然后我们创建了一个对象 a，并调用方法 foo。

## 类方法

如果我们想通过类来调用方法，而不是通过实例，那应该怎么办呢？

Python 提供了 `classmethod` 装饰器让我们实现上述功能，看下面的例子：

```python
class A(object):
    bar = 1
    @classmethod
    def class_foo(cls):
        print 'Hello, ', cls
        print cls.bar

>>> A.class_foo()   # 直接通过类来调用方法
Hello,  <class '__main__.A'>
1
```

在上面，我们使用了 `classmethod` 装饰方法 `class_foo`，它就变成了一个类方法，`class_foo` 的参数是 cls，代表类本身，当我们使用 `A.class_foo()` 时，cls 就会接收 A 作为参数。另外，被 `classmethod` 装饰的方法由于持有 cls 参数，因此我们可以在方法里面调用类的属性、方法，比如 `cls.bar`。

## 静态方法

在类中往往有一些方法跟类有关系，但是又不会改变类和实例状态的方法，这种方法是**静态方法**，我们使用 `staticmethod` 来装饰，比如下面的例子：

```python
class A(object):
    bar = 1
    @staticmethod
    def static_foo():
        print 'Hello, ', A.bar

>>> a = A()
>>> a.static_foo()
Hello, 1
>>> A.static_foo()
Hello, 1
```

可以看到，静态方法没有 self 和 cls 参数，可以把它看成是一个普通的函数，我们当然可以把它写到类外面，但这是不推荐的，因为这不利于代码的组织和命名空间的整洁。

# 小结

- 类方法使用 `@classmethod` 装饰器，可以使用类（也可使用实例）来调用方法。
- 静态方法使用 `@staticmethod` 装饰器，它是跟类有关系但在运行时又不需要实例和类参与的方法，可以使用类和实例来调用。


