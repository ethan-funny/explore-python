# 协程

与子程序（或者说函数）一样，**协程（coroutine）**也是一种程序组件。Donald Knuth 曾说，**子程序是协程的特例**。

一个子程序就是一次函数调用，它只有一个入口，一次返回，调用顺序是明确的。但协程的调用和子程序则大不一样，**协程允许有多个入口对程序进行中断、继续执行等操作**。

Python2 可以通过 yield 来实现基本的协程，但不够强大，第三方库 [gevent](http://www.gevent.org/) 对协程提供了强大的支持。另外，Python3.5 提供了 async/await 语法来实现对协程的支持。本文只讨论通过 yield 来实现协程。

对于经典的**生产者-消费者模型**，如果用多线程来实现，我们就需要一个线程写消息，一个线程读消息，而且需要锁机制来避免对共享资源的访问冲突。

相比多线程，协程的一大特点就是**它在一个线程内执行**，既避免了多线程之间切换带来的开销，也避免了对共享资源的访问冲突。

下面，让我们看看怎么用 yield 来实现简单的**生产者-消费者模型**。

```python
import time

def consumer():
    message = ''
    while True:
        n = yield message     # yield 使函数中断
        if not n:
            return
        print '[CONSUMER] Consuming %s...' % n
        time.sleep(2)
        message = '200 OK'

def produce(c):
    c.next()           # 启动生成器
    n = 0
    while n < 5:
        n = n + 1
        print '[PRODUCER] Producing %s...' % n
        r = c.send(n)  # 通过 send 切换到 consumer 执行
        print '[PRODUCER] Consumer return: %s' % r
    c.close()

if __name__ == '__main__':
    c = consumer()
    produce(c)
```

在上面的代码中，消费者 `consumer` 是一个生成器函数，我们把它作为参数传给 `produce`，其中，next 方法用于启动生成器，send 方法用于发送消息给 `consumer`，并切换到 `consumer` 执行。`consumer` 通过 yield 获取到消息，然后进行处理，又通过 yield 返回消息给 `produce`，并转到 `produce` 执行，如此反复。执行结果如下：

```python
[PRODUCER] Producing 1...
[CONSUMER] Consuming 1...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 2...
[CONSUMER] Consuming 2...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 3...
[CONSUMER] Consuming 3...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 4...
[CONSUMER] Consuming 4...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 5...
[CONSUMER] Consuming 5...
[PRODUCER] Consumer return: 200 OK
```

# 小结

- 子程序就是协程的一种特例
- 协程的特点在于是一个线程内执行，没有线程之间切换的开销
- 协程只有一个线程，不需多线程的锁机制
- 协程的切换由用户自己管理和调度
- 通过创建协程将异步编程同步化

# 参考资料

- [协程 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E5%8D%8F%E7%A8%8B)
- [Python 线程与协程 - Yu's](http://blog.rainy.im/2016/04/07/python-thread-and-coroutine/)
- [谈谈Python的生成器 – 思诚之道](http://www.bjhee.com/python-yield.html)
- [协程 - 廖雪峰的官方网站](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/0013868328689835ecd883d910145dfa8227b539725e5ed000)

