# Celery

在程序的运行过程中，我们经常会碰到一些耗时耗资源的操作，为了避免它们阻塞主程序的运行，我们经常会采用多线程或异步任务。比如，在 Web 开发中，对新用户的注册，我们通常会给他发一封激活邮件，而发邮件是个 IO 阻塞式任务，如果直接把它放到应用当中，就需要等邮件发出去之后才能进行下一步操作，此时用户只能等待再等待。更好的方式是在业务逻辑中触发一个发邮件的异步任务，而主程序可以继续往下运行。

[Celery][0] 是一个强大的分布式任务队列，它可以让任务的执行完全脱离主程序，甚至可以被分配到其他主机上运行。我们通常使用它来实现异步任务（async task）和定时任务（crontab）。它的架构组成如下图：

![Celery_framework](https://ooo.0o0.ooo/2016/12/10/584bbf78e1783.png)

可以看到，Celery 主要包含以下几个模块：

- 任务模块

    包含异步任务和定时任务。其中，**异步任务通常在业务逻辑中被触发并发往任务队列，而定时任务由 Celery Beat 进程周期性地将任务发往任务队列**。
    
- 消息中间件 Broker

    Broker，即为任务调度队列，**接收任务生产者发来的消息（即任务），将任务存入队列**。Celery 本身不提供队列服务，官方推荐使用 RabbitMQ 和 Redis 等。
    
- 任务执行单元 Worker

    Worker 是执行任务的处理单元，**它实时监控消息队列，获取队列中调度的任务，并执行它**。
    
- 任务结果存储 Backend

    Backend 用于**存储任务的执行结果**，以供查询。同消息中间件一样，存储也可使用 RabbitMQ, Redis 和 MongoDB 等。

# 异步任务

使用 Celery 实现异步任务主要包含三个步骤：

1. 创建一个 Celery 实例
2. 启动 Celery Worker
3. 应用程序调用异步任务

## 快速入门

为了简单起见，对于 Broker 和 Backend，这里都使用 redis。在运行下面的例子之前，请确保 redis 已正确安装，并开启 redis 服务，当然，celery 也是要安装的。可以使用下面的命令来安装 celery 及相关依赖：

```
$ pip install 'celery[redis]'
```

### 创建 Celery 实例

将下面的代码保存为文件 `tasks.py`：

```python
# -*- coding: utf-8 -*-

import time
from celery import Celery

broker = 'redis://127.0.0.1:6379'
backend = 'redis://127.0.0.1:6379/0'

app = Celery('my_task', broker=broker, backend=backend)

@app.task
def add(x, y):
    time.sleep(5)     # 模拟耗时操作
    return x + y
```

上面的代码做了几件事：

- 创建了一个 Celery 实例 app，名称为 `my_task`；
- 指定消息中间件用 redis，URL 为 `redis://127.0.0.1:6379`；
- 指定存储用 redis，URL 为 `redis://127.0.0.1:6379/0`；
- 创建了一个 Celery 任务 `add`，当函数被 `@app.task` 装饰后，就成为可被 Celery 调度的任务；

### 启动 Celery Worker

在当前目录，使用如下方式启动 Celery Worker：

```
$ celery worker -A tasks --loglevel=info
```

其中：

- 参数 `-A` 指定了 Celery 实例的位置，本例是在 `tasks.py` 中，Celery 会自动在该文件中寻找 Celery 对象实例，当然，我们也可以自己指定，在本例，使用 `-A tasks.app`；
- 参数 `--loglevel` 指定了日志级别，默认为 warning，也可以使用 `-l info` 来表示；

在生产环境中，我们通常会使用 [Supervisor](http://supervisord.org/) 来控制 Celery Worker 进程。

启动成功后，控制台会显示如下输出：

![celery](https://ooo.0o0.ooo/2016/12/10/584b7da9f2c17.png)

### 调用任务

现在，我们可以在应用程序中使用 `delay()` 或 `apply_async()` 方法来调用任务。

在当前目录打开 Python 控制台，输入以下代码：

```python
>>> from tasks import add
>>> add.delay(2, 8)
<AsyncResult: 2272ddce-8be5-493f-b5ff-35a0d9fe600f>
```

在上面，我们从 `tasks.py` 文件中导入了 `add` 任务对象，然后使用 `delay()` 方法将任务发送到消息中间件（Broker），Celery Worker 进程监控到该任务后，就会进行执行。我们将窗口切换到 Worker 的启动窗口，会看到多了两条日志：

```
[2016-12-10 12:00:50,376: INFO/MainProcess] Received task: tasks.add[2272ddce-8be5-493f-b5ff-35a0d9fe600f]
[2016-12-10 12:00:55,385: INFO/PoolWorker-4] Task tasks.add[2272ddce-8be5-493f-b5ff-35a0d9fe600f] succeeded in 5.00642602402s: 10
```

这说明任务已经被调度并执行成功。

另外，我们如果想获取执行后的结果，可以这样做：

```python
>>> result = add.delay(2, 6)
>>> result.ready()   # 使用 ready() 判断任务是否执行完毕
False
>>> result.ready()
False
>>> result.ready()
True
>>> result.get()     # 使用 get() 获取任务结果
8
```

在上面，我们是在 Python 的环境中调用任务。事实上，我们通常在应用程序中调用任务。比如，将下面的代码保存为 `client.py`:

```python
# -*- coding: utf-8 -*-

from tasks import add

# 异步任务
add.delay(2, 8)

print 'hello world'
```

运行命令 `$ python client.py`，可以看到，虽然任务函数 `add` 需要等待 5 秒才返回执行结果，但由于它是一个异步任务，不会阻塞当前的主程序，因此主程序会往下执行 `print` 语句，打印出结果。

## 使用配置

在上面的例子中，我们直接把 Broker 和 Backend 的配置写在了程序当中，更好的做法是将配置项统一写入到一个配置文件中，通常我们将该文件命名为 `celeryconfig.py`。Celery 的配置比较多，可以在[官方文档][1]查询每个配置项的含义。

下面，我们再看一个例子。项目结构如下：

```
celery_demo                    # 项目根目录
    ├── celery_app             # 存放 celery 相关文件
    │   ├── __init__.py
    │   ├── celeryconfig.py    # 配置文件
    │   ├── task1.py           # 任务文件 1
    │   └── task2.py           # 任务文件 2
    └── client.py              # 应用程序
```

`__init__.py` 代码如下：

```python
# -*- coding: utf-8 -*-

from celery import Celery

app = Celery('demo')                                # 创建 Celery 实例
app.config_from_object('celery_app.celeryconfig')   # 通过 Celery 实例加载配置模块
```

`celeryconfig.py` 代码如下：

```python
BROKER_URL = 'redis://127.0.0.1:6379'               # 指定 Broker
CELERY_RESULT_BACKEND = 'redis://127.0.0.1:6379/0'  # 指定 Backend

CELERY_TIMEZONE='Asia/Shanghai'                     # 指定时区，默认是 UTC
# CELERY_TIMEZONE='UTC'                             

CELERY_IMPORTS = (                                  # 指定导入的任务模块
    'celery_app.task1',
    'celery_app.task2'
)
```

`task1.py` 代码如下：

```python
import time
from celery_app import app

@app.task
def add(x, y):
    time.sleep(2)
    return x + y
```

`task2.py` 代码如下：

```python
import time
from celery_app import app

@app.task
def multiply(x, y):
    time.sleep(2)
    return x * y
```

`client.py` 代码如下：

```python
# -*- coding: utf-8 -*-

from celery_app import task1
from celery_app import task2

task1.add.apply_async(args=[2, 8])        # 也可用 task1.add.delay(2, 8)
task2.multiply.apply_async(args=[3, 7])   # 也可用 task2.multiply.delay(3, 7)

print 'hello world'
```

现在，让我们启动 Celery Worker 进程，在项目的根目录下执行下面命令：

```
celery_demo $ celery -A celery_app worker --loglevel=info
```

接着，运行 `$ python client.py`，它会发送两个异步任务到 Broker，在 Worker 的窗口我们可以看到如下输出：

```
[2016-12-10 13:51:58,939: INFO/MainProcess] Received task: celery_app.task1.add[9ccffad0-aca4-4875-84ce-0ccfce5a83aa]
[2016-12-10 13:51:58,941: INFO/MainProcess] Received task: celery_app.task2.multiply[64b1f889-c892-4333-bd1d-ac667e677a8a]
[2016-12-10 13:52:00,948: INFO/PoolWorker-3] Task celery_app.task1.add[9ccffad0-aca4-4875-84ce-0ccfce5a83aa] succeeded in 2.00600231002s: 10
[2016-12-10 13:52:00,949: INFO/PoolWorker-4] Task celery_app.task2.multiply[64b1f889-c892-4333-bd1d-ac667e677a8a] succeeded in 2.00601326401s: 21
```

## delay 和 apply_async

在前面的例子中，我们使用 `delay()` 或 `apply_async()` 方法来调用任务。事实上，`delay` 方法封装了 `apply_async`，如下：

```python
def delay(self, *partial_args, **partial_kwargs):
    """Shortcut to :meth:`apply_async` using star arguments."""
    return self.apply_async(partial_args, partial_kwargs)
```

也就是说，`delay` 是使用 `apply_async` 的快捷方式。`apply_async` 支持更多的参数，它的一般形式如下：

```python
apply_async(args=(), kwargs={}, route_name=None, **options)
```

apply_async 常用的参数如下：

- countdown：指定多少秒后执行任务

```
task1.apply_async(args=(2, 3), countdown=5)    # 5 秒后执行任务
```

- eta (estimated time of arrival)：指定任务被调度的具体时间，参数类型是 datetime

```python
from datetime import datetime, timedelta

# 当前 UTC 时间再加 10 秒后执行任务
task1.multiply.apply_async(args=[3, 7], eta=datetime.utcnow() + timedelta(seconds=10))
```

- expires：任务过期时间，参数类型可以是 int，也可以是 datetime

```python
task1.multiply.apply_async(args=[3, 7], expires=10)    # 10 秒后过期
```

更多的参数列表可以在[官方文档](http://docs.celeryproject.org/en/latest/reference/celery.app.task.html#celery.app.task.Task.apply_async)中查看。

# 定时任务

Celery 除了可以执行**异步任务**，也支持执行**周期性任务（Periodic Tasks）**，或者说定时任务。Celery Beat 进程通过读取配置文件的内容，周期性地将定时任务发往任务队列。

让我们看看例子，项目结构如下：

```
celery_demo                    # 项目根目录
    ├── celery_app             # 存放 celery 相关文件
        ├── __init__.py
        ├── celeryconfig.py    # 配置文件
        ├── task1.py           # 任务文件
        └── task2.py           # 任务文件
```

`__init__.py` 代码如下：

```python
# -*- coding: utf-8 -*-

from celery import Celery

app = Celery('demo')
app.config_from_object('celery_app.celeryconfig')
```

`celeryconfig.py` 代码如下：

```python
# -*- coding: utf-8 -*-

from datetime import timedelta
from celery.schedules import crontab

# Broker and Backend
BROKER_URL = 'redis://127.0.0.1:6379'
CELERY_RESULT_BACKEND = 'redis://127.0.0.1:6379/0'

# Timezone
CELERY_TIMEZONE='Asia/Shanghai'    # 指定时区，不指定默认为 'UTC'
# CELERY_TIMEZONE='UTC'

# import
CELERY_IMPORTS = (
    'celery_app.task1',
    'celery_app.task2'
)

# schedules
CELERYBEAT_SCHEDULE = {
    'add-every-30-seconds': {
         'task': 'celery_app.task1.add',
         'schedule': timedelta(seconds=30),       # 每 30 秒执行一次
         'args': (5, 8)                           # 任务函数参数
    },
    'multiply-at-some-time': {
        'task': 'celery_app.task2.multiply',
        'schedule': crontab(hour=9, minute=50),   # 每天早上 9 点 50 分执行一次
        'args': (3, 7)                            # 任务函数参数
    }
}
```

`task1.py` 代码如下：

```python
import time
from celery_app import app

@app.task
def add(x, y):
    time.sleep(2)
    return x + y
```

`task2.py` 代码如下：

```python
import time
from celery_app import app

@app.task
def multiply(x, y):
    time.sleep(2)
    return x * y
```

现在，让我们启动 Celery Worker 进程，在项目的根目录下执行下面命令：

```
celery_demo $ celery -A celery_app worker --loglevel=info
```

接着，启动 Celery Beat 进程，定时将任务发送到 Broker，在项目根目录下执行下面命令：

```
celery_demo $ celery beat -A celery_app
celery beat v4.0.1 (latentcall) is starting.
__    -    ... __   -        _
LocalTime -> 2016-12-11 09:48:16
Configuration ->
    . broker -> redis://127.0.0.1:6379//
    . loader -> celery.loaders.app.AppLoader
    . scheduler -> celery.beat.PersistentScheduler
    . db -> celerybeat-schedule
    . logfile -> [stderr]@%WARNING
    . maxinterval -> 5.00 minutes (300s)
```

之后，在 Worker 窗口我们可以看到，任务 `task1` 每 30 秒执行一次，而 `task2` 每天早上 9 点 50 分执行一次。

在上面，我们用两个命令启动了 Worker 进程和 Beat 进程，我们也可以将它们放在一个命令中：

```
$ celery -B -A celery_app worker --loglevel=info
```

Celery 周期性任务也有多个配置项，可参考[官方文档](http://docs.celeryproject.org/en/latest/userguide/periodic-tasks.html)。

# 参考资料

- [Celery - Distributed Task Queue — Celery 4.0.1 documentation](http://docs.celeryproject.org/en/latest/index.html)
- [使用Celery - Python之美](https://zhuanlan.zhihu.com/p/22304455)
- [分布式任务队列Celery的介绍 – 思诚之道](http://www.bjhee.com/celery.html)
- [异步任务神器 Celery 简明笔记](http://www.jianshu.com/p/1840035cb510)


[0]: https://github.com/celery/celery
[1]: http://docs.celeryproject.org/en/latest/userguide/configuration.html


