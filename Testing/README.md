# 单元测试

软件系统的开发是一个很复杂的过程，随着系统复杂性的提高，代码中隐藏的 bug 也可能变得越来越多。为了保证软件的质量，测试是一个必不可少的部分，甚至还有**测试驱动开发（Test-driven development, TDD）**的理念，也就是先测试再编码。

在计算机编程中，**单元测试（Unit Testing）**又称为模块测试，是针对程序模块（软件设计的最小单位）来进行正确性检验的测试工作，所谓的单元是指一个函数，一个模块，一个类等。

在 Python 中，我们可以使用 `unittest` 模块编写单元测试。

下面以[官方文档](https://docs.python.org/2/library/unittest.html)的例子进行介绍，该例子对字符串的一些方法进行测试：

```python
# -*- coding: utf-8 -*-

import unittest

class TestStringMethods(unittest.TestCase):

    def test_upper(self):
        self.assertEqual('foo'.upper(), 'FOO')    # 判断两个值是否相等

    def test_isupper(self):
        self.assertTrue('FOO'.isupper())          # 判断值是否为 True
        self.assertFalse('Foo'.isupper())         # 判断值是否为 False

    def test_split(self):
        s = 'hello world'
        self.assertEqual(s.split(), ['hello', 'world'])
        # check that s.split fails when the separator is not a string
        with self.assertRaises(TypeError):         # 检测异常
            s.split(2)
```

在上面，我们定义了一个 TestStringMethods 类，它从 `unittest.TestCase` 继承。注意到，我们的方法名都是以 `test` 开头，表明该方法是测试方法，不以 `test` 开头的方法测试的时候不会被执行。

在方法里面，我们使用了`断言（assert）`判断程序运行的结果是否和预期相符。其中：

- `assertEqual` 用于判断两个值是否相等；
- `assertTrue/assertFalse` 用于判断表达式的值是 True 还是 False；
- `assertRaises` 用于检测异常；

断言方法主要有三种类型：

- 检测两个值的大小关系：相等，大于，小于等
- 检查逻辑表达式的值：True/Flase
- 检查异常

下面列举了部分常用的断言方法：

| Method | Checks that |
| :--- | :--- |
|   assertEqual(a, b)          |   a == b   |
|   assertNotEqual(a, b)       |   a != b   |
|   assertGreater(a, b)        |   a > b    |
|   assertGreaterEqual(a, b)   |   a >= b   |
|   assertLess(a, b)           |   a < b    |
|   assertLessEqual(a, b)      |   a <= b   |
|   assertTrue(x)              |   bool(x) is True  |
|   assertFalse(x)             |   bool(x) is False  |   
|   assertIs(a, b)             |   a is b      |
|   assertIsNot(a, b)          |   a is not b  |
|   assertIsNone(x)            |   x is None   |
|   assertIsNotNone(x)         |   x is not None   |
|   assertIn(a, b)             |   a in b   |
|   assertNotIn(a, b)          |   a not in b   |
|   assertIsInstance(a, b)     |   isinstance(a, b)  |
|   assertNotIsInstance(a, b)  |   not isinstance(a, b)  |

现在，让我们来运行上面的单元测试，将上面的代码保存为文件 `mytest.py`，通过 `-m unittest` 参数运行单元测试：

```
$ python -m unittest mytest
test_isupper (mytest.TestStringMethods) ... ok
test_split (mytest.TestStringMethods) ... ok
test_upper (mytest.TestStringMethods) ... ok
```

执行结果：

```python
...
----------------------------------------------------------------------
Ran 3 tests in 0.000s

OK
```

上面的结果表明测试通过，我们也可以加 `-v` 参数得到更加详细的测试结果：

```python
$ python -m unittest -v mytest
test_isupper (mytest.TestStringMethods) ... ok
test_split (mytest.TestStringMethods) ... ok
test_upper (mytest.TestStringMethods) ... ok

----------------------------------------------------------------------
Ran 3 tests in 0.000s

OK
```

上面这种运行单元测试的方法是我们推荐的做法，当然，你也可以在代码的最后添加两行：

```python
if __name__ == '__main__':
    unittest.main()
```

然后再直接运行：

```python
$ python mytest.py
```

# setUp 和 tearDown

在某些情况下，我们需要在每个测试方法执行前和执行后做一些相同的操作，比如我们想在每个测试方法执行前连接数据库，执行后断开数据库连接，为了避免在每个测试方法中编写同样的代码，我们可以使用 setUp 和 tearDown 方法，比如：

```python
# -*- coding: utf-8 -*-

import unittest

class TestStringMethods(unittest.TestCase):

    def setUp(self):                              # 在每个测试方法执行前被调用
        print 'setUp, Hello'

    def tearDown(self):                           # 在每个测试方法执行后被调用
        print 'tearDown, Bye!'

    def test_upper(self):
        self.assertEqual('foo'.upper(), 'FOO')    # 判断两个值是否相等

    def test_isupper(self):
        self.assertTrue('FOO'.isupper())          # 判断值是否为 True
        self.assertFalse('Foo'.isupper())         # 判断值是否为 False

    def test_split(self):
        s = 'hello world'
        self.assertEqual(s.split(), ['hello', 'world'])
        # check that s.split fails when the separator is not a string
        with self.assertRaises(TypeError):         # 检测异常
            s.split(2)
```

看看执行结果：

```
$ python -m unittest -v mytest
test_isupper (mytest.TestStringMethods) ... setUp, Hello
tearDown, Bye!
ok
test_split (mytest.TestStringMethods) ... setUp, Hello
tearDown, Bye!
ok
test_upper (mytest.TestStringMethods) ... setUp, Hello
tearDown, Bye!
ok

----------------------------------------------------------------------
Ran 3 tests in 0.000s

OK
```

# 小结

- 通过从 `unittest.TestCase` 继承来编写测试类。
- 使用断言方法判断程序运行的结果是否和预期相符。
- setUp 在每个测试方法执行前被调用，tearDown 在每个测试方法执行后被调用。

# 参考资料

- [unittest — Unit testing framework — Python 2.7.12 documentation](https://docs.python.org/2/library/unittest.html)
- [python单元测试unittest | Lucia Garden](http://luciastar.com/2016/05/16/python%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95unittest/)
- [单元测试 - 廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/00140137128705556022982cfd844b38d050add8565dcb9000)
- [单元测试 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95)
- [“单元测试要做多细？” | 酷 壳 - CoolShell.cn](http://coolshell.cn/articles/8209.html)


