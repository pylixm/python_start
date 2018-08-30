
<!-- TOC -->

- [模块](#模块)
    - [模块](#模块-1)
    - [包](#包)
- [模块和包的使用](#模块和包的使用)
    - [相对导入](#相对导入)
    - [绝对导入](#绝对导入)
    - [导入中涉及到的变量](#导入中涉及到的变量)
        - [__name__](#__name__)
        - [dir()](#dir)
        - [__all__](#__all__)
    - [常出现的问题](#常出现的问题)
        - [循环导入](#循环导入)
        - [覆盖导入](#覆盖导入)
- [内建模块](#内建模块)
- [第三方模块](#第三方模块)
- [参考](#参考)

<!-- /TOC -->

## 模块

我们回顾下之前介绍的内容，我们学习了如下知识：

- Python的基本语法：数据类型、字符串、关键字、变量
- Python的高级数据结构：列表、元组、字典、集合、序列
- Python的控制结构：顺序结构、循环结构、分支结构
- Python的函数
- Python的错误异常处理

到目前为止，我们所有代码都比较简单，都是在python的交互式shell中或者简单的脚本py文件中执行。随着我们程序代码的增加，Python提供了一个`模块`的概念来组织管理复杂的代码。

### 模块

Python 代码在编写的时候，常常把相关链的变量和函数放在一起，存放在一个py脚本文件里，我们叫这个py文件为`模块`。

模块的使用除了解决复杂代码的组织问题，还有以下好处：

- 使代码结构化，可以重复的使用某功能的代码。
- 将变量放在不同的模块中，避免了变量命名的冲突。

Python提供给我们了很多内置的模块可以使用，像之前我们的练习题中的`math`模块和`copy`都是python的内置模块。

### 包

不同模块内的变量是可以使用相同名称的，但是模块的名称相同的时候，如何处理呢？Python中为了解决这个问题，定义了比模块高一级的概念`包`。

包是一个有多个模块文件组成的一个目录集合，目录内必须还有一个`__init__.py`文件。通常`__init__.py`文件是python包的一个识别条件。

```python
├── demo01
│   ├── __init__.py
│   ├── run1.py
│   └── utils.py
└── run2.py
```

## 模块和包的使用

我们将python的变量和函数组织在一个模块中，通过在其他模块中引入它来使用它。使用关键字`import`或`from ... import ...`，如下：

```python
# demo01/run1.py
from utils import add, circle
import utils

print(add(3, 5))
print(circle(4))
print(utils.add(4, 5))
print(utils.circle(4))


# run2.py 
from demo01.utils import add
import demo01.utils

print(add(3, 4))
print(demo01.utils.add(3, 4))

```

**知识点：**
- 导入模块使用时，可使用`from...import..`直接导入要使用的函数或变量来使用；也可使用`import`导入模块后，使用模块点来调用模块中的变量或函数。
- `from utils import add, circle` 可使用`from`导入多个函数或变量，使用逗号分隔。
- `from utils import *` 可使用`*`号来导入模块中的所有变量及函数，但是此种方式不利于编码的维护，所以不建议使用。
- 为进一步防止模块变量的重名，导入模块时，可使用`as` 关键字来给模块起别名，如：`from utils import add as utils_add`。
- `import demo01.utils` 只可导入到模块级别。 
- Python导入时，会根据`sys.path`中的目录路径来搜索导入的包。会先默认从本目录导入，在向外其他python系统变量中的路径搜索。可在python shell中查看，如下：
  ![](https://ws1.sinaimg.cn/large/8697aaedly1ft2jnnqcerj224s09aacx.jpg)

其中，引号表示当前目录，非模块文件所在目录。为若都没有找到要导入的包，会抛出`ImportError`错误，可通过`sys.path.append('/home/python/')`将模块路径追加到`sys.path`来添加导入的搜索路径。

### 相对导入

相对导入，是一种相对于本文件所在路径的路径导入方式。Python对相对导入的引入最早在2.4版本，可见[PEP0328](https://www.python.org/dev/peps/pep-0328/)，主要解决了`import` 导入时各级模块名的长度问题和名称的混淆问题。

Python中，使用点号来表示相对导入。如下：

```
from . import utils
```
多层次的目录结构时，可使用多个点号表示，官方推荐在使用相对导入时不超过2级的目录深度。

### 绝对导入

绝对导入，直接从`sys.path`的列表中的路径中搜索导入。绝对导入，可以运行代码程序在不同的地方执行而不用修改导入方式。

在Python3中，所有不是以`.`开头的导入均默认为是绝对导入。Python2中，默认为脚本所在目录，其次再从`sys.path`路径列表导入。为了在python2中使用默认为绝对导入，可在所有文件开头可加入如下代码：

```
from __future__ import absolute_import
```

下面我们通过例子看下：
```
$ tree
.
├── demo01
│   ├── __init__.py
│   ├── demo01_run.py
│   └── utils.py
└── run.py
```

在外层目录执行如下代码，来模拟程序调用包内模块的场景：
```
python -m demo01.demo01_run
# ModuleNotFoundError: No module named 'utils'
python2 -m demo01.demo01_run
# 成功执行
```
说明下，这里使用`-m`来执行，而不是直接使用`python demo01/demo01_run.py`是有原因的。在使用执行运行脚本时，它会将脚本所在的模块追加到`sys.path`的搜索路径列表中，即使为绝对路径搜索也是可以搜索到导入模块的，而在真实的包或者模块的调用时，是不会这样的。

通过上边的实例，可以区分python2和python3在默认导入时的区别。

那我们在使用时，如何写出更好的兼容性代码呢？可参考一下几点：

- 在模块导入时，多使用绝对导入。
- 在使用相对导入时，使用点号这种显式的方式。
- 使用`from __future__ import absolute_import` 来统一代码的导入默认行为。

### 导入中涉及到的变量

#### __name__

每个 Python 脚本中都包含的一个内建变量。当我们在执行某个脚本时，python解释器会将该变量的置为`__main__`。我们经常用这个来区别，是导入执行还是python脚本执行，如下：

```python
from utils import add , circle
import utils

if __name__=='__main__':
    print(add(3, 5))
    print(circle(4))
    print(utils.add(4, 5))
    print(utils.circle(4))
```
在我们将该模块导入其他模块时，`__name__`判断后的代码是不会执行的，只有使用`python`执行运行改模块文件时，才会执行。

#### dir()

当模块内的变量变多时，我们要找到需要的变量或者那些变量可以调用是比较麻烦的。我们可以使用内建函数`dir()`来，打印模块中可调用的变量和函数，然后再来使用。如内建模块`sys`的可调用变量为：

```python
>>> import sys
>>> dir(sys)
['__displayhook__', '__doc__', '__excepthook__', '__name__', '__package__', '__stderr__', '__stdin__', '__stdout__', '_clear_type_cache', '_current_frames', '_getframe', '_git', 'api_version', 'argv', 'builtin_module_names', 'byteorder', 'call_tracing', 'callstats', 'copyright', 'displayhook', 'dont_write_bytecode', 'exc_clear', 'exc_info', 'exc_type', 'excepthook', 'exec_prefix', 'executable', 'exit', 'flags', 'float_info', 'float_repr_style', 'getcheckinterval', 'getdefaultencoding', 'getdlopenflags', 'getfilesystemencoding', 'getprofile', 'getrecursionlimit', 'getrefcount', 'getsizeof', 'gettrace', 'hexversion', 'long_info', 'maxint', 'maxsize', 'maxunicode', 'meta_path', 'modules', 'path', 'path_hooks', 'path_importer_cache', 'platform', 'prefix', 'ps1', 'ps2', 'py3kwarning', 'setcheckinterval', 'setdlopenflags', 'setprofile', 'setrecursionlimit', 'settrace', 'stderr', 'stdin', 'stdout', 'subversion', 'version', 'version_info', 'warnoptions']
```

#### __all__

之前我们说过，可以使用`from utils import *`来导入模块中所有变量。虽然不推荐这种做法，是因为`*`比较模糊，维护不方便。但是我们可以使用内建变量`__all__`来控制这个`*`可以导入的模块，来进一步明确这个`*`的导入。操作如下：

```python
# utils.py
...

__all__ = ['add']
...

# demo01_run.py 
...
from utils import * 
...
```
在执行时，会报如下错误：`NameError: name 'circle' is not defined`。


### 常出现的问题

下边总结了些，我们在Python包和模块使用时常犯得错误。

#### 循环导入

我们在开发项目时，导入其他编写好的包时，常常会出现这样一种情况：我们导入的包或模块，也同时引用了我们正在编辑的包或模块。这样便造成了循环引用，出现了类似“先有鸡，还是先有蛋”的问题。

避免这种错误的方法有以下几点：
- 做好项目的功能结构划分，分好层级；
- 导入时，导入模块而非模块的属性；
- 在使用时，再导入，比如讲导入语句放到函数中；

#### 覆盖导入

我们在编写模块的时候，无意中起名和python内部模块或已有模块重名。在导入时，后导入的模块会覆盖之前导入的模块，从而使模块无法正常使用。

这种错误的解决方式有以下几点：
- 谨慎命名避免重复
- 在必须导入3个重复名字的模块时，使用`as`关键字来设置别名区分。

## 内建模块

内建模块，是Python自带的一些供大家使用的模块，在Python安装完之后，无需安装配置即可使用。之前我们接触到的`sys`模块就是内建模块。

Python的内建模块包括的功能很强大，它现在仍在不断的扩展。随着Python的发展，Python 会逐渐吸收一些优秀的模块，加到内建模块中，方便大家使用。

常用的内建模块有：

- `os` 提供和操作系统有关的功能 
- `sys` 提供和python自身相关功能
- `datetime`、 `date`和 `time` 提供和时间有关的相关功能
- `math` 提供和数据计算相关的功能
- `urllib` 提供和http请求相关的功能
- `re` 提供正则表达式相关功能

更多内建模块可见[官方文档：全局模块索引](https://docs.python.org/3/py-modindex.html)。

## 第三方模块

第三方模块，是有用户自己开发并打包分发的python模块。Python社区的活跃度非常高，也给我们提供了多种分发方式，主要有以下几种：

- 通过网站[Python Packaging Index](https://pypi.org/)，它是一个Python包的公共存储可，我们可以免费从上边下载包使用，只需要遵循包的相应开源协议即可。
- 代码包，发布在在[github](http://github.com)等代码托管网站，直接安装源码包。

Python提供给我们了一些打包和安装工具，可以将三方包，安装到Python的`site-package`目录下，供我们使用。我们常用的工具如下：

- `distutils` 此工具为1998年首次添加到Python的原始分发构建模块，至今仍可使用。
- `setuptools` 是`distutils` 的替代品，对包之间的依赖关系的处理有很好的支持。
- `wheel` 一种二进制的打包格式，可直接安装在操作系统上，无需本地构建。详见[PEP0427](https://www.python.org/dev/peps/pep-0427/)。

其他比较火热的第三方模块：

- `requests` http请求处理的第三方模块。
- `celery` python的分布式异步队列。
- `django` 大而全的python web 的开发框架。
- `flask` 简单灵活的python web 框架。

## 参考

- [http://www.cnblogs.com/xueweihan/p/5118222.html](http://www.cnblogs.com/xueweihan/p/5118222.html)
- [https://medium.com/pyladies-taiwan/python-%E7%9A%84-import-%E9%99%B7%E9%98%B1-3538e74f57e3](https://medium.com/pyladies-taiwan/python-%E7%9A%84-import-%E9%99%B7%E9%98%B1-3538e74f57e3)
- [pep0328](https://www.python.org/dev/peps/pep-0328/)
- [https://docs.python.org/3/tutorial/modules.html#modules](https://docs.python.org/3/tutorial/modules.html#modules)
  http://www.cnblogs.com/jasonzeng888/p/6477752.html