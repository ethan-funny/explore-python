# 线程

**线程（thread）是进程（process）中的一个实体，一个进程至少包含一个线程**。比如，对于视频播放器，显示视频用一个线程，播放音频用另一个线程。如果我们把进程看成一个容器，则线程是此容器的工作单位。

进程和线程的区别主要有：

- 进程之间是相互独立的，多进程中，同一个变量，各自有一份拷贝存在于每个进程中，但互不影响；而同一个进程的多个线程是内存共享的，所有变量都由所有线程共享；
- 由于进程间是独立的，因此一个进程的崩溃不会影响到其他进程；而线程是包含在进程之内的，线程的崩溃就会引发进程的崩溃，继而导致同一进程内的其他线程也奔溃；

# 多线程

在 Python 中，进行多线程编程的模块有两个：thread 和 threading。其中，thread 是低级模块，threading 是高级模块，对 thread 进行了封装，一般来说，我们只需使用 threading 这个模块。

下面，我们看一个简单的例子：

```python
from threading import Thread, current_thread

def thread_test(name):
    print 'thread %s is running...' % current_thread().name
    print 'hello', name
    print 'thread %s ended.' % current_thread().name

if __name__ == "__main__":
    print 'thread %s is running...' % current_thread().name
    print 'hello world!'
    t = Thread(target=thread_test, args=("test",), name="TestThread")
    t.start()
    t.join()
    print 'thread %s ended.' % current_thread().name
```

可以看到，创建一个新的线程，就是把一个函数和函数参数传给 Thread 实例，然后调用 start 方法开始执行。代码中的 current_thread 用于返回当前线程的实例。

执行结果如下：

```
thread MainThread is running...
hello world!
thread TestThread is running...
hello test
thread TestThread ended.
thread MainThread ended.
```

# 锁

由于同一个进程之间的线程是内存共享的，所以当多个线程对同一个变量进行修改的时候，就会得到意想不到的结果。

让我们先看一个简单的例子：

```python
from threading import Thread, current_thread

num = 0

def calc():
    global num
    print 'thread %s is running...' % current_thread().name
    for _ in xrange(10000):
        num += 1
    print 'thread %s ended.' % current_thread().name

if __name__ == '__main__':
    print 'thread %s is running...' % current_thread().name

    threads = []
    for i in range(5):
        threads.append(Thread(target=calc))
        threads[i].start()
    for i in range(5):
        threads[i].join()

    print 'global num: %d' % num
    print 'thread %s ended.' % current_thread().name
```

在上面的代码中，我们创建了 5 个线程，每个线程对全局变量 num 进行 10000 次的 加 1 操作，这里之所以要循环 10000 次，是为了延长单个线程的执行时间，使线程执行时能出现中断切换的情况。现在问题来了，当这 5 个线程执行完毕时，全局变量的值是多少呢？是 50000 吗？

让我们看下执行结果：

```python
thread MainThread is running...
thread Thread-34 is running...
thread Thread-34 ended.
thread Thread-35 is running...
thread Thread-36 is running...
thread Thread-37 is running...
thread Thread-38 is running...
thread Thread-35 ended.
thread Thread-38 ended.
thread Thread-36 ended.
thread Thread-37 ended.
global num: 30668
thread MainThread ended.
```

我们发现 num 的值是 30668，事实上，num 的值是不确定的，你再运行一遍，会发现结果变了。

原因是因为 `num += 1` 不是一个原子操作，也就是说它在执行时被分成若干步：

- 计算 num + 1，存入临时变量 tmp 中；
- 将 tmp 的值赋给 num.

由于线程是交替运行的，线程在执行时可能中断，就会导致其他线程读到一个脏值。

为了保证计算的准确性，我们就需要给 `num += 1` 这个操作加上`锁`。当某个线程开始执行这个操作时，由于该线程获得了锁，因此其他线程不能同时执行该操作，只能等待，直到锁被释放，这样就可以避免修改的冲突。创建一个锁可以通过 `threading.Lock()` 来实现，代码如下：

```python
from threading import Thread, current_thread, Lock

num = 0
lock = Lock()

def calc():
    global num
    print 'thread %s is running...' % current_thread().name
    for _ in xrange(10000):
        lock.acquire()    # 获取锁
        num += 1
        lock.release()    # 释放锁
    print 'thread %s ended.' % current_thread().name

if __name__ == '__main__':
    print 'thread %s is running...' % current_thread().name

    threads = []
    for i in range(5):
        threads.append(Thread(target=calc))
        threads[i].start()
    for i in range(5):
        threads[i].join()
    
    print 'global num: %d' % num
    print 'thread %s ended.' % current_thread().name
```

让我们看下执行结果：

```python
thread MainThread is running...
thread Thread-44 is running...
thread Thread-45 is running...
thread Thread-46 is running...
thread Thread-47 is running...
thread Thread-48 is running...
thread Thread-45 ended.
thread Thread-47 ended.
thread Thread-48 ended.
thread Thread-46 ended.
thread Thread-44 ended.
global num: 50000
thread MainThread ended.
```

## GIL 锁

讲到 Python 中的多线程，就不得不面对 `GIL` 锁，`GIL` 锁的存在导致 Python 不能有效地使用多线程实现多核任务，因为在同一时间，只能有一个线程在运行。

`GIL` 全称是 Global Interpreter Lock，译为**全局解释锁**。早期的 Python 为了支持多线程，引入了 GIL 锁，用于解决多线程之间数据共享和同步的问题。但这种实现方式后来被发现是非常低效的，当大家试图去除 GIL 的时候，却发现大量库代码已重度依赖 GIL，由于各种各样的历史原因，GIL 锁就一直保留到现在。

# 小结

- 一个程序至少有一个进程,一个进程至少有一个线程。
- 进程是操作系统**分配资源**（比如内存）的最基本单元，线程是操作系统能够进行**调度和分派**的最基本单元。
- 在 Python 中，进行多线程编程的模块有两个：thread 和 threading。其中，thread 是低级模块，threading 是高级模块，对 thread 进行了封装，一般来说，我们只需使用 threading 这个模块。
- 在执行多线程操作时，注意加锁。

# 参考资料

- [多线程 - 廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143192823818768cd506abbc94eb5916192364506fa5d000)
- [Python的GIL是什么鬼，多线程性能究竟如何 • cenalulu's Tech Blog](http://cenalulu.github.io/python/gil-in-python/)
- [python中多进程以及多线程编程的总结 - Codefly](http://sunms.codefly.top/2016/11/05/python%E4%B8%AD%E5%A4%9A%E8%BF%9B%E7%A8%8B%E4%BB%A5%E5%8F%8A%E5%A4%9A%E7%BA%BF%E7%A8%8B%E7%BC%96%E7%A8%8B%E7%9A%84%E6%80%BB%E7%BB%93/)


