<!-- TOC -->

- [控制流](#控制流)
    - [分支结构](#分支结构)
        - [if/elif/else](#ifelifelse)
    - [循环结构](#循环结构)
        - [for](#for)
        - [while](#while)
        - [break 和 continue](#break-和-continue)
        - [pass](#pass)
- [总结](#总结)
- [练习](#练习)
- [参考](#参考)

<!-- /TOC -->

## 控制流

上一张我们了解了Python的基本语法和数据结构。到目前位置，我们编写的代码都是一行一行的由上往下一次执行的。我们叫这种形式的代码为`顺序结构`。但是在真正的编码环境中，这种顺序结构实现的场景有限。下面我们来看下Python编程中的一些其他复杂控制结构。

### 分支结构

试想，一个简单的计算器的计算过程，当我们按下加号键的时候，它会执行相加操作；当我们按下减号的时候，它则会执行减法操作。这并产生了2个分支，它根据我们的输入这个未知的条件来决定该走那个分支。像这种根据某条件来判断走那个分支的结构叫做`分支结构`。

#### if/elif/else

Python 中使用关键字`if`、 `else` 和 `elif` 来实现分支结构。下面我们来看下它们的语法：

```python
# -*- coding:utf-8 -*- 
# calculator.py

a, b = 2, 3 
sign = input('请输入运算符：')
if sign == '+':  # 判断条件
    print(a+b)   # 逻辑代码块
elif sign == '-':
    print(a-b)
else:
    print('暂不支持该符号！')

```

语法说明：
- Python 中分支结构中判断条件以英文冒号 `:` 结尾；
- 分支结构中的逻辑代码块，以相对于条件语句向右4个空格或1个tab为分隔符，建议使用4个空格，tab在不同系统表现不一致可能引起混了，影响代码的跨平台性。
- `if` 和 `elif` 后必须加判断表达式，一般表达式返回值为布尔型，当非布尔型时，表示该值是否存在。
- 我们在输入运算符时，需要使用引号引起来，因为单独的一个运算符是会报错的。
- `if` 可单独使用，也可单独和`elif` 或 `else` 配合使用。

```python
# if 单独使用
if '1':
    print('这是真的')
# if 和 else 配合使用
if False:
    print('这是假的')
else:
    print('这是真的')
# if 和 elif 配合使用
if False:
    print('这是假的')
elif True:
    print('这是真的')
```
- 条件语句可嵌套使用。

```python
# -*- coding:utf-8 -*- 
# nesting_if.py 
a = int(input('请输入：'))

if a > 4:
    if a < 10:
        print('大于4，小于10')
    else:
        print('大于10')
else:
    print('小于4')
```

- 条件语句有一种简便的写法，被称为`三元操作符`。

```python
a = '上午好' if now_time < 12 else '下午好' 
```

### 循环结构 

上一章我们还学习了列表，那当我们要想依次去除列表中的元素时，便需要来循环依次取出，这种循环操作的结构叫做`循环结构`。

#### for 

Python 中使用关键字 `for` 来实现已知长度的循环。用法如下：

```python
l = [1,2,3,4,5]
sum = 0 
for i in l :
    sum += i
print(sum)
```

语法说明：
- `for` 使用关键字`in` 来遍历序列，获取其元素，有变量`i` 来表示，以便在之下的逻辑代码块中使用。
- `for` 语句以英文冒号结尾
- 逻辑代码块以4个空格或tab分隔。
- `for` 循环也是可以嵌套使用的。
- `for` 还可遍历成组元素的序列，如下：

```python
l = [(1,2),(1,3),(1,4)]
for i, j in l:
    print(i)
    print(j)
```

在Python中提供了一个内置函数 `range(start,length,step)`来专门生成用来遍历的整数序列，其返回值为迭代器（可理解为列表对象，后边会讲解）。`start`为开始整数，可省略，省略时默认为0；`length`为结束整数，但不包含自己；`step` 为步长，及2个整数之间的间隔，可省略，省略时默认为1。使用`range`函数来改写上边的代码为：

```python
sum = 0
for i in range(1,6):
    sum += i 
print(sum)
```

#### while 

Python 中还提供了另一种循环语句关键字 `while` 用来实现满足条件下的一个循环操作。

```python
# loop_while.py
i = 0 
sum = 0
while i <= 5:
    sum += i
    i += 1 
print(sum)
```

#### break 和 continue

我们试想，当`while`的条件永远为真时，会发生什么？while下的逻辑块会被一直执行下去，如果该逻辑段耗费计算机资源的话。计算机资源会被他很快的消耗殆尽。这种情况，我们把它叫做`死循环`或`无限循环`。这种循环，大多数情况下都是不对的，除非你明确的知道，你就是要使用一个这样的无限循环。Python 中提供了2个关键字`break`和`continue`，让我们的操作可以跳出无限循环。

**break 用法**

```python
# while 中使用
sum = 0
while True:
    if sum > 10:
        print('sum已大于10')
        break
    sum += 1 
    print(sum)

# for 中使用
sum = 0 
for i in range(100):
    if sum > 10:
        print('sum已大于10')
        break;
    sum += 1 
    print(sum)
```

语法说明：
- `break` 语句执行后，直接退出循环，不再循环


**continue用法**

```python
sum = 0 
for i in range(100):
    if sum > 10:
        print('sum已大于10')
        continue;
    sum += 1 
    print(sum)
```

语法说明：
- `continue` 语句执行后，当前循环语句之后的语句不再执行，直接跳入下一次的循环继续执行。


#### pass

有时候，我们的代码块想直接跳过，可使用关键字 `pass`。它常被用来占位使用。

```python
for i in range(5):
    pass # 占位
```

## 总结

本章我们学习了Python的代码逻辑控制流，我们来总结下：

- Python代码结构有顺序结构、分支结构和循环结构；
- 分支结构可使用`if`/`elif`/`else` 实现；
- 循环结构可使用`for`/`while` 实现；
- 可使用`break`和`continue`来截断循环结构；

## 练习

- 1、 输出乘法口诀表(九九表)

```python
for i in range(1, 10):
	for j in range(1, i + 1):
		print('%d*%d=%d' % (i, j, i * j), end='\t')
	print()
```
- 2、实现一个简易计算器， 支持 `+`、`-`、`*`、`/`的运算。

```python
first = int(input('请输入一个数值：'))
sign = input('请输入运算符：')
second = int(input('请输入一个数值：'))
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

- 3、 打印如下三角形图案。
```python
"""
*
**
***
****
*****

    *
   **
  ***
 ****
*****

    *
   ***
  *****
 *******
*********

"""
```

```python
row = int(input('请输入行数: '))
for i in range(row):
    for _ in range(i + 1):
        print('*', end='')
    print()


for i in range(row):
    for j in range(row):
        if j < row - i - 1:
            print(' ', end='')
        else:
            print('*', end='')
    print()

for i in range(row):
    for _ in range(row - i - 1):
        print(' ', end='')
    for _ in range(2 * i + 1):
        print('*', end='')
    print()
```

- 4、实现猜数字游戏, 要求：
    - 计算机出一个1~100之间的随机数由人来猜
    - 计算机根据人猜的数字分别给出提示大一点/小一点/猜对了


```python
import random

answer = random.randint(1, 100)
counter = 0
while True:
	counter += 1
	number = int(input('请输入: '))
	if number < answer:
		print('大一点')
	elif number > answer:
		print('小一点')
	else:
		print('恭喜你猜对了!')
		break
print('你总共猜了%d次' % counter)
if counter > 7:
	print('你的智商余额明显不足')

```

## 参考

- [https://docs.python.org/3.6/tutorial/index.html](https://docs.python.org/3.6/tutorial/index.html)
- [https://github.com/jackfrued/Python-100-Days/blob/master/Day01-15/Day02/%E8%AF%AD%E8%A8%80%E5%85%83%E7%B4%A0.md](https://github.com/jackfrued/Python-100-Days/blob/master/Day01-15/Day02/%E8%AF%AD%E8%A8%80%E5%85%83%E7%B4%A0.md)