# 第七章 错误和异常

<!-- TOC -->

- [第七章 错误和异常](#第七章-错误和异常)
  - [异常](#异常)
  - [语法错误](#语法错误)
  - [异常操作](#异常操作)
    - [异常处理](#异常处理)
    - [抛出异常](#抛出异常)
    - [扩展：用户自定义异常](#扩展用户自定义异常)
  - [练习](#练习)

<!-- /TOC -->

本章我们来讨论一下Python语言的中的异常和错误。

## 异常

之前在我们的程序中，大家已经见过许多方面的异常。比如在讲解元组时，我们对元组做更新操作：

```python
Python 3.6.5 (default, Mar 30 2018, 06:42:10)
[GCC 4.2.1 Compatible Apple LLVM 9.0.0 (clang-900.0.39.2)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> s = (1,2,3,4)
>>> s[0] = 5
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
>>>
```
这种执行时的错误，我们通常叫做`异常`。

## 语法错误
除了异常外，还有一种语法的错误。它抛出错误`SyntaxError: invalid syntax`，如下：

```python
>>> if 1=1
  File "<stdin>", line 1
    if 1=1
        ^
SyntaxError: invalid syntax
>>>
```
我们叫这种语法性的错误，我们通常叫做`语法错误`。

## 异常操作

异常和错误都会打断我们代码的执行，即错误和异常发生时，自发生错误或异常的语句之后的语句都不会执行。那我们应该如何处理错误和异常呢。`Python` 提供给我们了一些关键字来在我们的控制流中控制错误和异常的行为。

### 异常处理

在python中，异常我们是可以捕获来决定如何处理的。有以下几种处理方式

第一种，使用`try...except...else...finally`4个关键字：

```python
try:
    a = 10/0 

except ZeroDivisionError as e:
    print(e)
    # do something 
except IOError as e:
    print(e)
    # do something
except Exception as e:
    pass 
else:
    print('结果为：', n )
    # do other something
finally:
    # do something 
    print('finally')
```

**知识点：**
- `except` 之后的`ZeroDivisionError`为错误的类型，表示只捕获本类型的错误异常。错误异常类型可省略，当省略时，表示捕捉所有错误异常。这些错误类型都是python内建的，可参考[官网文档](https://docs.python.org/3/library/exceptions.html)查看更多类型。
- `except` 之后的类型可以为多个，当多个时，使用元组表示，如：`except (ZeroDivisionError, IOError) as e:`，或者多次使用`except`关键字加类型来捕捉。
- `except`异常捕获后将不再走后边的捕获异常`except`。
- 当多次使用`except`时，一般将明确或已知的错误类型放前边，最后使用`Excepttion`捕捉未知的异常。
- `as` 语法为重命名或别名的意思。
- `finally` 关键字之后的代码块不管有没有异常都会执行，可将与错误无关的逻辑放在此处。
- `else` 代码块在`try`代码块没有发生错误或异常时，执行。`else` 关键字可省略。
- `else` 代码块表现和直接放在`try`代码块后一样，但是，放在`else`出的好处是，使得`try`代码的错误更容易被铺抓到，使我们对错误处理可以更细力度。


### 抛出异常

有时候，代码出现错误时，我们不需要处理，或者说我们就想看下它是否出错。那我们可以通过`raise`关键字把特定的错误给抛出。

```python
try:
    a = 10/0
    # other code
except ZeroDivisionError as e:
    raise Exception('Zero <<<<<')
except IOError as e:
    print(e)

Traceback (most recent call last):
  File "/Users/pylixm/projects/test/temp.py", line 24, in <module>
    raise Exception('Zero <<<<<')
Exception: Zero <<<<<
```

**知识点：**
- `raise` 后边加错误类型，来抛出特定的错误或异常。
- 内建错误异常类型可以直接调用，传入一个字符串来自定义错误的描述。
- `raise` 后错误异常类型可省略。当省略时，则抛出`except`捕捉到的异常类型。


### 扩展：用户自定义异常

```python
>>> class MyError(Exception):
        def __init__(self, value):
            self.value = value
        def __str__(self):
            return repr(self.value)
   
>>> try:
        raise MyError(2*2)
    except MyError as e:
        print('My exception occurred, value:', e.value)
   
My exception occurred, value: 4
>>> raise MyError('oops!')
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
__main__.MyError: 'oops!'
```

## 练习

1、尝试修改下面代码，增加异常处理代码：
```python
def Calculator(first, sign, second):
    """ 计算器 """
    if sign == '+':
        print('%s+%s=%s' % (first, second, first+second))
    elif sign == '-':
        print('%s-%s=%s' % (first, second, first-second))
    elif sign == '*':
        print('%s*%s=%s' % (first, second, first*second))
    elif sign == '/':
        print('%s/%s=%s' % (first, second, first/second))
    else:
        print('不支持该运算符')
```

