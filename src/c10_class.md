<!-- TOC -->

- [基本概念](#基本概念)
    - [对象](#对象)
    - [类和实例](#类和实例)
    - [属性和方法](#属性和方法)
        - [静态方法](#静态方法)
        - [类方法](#类方法)
        - [私有属性和方法](#私有属性和方法)
        - [构造函数](#构造函数)
    - [继承和多态](#继承和多态)
        - [继承](#继承)
        - [多态](#多态)
- [编程范式](#编程范式)
    - [面向过程](#面向过程)
    - [面向对象](#面向对象)
    - [扩展阅读](#扩展阅读)
- [使用第三方类库来实现](#使用第三方类库来实现)

<!-- /TOC -->

在第一章介绍Python的时候，我们留了几个概念，其中一个是面向对象。那么到底什么是面向对象呢？本章我们来详细讲解下。在学习这个概念之前，我们先来看些基本概念来帮助我们理解它。

## 基本概念

### 对象

在Python中，将程序中的任何内容统称为对象。包括数字、字符串、函数等，可认为一切皆为对象。

### 类和实例

类，是用来描述具有相同的属性和方法的对象的集合。它定义了该集合中每个对象所共有的属性和方法。使用如下格式定义：

```python
class 类名(object):
    pass 
```

实例，是类的具体实现。在类的概念体系中，我们常常把实例叫做对象。即我们在说有关类方便的话术时，对象往往指的是类的实例，这个是狭义的对象概念，也是沿袭其他面向对象语言的一些概念。在python中，从广义上来说对象，可以人为“一切皆对象”。

实例的定义，又叫做类的实例化，使用如下方式定义：

```
实例名 = 类名()
```

下面我们举例说明类和实例的概念：

```python
# 类的定义
class Person(object):
    pass

# 实例化
person = Person()
```

### 属性和方法

类中可以定义变量和方法，这些变量和方法分别叫做类的属性和类的方法。

```python
class Person(object):
    name = '小明'

    def speak(self):
        print('你好，世界！')

if __name__=='__main__':
    ps = Person()
    print(ps.name)
    print(Person.name)
    ps.speak()
```
**知识点：**
- 类的属性，调用可使用`实例.属性名`和`类.属性名`调用；
- 类的方法，方法的第一个参数为`self`关键字，调用只能使用`实例.方法名`；
- 关键字`self`表示类的实例，绑定在实例上的属性和方法，只能通过实例调用，不能通过类来调用。

#### 静态方法

那么可否使用类来调用方法呢，答案是可以的。但该方法和普通的方法有些区别，这中方法叫做类的`静态方法`。如下定义：

```python
# -*- coding:utf-8 -*-
class Person(object):
    name = '小明'

    @staticmethod
    def speak():
        print('类说，你好！')


if __name__=='__main__':
    ps = Person()
    Person.speak()
    ps.speak()
```
**知识点：**
- 方法前有`@staticmethod`表示该方法为类的静态方法，该语法为一种python装饰器语法，后边我们会详细为大家讲解。
- 方法没有`self`参数。
- 静态方法，可使用`类.方法名`和`实例.方法名`来调用。

#### 类方法

除了静态方法，还有一种方法叫做`类方法`，只有类本身可以调用，定义如下：

```python
# -*- coding:utf-8 -*-

class Person(object):
    name = '小明'
    
    @classmethod
    def speak(cls):
        print('我是类方法')


if __name__=='__main__':
    ps = Person()
    Person.speak()
```
**知识点：**
- 方法前有`@classmethod`表示该方法为`类方法`。
- 方法第一个参数为`cls`。
- 类方法，使用通过`类.方法名`来调用。


#### 私有属性和方法

在Python的类中，有些属性和方法只允许在的内部调用使用，使用双下划线开头，这些属性和方法叫做`类的私有变量`。定义如下：

```python
# -*- coding:utf-8 -*-
class Person(object):
    name = '小明'
    __name = '小明'

    def __say(self):
        print(self.__name)

    def speak(self):
        print('你好，世界！')
        self.__say()


if __name__=='__main__':
    ps = Person()
    ps.speak()
```
**知识点：**
- 私有属性和方法只能在类中的方法中通过`self`实例来调用，在类外无法调用。
- 私有属性和方法存在的意义在于实现类属性和方法的隔离，防止外部修改引起的错误，实现更好的封装性，可以人为的控制暴露出的属性和方法。


#### 构造函数

Python中的类，有一个自带的方法叫做`__init__`，在我们实例化时会自动的调用该方法。该方法常常用来实现一些类的实例化时的一些初始化操作。使用方法如下：

```python

class Person(object):

    def __init__(self, name=None):
        self.name = name


if __name__=='__main__':
    person_xm = Person(name='小明')
    person_xg = Person(name='小刚')

    print('我是，%s'%person_xg.name)
    print('我是，%s'%person_xm.name)
```

除了`__init__`外，类还自带了一些其他方法，通过这些方法我们可以定制类的某些行为。

类的专有方法：

```
__init__ : 构造函数，在生成对象时调用
__del__ : 析构函数，释放对象时使用
__repr__ : 打印，转换
__setitem__ : 按照索引赋值
__getitem__: 按照索引获取值
__len__: 获得长度
__cmp__: 比较运算
__call__: 函数调用
__add__: 加运算
__sub__: 减运算
__mul__: 乘运算
__div__: 除运算
__mod__: 求余运算
__pow__: 乘方
```

### 继承和多态

#### 继承

在面向对象编程中，在类的定义时，可以通过`继承`，来获取被继承类的属性和方法。这个被继承的类，叫做`父类`、`基类`或`超类`，这个继承类叫做`子类`。这使我们在设计程序的时候，更加方便、高效，我们可以将一些公用的属性和方法放到父类中，通过子类的继承来获取，而不需要在每个子类中都定义一遍。

使用如下：

```python
# -*- coding:utf -*-


class Person(object):

    def __init__(self, name):
        self.name = name

    def say(self):
        print('你好，%s' % self.name)


class Jay(Person):

    def __init__(self):
        super(Jay, self).__init__('Jay')


class Tom(Person):

    def __init__(self):
        super(Tom, self).__init__('Tom')


if __name__=='__main__':
    jay = Jay()
    jay.say()
```

**知识点**
- 使用`class 子类(父类)` 来表示继承。
- 子类中`super(子类, self)`来表示父类，长用来在`__init__`函数中，调用父类`__init__`实现父类的初始化操作。


#### 多态

来看下这个函数：

```python
def say_hello(obj):
    obj.say() 
```
随着我们传入对象的不同，它的输出结果是不同的。这里这个obj可以是任意类对象，只要该类实现了`say`方法即可。这便是类的多态性，一种调用方式，不同的执行结果。只要`say_hello`这个函数不变，我们只需要继承`Person`类，便能继续使用`say_hello`函数。

多态，从字面理解就是多种表现形式。Python中多态提供了“接口与实现分离”。接口，是一种逻辑的抽象，在编程语言中，接口往往只定义方法，不定义方法的具体逻辑，类似Python中的父类。而具体的实现，则根据我们不同业务逻辑来实现不同的功能。这样便体现出了类的多态性。

继承是多态的基础。多态是继承的目的。

多态的好处，可以让我们定义多个类，而它们的调用不变。多态不但能改善程序的组织架构及可读性，更利于开发出“可扩充”的程序。这便是著名的“开闭”原则：
- 对扩展开放（Open for extension）：允许子类重写方法函数
- 对修改封闭（Closed for modification）：不重写，直接继承父类方法函数

#### 抽象方法

抽象方法，通常是定义在父类中的一种方法。它没有实现，只提供了方法名，并且该基类的元类是ABCMeta或从中派生出来类，该父类不能实例化，只能让子类继承。继承该父类的子类，必须实现该该方法，否则会报错。

实例：

```python
import abc

# 第一种 
class A(object):
    __metaclass__ = abc.ABCMeta

    @abc.abstractmethod
    def abtest(self):
        pass

# 第二种
# class A(object):
#     def abtest(self):
#         raise NotImplementedError

class B(A):

    def abtest(self):
        print('------')


if __name__=='__main__':
    # 实例化会抛出 TypeError: Can't instantiate abstract class A with abstract methods abtest
    # a = A()
    # a.abtest()

    b = B()
    b.abtest()
```

注意：
- 两种抽象方法各有优劣，第一种，比较复杂格式要求严格，若没有实现该方法，在运行时才能知道其错误。第二种，写法简单，当没有实现该方法时，在实例化时就知道了。


## 编程范式

编程范式(Programming Paradigm)是某种编程语言的典型编程风格或者说是编程方式。我们这里讨论2中主流的编程范式：`面向过程`(Object Oriented)和`面向对象`(Procedure Oriented)。

### 面向过程

首先来说面向过程，它是一种以过程为中心的编程思想。通过对处理问题步骤的抽象，来编写函数来解决，逐一调用即可，更多是顺序或分支结构的程序结构。

### 面向对象

面向对象编程，是一种以对象为中心的编程思想。通过对象的继承、多态来描述对象之间的复杂关系。从而实现，问题的处理。面向对象编程，更多使用的是类和对象实例来组织代码。

在编程中，面向对象和面向过程是冲突的，可以结合使用。

### 扩展阅读

[编程范式[ 程序员的编程世界观 ]](https://www.cnblogs.com/tracyzeng/articles/4108027.html)

## 使用第三方类库来实现

上面我们学习了有关面向对象编程的思想。我们分析下之前我们实现的爬虫，它的实现是按照步骤来逐一实现调用的，也就是面向过程的思想。接下来我们使用面向对象的思路来改进下。

大家可以看出，我们在实现上一章的爬虫脚本时，请求代码和解析代码还是比较繁琐的，不是很符合人类的思维方式。针对这一点呢，社区中的大牛们发布了自己的处理包，使上章的代码更优雅，更符合人类思考问题的思路。下面，我们就是用这些三方包来改进下的代码。

我们使用到的三方包主要有以下2个，分别用来处理url地址请求和解析html返回结果的：
- requests: 专门为人类提供的url处理库，官方地址：http://www.python-requests.org/en/master/
  基本简单使用如下：

```python
>>> r = requests.get('https://api.github.com/user', auth=('user', 'pass'))
>>> r.status_code
200
>>> r.headers['content-type']
'application/json; charset=utf8'
>>> r.encoding
'utf-8'
>>> r.text
u'{"type":"User"...'
>>> r.json()
{u'private_gists': 419, u'total_private_repos': 77, ...}
```

- BeautifulSoup: python爬虫中，长用的一个html解析包。官方地址：https://www.crummy.com/software/BeautifulSoup/bs4/doc/index.zh.html


```python
html_doc = """
<html><head><title>The Dormouse's story</title></head>
<body>
<p class="title"><b>The Dormouse's story</b></p>

<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""
from bs4 import BeautifulSoup
soup = BeautifulSoup(html_doc)
soup.title
# <title>The Dormouse's story</title>

soup.title.name
# u'title'

soup.title.string
# u'The Dormouse's story'

soup.title.parent.name
```

可见，`BeautifulSoup`是一个类，将html实例化，可以通过`实例.属性`的方式来调用html中的dom，比使用正则简单方便。

整体实现代码如下：

```python

def run(path='https://toutiao.io/c/ai'):
    """
    主函数
    :param path:
    :return:
    """
    ret = []
    if not path:
        print('path 参数不能为空')
        return ret
    # 1、请求
    header = {'User-Agent':
                   'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36'}
    try:
        resp = requests.get(path, headers=header)
    except Exception as err:
        print(err)
        return ret
    print(resp.status_code)
    if resp.status_code == 200:
        # 2、解析 使用 BeautifulSoup类来处理接下html
        bsobj = bs4.BeautifulSoup(resp.content, 'lxml')
        a_list = bsobj.find_all('a', target="_blank", rel="external")
        for a in a_list:
            post_dict = {}
            post_dict['title'] = 'https://toutiao.io{0}'.format(a.get_text()).replace('posts', 'k')
            post_dict['href'] = a.get('href')
            # 3、保存
            ret.append(post_dict)
    return ret

```