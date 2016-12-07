# ThreadLocal

我们知道，同一进程的多个线程之间是内存共享的，这意味着，当一个线程对全局变量做了修改，将会影响到其他所有线程，这是很危险的。为了避免多个线程同时修改全局变量，我们就需要对全局变量的修改加锁。

除了对全局变量的修改进行加锁，你可能也想到了可以使用线程自己的局部变量，因为局部变量只有线程自己能看见，对同一进程的其他线程是不可访问的。确实如此，让我们先看一个例子：

```python
from threading import Thread, current_thread

def echo(num):
    print current_thread().name, num

def calc():
    print 'thread %s is running...' % current_thread().name
    local_num = 0
    for _ in xrange(10000):
        local_num += 1
    echo(local_num)
    print 'thread %s ended.' % current_thread().name

if __name__ == '__main__':
    print 'thread %s is running...' % current_thread().name

    threads = []
    for i in range(5):
        threads.append(Thread(target=calc))
        threads[i].start()
    for i in range(5):
        threads[i].join()

    print 'thread %s ended.' % current_thread().name
```

在上面的代码中，我们创建了 5 个线程，每个线程都对自己的局部变量 local_num 进行 10000 次的加 1 操作。由于对线程局部变量的修改不会影响到其他线程，因此，我们可以看到，每个线程结束时打印的 local_num 的值都为 10000，执行结果如下：

```python
thread MainThread is running...
thread Thread-4 is running...
Thread-4 10000
thread Thread-4 ended.
thread Thread-5 is running...
Thread-5 10000
thread Thread-5 ended.
thread Thread-6 is running...
Thread-6 10000
thread Thread-6 ended.
thread Thread-7 is running...
Thread-7 10000
thread Thread-7 ended.
thread Thread-8 is running...
Thread-8 10000
thread Thread-8 ended.
thread MainThread ended.
```

上面这种**线程使用自己的局部变量**的方法虽然可以避免多线程对同一变量的访问冲突，但还是有一些问题。在实际的开发中，我们会调用很多函数，每个函数又有很多个局部变量，这时每个函数都这么传参数显然是不可取的。

为了解决这个问题，一个比较容易想到的做法就是创建一个全局字典，以线程的 ID 作为 key，线程的局部数据作为 value，这样就可以消除函数传参的问题，代码如下：

```python
from threading import Thread, current_thread

global_dict = {}

def echo():
    num = global_dict[current_thread()]    # 线程根据自己的 ID 获取数据
    print current_thread().name, num

def calc():
    print 'thread %s is running...' % current_thread().name
    
    global_dict[current_thread()] = 0
    for _ in xrange(10000):
        global_dict[current_thread()] += 1
    echo()
    
    print 'thread %s ended.' % current_thread().name

if __name__ == '__main__':
    print 'thread %s is running...' % current_thread().name

    threads = []
    for i in range(5):
        threads.append(Thread(target=calc))
        threads[i].start()
    for i in range(5):
        threads[i].join()

    print 'thread %s ended.' % current_thread().name
```

看下执行结果：

```
thread MainThread is running...
thread Thread-64 is running...
thread Thread-65 is running...
thread Thread-66 is running...
thread Thread-67 is running...
thread Thread-68 is running...
Thread-67 10000
thread Thread-67 ended.
Thread-65 10000
thread Thread-65 ended.
Thread-68 10000
thread Thread-68 ended.
Thread-66 10000
thread Thread-66 ended.
Thread-64 10000
thread Thread-64 ended.
thread MainThread ended.
```

上面的做法虽然消除了函数传参的问题，但是还是有些不完美，为了获取线程的局部数据，我们需要先获取线程 ID，另外，global_dict 是个全局变量，所有线程都可以对它进行修改，还是有些危险。

那到底如何是好？

事实上，Python 提供了 ThreadLocal 对象，它真正做到了线程之间的数据隔离，而且不用查找 dict，代码如下：

```python
from threading import Thread, current_thread, local

global_data = local()

def echo():
    num = global_data.num
    print current_thread().name, num

def calc():
    print 'thread %s is running...' % current_thread().name
    
    global_data.num = 0
    for _ in xrange(10000):
        global_data.num += 1
    echo()
    
    print 'thread %s ended.' % current_thread().name

if __name__ == '__main__':
    print 'thread %s is running...' % current_thread().name

    threads = []
    for i in range(5):
        threads.append(Thread(target=calc))
        threads[i].start()
    for i in range(5):
        threads[i].join()

    print 'thread %s ended.' % current_thread().name
```

在上面的代码中，global_data 就是 ThreadLocal 对象，你可以把它当作一个全局变量，但它的每个属性，比如 `global_data.num` 都是线程的局部变量，没有访问冲突的问题。

让我们看下执行结果：

```
thread MainThread is running...
thread Thread-94 is running...
thread Thread-95 is running...
thread Thread-96 is running...
thread Thread-97 is running...
thread Thread-98 is running...
Thread-96 10000
thread Thread-96 ended.
Thread-97 10000
thread Thread-97 ended.
Thread-95 10000
thread Thread-95 ended.
Thread-98 10000
thread Thread-98 ended.
Thread-94 10000
thread Thread-94 ended.
thread MainThread ended.
```

# 小结

- 使用 ThreadLocal 对象来线程绑定自己独有的数据。

# 参考资料

- [ThreadLocal - 廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386832845200f6513494f0c64bd882f25818a0281e80000)
- [深入理解Python中的ThreadLocal变量（上） | Just For Fun](http://selfboot.cn/2016/08/22/threadlocal_overview/)
- [Python线程同步机制 | Python见闻志](https://harveyqing.gitbooks.io/python-read-and-write/content/python_advance/python_thread_sync.html)


