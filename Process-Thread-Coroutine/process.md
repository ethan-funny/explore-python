# 进程

**进程（process）是正在运行的程序的实例，但一个程序可能会产生多个进程**。比如，打开 Chrome 浏览器程序，它可能会产生多个进程，主程序需要一个进程，一个网页标签需要一个进程，一个插件也需要一个进程，等等。

每个进程都有自己的地址空间，内存，数据栈以及其他记录其运行状态的辅助数据，不同的进程只能使用消息队列、共享内存等进程间通讯（IPC）方法进行通信，而不能直接共享信息。

# fork()

在介绍 Python 的进程编程之前，让我们先看看 Unix/Linux 中的 `fork` 函数。在 Unix/Linux 系统中，`fork` 函数被用于创建进程。这个函数很特殊，对于普通的函数，调用它一次，返回一次，但是调用 `fork` 一次，它返回两次。事实上，`fork` 函数创建了新的进程，我们把它称为子进程，子进程几乎是当前进程（即父进程）的一个拷贝：它会复制父进程的代码段，堆栈段和数据段。

对于父进程，`fork` 函数返回了子进程的进程号 pid，对于子进程，`fork` 函数则返回 `0`，这也是 `fork` 函数返回两次的原因，根据返回值，我们可以判断进程是父进程还是子进程。

下面我们看一段 C 代码，它展示了 fork 的基本使用：

```c
#include <unistd.h>
#include <stdio.h>

int main(int argc, char const *argv[])
{
    int pid;
    pid = fork();    // 使用 fork 函数

    if (pid < 0) {
        printf("Fail to create process\n");
    }
    else if (pid == 0) {
        printf("I am child process (%d) and my parent is (%d)\n", getpid(), getppid());
    }
    else {
        printf("I (%d) just created a child process (%d)\n", getpid(), pid);
    }
    return 0;
}
```

其中，`getpid` 用于获取当前进程号，`getppid` 用于获取父进程号。

事实上，Python 的 os 模块包含了普遍的操作系统功能，该模块也提供了 `fork` 函数，把上面的代码改成用 Python 来实现，如下：

```python
import os

pid = os.fork()

if pid < 0:
    print 'Fail to create process'
elif pid == 0:
    print 'I am child process (%s) and my parent is (%s).' % (os.getpid(), os.getppid())
else:
    print 'I (%s) just created a child process (%s).' % (os.getpid(), pid)
```

运行上面的代码，产生如下输出：

```
I (86645) just created a child process (86646).
I am child process (86646) and my parent is (86645).
```

需要注意的是，虽然子进程复制了父进程的代码段和数据段等，但是一旦子进程开始运行，子进程和父进程就是相互独立的，它们之间不再共享任何数据。

# 多进程

Python 提供了一个 [multiprocessing][mp] 模块，利用它，我们可以来编写跨平台的多进程程序，但需要注意的是 multiprocessing 在 Windows 和 Linux 平台的不一致性：一样的代码在 Windows 和 Linux 下运行的结果可能不同。因为 Windows 的进程模型和 Linux 不一样，Windows 下没有 fork。

我们先来看一个简单的例子，该例子演示了在主进程中启动一个子进程，并等待其结束，代码如下：

```python
import os
from multiprocessing import Process

# 子进程要执行的代码
def child_proc(name):
    print 'Run child process %s (%s)...' % (name, os.getpid())

if __name__ == '__main__':
    print 'Parent process %s.' % os.getpid()
    p = Process(target=child_proc, args=('test',))
    print 'Process will start.'
    p.start()
    p.join()
    print 'Process end.'
```

在上面的代码中，我们从 multiprocessing 模块引入了 Process，Process 是一个用于创建进程对象的类，其中，target 指定了进程要执行的函数，args 指定了参数。在创建了进程实例 p 之后，我们调用 start 方法开始执行该子进程，接着，我们又调用了 join 方法，该方法用于阻塞子进程以外的所有进程（这里指父进程），当子进程执行完毕后，父进程才会继续执行，它通常用于进程间的同步。

可以看到，用上面这种方式来创建进程比直接使用 `fork` 更简单易懂。现在，让我们看下输出结果：

```
Parent process 7170.
Process will start.
Run child process test (10075)...
Process end.
```

## multiprocessing 与平台有关

``` python
import random
import os
from multiprocessing import Process

num = random.randint(0, 100)

def show_num():
    print("pid:{}, num is {}".format(os.getpid(), num))

if __name__ == "__main__":
    print("pid:{}, num is {}".format(os.getpid(), num))
    p = Process(target=show_num)
    p.start()
    p.join()
```

在 Windows 下运行以上代码，输出的结果如下（你得到不一样的结果也是对的）：

```
pid:6504, num is 25
pid:6880, num is 6
```

我们发现，num 的值是不一样的！

在 Linux 下运行以上代码，可以看到 num 的值是一样的：

```
pid:11747, num is 13
pid:11748, num is 13
```

## 使用进程池创建多个进程

在上面，我们只是创建了一个进程，如果要创建多个进程呢？Python 提供了**进程池**的方式，让我们批量创建子进程，让我们看一个简单的示例：

```python
import os, time
from multiprocessing import Pool

def foo(x):
    print 'Run task %s (pid:%s)...' % (x, os.getpid())
    time.sleep(2)
    print 'Task %s result is: %s' % (x, x * x)

if __name__ == '__main__':
    print 'Parent process %s.' % os.getpid()
    p = Pool(4)         # 设置进程数
    for i in range(5):
        p.apply_async(foo, args=(i,))    # 设置每个进程要执行的函数和参数
    print 'Waiting for all subprocesses done...'
    p.close()
    p.join()
    print 'All subprocesses done.'
```

在上面的代码中，Pool 用于生成进程池，对 Pool 对象调用 apply_async 方法可以使每个进程异步执行任务，也就说不用等上一个任务执行完才执行下一个任务，close 方法用于关闭进程池，确保没有新的进程加入，join 方法会等待所有子进程执行完毕。

看看执行结果：

```python
Parent process 7170.
Run task 1 (pid:10320)...
Run task 0 (pid:10319)...
Run task 3 (pid:10322)...
Run task 2 (pid:10321)...
Waiting for all subprocesses done...
Task 1 result is: 1
Task 0 result is: 0
Run task 4 (pid:10320)...
Task 3 result is: 9
Task 2 result is: 4
Task 4 result is: 16
All subprocesses done.
```

# 进程间通信

进程间的通信可以通过管道（Pipe），队列（Queue）等多种方式来实现。Python 的 multiprocessing 模块封装了底层的实现机制，让我们可以很容易地实现进程间的通信。

下面以队列（Queue）为例，在父进程中创建两个子进程，一个往队列写数据，一个从对列读数据，代码如下：

```python
# -*- coding: utf-8 -*-

from multiprocessing import Process, Queue

# 向队列中写入数据
def write_task(q):
    try:
        n = 1
        while n < 5:
            print "write, %d" % n
            q.put(n)
            time.sleep(1)
            n += 1
    except BaseException:
        print "write_task error"
    finally:
        print "write_task end"

# 从队列读取数据
def read_task(q):
    try:
        n = 1
        while n < 5:
            print "read, %d" % q.get()
            time.sleep(1)
            n += 1
    except BaseException:
        print "read_task error"
    finally:
        print "read_task end"

if __name__ == "__main__":
    q = Queue()  # 父进程创建Queue，并传给各个子进程

    pw = Process(target=write_task, args=(q,))
    pr = Process(target=read_task, args=(q,))

    pw.start()   # 启动子进程 pw，写入
    pr.start()   # 启动子进程 pr，读取
    pw.join()    # 等待 pw 结束
    pr.join()    # 等待 pr 结束
    print "DONE"
```

执行结果如下：

```python
write, 1
read, 1
write, 2
read, 2
write, 3
read, 3
write, 4
read, 4
write_task end
read_task end
DONE
```

# 小结

- 进程是正在运行的程序的实例。
- 由于每个进程都有各自的内存空间，数据栈等，所以只能使用进程间通讯（Inter-Process Communication, IPC），而不能直接共享信息。
- Python 的 multiprocessing 模块封装了底层的实现机制，让我们可以更简单地编写多进程程序。

# 参考资料

- [多进程 - 廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/0013868323401155ceb3db1e2044f80b974b469eb06cb43000)
- [Linux下Fork与Exec使用 - hicjiajia - 博客园](http://www.cnblogs.com/hicjiajia/archive/2011/01/20/1940154.html)
- [Python 中的进程、线程、协程、同步、异步、回调 - 七牛云存储 - SegmentFault](https://segmentfault.com/a/1190000001813992)
- [编程中的进程、线程、协程、同步、异步、回调 · 浮生半日闲](https://wangdashuaihenshuai.github.io/2015/10/17/%E7%BC%96%E7%A8%8B%E4%B8%AD%E7%9A%84%E8%BF%9B%E7%A8%8B%E3%80%81%E7%BA%BF%E7%A8%8B%E3%80%81%E5%8D%8F%E7%A8%8B%E3%80%81%E5%90%8C%E6%AD%A5%E3%80%81%E5%BC%82%E6%AD%A5%E3%80%81%E5%9B%9E%E8%B0%83/)
- [python中多进程以及多线程编程的总结 - Codefly](http://sunms.codefly.top/2016/11/05/python%E4%B8%AD%E5%A4%9A%E8%BF%9B%E7%A8%8B%E4%BB%A5%E5%8F%8A%E5%A4%9A%E7%BA%BF%E7%A8%8B%E7%BC%96%E7%A8%8B%E7%9A%84%E6%80%BB%E7%BB%93/)
- [multithreading - Python multiprocessing.Pool: when to use apply, apply_async or map? - Stack Overflow](http://stackoverflow.com/questions/8533318/python-multiprocessing-pool-when-to-use-apply-apply-async-or-map)



[mp]: https://docs.python.org/2/library/multiprocessing.html


