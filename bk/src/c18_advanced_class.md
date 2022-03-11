# 第十八章 面向对象

<!-- TOC -->

- [第十八章 面向对象](#第十八章-面向对象)
  - [组合和聚合](#组合和聚合)
  - [docstring](#docstring)
  - [下划线](#下划线)
    - [单下划线（_）](#单下划线_)
    - [名称前的单下划线（如：_shahriar）](#名称前的单下划线如_shahriar)
    - [名称前的双下划线（如：__shahriar）](#名称前的双下划线如__shahriar)
    - [名称前后的双下划线（如：__init__）](#名称前后的双下划线如init)
  - [魔术方法](#魔术方法)
    - [类初始化](#类初始化)
    - [类的表现](#类的表现)
    - [属性控制访问](#属性控制访问)
    - [容器类](#容器类)
    - [调用对象](#调用对象)
    - [回话管理](#回话管理)
    - [描述器](#描述器)
  - [getter 和setter](#getter-和setter)
  - [元类](#元类)
    - [元类 —— 道生一，一生二](#元类--道生一一生二)
    - [实例](#实例)
      - [挑战一：通过元类创建ORM](#挑战一通过元类创建orm)
      - [挑战二：网络代理的爬取](#挑战二网络代理的爬取)
  - [抽象基类](#抽象基类)
  - [参考](#参考)

<!-- /TOC -->

## 组合和聚合

`聚合(aggregation)`：指的是整体与部分的关系。通常在定义一个整体类后，再去分析这个整体类的组成结构。从而找出一些组成类，该整体类和组成类之间就形成了聚合关系。需求描述中“包含”、“组成”、“分为…部分”等词常意味着聚合关系。

`组合(composition)`：也表示类之间整体和部分的关系，但是组合关系中部分和整体具有统一的生存期。一旦整体对象不存在，部分对象也将不存在。部分对象与整体对象之间具有共生死的关系。

类组合，Computer实例对象不存在了，内部组合的Cpu实例也不存在。
聚合Computer实例对象不存在了，从初始化方法传入的Cpu实例不受Computer实例对象存在不存在的影响。
下面看下实例：

```python
#! /usr/bin/env python
# coding:utf-8

'''
类对象组合关系
'''

class Cpu(object):

    def __init__(self):
        self.type = '286'
        
    def __del__(self):
        print ("Cpu by by!")

class Computer(object):

    def __init__(self):
        self.cpu = Cpu()  # 包含Cpu类的实例对象
        
    def __del__(self):
        print ("Cpu by by!")

old_computer = Computer()
del old_computer
```

```python
#! /usr/bin/env python
# coding:utf-8

'''
类对象聚合关系
'''


class Cpu(object):

    def __init__(self):
        self.type = '286'

    def __del__(self):
        print ("没有权力和Cpu by by!")

class Computer(object):

    def __init__(self, cpu):
        self.cpu = cpu  # 有一个CPu类的实例对象

    def __del__(self):
        print ("没有权力和Cpu by by!")

old_cpu = Cpu()
old_computer = Computer(old_cpu)
del old_computer
```

类的组合和聚合却别与继承，没有特别的关系仅为类和属性的关系，但它扩展了类的功能，且也是一种减少重复代码的常用方式。

## docstring

为了解决代码文档的问题，Python提出了一种方案就是docstring，在写代码的时候，增加上相应的注释。这些注释，我们可以使用内建函数`help`来查看。

docstring可以写在三个地方：模块或包，对象，函数。

对于模块，docstring就是在模块文件的最前面，如下示例：

```python
# -*- coding: utf-8 -*-

"""
requests.api
~~~~~~~~~~~~

This module implements the Requests API.

:copyright: (c) 2012 by Kenneth Reitz.
:license: Apache2, see LICENSE for more details.
"""

from . import sessions
```

docstring使用三个双引号（”），或者三个单引号（‘）都是可以的。

对于包，docstring就是在package的__init__.py这个文件的任何语句的前面（docstring的前面还可以有#注释），如下示例：

```python
# -*- coding: utf-8 -*-

#   __
#  /__)  _  _     _   _ _/   _
# / (   (- (/ (/ (- _)  /  _)
#          /

"""
Requests HTTP Library
~~~~~~~~~~~~~~~~~~~~~

Requests is an HTTP library, written in Python, for human beings. Basic GET
usage:

   >>> import requests
   >>> r = requests.get('https://www.python.org')
   >>> r.status_code
   200
   >>> 'Python is a programming language' in r.content
   True

... or POST:

   >>> payload = dict(key1='value1', key2='value2')
   >>> r = requests.post('http://httpbin.org/post', data=payload)
   >>> print(r.text)
   {
     ...
     "form": {
       "key2": "value2",
       "key1": "value1"
     },
     ...
   }

The other HTTP methods are supported - see `requests.api`. Full documentation
is at <http://python-requests.org>.

:copyright: (c) 2017 by Kenneth Reitz.
:license: Apache 2.0, see LICENSE for more details.
"""

import urllib3
 
```

对于函数，docstring就是在函数定义的最前面，如下示例：

```python
def get(url, params=None, **kwargs):
    r"""Sends a GET request.

    :param url: URL for the new :class:`Request` object.
    :param params: (optional) Dictionary or bytes to be sent in the query string for the :class:`Request`.
    :param \*\*kwargs: Optional arguments that ``request`` takes.
    :return: :class:`Response <Response>` object
    :rtype: requests.Response
    """

    kwargs.setdefault('allow_redirects', True)
    return request('get', url, params=params, **kwargs)
```

注意上例的三个双引号前有个r，r是raw的意思，表示这个字符串里面的\就是它自己，不是用来转意的。

对于对象定义，docstring就是在class定义的最前面，如下示例：

```python
class Timeout(RequestException):
    """The request timed out.

    Catching this error will catch both
    :exc:`~requests.exceptions.ConnectTimeout` and
    :exc:`~requests.exceptions.ReadTimeout` errors.
    """


class ConnectTimeout(ConnectionError, Timeout):
    """The request timed out while trying to connect to the remote server.

    Requests that produced this error are safe to retry.
    """
```

还有一种单行docstring的写法，如下：

```python
def getStartLink():
    """get start link"""
    startLink = str()
    
    # get start link value
    if __name__ == "__main__":
```

Python builtin的help函数主要是在交互模式下使用，在交互模式下，使用help，就能够看到文档中的这些被称为docstring的注释，被一种良好的格式显示出来。

在调整代码的时候，维护注释，维护docstring，进而保持代码和文档（help函数的输出）一致，麦新杰认为是一种简化的思想。docstring不是强制的，如果不写，help函数的输出就是空空的。

下面是help(requests)的输出：

```python
>>>
>>> import requests
>>> help(requests)
Help on package requests:

NAME
    requests

DESCRIPTION
    Requests HTTP Library
    ~~~~~~~~~~~~~~~~~~~~~

    Requests is an HTTP library, written in Python, for human beings. Basic GET
    usage:

       >>> import requests
       >>> r = requests.get('https://www.python.org')
       >>> r.status_code
       200
       >>> 'Python is a programming language' in r.content
       True

    ... or POST:

       >>> payload = dict(key1='value1', key2='value2')
       >>> r = requests.post('http://httpbin.org/post', data=payload)
       >>> print(r.text)
       {
         ...
         "form": {
           "key2": "value2",
           "key1": "value1"
         },
         ...
       }

    The other HTTP methods are supported - see `requests.api`. Full documentatio
n
    is at <http://python-requests.org>.

    :copyright: (c) 2017 by Kenneth Reitz.
    :license: Apache 2.0, see LICENSE for more details.

PACKAGE CONTENTS
    __version__
    _internal_utils
    adapters
    api
    auth
```

看description部分，这就是写requests包的__init__.py文档中的docstring。

更多docstring相关，可参阅[PEP0257](
https://www.python.org/dev/peps/pep-0257/)


## 下划线

Python代码中，我们往往会看到许多用到下滑线的地方，包括在类中属性和方法命名、代码逻辑中的占位等等。下面我们就来总结下Python中下划线的用法。

### 单下划线（_）

通常情况下，会在以下3种场景中使用：

- 1、在解释器中：在这种情况下，“_”代表交互式解释器会话中上一条执行的语句的结果。这种用法首先被标准CPython解释器采用，然后其他类型的解释器也先后采用。

```python
>>> _ 
Traceback (most recent call last): 
File "<stdin>", line 1, in <module> 
NameError: name '_' is not defined 
>>> 42 
>>> _ 
42 
>>> 'alright!' if _ else ':(' 
'alright!' 
>>> _ 
'alright!'
```

- 2、作为一个名称：这与上面一点稍微有些联系，此时“_”作为临时性的名称使用。这样，当其他人阅读你的代码时将会知道，你分配了一个特定的名称，但是并不会在后面再次用到该名称。例如，下面的例子中，你可能对循环计数中的实际值并不感兴趣，此时就可以使用“_”。

```python
n = 42 
for _ in range(n): 
    do_something()
```

- 3、国际化：也许你也曾看到”_“会被作为一个函数来使用。这种情况下，它通常用于实现国际化和本地化字符串之间翻译查找的函数名称，这似乎源自并遵循相应的C约定。例如，在Django文档“转换”章节中，你将能看到如下代码：

```python
from django.utils.translation import ugettext as _ 
from django.http import HttpResponse 
def my_view(request): 
	output = _("Welcome to my site.") 
	return HttpResponse(output)
```

可以发现，场景二和场景三中的使用方法可能会相互冲突，所以我们需要避免在使用“_”作为国际化查找转换功能的代码块中同时使用“_”作为临时名称。

### 名称前的单下划线（如：_shahriar）

程序员使用名称前的单下划线，用于指定该名称属性为“私有”。这有点类似于惯例，为了使其他人（或你自己）使用这些代码时将会知道以“_”开头的名称只供内部使用。正如Python文档中所述：

>以下划线“_”为前缀的名称（如_spam）应该被视为API中非公开的部分（不管是函数、方法还是数据成员）。此时，应该将它们看作是一种实现细节，在修改它们时无需对外部通知。

正如上面所说，这确实类似一种惯例，因为它对解释器来说确实有一定的意义，如果你写了代码“from <模块/包名> import *”，那么以“_”开头的名称都不会被导入，除非模块或包中的“__all__”列表显式地包含了它们。了解更多请查看“[Importing * in Python](http://shahriar.svbtle.com/importing-star-in-python)”。

### 名称前的双下划线（如：__shahriar）

名称（具体为一个方法名）前双下划线（__）的用法并不是一种惯例，对解释器来说它有特定的意义。Python中的这种用法是为了避免与子类定义的名称冲突。Python文档指出，“__spam”这种形式（至少两个前导下划线，最多一个后续下划线）的任何标识符将会被“_classname__spam”这种形式原文取代，在这里“classname”是去掉前导下划线的当前类名。例如下面的例子：

```python
>>> class A(object): 
... def _internal_use(self): 
...     pass 
... def __method_name(self): 
...     pass 
... 
>>> dir(A()) 
['_A__method_name', ..., '_internal_use']
```

正如所预料的，“_internal_use”并未改变，而“__method_name”却被变成了“_ClassName__method_name”。此时，如果你创建A的一个子类B，那么你将不能轻易地覆写A中的方法“__method_name”。

```Python
>>> class B(A): 
... def __method_name(self): 
...     pass 
... 
>>> dir(B()) 
['_A__method_name', '_B__method_name', ..., '_internal_use']
```

这里的功能几乎和Java中的final方法和C++类中标准方法（非虚方法）一样。

### 名称前后的双下划线（如：__init__）

这种用法表示Python中特殊的方法名。其实，这只是一种惯例，对Python系统来说，这将确保不会与用户自定义的名称冲突。通常，你将会覆写这些方法，并在里面实现你所需要的功能，以便Python调用它们。例如，当定义一个类时，你经常会覆写“__init__”方法。

虽然你也可以编写自己的特殊方法名，但不建议这样做，这将会改变Python的某些默认的特性。

```Python
>>> class C(object): 
... def __mine__(self): 
... pass 
... 
>>> dir(C) 
... [..., '__mine__', ...]
```
其实，很容易摆脱这种类型的命名，而只让Python内部定义的特殊名称遵循这种约定。

## 魔术方法

魔术方法，英文 Magic Methods。顾名思义，它的使用方式不同于一般python方法的使用，它们的使用显得让人捉摸不透，就像魔法一样。在某些场景下python会隐式的调用相应的魔术方法，来完成对应的功能。我们可以重写这些方法来实现与python内建类型相同的对象，或改变python内建类型的相关行为。

魔术方法，通常以双下划线包围，例如我们最熟悉的 `__init__` 就是一个魔术方法。

下面分类别总结下python中的常用魔术方法，分享给大家。

### 类初始化 

- `__new__` 类初始化时执行，在 `__init__` 函数之前,它的第一个参数是这个类，其他的参数是用来直接传递给 __init__ 方法。详解，[python文档](https://www.python.org/download/releases/2.2/descrintro/#__new__)

- `__init__` 类的初始化方法。new 和 init 可以理解为构造函数。
- `__del__` 析构器，它不实现语句 del x ，定义的是当一个对象进行垃圾回收时候的行为。

**new vs init**

- `__new__`是用来创建类并返回这个类的实例, 而`__init__`只是将传入的参数来初始化该实例.
- `__new__`在创建一个实例的过程中必定会被调用,但`__init__`就不一定，比如通过pickle.load的方式反序列化一个实例时就不会调用`__init__`。
- `__new__`方法总是需要返回该类的一个实例，而`__init__`不能返回除了None的任何值。

### 类的表现

- `__str__` 定义当 str() 调用的时候的返回值。
- `__repr__` 定义 repr() 被调用的时候的返回值, str() 和 repr() 的主要区别在于 repr() 返回的是机器可读的输出，而 str() 返回的是人类可读的。
- `__unicode__(self)` 定义当 unicode() 调用的时候的返回值, unicode() 和 str() 很相似，但是返回的是unicode字符串
- `__hash__(self)` 定义当 hash() 调用的时候的返回值,它返回一个整形。

### 属性控制访问

- `__getarr__(self, name)` 你可以定义当用户试图获取一个不存在的属性时的行为，这适用于对普通拼写错误的获取和重定向，对获取一些不建议的属性时候给出警告(如果你愿意你也可以计算并且给出一个值)或者处理一个 AttributeError；
- `__setatt__(self, name, value)` 定义了你对属性进行赋值和修改操作时的行为, 避免"无限递归"的错误；
- `__delattr__(self, name)` 定义了删除属性时的行为；
- `__getattribute__(self, name)` 定义了你的属性被访问时的行为，同样要避免"无限递归"的错误。需要提醒的是，最好不要尝试去实现`__getattribute__`,因为很少见到这种做法，而且很容易出bug；

### 容器类

- `__len__(self)` 需要返回数值类型，以表示容器的长度。该方法在可变容器和不可变容器中必须实现。
- `__getitem__(self, key)` 当你执行self[key]的时候，调用的就是该方法。该方法在可变容器和不可变容器中也都必须实现。调用的时候,如果key的类型错误，该方法应该抛出TypeError；如果没法返回key对应的数值时,该方法应该抛出ValueError。
- `__setitem__(self, key, value)` 当你执行self[key] = value时，调用的是该方法。
- `__delitem__(self, key)` 当你执行del self[key]的时候，调用的是该方法。
- `__iter__(self)` 该方法需要返回一个迭代器(iterator)。当你执行for x in container: 或者使用iter(container)时，该方法被调用。
- `__reversed__(self)` 如果想要该数据结构被內建函数reversed()支持,就还需要实现该方法。
- `__contains__(self, item)` 如果定义了该方法，那么在执行item in container 或者 item not in container时该方法就会被调用。如果没有定义，那么Python会迭代容器中的元素来一个一个比较，从而决定返回True或者False。
- `__missing__(self, key)` dict字典类型会有该方法，它定义了key如果在容器中找不到时触发的行为。比如d = {'a': 1}, 当你执行d[notexist]时，d.__missing__['notexist']就会被调用。
- `__concat__(self, other) ` 来定义当用其他的来连接两个序列时候的行为, 当 + 操作符被调用时候会返回一个 self 和 other.__concat__ 被调用后的结果产生的新序列。 

### 调用对象 

- `__call__(self, [args...])` 允许一个类的实例像函数一样被调用。在那些类的实例经常改变状态的时候会非常有效。调用这个实例是一种改变这个对象状态的直接和优雅的做法

### 回话管理

- `__enter__(self)` 定义当使用 with 语句的时候会话管理器应该初始化被创建的时候的行为。返回值被 with 语句的 as 后的名字绑定;
- `__exit__(self, exception_type, exception_value, traceback)` 定义当一个代码块被执行或者终止后会话管理器应该做什么,它可以被用来处理异常，清除工作或者做一些代码块执行完毕之后的日常工作;如果代码块执行成功， exception_type , exception_value , 和 traceback 将会是 None 。否则的话你可以选择处理这个异常或者是直接交给用户处理。如果你想处理这个异常的话，确认 __exit__ 在所有结束之后会返回 True 。


### 描述器 

描述器对象不能独立存在, 它需要被另一个所有者类所持有。描述器对象可以访问到其拥有者实例的属性，在面向对象编程时，如果一个类的属性有相互依赖的关系时，使用描述器来编写代码可以很巧妙的组织逻辑。
如 Django的ORM中, models.Model中的InterField等字段, 就是通过描述器来实现功能的。

为了构建一个描述器，一个类必须有至少实现 __get__ ,__set__ , __delete__ 其中一个。
- `__get__(self, instance, owner)` 参数instance是拥有者类的实例。参数owner是拥有者类本身。__get__在其拥有者对其读值的时候调用。
- `__set__(self, instance, value)` 在其拥有者对其进行修改值的时候调用。
- `__delete__(self, instance)` 在其拥有者对其进行删除的时候调用。

```python
class RevealAccess(object):
    """A data descriptor that sets and returns values
       normally and prints a message logging their access.
    """

    def __init__(self, initval=None, name='var'):
        self.val = initval
        self.name = name

    def __get__(self, obj, objtype):
        print 'Retrieving', self.name
        return self.val

    def __set__(self, obj, val):
        print 'Updating', self.name
        self.val = val

>>> class MyClass(object):
...     x = RevealAccess(10, 'var "x"')
...     y = 5
...
>>> m = MyClass()
>>> m.x
Retrieving var "x"
10
>>> m.x = 20
Updating var "x"
>>> m.x
Retrieving var "x"
20
>>> m.y
5
```

更多魔术方法，可参阅[python 魔法方法](http://www.pylixm.top/post/1/)。

## getter 和setter

在python中没有像java那种get/set方法，如果我们想让别人使用某个属性的时候添加一些额外的东西，或者强制指定该参数的类型，可以用如下的注解 @property 和@xx.setter,其中xx表示该变量的名字，@property修饰的方法类似于java中的get方法，@xx.setter 修饰的方法类似于java中的set方法，可以在这两个方法中添加一些其他逻辑，如下：

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-

class Person(object):

    __privateVal=100

    def __init__(self, name):
        self._name = name

    @property
    def name(self):
        print("getter 被调用")
        return self._name

    @name.setter
    def name(self, name):
        print("setter 被调用")
        if not isinstance(name, str):
            raise TypeError("类型不匹配，应该为字符串型")
        self._name = name

# 直接调用name方法属性
person = Person("Tom")
print(person.name)

# 设置name方法属性的值，调用了setter修饰的name方法
person.name = 'John'
print(person.name)

# 当name非str时，报错
person.name = 12345
print(person.name)
```

## 元类

>元类是Python中一个非常重要的概念，理解元类有助于我们对Python的类更好的理解和使用。这块比较晦涩难懂，学习起来也是乏味。之前看过一篇文章，把元类和‘道’联系起来，非常确切，让人理解起来简单易懂，特此摘录分享。
>原文地址: https://segmentfault.com/a/1190000011447445?share_user=1030000014921424

----------------------------------------

学懂元类，你只需要知道两句话：
- 道生一，一生二，二生三，三生万物
- 我是谁？我从哪来里？我要到哪里去？
在python世界，拥有一个永恒的道，那就是“type”，请记在脑海中，type就是道。如此广袤无垠的python生态圈，都是由type产生出来的。

>道生一，一生二，二生三，三生万物。

- 道 即是 type
- 一 即是 metaclass(元类，或者叫类生成器)
- 二 即是 class(类，或者叫实例生成器)
- 三 即是 instance(实例)
- 万物 即是 实例的各种属性与方法，我们平常使用python时，调用的就是它们。

道和一，是我们今天讨论的命题，而二、三、和万物，则是我们常常使用的类、实例、属性和方法，用hello world来举例：

```python
# 创建一个Hello类，拥有属性say_hello ----二的起源
class Hello():
    def say_hello(self, name='world'):
        print('Hello, %s.' % name)
 
# 从Hello类创建一个实例hello ----二生三
hello = Hello()
 
# 使用hello调用方法say_hello ----三生万物
hello.say_hello()
Hello, world.
```

这就是一个标准的“二生三，三生万物”过程。 从类到我们可以调用的方法，用了这两步。

那我们不由自主要问，类从何而来呢？回到代码的第一行。`class Hello`其实是一个函数的“语义化简称”，只为了让代码更浅显易懂，它的另一个写法是：

```python
def fn(self, name='world'): # 假如我们有一个函数叫fn
    print('Hello, %s.' % name

Hello = type('Hello', (object,), dict(say_hello=fn)) # 通过type创建Hello class ---- 神秘的“道”，可以点化一切，这次我们直接从“道”生出了“二”
```

这样的写法，就和之前的Class Hello写法作用完全相同，你可以试试创建实例并调用

```python
# 从Hello类创建一个实例hello ----二生三，完全一样
hello = Hello()
# 使用hello调用方法say_hello ----三生万物，完全一样
hello.say_hello()
```

我们回头看一眼最精彩的地方，道直接生出了二：

```python
Hello = type(‘Hello’, (object,), dict(say_hello=fn))
```

注意它的三个参数！暗合人类的三大永恒命题：我是谁，我从哪里来，我要到哪里去。

- 第一个参数：我是谁。 在这里，我需要一个区分于其它一切的命名，以上的实例将我命名为“Hello”
- 第二个参数：我从哪里来。在这里，我需要知道从哪里来，也就是我的“父类”，以上实例中我的父类是“object”——python中一种非常初级的类。
- 第三个参数：我要到哪里去。在这里，我们将需要调用的方法和属性包含到一个字典里，再作为参数传入。以上实例中，我们有一个say_hello方法包装进了字典中。

值得注意的是，三大永恒命题，是一切类，一切实例，甚至一切实例属性与方法都具有的。理所应当，它们的“创造者”，道和一，即type和元类，也具有这三个参数。但平常，类的三大永恒命题并不作为参数传入，而是以如下方式传入

```python
class Hello(object){
# class 后声明“我是谁”
# 小括号内声明“我来自哪里”
# 中括号内声明“我要到哪里去”
    def say_hello(){
        pass
    }
}
```

造物主，可以直接创造单个的人，但这是一件苦役。造物主会先创造“人”这一物种，再批量创造具体的个人。并将三大永恒命题，一直传递下去。

“道”可以直接生出“二”，但它会先生出“一”，再批量地制造“二”。

type可以直接生成类（class），但也可以先生成元类（metaclass），再使用元类批量定制类（class）。

### 元类 —— 道生一，一生二

一般来说，元类均被命名后缀为`MetaClass`。想象一下，我们需要一个可以自动打招呼的元类，它里面的类方法呢，有时需要say_Hello，有时需要say_Hi，有时又需要say_Sayolala，有时需要say_Nihao。

如果每个内置的say_xxx都需要在类里面声明一次，那将是多么可怕的苦役！ 不如使用元类来解决问题。

以下是创建一个专门“打招呼”用的元类代码：

```python
class SayMetaClass(type):
    def __new__(cls, name, bases, attrs):
        attrs['say_'+name] = lambda self,value,saying=name: print(saying+','+value+'!')
        return type.__new__(cls, name, bases, attrs)
```

**记住两点：**
- 1、元类是由“type”衍生而出，所以父类需要传入type。【道生一，所以一必须包含道】
- 2、元类的操作都在 `__new__` 中完成，它的第一个参数是将创建的类，之后的参数即是三大永恒命题：我是谁，我从哪里来，我将到哪里去。 它返回的对象也是三大永恒命题，接下来，这三个参数将一直陪伴我们。

在`__new__`中，我只进行了一个操作，就是

```python
attrs['say_'+name] = lambda self,value,saying=name: print(saying+','+value+'!')
```

它跟据类的名字，创建了一个类方法。比如我们由元类创建的类叫“Hello”，那创建时就自动有了一个叫“say_Hello”的类方法，然后又将类的名字“Hello”作为默认参数saying，传到了方法里面。然后把hello方法调用时的传参作为value传进去，最终打印出来。

那么，一个元类是怎么从创建到调用的呢？

来！一起根据道生一、一生二、二生三、三生万物的准则，走进元类的生命周期吧！

```python
# 道生一：传入type
class SayMetaClass(type):
    # 传入三大永恒命题：类名称、父类、属性
    def __new__(cls, name, bases, attrs):
        # 创造“天赋”
        attrs['say_'+name] = lambda self,value,saying=name: print(saying+','+value+'!')
        # 传承三大永恒命题：类名称、父类、属性
        return type.__new__(cls, name, bases, attrs)
# 一生二：创建类
class Hello(object, metaclass=SayMetaClass):
    pass
# 二生三：创建实列
hello = Hello()
# 三生万物：调用实例方法
hello.say_Hello('world!')
```

注意：通过元类创建的类，第一个参数是父类，第二个参数是metaclass

普通人出生都不会说话，但有的人出生就会打招呼说“Hello”，“你好”,“sayolala”，这就是天赋的力量。它会给我们面向对象的编程省下无数的麻烦。

现在，保持元类不变，我们还可以继续创建Sayolala， Nihao类，如下：

```python
# 一生二：创建类
class Sayolala(object, metaclass=SayMetaClass):
    pass
# 二生三：创建实列
s = Sayolala()
# 三生万物：调用实例方法
s.say_Sayolala('japan!')
# 输出
Sayolala, japan!

# 也可以说中文
# 一生二：创建类
class Nihao(object, metaclass=SayMetaClass):
    pass
 
# 二生三：创建实列
n = Nihao()
 
# 三生万物：调用实例方法
n.say_Nihao('中华!')
# 输出
Nihao, 中华!
```

再来一个小例子：

```python
# 道生一
class ListMetaclass(type):
    def __new__(cls, name, bases, attrs):
        # 天赋：通过add方法将值绑定
        attrs['add'] = lambda self, value: self.append(value)
        return type.__new__(cls, name, bases, attrs)
        
# 一生二
class MyList(list, metaclass=ListMetaclass):
    pass
    
# 二生三
L = MyList()
 
# 三生万物
L.add(1)
```

现在我们打印一下L

```python
print(L)
 
>>> [1]
```

而普通的list没有add()方法

```python
L2 = list()
L2.add(1)
>>>AttributeError: 'list' object has no attribute 'add'
```

太棒了！学到这里，你是不是已经体验到了造物主的乐趣？

python世界的一切，尽在掌握。

### 实例

年轻的造物主，请随我一起开创新世界。
我们选择两个领域，一个是Django的核心思想，“Object Relational Mapping”，即对象-关系映射，简称ORM。这是Django的一大难点，但学完了元类，一切变得清晰。你对Django的理解将更上一层楼！另一个领域是爬虫领域（黑客领域），一个自动搜索网络上的可用代理，然后换着IP去突破别的人反爬虫限制。

这两项技能非常有用，也非常好玩！

#### 挑战一：通过元类创建ORM

准备工作，创建一个Field类。

```python
class Field(object):
 
    def __init__(self, name, column_type):
        self.name = name
        self.column_type = column_type
 
    def __str__(self):
        return '<%s:%s>' % (self.__class__.__name__, self.name)
```

它的作用是在Field类实例化时将得到两个参数，name和column_type，它们将被绑定为Field的私有属性，如果要将Field转化为字符串时，将返回“Field:XXX” ， XXX是传入的name名称。

准备工作：创建`StringField`和`IntergerField`。

```python
class StringField(Field):
 
    def __init__(self, name):
        super(StringField, self).__init__(name, 'varchar(100)')
 
class IntegerField(Field):
 
    def __init__(self, name):
        super(IntegerField, self).__init__(name, 'bigint')
```

它的作用是在`StringField`，`IntegerField` 实例初始化时，自动调用父类的初始化方式。

**道生一**

```python
class ModelMetaclass(type):
 
    def __new__(cls, name, bases, attrs):
        if name=='Model':
            return type.__new__(cls, name, bases, attrs)
        print('Found model: %s' % name)
        mappings = dict()
        for k, v in attrs.items():
            if isinstance(v, Field):
                print('Found mapping: %s ==> %s' % (k, v))
                mappings[k] = v
        for k in mappings.keys():
            attrs.pop(k)
        attrs['__mappings__'] = mappings # 保存属性和列的映射关系
        attrs['__table__'] = name # 假设表名和类名一致
        return type.__new__(cls, name, bases, attrs)
```

它做了以下几件事

- 创建一个新的字典mapping
- 将每一个类的属性，通过.items()遍历其键值对。如果值是Field类，则打印键值，并将这一对键值绑定到mapping字典上。
- 将刚刚传入值为Field类的属性删除。
- 创建一个专门的__mappings__属性，保存字典mapping。
- 创建一个专门的__table__属性，保存传入的类的名称。

**一生二**

```python
class Model(dict, metaclass=ModelMetaclass):
 
    def __init__(self, **kwarg):
        super(Model, self).__init__(**kwarg)
 
    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError("'Model' object has no attribute '%s'" % key)
 
    def __setattr__(self, key, value):
        self[key] = value
 
    # 模拟建表操作
    def save(self):
        fields = []
        args = []
        for k, v in self.__mappings__.items():
            fields.append(v.name)
            args.append(getattr(self, k, None))
        sql = 'insert into %s (%s) values (%s)' % (self.__table__, ','.join(fields), ','.join([str(i) for i in args]))
        print('SQL: %s' % sql)
        print('ARGS: %s' % str(args))
```

如果从Model创建一个子类User：

```python
class User(Model):
    # 定义类的属性到列的映射：
    id = IntegerField('id')
    name = StringField('username')
    email = StringField('email')
    password = StringField('password')
```

这时

`id = IntegerField('id')`就会自动解析为：

`Model.__setattr__(self, 'id', IntegerField('id'))`

因为`IntergerField('id')`是Field的子类的实例，自动触发元类的`__new__`，所以将`IntergerField('id')`存入`__mappings__`并删除这个键值对。

**二生三、三生万物**

当你初始化一个实例的时候并调用save()方法时候

```python
u = User(id=12345, name='Batman', email='batman@nasa.org', password='iamback')
u.save()
```
这时先完成了二生三的过程：

先调用`Model.__setattr__`，将键值载入私有对象。然后调用元类的“天赋”，`ModelMetaclass.__new__`，将Model中的私有对象，只要是Field的实例，都自动存入`u.__mappings__`。

接下来完成了三生万物的过程：

通过`u.save()`模拟数据库存入操作。这里我们仅仅做了一下遍历`__mappings__`操作，虚拟了sql并打印，在现实情况下是通过输入sql语句与数据库来运行。

输出结果为:

```bash
Found model: User
Found mapping: name ==> <StringField:username>
Found mapping: password ==> <StringField:password>
Found mapping: id ==> <IntegerField:id>
Found mapping: email ==> <StringField:email>
SQL: insert into User (username,password,id,email) values (Batman,iamback,12345,batman@nasa.org)
ARGS: ['Batman', 'iamback', 12345, 'batman@nasa.org']
```

年轻的造物主，你已经和我一起体验了由“道”演化“万物”的伟大历程，这也是Django中的Model版块核心原理。
接下来，请和我一起进行更好玩的爬虫实战（嗯，你现在已经是初级黑客了）：网络代理的爬取吧！

#### 挑战二：网络代理的爬取

准备工作，先爬个页面玩玩
请确保已安装`requests`和`pyquery`这两个包。

```python
# 文件：get_page.py
import requests
 
base_headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.71 Safari/537.36',
    'Accept-Encoding': 'gzip, deflate, sdch',
    'Accept-Language': 'zh-CN,zh;q=0.8'
}
 
 
def get_page(url):
    headers = dict(base_headers)
    print('Getting', url)
    try:
        r = requests.get(url, headers=headers)
        print('Getting result', url, r.status_code)
        if r.status_code == 200:
            return r.text
    except ConnectionError:
        print('Crawling Failed', url)
        return None
```

这里，我们利用request包，把百度的源码爬了出来。

试一试抓百度，把这一段粘在get_page.py后面，试完删除

```python
if(__name__ == '__main__'):
    rs = get_page('https://www.baidu.com')
    print('result:\r\n', rs)
```

试一试抓代理，把这一段粘在get_page.py后面，试完删除

```python
if(__name__ == '__main__'):
    from pyquery import PyQuery as pq
    start_url = 'http://www.proxy360.cn/Region/China'
    print('Crawling', start_url)
    html = get_page(start_url)
    if html:
        doc = pq(html)
        lines = doc('div[name="list_proxy_ip"]').items()
        for line in lines:
            ip = line.find('.tbBottomLine:nth-child(1)').text()
            port = line.find('.tbBottomLine:nth-child(2)').text()
            print(ip+':'+port)
```

接下来进入正题：使用元类批量抓取代理

```python
# 批量处理抓取代理
from getpage import get_page
from pyquery import PyQuery as pq
 
 
# 道生一：创建抽取代理的metaclass
class ProxyMetaclass(type):
    """
        元类，在FreeProxyGetter类中加入
        __CrawlFunc__和__CrawlFuncCount__
        两个参数，分别表示爬虫函数，和爬虫函数的数量。
    """
    def __new__(cls, name, bases, attrs):
        count = 0
        attrs['__CrawlFunc__'] = []
        attrs['__CrawlName__'] = []
        for k, v in attrs.items():
            if 'crawl_' in k:
                attrs['__CrawlName__'].append(k)
                attrs['__CrawlFunc__'].append(v)
                count += 1
        for k in attrs['__CrawlName__']:
            attrs.pop(k)
        attrs['__CrawlFuncCount__'] = count
        return type.__new__(cls, name, bases, attrs)
 
 
# 一生二：创建代理获取类
 
class ProxyGetter(object, metaclass=ProxyMetaclass):
    def get_raw_proxies(self, site):
        proxies = []
        print('Site', site)
        for func in self.__CrawlFunc__:
            if func.__name__==site:
                this_page_proxies = func(self)
                for proxy in this_page_proxies:
                    print('Getting', proxy, 'from', site)
                    proxies.append(proxy)
        return proxies
 
 
    def crawl_daili66(self, page_count=4):
        start_url = 'http://www.66ip.cn/{}.html'
        urls = [start_url.format(page) for page in range(1, page_count + 1)]
        for url in urls:
            print('Crawling', url)
            html = get_page(url)
            if html:
                doc = pq(html)
                trs = doc('.containerbox table tr:gt(0)').items()
                for tr in trs:
                    ip = tr.find('td:nth-child(1)').text()
                    port = tr.find('td:nth-child(2)').text()
                    yield ':'.join([ip, port])
 
    def crawl_proxy360(self):
        start_url = 'http://www.proxy360.cn/Region/China'
        print('Crawling', start_url)
        html = get_page(start_url)
        if html:
            doc = pq(html)
            lines = doc('div[name="list_proxy_ip"]').items()
            for line in lines:
                ip = line.find('.tbBottomLine:nth-child(1)').text()
                port = line.find('.tbBottomLine:nth-child(2)').text()
                yield ':'.join([ip, port])
 
    def crawl_goubanjia(self):
        start_url = 'http://www.goubanjia.com/free/gngn/index.shtml'
        html = get_page(start_url)
        if html:
            doc = pq(html)
            tds = doc('td.ip').items()
            for td in tds:
                td.find('p').remove()
                yield td.text().replace(' ', '')
 
 
if __name__ == '__main__':
    # 二生三：实例化ProxyGetter
    crawler = ProxyGetter()
    print(crawler.__CrawlName__)
    # 三生万物
    for site_label in range(crawler.__CrawlFuncCount__):
        site = crawler.__CrawlName__[site_label]
        myProxies = crawler.get_raw_proxies(site)
```

道生一：元类的__new__中，做了四件事：
- 将“crawl_”开头的类方法的名称推入ProxyGetter.__CrawlName__
- 将“crawl_”开头的类方法的本身推入ProxyGetter.__CrawlFunc__
- 计算符合“crawl_”开头的类方法个数
- 删除所有符合“crawl_”开头的类方法

怎么样？是不是和之前创建ORM的__mappings__过程极为相似？

一生二：类里面定义了使用pyquery抓取页面元素的方法
分别从三个免费代理网站抓取了页面上显示的全部代理。

二生三：创建实例对象crawler

三生万物：遍历每一个__CrawlFunc__
- 在ProxyGetter.__CrawlName__上面，获取可以抓取的的网址名。
- 触发类方法ProxyGetter.get_raw_proxies(site)
- 遍历ProxyGetter.__CrawlFunc__,如果方法名和网址名称相同的，则执行这一个方法，把每个网址获取到的代理整合成数组输出。


## 抽象基类

ABC，Abstract Base Class（抽象基类），主要定义了基本类和最基本的抽象方法，可以为子类定义共有的API，不需要具体实现。相当于是Java中的接口或者是抽象类。
抽象基类可以不实现具体的方法（当然也可以实现，只不过子类如果想调用抽象基类中定义的方法需要使用super()）而是将其留给派生类实现。

抽象基类提供了逻辑和实现解耦的能力，即在不同的模块中通过抽象基类来调用，可以用最精简的方式展示出代码之间的逻辑关系，让模块之间的依赖清晰简单。同时，一个抽象类可以有多个实现，让系统的运转更加灵活。而针对抽象类的编程，让每个人可以关注当前抽象类，只关注其方法和描述，而不需要考虑过多的其他逻辑，这对协同开发有很大意义。极简版的抽象类实现，也让代码可读性更高。

抽象基类的使用：
- 1、直接继承，直接继承抽象基类的子类就没有这么灵活，抽象基类中可以声明”抽象方法“和“抽象属性”，只有完全覆写（实现）了抽象基类中的“抽象”内容后，才能被实例化，而虚拟子类则不受此影响。
- 2、虚拟子类，将其他的类”注册“到抽象基类下当虚拟子类（调用register方法），虚拟子类的好处是你实现的第三方子类不需要直接继承自基类，可以实现抽象基类中的部分API接口，也可以根本不实现，但是issubclass(), issubinstance()进行判断时仍然返回真值。

Python 对于ABC的支持模块是`abc`模块，定义了一个特殊的metaclass：`ABCMeta`还有一些装饰器：`@abstractmethod` 和` @abstarctproperty` 。`abc.ABCMeta` 用于在Python程序中创建抽象基类。而抽象基类如果想要声明“抽象方法”，可以使用 `@abstractmethod` ，如果想声明“抽象属性”，可以使用 `@abstractproperty` 。

为了解决Python2&3的兼容问题，需要引入six模块，该模块中有一个针对类的装饰器 `@six.add_metaclass(MetaClass)` 可以为两个版本的Python类方便地添加metaclass。

- 通用做法`@six.add_metaclass(MetaClass) `的作用是在不同版本的Python之间提供一个优雅的声明类的metaclass的手段，事实上不用它也可以，只是使用了它代码更为整洁明了。

```python
import six

@six.add_metaclass(Meta)
class MyClass(object):
    pass
```


- 在Python 3 等价于	

```python
import six

class MyClass(object, metaclass = Meta):
    pass
```

- 在Python 2.x (x >= 6)中等价于	

```python
import six

class MyClass(object):
    __metaclass__ = Meta
    pass
```

- 或者直接调用装饰器，这里也能看出来装饰器就是个方法包装而已。

```python
import six

class MyClass(object):
    pass
MyClass  = six.add_metaclass(Meta)(MyClass)
```

实例：

```python
import abc
import six


@six.add_metaclass(abc.ABCMeta)
class BaseClass(object):
    @abc.abstractmethod
    def func_a(self, data):
        """
        an abstract method need to be implemented
        """

    @abc.abstractmethod
    def func_b(self, data):
        """
        another abstract method need to be implemented
        """

class SubclassImpl(BaseClass):
    def func_a(self, data):
        print("Overriding func_a, " + str(data))

    @staticmethod
    def func_d(self, data):
        print(type(self) + str(data))

class RegisteredImpl(object):
    @staticmethod
    def func_c(data):
        print("Method in third-party class, " + str(data))
        
BaseClass.register(RegisteredImpl)


if __name__ == '__main__':
    for subclass in BaseClass.__subclasses__():
        print("subclass of BaseClass: " + subclass.__name__)
    print("subclass do not contains RegisteredImpl")
    print("-----------------------------------------------")

    print("RegisteredImpl is subclass: " + str(issubclass(RegisteredImpl, BaseClass)))
    print("RegisteredImpl object  is instance: " + str(isinstance(RegisteredImpl(), BaseClass)))
    print("SubclassImpl is subclass: " + str(issubclass(SubclassImpl, BaseClass)))

    print("-----------------------------------------------")
    obj1 = RegisteredImpl()
    obj1.func_c("RegisteredImpl new object OK!")
    print("-----------------------------------------------")
    obj2 = SubclassImpl()  #由于没有实例化所有的方法，所以这里会报错 Can't instantiate abstract class SubclassImpl with abstract methods func_b
    obj2.func_a("It's right!")
```
结果如下：

```python
subclass of BaseClass: SubclassImpl
subclass do not contains RegisteredImpl
-----------------------------------------------
RegisteredImpl is subclass: True
RegisteredImpl object  is instance: True
SubclassImpl is subclass: True
-----------------------------------------------
Method in third-party class, RegisteredImpl new object OK!
-----------------------------------------------
Traceback (most recent call last):
  File "/Users/wangqi/Git/Python/scrapy_crawler_learn/test/ABCTest.py", line 51, in <module>
    obj2 = SubclassImpl()  #由于没有实例化所有的方法，所以这里会报错 Can't instantiate abstract class SubclassImpl with abstract methods func_b
TypeError: Can't instantiate abstract class SubclassImpl with abstract methods func_b

```

## 参考

- [http://www.maixj.net/ict/python-docstring-16247](http://www.maixj.net/ict/python-docstring-16247)
- [https://blog.csdn.net/qq_20641565/article/details/78541546?locationNum=5&fps=1](https://blog.csdn.net/qq_20641565/article/details/78541546?locationNum=5&fps=1)
- [https://blog.csdn.net/weixin_40907382/article/details/80277170](https://blog.csdn.net/weixin_40907382/article/details/80277170)