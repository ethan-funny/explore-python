# 使用 @property

在使用 `@property` 之前，让我们先来看一个简单的例子：

```python
class Exam(object):
    def __init__(self, score):
        self._score = score

    def get_score(self):
        return self._score

    def set_score(self, val):
        if val < 0:
            self._score = 0
        elif val > 100:
            self._score = 100
        else:
            self._score = val

>>> e = Exam(60)
>>> e.get_score()
60
>>> e.set_score(70)
>>> e.get_score()
70
```

在上面，我们定义了一个 Exam 类，为了避免直接对 `_score` 属性操作，我们提供了 get_score 和 set_score 方法，这样起到了封装的作用，把一些不想对外公开的属性隐蔽起来，而只是提供方法给用户操作，在方法里面，我们可以检查参数的合理性等。

这样做没什么问题，但是我们有更简单的方式来做这件事，Python 提供了 `property` 装饰器，被装饰的方法，我们可以将其『当作』属性来用，看下面的例子：

```python
class Exam(object):
    def __init__(self, score):
        self._score = score

    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, val):
        if val < 0:
            self._score = 0
        elif val > 100:
            self._score = 100
        else:
            self._score = val

>>> e = Exam(60)
>>> e.score
60
>>> e.score = 90
>>> e.score
90
>>> e.score = 200
>>> e.score
100
```

在上面，我们给方法 score 加上了 `@property`，于是我们可以把 score 当成一个属性来用，此时，又会创建一个新的装饰器 `score.setter`，它可以把被装饰的方法变成属性来赋值。

另外，我们也不一定要使用 `score.setter` 这个装饰器，这时 score 就变成一个只读属性了：

```python
class Exam(object):
    def __init__(self, score):
        self._score = score

    @property
    def score(self):
        return self._score

>>> e = Exam(60)
>>> e.score
60
>>> e.score = 200  # score 是只读属性，不能设置值
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-676-b0515304f6e0> in <module>()
----> 1 e.score = 200

AttributeError: can't set attribute

```

# 小结

- `@property` 把方法『变成』了属性。


