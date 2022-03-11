---
title: "简单结构"
date: 2022-03-11T20:00:23+08:00
weight: 2
chapter: true
pre: "<b>2.3 </b>"
---

## 基本语法

从本章开始，我们开始学习Python的基本语法。

### Hello World

在上一章，我们python环境已经安装完毕，也选择好了合适自己的编辑器，现在我们开始进入python的编程世界。

让我们来看一个python的简单程序，`HelloWorld`。

**解释器运行**

打开系统终端输入`python`，启动python解释器，录入如下代码，回车观察返回值。

```python
$ python3
Python 3.6.5 (default, Mar 30 2018, 06:42:10)
[GCC 4.2.1 Compatible Apple LLVM 9.0.0 (clang-900.0.39.2)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> print('Hello world!')
```

代码返回如下：

```
Hello world!
```

下面我们来解释下这段代码：
- 1、`>>>` python的交互环境的提示符
- 2、`print` python 内置的打印方法，它会打印括号中的字符串。

**代码文件运行**

我们可以把以上代码保存成python的脚本文件，并保存为`hello.py`, python的代码文件是以`py`作为扩展名的。下面我们来执行它，在我们的系统终端执行如下命令：

```
$python hello.py
```

代码返回如下：

```
Hello world!
你好，世界！
```

到这，我们知道了如何在python 解释器和代码文件中运行代码。

#### 代码注释

**定义**

我们在编写代码的时候，往往需要添加一些说明以帮助我们来理解代码逻辑，在代码执行的时候，这些说明不会被执行。这些说明叫做`注释`。Python中的注释，使用井号、单引号或双引号标识，如下：

```python
# 单行注释

'''
我是多行注释
我是多行注释
'''

"""
我是多行注释
我是多行注释
"""
```

**注释常用场景**

- 脚本文本的开头，说明脚本主要功能。
- 代码中，说明代码功能。
- 函数和方法名(是python中的语法结构，后边会讲到)下，作为说明出现。


#### 关键字

程序设计中，预留了一些标识符号供语言本身或系统使用，这些标识符被称为`关键字`。每个关键字都有自己的含义，在python中可通过以下方式查看关键字：

```python
>>> import keyword
>>> keyword.kwlist
['and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'exec', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'not', 'or', 'pass', 'print', 'raise', 'return', 'try', 'while', 'with', 'yield']
```
我们可以通过如下方法判断字符串是否为关键字：

```python
>>> keyword.iskeyword('and')
True
>>> keyword.iskeyword('have')
False
```

关键字不可作为变量的名称使用。

#### 输入输出

计算机要处理任务，则需要与人交互。那么python中的输入输出是如何实现的呢？

python2 中提供了输入函数 `input`、`raw_input`来输入，函数`print` 来输出。python3中`raw_input`函数去掉了，功能合并到`input`。

**输入**

先来看下，python2 中`raw_input`。

```python
>>> raw_input('请输入：')
>>> raw_input('请输入：')
请输入：123
'123'
>>> raw_input('请输入：')
请输入：abc
'abc'
```
可见 `raw_input` 返回我们输入的数据为一个字符串返回。

再来看下 `input`，它除了支持字符串还支持表达式，如下：

```python
>>> input('请输入：')
请输入：1+2
3
>>>
```

可见它输出了表达式的值，也就是说它执行了表达式。试想，如果这里放一个破坏我们系统执行的表达式，我们系统便会受到安全威胁。在人们衡量后，在python3中，决定去除该函数，并将原来的`raw_input` 改名为 `input`。

**输出**

python2 中使用`print`语句来输出，python3中则改为了 `print`函数。

```python
>>> print('你好', end=',')
你好,>>> a = '世界'
>>> print('你好，%s'%a)
你好，世界
>>> print('你好', '世界')
你好世界
```

语法说明：
- `end=`参数, 传递给参数的字符将追加到打印字符串结尾，当省略时默认为回车换行。
- `print` 函数可传入多个字符串来打印，当传入多个时，会自动合并链接。
- `print` 函数常被用来在调试代码时，打印变量使用。


### 基本类型
#### 数据类型

变量中储存数据的类型可以是多种多样的，我们把变量中存储数据的类型叫`数据类型`。而python 中常用的数据类型，有这么几种：

- `整型(int)`: Python3中可以处理任意大小的整数（Python 2.x中有int和long两种类型的整数，但这种区分对Python来说意义不大，在python中内存分配是自动的，用户并不关心这些细节，反而给用户无限大的使用空间更好，因此在Python 3.x中整数只有int这一种了），而且支持二进制（如0b100，换算成十进制是4）、八进制（如0o100，换算成十进制是64）、十进制（100）和十六进制（0x100，换算成十进制是256）的表示法。

- `复数型(complex)`：形如3+5j，跟数学上的复数表示一样，唯一不同的是虚部的i换成了j。

- `浮点型(float)`：浮点数也就是小数，之所以称为浮点数，是因为按照科学记数法表示时，一个浮点数的小数点位置是可变的，浮点数除了数学写法（如123.456）之外还支持科学计数法（如1.23456e2）。

- `字符串型(str)`：字符串是以单引号或双引号括起来的任意文本，比如'hello'和"hello",字符串还有原始字符串表示法、字节字符串表示法、Unicode字符串表示法，而且可以书写成多行的形式（用三个单引号或三个双引号开头，三个单引号或三个双引号结尾）。

- `布尔型(bool)`：布尔值只有True、False两种值，要么是True，要么是False，在Python中，可以直接用True、False表示布尔值（请注意大小写），也可以通过布尔运算计算出来（例如3 < 5会产生布尔值True，而2 == 1会产生布尔值False）。python中把None、0、'' 都视为False。

- `空类型`: `None` 为空类型。

各数据类型之间是可以相互转换的，可使用Python提供的内置函数：

- int()：将一个数值或字符串转换成整数，可以指定进制。
- float()：将一个字符串转换成浮点数。
- str()：将指定的对象转换成字符串形式，可以指定编码。

另外2个字符转化内置函数：
- chr()：将整数转换成该ASCII编码对应的字符串（一个字符,如 chr(65) 返回为字符串 'A' ）。
- ord()：将字符串转换成对应的ASCII编码。

### 变量、常量

#### 变量

`变量`（英语：Variable，scalar），是内存中实际存在的数据或存储器中存储数据的一块内存空间地址，变量的值可以被读取和修改。

Python 中的变量是内存中保存数据的地址的一个别称。变量是没有类型的，变量对应的值对象是有类型的。变量的类型是跟着值对象走的。严格来说，类型是属于对象的，而不是变量, 变量和对象是分离的，对象是内存中储存数据的实体，变量则是指向对象的内存地址。

#### 变量赋值

在Python 的语法中，变量无需声明，可直接使用，区别于其他语言，有些语言的变量需要先声明后使用。python中，使用像上边代码中的等号‘=’来表示`赋值`，即表示将等号右边的数据赋值给左边的变量，变量的类型有它存储的数值来决定。

```python
>>> a = 123
>>> b = '123'
>>> c = True
>>> print(type(a),type(b),type(c))
(<type 'int'>, <type 'str'>, <type 'bool'>)
>>> d, e = 1,2
```

>知识点：
>- 我们可以使用内置函数 `type` 来查看变量的数据类型。
>- 可同时给多个变量赋值

#### 变量命名

变量的命令是一个重要的规则，好的命名可以增加代码的可读性，提高代码的可维护性。python中变量的命名有如下要求：
- 只能使用数据、字母和下划线，且只能以字母或下划线开头。
- 大小写敏感，即区分大小写。a和A 是2个变量名。
- 不能使用关键字及系统预留字重复。

>说明：
>- 系统预留字，及系统的函数及模块的名字。

除了以上这些硬性规定外，变量命名还是比较宽松的。为了更好的处理命名问题，python 社区指定了一个开发规范 [PEP8](https://www.python.org/dev/peps/pep-0008/)。其中除了命名规范外，还描述了其他一些语法的最佳实践。它可作为我们日后编写代码及项目开发的规范使用，对我们提高代码的质量有很大的好处。

#### 变量的作用域

作用域，顾名思义，可以理解为在一定范围内起作用。映射到我们的编码中时，便是在一定的代码范围内有效。在编码时，代码分各种逻辑块的，类似我们文章的段落，可以更好的理解阅读分类。那么在这些限定的范围内，变量的有效性是有影响的。根据有效范围作用域分为`全局变量（Local）`和`局部变量（Global）`。`全局变量` 是在整个程序系统或文件全局中有效的变量。`局部变量`表示在一个代码逻辑块中有效的变量。全局和局部变量是相对的，如我们可以叫一个相对于文件来说的全局变量，但是在多个文件或者文件包中便成了局部变量。除了全局和局部变量，Python中还有以下作用域：

- E （Enclosing） 闭包函数外的函数中
- B （Built-in） 内建作用域

作用域以 L –> E –> G –>B 的规则查找，即：在局部找不到，便会去局部外的局部找（例如闭包，稍后会讲），再找不到就会去全局找，再者去内建中找。

#### 常量

变量中存储的数值是可以变动的，那么有没有不变的呢？答案是有的，如数学计算使用的`PI`。我们经常把代码中，存储不变数据的变量称之为`常量`。可见，常量是一种特殊的变量。

### 字符串与编码

上节我们谈到变量有许多的数据类型，其中字符串类型比较复杂，这里单独讲解下。

#### 字符转义

字符串，故名思意，是零个或多个字符组成的有限序列，以单引号或双引号包裹。既然是以字符组成，那么便包括单引号或双引号自身，如下：

```python
a = 'I'm DeanWu!'
```
我们在执行以上语句的时候会报错：

```
>>> a = 'I'm DeanWu!'
  File "<stdin>", line 1
    a = 'I'm DeanWu!'
           ^
SyntaxError: invalid syntax
```
这是因为字符串中间的单引号把字符串给截断了，当python解释器执行到该单引号后，后边的语法便报错了。如何解决？这便需要进行转义，所谓转义，便是让python解释器解释执行的时候，被转义的字符不在表示原先的意思，而变为不解释执行的字符串。Python中使用 `\` 进行转义。上边的语法可以写成这样：

```python
a = 'I\'m DeanWu!'
```

#### 字符编码

在计算机基础部分，我们知道了计算机编码相关知识,概括如下：

- ASCII 使用1个字节，只支持英文；
- GB2312 使用2个字节，支持6700+汉字；
- GBK GB2312的升级版，支持21000+汉字；
- Unicode编码，使用2、3或4个字节表示字符；
- UTF 编码为Unicode编码的一种可变长实现；
- 计算机内存中使用Unicode编码处理，存储和传输则使用UTF编码。

接下来，我们看下Python中的编码。Python2中的默认编码为`ASCII编码`，Python3中使用的则是`UTF-8编码`。可使用如下命令查看：

```python
import sys 
sys.getdefaultencoding()
```

在Python 编程中，编码问题往往是新手的一个困扰，我们来深入展开说下这个问题。Python2和Python3的默认编码是不一样的，它们处理编码方式也是不一样。

**Python2中的编码**

在 Python2中字符串类型有4种：`str`、`unicode`、`basestring`和`bytes`。

- `basestring` 是一个基类（稍后会讲到此概念，可暂时理解为父亲或基础即可），`str`和`unicode`类型在此基础上构建。
- `str` 是Python设计之初的字符串类型，实际上是一个字节串，默认使用系统编码。
- `unicode`  是了使Python支持Unicode编码的字符串，在2.0版本之后添加的一种字符串类型。
- `bytes` 是字节串，`str`本身便是一个字节串，那可认为`bytes`是`str`的一个别称，使用和`str`完全一致。

看一实例：

```python
>>> a = '你好'  # 使用系统编码的str 类型
>>> type(a)
<type 'str'>
>>> print a
你好
>>> a
'\xe4\xbd\xa0\xe5\xa5\xbd'  # 系统编码为 utf-8的字节码，使用了3个字节表示一个汉字；
>>> ua = u'你好'   # unicode 编码
>>> type(ua)
<type 'unicode'>
>>> print ua
你好
>>> ua
u'\u4f60\u597d'  # unicode 编码格式的你好，对应unicode代码表中代码
>>> b = b'你好'  # bytes 类型的字符串
>>> type(b)  
<type 'str'>
>>> print b
你好
>>> b  
'\xe4\xbd\xa0\xe5\xa5\xbd'  # 使用3个字节表示1个汉字，且字节码和str类型一样；
```

>知识点：
>- Python 解释器默认使用系统的编码方式声明变量。
>- unicode类型以u开头标识
>- bytes 类型以 b 开头标识
>- str类型以16进制的ASCII字节码表示，实际上是一个字节串，回应了它的另一个名字bytes。
>- unicode类型以unicode字符码表示，是真正的字符串。

`str`与`unicode` 类型之间是可以相互转化的，通过 `encode`和`decode` 来实现。来看实例：

```python
>>> a = '你好'
>>> a.decode('utf-8')  # decode 解码: str类型(UTF-8) --> unicode类型(Unicode)
u'\u4f60\u597d'  # 对应的unicode代码
>>> ua
u'\u4f60\u597d'
>>> ua.encode('utf-8')  # encode 编码: unicode类型(Unicode) --> str类型(UTF-8)
'\xe4\xbd\xa0\xe5\xa5\xbd'  # utf-8的编码的 str字节串
>>> a.decode('gbk')  # 尝试使用 gbk 解码，结果出现乱码
u'\u6d63\u72b2\u30bd'
>>> print a.decode('gbk')  
浣犲ソ
>>> a.decode('gbk').encode('gbk')  # 使用 gbk 解码，再编码，成功还原
'\xe4\xbd\xa0\xe5\xa5\xbd'
>>> print a.decode('gbk').encode('gbk')
你好
>>> a.decode('gbk').encode('utf-8')  # 使用不同的编码来解码和编码, 也出现乱码
'\xe6\xb5\xa3\xe7\x8a\xb2\xe3\x82\xbd'
>>> print a.decode('gbk').encode('utf-8')
浣犲ソ
```

>知识点：
>- 使用什么编码编码，就需要使用什么编码解码，否则会出现乱码。
>- 转为`unicode`类型要decode解码。
>- 转为`str`类型要encode编码。
>- 区分Unicode类型和Unicode编码，Unicode编码是一套编码集，内容丰富，编码内容涵盖了世界各地的语言，实现方式有UTF-8、UTF-16、UTF-32；Unicode类型只是python字符串的一种类型，使用Unicode编码作为其编码格式，可经过各种编码方式（UTF、GBK、ASCII）编码成str类型字符或者叫bytes（字节序列）类型供计算机使用。
>- Unicode 编码的兼容性，可作为其他编码格式的转码的中间站。


上边这些例子是直接在python解释器中跑的代码，而python代码文件在执行时，有所不同，会受到文件编码的影响。

文件的存储是以二进制流的方式保存在硬盘上。当Python文件被执行时，这些文件会以二进制流的方式加载到内存中，然后按照Python的默认编码方式解码成相应的python代码对应的unicode编码的字节码来解释执行。在python2中，默认编码为ASCII码，那么当文件中有非ASCII码时，这个解码过程便会出错。Python 为了解决这个问题，在Python 文件头部增加了文件的编码声明，[PEP236](https://www.python.org/dev/peps/pep-0263/)就是为这个问题而创建的改进意见。

Python 文件格式声明如下：

```python
# coding:utf-8 
```
或

```python
# -*- coding:utf-8 -*- 
```

来看实例 `coding_utf.py`：

```python
# -*- coding:utf-8 -*- 

a = '你好'
print(a)
print(type(a))

ua = u'你好'
print(ua)
print(type(ua))

```
在命令行执行`python coding_utf.py`, 返回如下：

```
你好
<type 'str'>
你好
<type 'unicode'>
```

ok,没有问题。来看下执行时都做了些什么事：

- 1、python解释器读取文件头部声明的编码，将其作为变量解码时使用的编码。
- 2、python读取代码，自动将非unicode编码的变量解码成unicode给计算机内存使用。
- 3、python解释器执行代码，打印utf-8编码的a和unicode编码的ua。

接下来，当我们把文件编码和开头标识改为`GBK`后，再次执行：

```bash
���
<type 'str'>
你好
你好
<type 'unicode'>
```

大家看到第一个`你好`成为乱码，这是为什么? 根据上边的分析，打印时，a的编码为gbk, ua的编码为unicode。而我终端的编码为utf-8,对gbk的不兼容，所以导致乱码。同样的代码，当放到默认gbk编码的windows终端中，则会输出正常。

那么有没有办法让他输出正常呢？有的，我们只要手动显示的把它解码成兼容的 unicode 编码即可。

```python
print(a.decode('gbk'))
```

到这里，总结以上说的问题，影响Python 字符编码的地方主要有以下几点：
- Python解释器的默认编码，python2中默认为ASCII。
- Python源文件文件声明编码及保存的编码方式。
- 系统终端使用的编码，会影响python文件执行时的编码方式。
- 操作系统的编码，会影响终端的编码方式。

**Python3中编码**

在python2中，编码问题有2个大的问题：

- 使用 ASCII 作为默认编码方式，不能很好的支持非ASCII码字符；
- 将字符串分为了`str` 和 `unicode`两种类型，让大家容易混淆；

Python3 对以上问题做了很好的修正。Python3 默认编码改为了 `UTF-8`,对于非ASCII码支持更强大。其次，Python3 将Python2的`unicode` 类型，改名为 `str` 类型，而Python2的`str`类型和`bytes`则合并统一使用使用 `bytes`类型来表示字节类型。这样很好的区分了字符串和字节串，`str`即为字符串，`bytes` 为字节串或叫二进制字节。

看下面的例子：

```python
>>> a = '你好'  # utf-8 编码的str类型
>>> type(a)
<class 'str'> 
>>> print(a)
你好
>>> repr(a)
"'你好'"
>>> a  # 显示为原字符串
'你好'
>>> ua = u'你好'
>>> type(ua)    # unicode 编码的 str类型
<class 'str'>
>>> print(ua)
你好
>>> repr(ua)
"'你好'"
>>> ua  # 可以看到，字符串直接显示并没有显示unicode编码
'你好'
>>> a.decode('utf-8')  # unicode 字符串已不能再解码了
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'str' object has no attribute 'decode'
>>> a.encode('utf-8')  # 显示为字节串
b'\xe4\xbd\xa0\xe5\xa5\xbd'
>>> e = 'Test'
>>> e
'Test'
>>> e.encode('utf-8')  # 显示为原字符串
b'Test'
```

>知识点：
>- 1、可以看到Unicode类型的字符串类型为 str，带不带u 是一样的。
>- 2、unicode 字符串编码成utf-8格式的字节码，前边带 b 说明是bytes 字节类型。
>- 3、在Python3中，所有unicode编码显示均为原字符串，非 unicode 编码的 非ASCII码范围的字符 显示均为字节串。

#### 字符串操作

**字符串中的运算符**

- Python 中字符串可以直接使用 "+" 链接。

```python
>>> a = '你好'
>>> b = '世界'
>>> print(a+b)
你好世界
```

- "*2" 标识2次重复输出

```python
>>> print(a*2)
你好你好
```

- `len` 内建函数，可以获取字符串的字节长度

```python
>>> a = '你好'
>>> a 
'\xe4\xbd\xa0\xe5\xa5\xbd'  # utf-8 编码，3个字节代表一个汉字。
print(len(a))
>>> e = 'hello'
>>> print(len(e))
5
```

- `[]` 索引，可通过下标的方式来获取字符串的某个字节，下标是从 0 开始的，最后一个为'字符串长度-1'或'-1'。

```python
>>> print(a)
helloworld
>>> a[2]
'l'
>>> a[0]
'h'
>>> a[9]
'd'
>>> a[-1]
'd'
```

- `[:]` 切片，可使用字符串下标来截取字符串

```python
>>> a = 'helloworld'
>>> a[0:5]
'hello'
```

> 说明：
> - 下标从零开始
> - 开始可以省略，默认为0
> - 结束可以省略，默认为-1

- `in` 判断某变量是否在字符串中。

```python
>>> a = 'helloworld'
>>> 'hello' in a
True
```


**格式化**

有时候，我们要将多个变量组成一个我们需要的字符串来使用，这个过程叫做`格式化`。Python中格式化的方式有两种，一种使用‘%’ ，一种是使用`format`内建函数。

- 使用 `%` 格式化，格式化变量，依次由左向右对应。

```python
>>> c = '%s,%s!'%(a,b)  
>>> print(c)
你好,世界!
```

使用 `%`来格式化时，`%s`中的`s`叫做`占位符`，不同的类型需要不同的`占位符`，如下：

| 占位符  | 类型     |
| ---- | ------ |
| %d   | 整数     |
| %f   | 浮点数    |
| %s   | 字符串    |
| %x   | 十六进制整数 |

- 使用`format` 格式化，格式化变量，由左向右根据大括号的序号对应。

```python
>>> d = '{1}，{0}'.format('世界','你好')
>>> print(d)
你好，世界
```

**常用方法**

可使用内建函数`dir`查看某对象的方法:

```python
>>> dir(a)
['__add__', '__class__', '__contains__', '__delattr__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__getslice__', '__gt__', '__hash__', '__init__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '_formatter_field_name_split', '_formatter_parser', 'capitalize', 'center', 'count', 'decode', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'index', 'isalnum', 'isalpha', 'isdigit', 'islower', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'partition', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']
>>>
```

- `endswith(‘’)` 是否以某字符串结尾，是则返回`True`，否则返回`False`；
- `startswith('')` 是否以某字符串开始，是则返回`True`，否则返回`False`；
- `split()` 分隔字符串，返回一个列表（python高级数据结构，稍后讲解）。
- `lower()` 转为小写。
- `upper()` 转为大写。
- `strip('')` 2侧去除某字符串。
- `lstrip('')` 左侧去除某字符串。
- `rstrip('')` 右侧去除某字符串。
- `join()` 以某字符串为连接符，合并某列表。  
- `ljust(int)` 左对齐，并使用空格填充至指定长度的新字符串
- `rjust(int)` 右对齐，并使用空格填充至指定长度的新字符串

### 运算符与表达式

在编程世界中的表达式和我们数学上的表达式有些类似，都是一种句法，此处意义更纯粹，有运算符和操作数组成。例如：`2+3`，其中像`+`类似的符号即为`运算符`，可通过它对操作数做相应操作。2和3 即为操作数。

#### 运算符

运算符，顾名思义，可以通过它对一些变量做`运算`处理。此处的运算除了简单的四则运算，还包括比较、赋值等复杂操作。Python中运算符主要包括以下几种：

- 算数运算符
- 比较运算符
- 逻辑运算符
- 赋值运算符
- 位运算符
- 成员运算符
- 身份运算符

下面我们一一来看。

**算数运算符**

算数运算符，主要对程序中的变量做四则运算使用。主要包含如下运算符：

![](/images/arithmetic.png)

**比较运算符**

比较运算符，用来比较变量是否相等，返回置为布尔类型。主要包含以下运算符：

![](/images/compare.png)

>注意：
> - python2中运行不同类型的变量作比较，比较时会自动做类型转化，由简单类型向复杂类型转变。
> - python3只允许同类型变量比较。

**逻辑运算符**

逻辑运算符，将变量按一定逻辑组合成一个新的表达式，该表达式返回一个布尔类型的值。常常用来作为判断条件时间，判断组合的新表达式是否成立。逻辑运算符主要包含以下：

![](/images/logic.png)

**赋值运算符**

赋值运算符，即将某个值赋给默一遍量的运算符。主要包含如下：

![](/images/assignment.png)

>注意：
>- `//=`与`/=` 在python2中都为取整除法


**位运算符**

位运算符，是针对二进制数据的一种字节位的运算，主要包含以下符号：

![](/images/bit.png)


**成员运算符**

成员运算符，判断某变量是否包含另一变量。主要包含以下符号：

![](/images/member.png)

**身份运算符**

身份运算符，判断是否2个变量的值及引用的内存地址是否一样。

![](/images/identiry.png)

> 注意：
> - 与比较运算符`==` 比较，`==` 为值相等即可，内存引用地址可不同；`is`则为值和内存引用地址均相同。

**运算符优先级**

各运算符按照优先级由高到低的顺序排列如下：

![](/images/priority.png)

#### 表达式

前边说过，表达式是一种句法，有`运算符`和`操作数`组成。下面我们来看一个例子：

```python
# -*- coding:utf-8 -*- 

length = 10
width = 5
area = length*width
print(area)
```

本例子，使用表达式计算了长方形的面积。变量`length`表示长方形的长，变量`width` 表示长方形的宽，使用算数运算符`*`，利用表达式进行了乘法计算。通过赋值运算符`=`将表达式的值赋值给了变量 `area`。这个过程中，表达式在其中起了核心逻辑的作用。这个简单的程序也映射了我们平时的编码过程，表达式在其中起了至关重要的作用，我们要善加利用。


## 总结

到此，我们把Python的基本语法和数据结构过了一遍。那我们来总结下，我们都学到了什么：

- 1、Python的基本语法：变量、常量、基本数据类型、字符串和编码、运算符与表达式；

这些只是基本语法的组成元素。在程序运行时，可能会有多种情况，需要对这些结构做判断或者需要按顺序读取列表的全部元素，那么这个时候便需要逻辑处理结构。下一章，我们来讲解Python中的逻辑处理的控制流语法。

## 练习 

- 1、如何检查一个字符串是否以某个字符串开头或结尾？

```python
'Hello'.startswith('H')
'Hello'.endswith('o')
```
- 2、你想通过某种对齐方式来格式化字符串？

```python
>>> text = 'Hello World'
>>> text.ljust(20)
'Hello World         '
>>> text.rjust(20)
'         Hello World'
>>> text.center(20)
'    Hello World     '
>>>
```
- 3、华氏温度转摄氏温度。

```Python
"""
将华氏温度转换为摄氏温度公式：
F = 1.8C + 32
"""

f = float(input('请输入华氏温度: '))
c = (f - 32) / 1.8
print('%.1f华氏度 = %.1f摄氏度' % (f, c))

```

- 4、输入圆的半径计算计算周长和面积。

```Python
"""
输入半径计算圆的周长和面积
"""
import math

radius = float(input('请输入圆的半径: '))
perimeter = 2 * math.pi * radius
area = math.pi * radius * radius
print('周长: %.2f' % perimeter)
print('面积: %.2f' % area)

```

- 5、输入年份判断是不是闰年。

```Python
"""
输入年份 如果是闰年输出True 否则输出False
"""

year = int(input('请输入年份: '))
# 如果代码太长写成一行不便于阅读 可以使用\或()折行
is_leap = (year % 4 == 0 and year % 100 != 0 or
           year % 400 == 0)
print(is_leap)
```


## 参考：

- [https://github.com/jackfrued/Python-100-Days/blob/master/Day01-15/Day02/%E8%AF%AD%E8%A8%80%E5%85%83%E7%B4%A0.md](https://github.com/jackfrued/Python-100-Days/blob/master/Day01-15/Day02/%E8%AF%AD%E8%A8%80%E5%85%83%E7%B4%A0.md)

- [http://www.runoob.com/python/python-basic-syntax.html](http://www.runoob.com/python/python-basic-syntax.html)
- [python 之路，致那些年，我们依然没搞明白的编码](https://www.cnblogs.com/alex3714/articles/7550940.html)
- [熟悉又陌生的字符编码](https://funhacks.net/2016/11/25/character_encoding/)