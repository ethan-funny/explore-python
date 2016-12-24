# datetime

Python 提供了两个标准库用于处理跟时间相关的问题，一个是 `time`，另一个是 `datetime`，`datetime` 对 `time` 进行了封装，提供了更多实用的函数。本文介绍 `datetime` 库的简单使用。

# 当前时间

获取当前时间可以使用 `now()` 或 `utcnow()` 方法，其中，`now()` 用于获取当地时间，而 `utcnow()` 用于获取 UTC 时间。

```python
>>> from datetime import datetime

>>> datetime.now()     # 返回一个 datetime 对象，这里是当地时间
datetime.datetime(2016, 12, 10, 11, 32, 43, 806970)

>>> datetime.utcnow()  # 返回一个 datetime 对象，这里是 UTC 时间
datetime.datetime(2016, 12, 10, 3, 32, 49, 999423)

>>> datetime.now().year, datetime.now().month, datetime.now().day     # 年月日
(2016, 12, 10)

>>> datetime.now().hour, datetime.now().minute, datetime.now().second  # 时分秒
(11, 35, 37)
```

# 时间格式化

有时，我们需要对时间做格式化处理，可以使用 `strftime()` 或 `strptime()` 方法，其中，`strftime` 用于对 `datetime` 对象进行格式化，`strptime` 用于对字符串对象进行格式化。

```python
>>> from datetime import datetime

# 获取当前当地时间
>>> now = datetime.now()
>>> now
datetime.datetime(2016, 12, 10, 11, 46, 24, 432168)

# 对 datetime 对象进行格式化，转为字符串格式
>>> now_str = now.strftime('%Y-%m-%d %H:%M:%S.%f')
>>> now_str
'2016-12-10 11:46:24.432168'

# 对字符串对象进行格式化，转为 datetime 对象
>>> datetime.strptime(now_str, '%Y-%m-%d %H:%M:%S.%f')
datetime.datetime(2016, 12, 10, 11, 46, 24, 432168)
```

# 时间戳

[Unix 时间戳](http://funhacks.net/2015/04/29/Unix-timestamp/)根据精度的不同，有 10 位（秒级），13 位（毫秒级），16 位（微妙级）和 19 位（纳秒级）。

要注意的是，由于每个时区都有自己的本地时间（北京在东八区），因此也产生了世界标准时间（UTC, Universal Time Coordinated）。所以，在将一个时间戳转换为普通时间（比如 2016-01-01 12:00:00）时，要注意是要本地时区的时间还是世界时间等。

- 获取当前当地时间戳

```python
>>> import time
>>> from datetime import datetime

# 获取当前当地时间，返回一个 datetime 对象
>>> now = datetime.now()
>>> now
datetime.datetime(2016, 12, 9, 11, 56, 47, 632778)

# 13 位的毫秒时间戳
>>> long(time.mktime(now.timetuple()) * 1000.0 + now.microsecond / 1000.0)
1481255807632L

# 10 位的时间戳
>>> int(time.mktime(now.timetuple()))
1481255807
```

- 获取当前 UTC 时间戳

```python
>>> import calendar
>>> from datetime import datetime

# 获取当前的 UTC 时间，返回 datetime 对象
>>> utc_now = datetime.utcnow()
>>> utc_now
datetime.datetime(2016, 12, 9, 4, 0, 53, 356641)

# 13 位的时间戳
>>> long(calendar.timegm(utc_now.timetuple()) * 1000.0 + utc_now.microsecond / 1000.0)
1481256053356L

# 10 位的时间戳
>>> calendar.timegm(utc_now.timetuple())
1481256053
```

- 将时间戳转为字符串形式

```python
>>> from datetime import datetime

# 13 位的毫秒时间戳
>>> timestamp = 1456402864242

# 根据时间戳构建当地时间
>>> datetime.fromtimestamp(timestamp / 1000.0).strftime('%Y-%m-%d %H:%M:%S.%f')
'2016-02-25 20:21:04.242000'

# 根据时间戳构建 UTC 时间
>>> datetime.utcfromtimestamp(timestamp / 1000.0).strftime('%Y-%m-%d %H:%M:%S.%f')
'2016-02-25 12:21:04.242000'

# 10 位的时间戳
>>> timestamp = 1456402864

# 根据时间戳构建当地时间
>>> datetime.fromtimestamp(timestamp).strftime('%Y-%m-%d %H:%M:%S')
'2016-02-25 20:21:04'

# 根据时间戳构建 UTC 时间
>>> datetime.utcfromtimestamp(timestamp).strftime('%Y-%m-%d %H:%M:%S')
'2016-02-25 12:21:04'
```

- 将时间戳转为 datetime 形式

```python
>>> from datetime import datetime

# 13 位的毫秒时间戳
>>> timestamp = 1456402864242

# 根据时间戳构建当地时间
>>> datetime.fromtimestamp(timestamp / 1000.0)
datetime.datetime(2016, 2, 25, 20, 21, 4, 242000)

# 根据时间戳构建 UTC 时间
>>> datetime.utcfromtimestamp(timestamp / 1000.0)
datetime.datetime(2016, 2, 25, 12, 21, 4, 242000)

# 10 位的时间戳
>>> timestamp = 1456402864

# 根据时间戳构建当地时间
>>> datetime.fromtimestamp(timestamp)
datetime.datetime(2016, 2, 25, 20, 21, 4)

# 根据时间戳构建 UTC 时间
>>> datetime.utcfromtimestamp(timestamp)
datetime.datetime(2016, 2, 25, 12, 21, 4)
```

# 参考资料

- [python-datetime-time-conversions](http://www.saltycrane.com/blog/2008/11/python-datetime-time-conversions/)


