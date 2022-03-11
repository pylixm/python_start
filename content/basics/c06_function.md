---
title: "函数"
date: 2022-03-11T20:00:23+08:00
weight: 7
chapter: true
pre: "<b>2.6 </b>"
---

## 函数


在维基百科上函数式这样描述的：
> 函数在数学中为两集合间的一种对应关系：输入值集合中的每项元素皆能对应唯一一项输出值集合中的元素。

此处的函数区别于我们数学上的函数，在编程世界中，函数（Functions）是指可重复使用的程序片段。它们允许你为某个代码块赋予名字，允许你
通过这一特殊的名字在你的程序任何地方来运行代码块，并可重复任何次数。这就是所谓的调用函数。我们已经使用过了许多内置的函数，例如 `len` 和 `range` 。

基本结构如下：

```
def 函数名（参数列表）:
    " 函数说明 "
    函数体
```

### 函数定义

在Python中，使用关键字`def`来定义一个函数。结构如下：

```python
def hello(name):  
    " 返回字符串"
    return name
    
def hello2():
    pass 
```

语法说明：
- 函数的参数支持多种形式
- 函数说明，可通过内部方法`hello.__doc__` 获取
- 函数的返回值支持返回多个
- 我们可以使用 `pass` 来定义个空函数

下面来展开一一讲述。

#### 返回值

函数中返回值，使用关键字`return` 来表示，返回值可以是任意类型，当没有`return`语句时函数返回None。函数返回值支持多个，返回多个时，使用逗号分隔。如下：

```python
def say_hello(name):
    print(name)
    return 'Hello, %s' % name, name
```

#### 函数的调用

函数的调用，上边我们已经使用了很多次了，函数名加上需要传入的响应参数即可。这里需要注意的是，在python中，一切结尾对象，当然函数也是对象。函数名也可以作为参数传递到函数中使用。

```python
def say_hello(name):
    print('hello, %s' % name)

def wapper(fun):
    print('调用之前做一些事')
    fun('Dean')
    print('调用之后做一些事')

wapper(say_hello)
"""
调用之前做一些事
hello, Dean
调用之后做一些事
"""
```

### 函数的参数 

在程序设计中函数参数有2个比较通用的概念，形参和实参，在各高级语言中都会有。形参，即形式上的参数，不占用内存空间。实参，是实际真正的参数。

看下面这个实例：

```python
def say_hello(name):  
    print('hello, %s' % name )
say_hello('Dean')
```

其中，函数参数`name`即为函数的形参。我们调用函数时，传入的参数`Dean`便是实参。在Python编程时，这个概念并不是那么重要，此种了解即可。

#### 顺序参数

在Python 函数中，多个参数按照一定的顺序依次传入函数，其值依次对应赋给函数声明时参数，这种参数叫`顺序参数`。

```python
def say_hello(name, age):
    print('hello, %s' % name)
    print('you age: %s ' % age)

say_hello('Dean', 30)
```

如上，`Dean`与30 这2个参数按照由左到右依次赋值给形参`name`和`age`供函数内部使用。需要注意的是，顺序参数中，形参和实参的个数需要一直，否则函数会报调用错误。

#### 参数的默认值

在函数声明时，可以给参数设置默认值，如下：

```python
def say_hello(name='Tim', age=20):
    print('hello, %s' % name)
    print('you age: %s ' % age)

say_hello('Dean', 30)
say_hello('Dean')
```

当函数参数设置默认值后，该参数在调用函数时，可以省略，省略时则会使用默认值。有此可以退出，参数没有设置默认值，则是必传的。

#### 可变参数

有时候我们在声明函数时，没法确定函数的参数个数，或者我们本身就想设计一种动态的参数，这种设计是否可以满足呢？Python的函数对这种情况已做了考虑，提供了`可变参数`来满足这种需求。

```python
def say_hello(*args):
    print(args)
    for name in args:
        print('hello, %s' % name)

say_hello('Dean','Tim')
('Dean', 'Tim')
hello, Dean
hello, Tim
```

可变参数使用`*`加一个形参来表示，其值为一个元组类型，元组内的元素为我们传入的实参。

#### 关键字参数

在可变参数中，我们传入函数的参数只能按顺序或着通过下标来获取，那么可不可以给参数起一个名字呢，答案是可以的。Python 为这种情况提供了一种参数形式，叫做`关键字参数`。

```python
def say_hello(**kwargs):
        print('hello, %s' % kwargs['name'])
        print('you age: %s' % kwargs['age'])

say_hello(name='Dean', age=30)
```

使用`**`加一个形参来表示，其值为一个字典，key为我们实参中传入的变量，value值为实参中的变量的值。

#### 参数的组合

Python 函数中，以上参数可组合使用。当参数组合使用时，需要注意以下原则：

- 有默认值的参数需要放在无默认值参数的后边
- 当顺序参数和可变参数混合使用时，可变参数的取值为超出顺序参数的实参部分，且可变参数在顺序参数之后。
- *args 和 **kwargs 是 Python 的惯用写法，变量名可以更换。

参数的常用排列顺序为，无默认值顺序参数、有默认值顺序参数、可变参数、关键字参数。

```python
def say_hello(name, age=30, *args, **kwargs):
    print(args)
    print(kwargs)

    print('hello, %s' % name)
    print('you age: %s' % age)
    print('address: %s' % kwargs['address'])

say_hello('Dean', 20, address='Beijing')
```

#### 参数的专递

我们在调用函数时，将实参传递给了函数的形参，供函数中逻辑代码使用。那么在这个过程中，计算机内存是一个怎么的过程呢？我们在学习变量时知道，Python中的变量是其内部存储值的一个内存地址或者叫做一个引用。在传入函数时，便是将这个引用传入了函数。那么我们在函数内部修改变量的值时，是不是就修改了外部的变量的值呢？答案是不一定。

在我们学习基本数据结构时，我们知道有可变类型（字典、列表、集合）和不可变类型（数值、字符串、元组）。当我们传入函数的参数为可变类型时，函数中的形参使用的内存地址同实参的内存地址，当改变形参的值时，实参的值也相应跟着修改了。当我们传入的为不可变量时，形参会重新分配一块内存地址保存实参传给他的值，当形参修改时，实参自然不会修改。

来看下边的例子：

```python
# func.py 
def say_hello1(name):
    '不可变类型参数'
    name = 'Time'
    print('name: %s' % name )


def say_hello2(user_dict):
    '可变类型参数'
    user_dict['name'] ='Time'
    print('name: %s' % user_dict['name'])

name = 'Dean'
print('name before:', name)
say_hello1(name)
print('name after:', name)

user_dict = {'name':'Dean'}
print('user_dict before:', user_dict)
say_hello2(user_dict)
print('user_dict after:', user_dict)

"""
# 不可变参数，值没有变
('name before:', 'Dean')
name: Time
('name after:', 'Dean')

# 可变参数，值变了
('user_dict before:', {'name': 'Dean'})
name: Time
('user_dict after:', {'name': 'Time'})
"""
```

### 函数作用域

在第三章，我们学习变量时，知道变量的作用域决定了在哪一部分程序可以访问哪个特定的变量名称。在Python中，函数会重新开启一个作用域，当在函数内部定义的变量，在函数外部是无法访问的。那么该变量便是函数的`局部变量`，函数外部的变量便可成为`全局变量`。

```python
total = 0 # 这是一个全局变量
# 可写函数说明
def sum( arg1, arg2 ):
    #返回2个参数的和.
    total = arg1 + arg2 # total在这里是局部变量.
    print ("函数内是局部变量 : ", total)
    return total

#调用sum函数
sum( 10, 20 )
print ("函数外是全局变量 : ", total)
```

那么当内部的局部变量想要暴露出来给外部使用时如何实现呢？Python提供了2个关键字，`gobal` 和 `nonlocal` 。

`global` 将变量作用域变为函数外层全局作用域。

```python
num = 1
def fun1():
    global num  # 需要使用 global 关键字声明
    print(num) 
    num = 123
    print(num)
fun1()
```

`nonlocal` 将变量作用域变为函数外部非全局变量，及闭包（Enclosing）作用域。

```python
def outer():
    num = 10
    def inner():
        nonlocal num   # nonlocal关键字声明
        num = 100
        print(num)
    inner()
    print(num)
outer()
```


### 内建函数

内建函数，是Python中已经定义好的函数可以在编写代码时直接使用。我们已经使用过内建函数，如`len`、`range` 等。更多的内建函数，可参阅[官方文档中函数说明](https://docs.python.org/3/library/functions.html)。


### 匿名函数

匿名函数，就是不使用`def`来定义的函数。Python中使用关键字`lambda` 来实现匿名函数。

```python
# 语法
lambda [arg1 [,arg2,.....argn]]:表达式
```

语法说明：
- `arg`为参数，可以任意多个，使用逗号分隔
- `表达式` 为函数体，使用冒号开头
- `表达式` 的返回值即为函数的返回值

实例：

```python
# 内名函数
sum = lambda arg1, arg2: arg1 + arg2
 
# 调用sum函数
print ("相加后的值为 : ", sum( 10, 20 ))
print ("相加后的值为 : ", sum( 20, 20 ))
```

### 函数注解

函数注解，为函数和参数增加了一些说明特性。python3.0 新增的特性，见[PEP3107](https://www.python.org/dev/peps/pep-3107/)。可通过函数的`__annotations__` 熟悉查看。

```python
def f(ham: 42, eggs: int = 'spam') -> "Nothing to see here":
    print("Annotations:", f.__annotations__)
    print("Arguments:", ham, eggs)

f('wonderful')
'''
Annotations: {'eggs': <class 'int'>, 'return': 'Nothing to see here', 'ham': 42}
Arguments: wonderful spam
'''

def foo(a: 'x', b: 5 + 6, c: list) -> max(2, 9):
    pass 
```

语法说明：
- 参数的说明在参数后边，使用冒号和参数隔离，注释为一个表达式。
- 函数的注解在函数参数括号后，使用'->' 符号开头，也是一个表达式。

在Python的编程时，注解使用的比较少，以方便人们更多的使用docstring来说明参数及返回值，另一方面Python函数参数可进一步通过编码规范一眼就能看出什么类型。这种注释语法，反而显得啰嗦冗余。

## 总结

本章我们学习了函数及其使用，知识重点总结如下：

- Python提供了许多内建函数共我们使用，内建函数可直接使用无需定义。
- 参数组合在使用的时候是有顺序的，依次是无默认值顺序参数、有默认值顺序参数、可变参数、关键字参数。
- *args 表示可变参数，**kwargs 表示关键字参数。
- 当参数为不可变参数时，函数不会影响外部实参值，当参数为不可变参数时，函数则会影响外部实参值。
- 函数本身可作为参数传递。
- Python 中使用`lambda` 来实现匿名函数。


## 练习

- 1、求n的阶乘

```python
def factorial(num):
    """
    求阶乘
    :param num: 非负整数
    :return: num的阶乘
    """
    result = 1
    for n in range(1, num + 1):
        result *= n
    return result

n = int(input('n = '))
factorial(n)
```

- 2、实现判断一个数是不是素数的函数。

```python
# 素数，在大于1的自然数中，除了1和它本身以外不再有其他因数
def is_prime(num):
	for factor in range(2, num):
		if num % factor == 0:
			return False
	return True if num != 1 else False
```

## 参考

- [廖雪峰-python3教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143167832686474803d3d2b7d4d6499cfd093dc47efcd000)

- [python官方文档](http://www.pythondoc.com/pythontutorial3/controlflow.html#tut-functions)