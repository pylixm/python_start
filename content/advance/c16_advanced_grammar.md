# 第十六章 语法进阶

<!-- TOC -->

- [第十六章 语法进阶](#第十六章-语法进阶)
  - [Python的内存管理](#python的内存管理)
    - [Python的内存分配](#python的内存分配)
    - [Python的内存回收](#python的内存回收)
  - [数据结构](#数据结构)
    - [推导式](#推导式)
    - [数据结构的选用](#数据结构的选用)
  - [函数](#函数)
    - [闭包](#闭包)
    - [递归函数](#递归函数)
    - [装饰器](#装饰器)
    - [生成器](#生成器)
    - [迭代器](#迭代器)
    - [lambda 表达式与匿名函数](#lambda-表达式与匿名函数)
  - [并发编程](#并发编程)
    - [协程](#协程)
  - [思考](#思考)
  - [参考](#参考)

<!-- /TOC -->
 
## Python的内存管理

`内存管理`主要是指对计算机内存的使用，主要包括在程序执行时内存的分配和回收。对内存管理原理方面的了解，往往可以帮助我们写出更高效率的代码。像是C/C++等以效率著称的语言，往往需要自己来编写内存管理的代码，懂得内存管理的知识尤为重要。在Python中，我们不需要编写内存的分配和回收的代码逻辑，因为Python内部解释器已经为我们做好了，但是，了解一些内存管理的原理可以帮助我们更好的来优化代码和分析排错。

下面我们分别从分配和回收这两个方面说说Python的内存管理。

### Python的内存分配

大家知道，Python语法中的变量无需事先声明，便可直接使用来赋值。这是因为Python的动态语言的特性，在程序运行时根据表达式右则的值来确定变量的类型。动态类型的核心便是引用和对象分离，右则的值是一个存储在内存中的对象，变量则是对这个对象的一个引用。简单理解，就是说Python中的变量仅仅是值的一个引用，真正保存在内存中的是值本身。

Python内存管理模型如下，采用分层的方式管理：

```
    _____   ______   ______       ________
   [ int ] [ dict ] [ list ] ... [ string ]       Python core         |
+3 | <----- Object-specific memory -----> | <-- Non-object memory --> |
    _______________________________       |                           |
   [   Python's object allocator   ]      |                           |
+2 | ####### Object memory ####### | <------ Internal buffers ------> |
    ______________________________________________________________    |
   [          Python's raw memory allocator (PyMem_ API)          ]   |
+1 | <----- Python memory (under PyMem manager's control) ------> |   |
    __________________________________________________________________
   [    Underlying general-purpose allocator (ex: C library malloc)   ]
 0 | <------ Virtual memory allocated for the python process -------> |

   =========================================================================
    _______________________________________________________________________
   [                OS-specific Virtual Memory Manager (VMM)               ]
-1 | <--- Kernel dynamic storage allocation & management (page-based) ---> |
    __________________________________   __________________________________
   [                                  ] [                                  ]
-2 | <-- Physical memory: ROM/RAM --> | | <-- Secondary storage (swap) --> |
```

可以看出，python的内存管理主要分为四个层次结构
- 第0层是操作系统的内存管理借口，python无法干涉；
- 第1层是python为了适应不同的系统可移植性的需要建立的，是对第0层的进一步封装；
- 第2层是python 内存缓冲池，负责对象的建立和销毁，包含参数和引用计数等；
- 第3层是python 对象缓冲池，直接面向用户，它提供给我们int，list，string，dict等方法；


Python的内存分配分为大内存和小内存。大小以256字节为界限，对于大内存使用Malloc（动态内存分配法）进行分配，而对于小内存则使用内存池进行分配。对于像整数、短字符串等不可变类型，则在对象缓冲池中缓存保存，来便重复的创建和销毁。比如：

```python
a = 1
b = 3
c = 1
hex(id(a))
Out[5]: '0x23c2168'
hex(id(b))
Out[6]: '0x23c2138'
hex(id(c))
Out[7]: '0x23c2168'
```

可以看出，虽然a和c不是用a = c赋值的，但是实际上他们还是指向同一个位置。整数变量1在对象缓存池中缓存，该程序中所有赋值1的变量均为该内存地址的引用。短字符串也是这样的：

```python
a = 'haha'
b = 'hahaha'
c = 'haha'
hex(id(a))
Out[11]: '0x7fc94388bab0'
hex(id(b))
Out[12]: '0x7fc94388b870'
hex(id(c))
Out[13]: '0x7fc94388bab0'
```

>这里的可变不可变，是指内存中的值是否可以被改变。如果是不可变类型，在对对象本身操作的时候，必须在内存中新申请一块区域(因为老区域#不可变#)。如果是可变类型，对对象操作的时候，不需要再在其他地方申请内存，只需要在此对象后面连续申请(+/-)即可，也就是它的地址会保持不变，但区域会变长或者变短。

### Python的内存回收

Python的内存回收（或者叫垃圾回收，Garbage collection，简称GC）采用的是`引用计数`机制为主，`标记-清除`和`分代收集`两种机制为辅的策略。

Python的内存分配时说道变量其实是指向某个对象的引用，从对象这边来说，除了保存了值本身外，还保存了指向它本身的变量个数，称为`引用计数`。
当有新的变量指向这个对象时，引用计数会加一；当移除一个变量指向时，引用计数会减一。当引用计数减到0时，会触发垃圾回收机制来回收对象所占用的内存空间，从而将空间释放。

可以使得引用增加的操作：

```
对象被创建，a = 1
对象被引用，b = a
对象被作为参数传入 fun(a)
对象被作为容器元素 c = [a,a,a]
```

可以使得引用减少的操作：

```
对象被显式销毁，del a
对象的引用指向了新的对象，如本来a = b，然后a = c，那么b的引用就少一个
对象所在的容器被销毁
局部变量离开作用域
```

使用`引用计数`来进行垃圾回收，优点是非常明显的。它的逻辑比较简单，其次垃圾回收比较及时，一旦有需要收回的内存便立即被回收释放了。但是引用计数，也是有问题的，那便是循环引用。除此之外，在每次计数变更时都需要去维护这个计数，这将是非常消耗资源的。

基于以上问题，Python引入了`分代回收`机制。简单来说就是，将存在时间短的对象容易死掉，而老年的对象不太容易死，这叫做弱代假说（weak generation hypothesis），这也很好理解，一般生命周期长的对象往往是全局变量，而短的多为局部变量或者临时定义的变量。那么，我们把当前的对象作为第0代，我们每当分配（allocation） 比释放（deallocation）多到某个阈值时，就对这些对象做一次检查和清理，没有被清理的那些就存活下来，进入第1代，第一代检查做若干次后，对1代清理，存活下来的进入第2代，第二代也是如此。这样就实现了分代回收的操作。

**扩展阅读**

- [话说Ruby和Python的垃圾回收-英文原版](http://patshaughnessy.net/2013/10/24/visualizing-garbage-collection-in-ruby-and-python)
- [话说Ruby和Python的垃圾回收-中文译版](https://ruby-china.org/topics/28127)
- [使用gc、objgraph干掉python内存泄露与循环引用！](https://www.cnblogs.com/xybaby/p/7491656.html)
- [Python内存管理机制及优化](http://kkpattern.github.io/2015/06/20/python-memory-optimization-zh.html)

更多关于Python内存管理的知识，大家有兴趣的可以阅读《Python源码分析》一书，此书对Python的底层和内部结构原理分析的非常详尽。

## 数据结构

### 推导式

**列表推导式**

列表推导式是一种创建列表的快捷方式。使用方法如下：
```python
# 普通写法
In [1]: nums = []
In [2]: for i in range(5):
   ...:     nums.append(i)
   ...:
In [3]: nums
Out[3]: [0, 1, 2, 3, 4]

# 列表推导式写法
In [4]: [i for i in range(5)]
Out[4]: [0, 1, 2, 3, 4]
```

**字典推导式**

字典推导式，可以快速构建字典。

```python
DIAL_CODES = [(86, 'China'), (91, 'India'), (1, 'United States'), (62, 'Indonesia'), (55, 'Brazil'), (92, 'Pakistan'), (880, 'Bangladesh'), (234, 'Nigeria'), (7, 'Russia'), (81, 'Japan'), ]
In [8]: DIAL_CODES = [(86, 'China'), (91, 'India'), (1, 'United States'), (62, 'Indonesia'), (55, 'Brazil'), (92, 'Pakistan'), (880, 'Bangladesh'), (234, 'Nigeria'), (7, 'Russia'), (81, 'Japan'), ]

In [9]: {key:value for key, value in DIAL_CODES}
Out[9]:
{1: 'United States',
 7: 'Russia',
 55: 'Brazil',
 62: 'Indonesia',
 81: 'Japan',
 86: 'China',
 91: 'India',
 92: 'Pakistan',
 234: 'Nigeria',
 880: 'Bangladesh'}
```

### 数据结构的选用

选择时，可参考以下特性：
- 列表是动态数组，它们可变且可以重设长度（改变其内部元素的个数），意味着创建时会占用更多的内存空间和花费更多的时间。
- 元组是静态数组，它们不可变，且其内部数据一旦创建便无法改变，占用空间少，创建更快。
- 元组属于不可变类型，创建后被缓存于Python 运行时环境，这意味着我们每次使用元组时无须访问内核去分配内存。
- 字典，无序数据可以被唯一索引来引用。
- 集合，集合可以看成一堆字典建的组合，集合非常适合集合操作。


## 函数

### 闭包

什么是闭包，维基百科是这样说的：

>在一些语言中，在函数中可以（嵌套）定义另一个函数时，如果内部的函数引用了外部的函数的变量，则可能产生闭包。闭包可以用来在一个函数与一组“私有”变量之间创建关联关系。在给定函数被多次调用的过程中，这些私有变量能够保持其持久性。—— 维基百科

简单来讲，就是当某个函数被当成对象返回时，夹带了外部变量，就形成了一个闭包。闭包是由函数及其相关的引用环境组合而成的实体(即：闭包=函数+引用环境)。

如下：

```python
def outer(n):
    sum=n
    def inner():
        return sum+1
    return inner
```

支持将函数当成对象使用的编程语言，一般都支持闭包。

闭包存在的意义在于，闭包函数包含了外部的变量，不同的变量闭包函数的表现是不一样的。提供了我们一个模板，我们具体怎么使用都可以。

### 递归函数

在函数的函数体中调用自己的函数就叫做递归函数。

例如：

```python
# n 的阶乘
def fact(n):
    if n==1:
        return 1
    return n * fact(n - 1)
```

理论上所有的递归函数都可以使用循环逻辑来实现，但是递归更简单，逻辑更清晰。

使用递归函数需要注意防止栈溢出。在计算机中，函数调用是通过栈（stack）这种数据结构实现的，每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧。由于栈的大小不是无限的，所以，递归调用的次数过多，会导致栈溢出。

解决递归调用栈溢出的方法是通过尾递归优化，事实上尾递归和循环的效果是一样的，所以，把循环看成是一种特殊的尾递归函数也是可以的。

```python
def fact(n):
    return fact_iter(n, 1)

def fact_iter(num, product):
    if num == 1:
        return product
    return fact_iter(num - 1, num * product)
```

尾递归是指，在函数返回的时候，调用自身本身，并且，return语句不能包含表达式。这样，编译器或者解释器就可以把尾递归做优化，使递归本身无论调用多少次，都只占用一个栈帧，不会出现栈溢出的情况。

尾递归调用时，如果做了优化，栈不会增长，因此，无论多少次调用也不会导致栈溢出。

遗憾的是，大多数编程语言没有针对尾递归做优化，Python解释器也没有做优化，所以，即使把上面的fact(n)函数改成尾递归方式，也会导致栈溢出。

### 装饰器

Python的装饰器顾名思义，是一种扩展装饰函数功能的函数。它主要作用是在已有函数的功能基础上，添加额外的功能，例如，日志的插入、性能测试、事务处理、用户鉴权机制等等。

装饰器本身是一个返回一个函数的闭包函数，使用@来调用。用法如下：

```python

def recode_time(func):
    def wrapper(*args, **kw):
        print('start time', time.time())
        func(*args, **kw)
        print('end time', time.time())
    return wrapper

@recode_time
def test():
    time.sleep(6) 
```

装饰器是可以叠加使用的，那么这是就涉及到装饰器调用顺序了。对于Python中的”@”语法糖，装饰器的调用顺序与使用 @ 语法糖声明的顺序相反。

**Python内置的装饰器**

Python中有许多内置的装饰器，可直接通过@来调用。

- `staticmethod` 是类静态方法，其跟成员方法的区别是没有 self 参数，并且可以在类不进行实例化的情况下调用;
- `classmethod` 与成员方法的区别在于所接收的第一个参数不是 self （类实例的指针），而是cls（当前类的具体类型）;
- `property` 是属性的意思，表示可以通过通过类实例直接访问的信息;

### 生成器

生成器（generator），是一个特殊对象，它在遍历时才返回其中的元素。可以简单理解为它创建了一个不完整的列表，列表中的元素是在循环时实时计算的，这种特性很好的减少内存的占用。

创建生成器，有两种方式，第一种使用小括号，

```python
In [11]: g = (x*x for x in range(5))

In [12]: g
Out[12]: <generator object <genexpr> at 0x10ca71938>

```

第二种，使用`yield`关键字来构建生成器。

```python
In [18]: def sequence(n):
    ...:     for i in range(n):
    ...:         yield i
    ...:

In [19]: s =sequence(5)

In [20]: for i in s:
    ...:     print(i)
    ...:
0
1
2
3
4
```

生成器除了使用for等循环结构遍历外，也可以调用`next()`函数来获取其中的元素。当遍历到最后一个元素，再次执行时会抛出`StopIteration`异常。

```python
>>> s = sequence(5)
>>> next(s)
0
>>> next(s)
1
>>> next(s)
2
>>> next(s)
3
>>> next(s)
4
>>> next(s)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```


### 迭代器

迭代器（Iterator），迭代器是一个可以记住遍历的位置的对象。

在Python中可迭代的对象有两种：

- 一种是集合数据类型，如list、tuple、dict、set、str等；
- 一种是生成器，包括生成器和带yield的generator function。

其中，集合数据类型，是可迭代对象而非迭代器，因为无法记录遍历位置。不过可以使用函数`iter()`转化为迭代器。

生成器，可以使用`next()`函数来获取元素，且可以记录遍历位置，所以可以说生成器是一种迭代器。


**扩展阅读**
- [12步理解Python装饰器](https://www.jianshu.com/p/d68c6da1587a)

### lambda 表达式与匿名函数

在编程过程中，有时候不需要显示的定义函数名。像这种没有名称的函数，我们成为匿名函数。

在Python中，使用关键字`lambda 参数:函数体`来现实匿名函数。使用如下：

```python
>>> f = lambda x:x*x
>>> f
<function <lambda> at 0x101fed5f0>
>>> f(2)
4
>>>
```

`lambda`匿名函数常作为函数参数使用，如下：

```python
>>> map(lambda x:x*x, range(5))
[0, 1, 4, 9, 16]
```

## 并发编程

### 协程

> 协程的概念，比较晦涩难懂。看了廖老师的讲解，简单清楚明了，便摘录分享。原文：[<廖雪峰Python3教程>](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432090171191d05dae6e129940518d1d6cf6eeaaa969000)

协程，又称微线程，纤程。英文名Coroutine。
协程的概念很早就提出来了，但直到最近几年才在某些语言（如Lua）中得到广泛应用。

子程序，或者称为函数，在所有语言中都是层级调用，比如A调用B，B在执行过程中又调用了C，C执行完毕返回，B执行完毕返回，最后是A执行完毕。
所以子程序调用是通过栈实现的，一个线程就是执行一个子程序。子程序调用总是一个入口，一次返回，调用顺序是明确的。

协程的调用和子程序不同。协程看上去也是子程序，但执行过程中，在子程序内部可中断，然后转而执行别的子程序，在适当的时候再返回来接着执行。

注意，在一个子程序中中断，去执行其他子程序，不是函数调用，有点类似CPU的中断。比如子程序A、B：

```python
def A():
    print('1')
    print('2')
    print('3')

def B():
    print('x')
    print('y')
    print('z')

```

假设由协程执行，在执行A的过程中，可以随时中断，去执行B，B也可能在执行过程中中断再去执行A，结果可能是：

```python
1
2
x
y
3
z
```

但是在A中是没有调用B的，所以协程的调用比函数调用理解起来要难一些。

看起来A、B的执行有点像多线程，但协程的特点在于是一个线程执行，那和多线程比，协程有何优势？

最大的优势就是协程极高的执行效率。因为子程序切换不是线程切换，而是由程序自身控制，因此，没有线程切换的开销，和多线程比，线程数量越多，协程的性能优势就越明显。

第二大优势就是不需要多线程的锁机制，因为只有一个线程，也不存在同时写变量冲突，在协程中控制共享资源不加锁，只需要判断状态就好了，所以执行效率比多线程高很多。

因为协程是一个线程执行，那怎么利用多核CPU呢？最简单的方法是多进程+协程，既充分利用多核，又充分发挥协程的高效率，可获得极高的性能。


Python对协程的支持是通过`generator`实现的。

在`generator`中，我们不但可以通过for循环来迭代，还可以不断调用next()函数获取由`yield`语句返回的下一个值。

但是Python的`yield`不但可以返回一个值，它还可以接收调用者发出的参数。

来看例子：

传统的生产者-消费者模型是一个线程写消息，一个线程取消息，通过锁机制控制队列和等待，但一不小心就可能死锁。

如果改用协程，生产者生产消息后，直接通过yield跳转到消费者开始执行，待消费者执行完毕后，切换回生产者继续生产，效率极高：

```python
def consumer():
    r = ''
    while True:
        n = yield r
        if not n:
            return
        print('[CONSUMER] Consuming %s...' % n)
        r = '200 OK'

def produce(c):
    c.send(None)
    n = 0
    while n < 5:
        n = n + 1
        print('[PRODUCER] Producing %s...' % n)
        r = c.send(n)
        print('[PRODUCER] Consumer return: %s' % r)
    c.close()

c = consumer()
produce(c)
```

执行结果：

```bash
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

注意到consumer函数是一个`generator`，把一个consumer传入produce后：

首先调用`c.send(None)`启动生成器；

然后，一旦生产了东西，通过`c.send(n)`切换到consumer执行；

consumer通过`yield`拿到消息，处理，又通过`yield`把结果传回；

produce拿到consumer处理的结果，继续生产下一条消息；

produce决定不生产了，通过c.close()关闭consumer，整个过程结束。

整个流程无锁，由一个线程执行，produce和consumer协作完成任务，所以称为“协程”，而非线程的抢占式多任务。

最后套用`Donald Knuth`的一句话总结协程的特点：

“子程序就是协程的一种特例。”

## 思考
- 动态类型和静态类型语言的特色区别？



## 参考

- [https://blog.csdn.net/edogawachia/article/details/79919038](https://blog.csdn.net/edogawachia/article/details/79919038)
- [廖雪峰Python教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)