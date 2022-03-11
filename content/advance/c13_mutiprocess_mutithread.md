# 第十三章 爬虫中的并发编程


<!-- TOC -->

- [第十三章 爬虫中的并发编程](#第十三章-爬虫中的并发编程)
  - [进程和线程](#进程和线程)
  - [Python 的多进程和多线程](#python-的多进程和多线程)
    - [Python 多进程](#python-多进程)
    - [Python 多线程](#python-多线程)
    - [多进程和多线程](#多进程和多线程)
    - [单线程+异步I/O](#单线程异步io)
  - [使用并发思想优化爬虫](#使用并发思想优化爬虫)
  - [参考](#参考)

<!-- /TOC -->

## 进程和线程

现在的计算机都是支持多任务运行的，我们在浏览网页的同事还在登录着QQ，而每个网页和QQ内部又有无数个子任务在运行。这样既能够缩短处理事务的时间，有提高了用户的体验。我们把这种多个任务同时执行的方式，叫做并发执行。现如今的CPU都是多核的，那么并发映射到CPU上的话，便是多个核同时在处理任务。其实，并发不仅可以在多核上，在单核CPU上也可以。并发执行，其实并不是实际意义上的并行。而是一种按时间切片的方式，多个任务来轮询获取CPU资源来执行任务。只不过这个轮询的时间比较短，在我们人看来就像是多个任务并行一样。多核也一样，在一定时间切片内来获取多个CPU资源。所以，真正的并行只有多核CPU才能实现。更多CPU并行的问题，大家可以参考[维基百科](https://zh.wikipedia.org/wiki/%E4%B8%AD%E5%A4%AE%E5%A4%84%E7%90%86%E5%99%A8#%E5%B9%B6%E8%A1%8C)

大家知道了并发执行后，再来了解2个概念，进程和线程。CPU执行的任务，我们可以看成一个进程。而进程中往往运行了若干个子任务，这些子任务便是线程。例如，我们打开系统笔记本，这个能便是一个进程，而我们又可以写字，可以删除等等子任务，那这些子任务呢便是线程。

每个进程至少干一件事，所以，一个进程至少有一个线程。

进程有下边这些特性：
- 进程是系统资源的分配单位，系统会为每个进程分配的地址空间、数据栈等等资源。
- 进程可以创建子进程来执行任务，子进程也是有自己的地址空间和资源的。
- 进程间的资源是不共享的，需要通过通信机制来通信，例如：管道、信号、套接字、共享内存区等。

线程有如下特性：
- 线程是进程中真正获取CPU调度的执行单元
- 同一进程下的线程，共享进程的资源。

多进程和多线程，是现代编码技术中，常用的提高软件性能的技术。Python中即支持多线程又支持多进程。

线程的资源是共享的，那么便会涉及到一个竞争的问题。解决这个问题最简单的方式便是加锁，在线程使用资源时，将资源锁定，当线程完成，再讲资源释放。Python为了解决这个问题，Python加了一把超级大锁GIL(Global Interpreter Lock:全局解释锁)。GIL 往往是人们吐槽Python性能的槽点之一，但是GIL也给编程带来了方便，在使用Python的时候无需考虑内核中资源的[线程安全](https://zh.wikipedia.org/wiki/%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8)问题，可认为Python内核对象都是线程安全的。

更多GIL锁的知识课参阅[GIL锁的来龙去脉](http://www.pylixm.top/post/90/)


## Python 的多进程和多线程

### Python 多进程

Unix和Linux操作系统上提供了`fork()`系统调用来创建进程，调用`fork()`函数的是父进程，创建出的是子进程，子进程是父进程的一个拷贝，但是子进程拥有自己的[PID](https://zh.wikipedia.org/wiki/%E8%BF%9B%E7%A8%8BID)。Python系统模块提供了`fork()`来调用系统级的`fork()`来创建进程。Windows系统没有`fork()`，无法使用该方式创建进程。Python提供了更加强大且夸平台的（Unix/Linux/Windows）的进程管理模块`multiprocessing`。它的功能包括进程的创建、通信、批量进程管理的进程池等。

下面我们来看下这2中方式是如何使用的。

**os.fork()**

```python
import os

print('Process (%s) start...' % os.getpid())
# Only works on Unix/Linux/Mac:
pid = os.fork()
if pid == 0:
    print('I am child process (%s) and my parent is %s.' % (os.getpid(), os.getppid()))
else:
    print('I (%s) just created a child process (%s).' % (os.getpid(), pid))
```

知识点：
- 可使用`os.getpid()`来查看经常的唯一标识pid。
- 可使用`os.getppid()`来查看父进程的pid。
- `os.fork()`语句之后的语句，在父进程和子进程中都会执行。

**multiprocessing**

如果你在windows部署多进程项目，那么`multiprocessing`是你的选择。下面是基本用法：


```python
from multiprocessing import Process
import os

# 子进程要执行的代码
def run_proc(name):
    print('Run child process %s (%s)...' % (name, os.getpid()))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Process(target=run_proc, args=('test',))
    print('Child process will start.')
    p.start()
    p.join()
    print('Child process end.')
```

知识点：
- 使用`Process`类来创建进程。参数`target`参数是进程执行的函数名，`args`参数是进程执行函数的参数，还有其他线程初始化参数，可见定义。
- `start()`来启动子进程。
- `join()`来阻塞父进程，等待所有的子进程完成后再继续执行，通常用于进程间的同步。


### Python 多线程

对于多线程，Python的标准库提供了两个模块：`_thread`和`threading`，`threading`是对`_thread`进行了封装。绝大多数情况下，我们只需要使用`threading`这个高级模块。

下面我们看下它的基本使用：

```python
import time, threading

# 新线程执行的代码:
def loop():
    print('thread %s is running...' % threading.current_thread().name)
    n = 0
    while n < 5:
        n = n + 1
        print('thread %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
    print('thread %s ended.' % threading.current_thread().name)

print('thread %s is running...' % threading.current_thread().name)
t = threading.Thread(target=loop, name='LoopThread')
t.start()
t.join()
print('thread %s ended.' % threading.current_thread().name)
```

知识点：
- 使用`Thread`来创建线程，参数`target`为线程执行的函数名，`name`为线程命名，其他参数见定义。
- `start()` 为启动线程，`join()`为阻塞主进程，等待其他线程完成继续。

**线程锁Lock**

大家知道线程之间的资源是共享的，存在竞争问题。Python的线程模块提供了一个`Lock`来解决线程资源的线程竞争问题。

那么有了GIL为什么还要有这个线程锁呢？引用《Python源码分析》的一段话来解释：

>Python的线程在GIL的控制下，线程之间，对整个Python解释器，对Python提供的C API访问，都是互斥的，这可以看做是Python内核级的互斥机制。但是这种互斥是我们不能控制的，我们还需要另一种互斥机制 -- 用户级别的互斥。内核通过GIL实现的互斥保护了内核的共享资源，同样，用户级的互斥保护了用户程序中的共享资源。

线程锁的使用方法如下：

```python
import time, threading

# 假定这是你的银行存款:
balance = 0
lock = threading.Lock()


def change_it(n):
    # 先存后取，结果应该为0:
    global balance
    balance = balance + n
    balance = balance - n


def run_thread(n):
    for i in range(100000):
        # 先要获取锁:
        lock.acquire()
        try:
            # 放心地改吧:
            change_it(n)
        finally:
            # 改完了一定要释放锁:
            lock.release()


t1 = threading.Thread(target=run_thread, args=(5,))
t2 = threading.Thread(target=run_thread, args=(8,))
t1.start()
t2.start()
t1.join()
t2.join()
print(balance)

```

知识点：
- 使用`lock.acquire()`来获取锁，通多个线程同时执行时，只有一个线程可获取锁。
- 使用`lock.release()`来释放锁。

### 多进程和多线程

多进程和多线程的方式各有优缺点。在实际的使用中，我们需要更加具体的业务需求来选择该使用哪种方式。

多进程方式的优点：
- 稳定性高，有独立的内存空间资源，互不影响。

多进程方式的缺点：
- 占用资源高；
- 创建进程代价大；

多线程方式的优点：
- 创建代价小；
- 共享资源，交互方便；

多线程方式的缺点：
- 线程切换花费时间
- GIL的缘故，对应计算密集型引用，不能很好的利用多线程。
- 稳定性不如多进程；

那我们应该如何选择，大家本着以下原则即可：
- 不管事进程还是现场，任务数都不易过多
- 计算密集型的应用，可使用多进程；IO密集型的应用，可使用多线程；

### 单线程+异步I/O

现代操作系统对I/O操作的改进中最为重要的就是支持异步I/O。如果充分利用操作系统提供的异步I/O支持，就可以用单进程单线程模型来执行多任务，这种全新的模型称为`事件驱动模型`。Nginx就是支持异步I/O的Web服务器，它在单核CPU上采用单进程模型就可以高效地支持多任务。在多核CPU上，可以运行多个进程（数量与CPU核心数相同），充分利用多核CPU。用Node.js开发的服务器端程序也使用了这种工作模式，这也是当下实现多任务编程的一种趋势。

在Python语言中，单线程+异步I/O的编程模型称为`协程`，有了协程的支持，就可以基于事件驱动编写高效的多任务程序。协程最大的优势就是极高的执行效率，因为子程序切换不是线程切换，而是由程序自身控制，因此，没有线程切换的开销。协程的第二个优势就是不需要多线程的锁机制，因为只有一个线程，也不存在同时写变量冲突，在协程中控制共享资源不用加锁，只需要判断状态就好了，所以执行效率比多线程高很多。如果想要充分利用CPU的多核特性，最简单的方法是多进程+协程，既充分利用多核，又充分发挥协程的高效率，可获得极高的性能。

>**说明**本节内容来自[<>](https://github.com/jackfrued/Python-100-Days/blob/master/Day01-15/Day13/%E8%BF%9B%E7%A8%8B%E5%92%8C%E7%BA%BF%E7%A8%8B.md)，个人感觉作者这段对协程的解释简单易懂，故拿来分享。

## 使用并发思想优化爬虫

```python
pass 
```


## 参考

- [Python-100-Days/Day01-15/Day13/进程和线程.md](https://github.com/jackfrued/Python-100-Days/blob/master/Day01-15/Day13/%E8%BF%9B%E7%A8%8B%E5%92%8C%E7%BA%BF%E7%A8%8B.md)
- [廖雪峰-Python教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014319292979766bd3285c9d6b4942a8ea9b4e9cfb48d8000)