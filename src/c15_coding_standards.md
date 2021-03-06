# 第十五章 代码质量控制

<!-- TOC -->

- [第十五章 代码质量控制](#第十五章-代码质量控制)
  - [编码规范](#编码规范)
  - [python之禅](#python之禅)
  - [测试](#测试)
    - [调试（pdb）](#调试pdb)
    - [单元测试](#单元测试)
  - [参考](#参考)

<!-- /TOC -->


## 编码规范

Python语言语法向来以简洁著称，更是以强制缩进作为了语法的一部分。但是即使这样，在编写的时候不同的人也是有不同的风格的，在代码后期的维护和阅读上造成一定的困难。为了约束和统一人们的编码风格，出现了很多规范编码的规范，其中使用最广泛的便是 

- [PEP8](https://www.python.org/dev/peps/pep-0008/)
- [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html)。

Python 规范是冗长的，大多数人都记不住。为了解决这一问题，代码规范插件便诞生了，人们依赖这些插件来检测代码的规范化，如Pylint 和 Flake8。

- [Pylint官方文档](https://www.pylint.org/)
- [Flake8官方文档](http://flake8.pycqa.org/en/latest/)

## python之禅

除了编码规范外，Python开发者还增加了一个彩蛋--- Python 之禅。大家在Python shell中导入`this`关键字即可看到。大致意思如下：

```python
Beautiful is better than ugly.
# 优美胜于丑陋（Python以编写优美的代码为目标）
Explicit is better than implicit.
# 明了胜于晦涩（优美的代码应当是明了的，命名规范，风格相似） 
Simple is better than complex.
# 简洁胜于复杂（优美的代码应当是简洁的，不要有复杂的内部实现） 
Complex is better than complicated.
# 复杂胜于凌乱（如果复杂不可避免，那代码间也不能有难懂的关系，要保持接口简洁）
Flat is better than nested.
# 扁平胜于嵌套（优美的代码应当是扁平的，不能有太多的嵌套） 
Sparse is better than dense.
# 间隔胜于紧凑（优美的代码有适当的间隔，不要奢望一行代码解决问题） 
Readability counts.
# 可读性很重要（优美的代码是可读的） 
Special cases aren't special enough to break the rules.
Although practicality beats purity.
# 即便假借特例的实用性之名，也不可违背这些规则（这些规则至高无上） 
Errors should never pass silently.
Unless explicitly silenced.
# 不要包容所有错误，除非你确定需要这样做（精准地捕获异常，不写except:pass风格的代码） 
In the face of ambiguity, refuse the temptation to guess.
# 当存在多种可能，不要尝试去猜测 
There should be one-- and preferably only one --obvious way to do it.
# 而是尽量找一种，最好是唯一一种明显的解决方案（如果不确定，就用穷举法） 
Although that way may not be obvious at first unless you're Dutch.
# 虽然这并不容易，因为你不是 Python 之父（这里的Dutch是指Guido）
Now is better than never.
Although never is often better than *right* now.
# 做也许好过不做，但不假思索就动手还不如不做（动手之前要细思量）
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
# 如果你无法向人描述你的方案，那肯定不是一个好方案；反之亦然（方案测评标准） 
Namespaces are one honking great idea -- let's do more of those!
# 命名空间是一种绝妙的理念，我们应当多加利用（倡导与号召）
```

这些都是我们在编码时的一些指导建议。


## 测试

写出优秀的代码，除了遵循一定的编码规范外，那便是测试了。

### 调试（pdb）

Python内建了一个代码调式模块，可在执行Python代码时，启动命令行调式工具，根据我们增加的断点来查看Python代码中对象栈的值。

简单使用如下：

```python
# -*- coding:utf-8 -*-
import pdb


def add(a, b):
    return a+b


def main(a, b):
    pdb.set_trace()  # 在代码中设置跟踪点(断点)
    c = add(a, b)
    print(c)


if __name__ == '__main__':
    main(3, 4)

```

```bash
-> c = add(a, b)
(Pdb) c
7
```

各命令解释如下：

```
1）进入命令行Debug模式，python -m pdb xxx.py

2）h：（help）帮助

3）w：（where）打印当前执行堆栈

4）d：（down）执行跳转到在当前堆栈的深一层（个人没觉得有什么用处）

5）u：（up）执行跳转到当前堆栈的上一层

6）b：（break）添加断点

                b 列出当前所有断点，和断点执行到统计次数

                b line_no：当前脚本的line_no行添加断点

                b filename:line_no：脚本filename的line_no行添加断点

                b function：在函数function的第一条可执行语句处添加断点

7）tbreak：（temporary break）临时断点

                在第一次执行到这个断点之后，就自动删除这个断点，用法和b一样

8）cl：（clear）清除断点

            cl 清除所有断点

            cl bpnumber1 bpnumber2... 清除断点号为bpnumber1,bpnumber2...的断点

            cl lineno 清除当前脚本lineno行的断点

            cl filename:line_no 清除脚本filename的line_no行的断点

9）disable：停用断点，参数为bpnumber，和cl的区别是，断点依然存在，只是不启用

10）enable：激活断点，参数为bpnumber

11）s：（step）执行下一条命令

            如果本句是函数调用，则s会执行到函数的第一句

12）n：（next）执行下一条语句

            如果本句是函数调用，则执行函数，接着执行当前执行语句的下一条。

13）r：（return）执行当前运行函数到结束

14）c：（continue）继续执行，直到遇到下一条断点

15）l：（list）列出源码

                l 列出当前执行语句周围11条代码

                l first 列出first行周围11条代码

                l first second 列出first--second范围的代码，如果second<first，second将被解析为行数

16）a：（args）列出当前执行函数的函数

17）p expression：（print）输出expression的值

18）pp expression：好看一点的p expression

19）run：重新启动debug，相当于restart

20）q：（quit）退出debug

21）j lineno：（jump）设置下条执行的语句函数

            只能在堆栈的最底层跳转，向后重新执行，向前可直接执行到行号

22）unt：（until）执行到下一行（跳出循环），或者当前堆栈结束

23）condition bpnumber conditon，给断点设置条件，当参数condition返回True的时候bpnumber断点有效，否则bpnumber断点无效
```

### 单元测试

所谓单元测试，就是用来对一个模块、一个函数或者一个类来进行正确性检验的测试工作。我们这里，来介绍Python自带的单元测试库[unittest](https://docs.python.org/3/library/unittest.html)。

比如我们来对上节的调试代码中的加法函数做单元测试，代码如下：

```python
import unittest
from .L14_pdb import add


class TestAdd(unittest.TestCase):  ## 单元测试类继承 unittest.TestCase

    def setUp(self):  # 钩子函数，在调用测试方法前执行，可做测试前的准备工作，如初始化数据库链接
        print('setUp...')

    def tearDown(self):  # 钩子函数，在调用测试方法后执行，可做测试后的资源释放工作，如关闭数据库链接
        print('tearDown...')
        
    def test_init(self):
        d = add(3, 4)
        self.assertEqual(d, 7)  ## 使用断言函数，判断是否函数结果是否与预定值相等
        self.assertTrue(isinstance(d, int))


if __name__=='__main__':
    unittest.main()

```

## 参考

- [廖雪峰Python教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143191629979802b566644aa84656b50cd484ec4a7838000)