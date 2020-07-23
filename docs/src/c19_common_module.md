<!-- TOC -->

- [常用内建模块](#常用内建模块)
    - [datetime：Python处理日期和时间的标准库](#datetimepython处理日期和时间的标准库)
    - [collections](#collections)
    - [base64](#base64)
    - [hashlib](#hashlib)
    - [itertools: 提供用于操作迭代对象的函数](#itertools-提供用于操作迭代对象的函数)
- [常用第三发模块](#常用第三发模块)
    - [requests](#requests)
    - [psutil](#psutil)
- [参考](#参考)

<!-- /TOC -->

## 常用内建模块

Python有大量的内建模块供我们使用，见官方文档：https://docs.python.org/3/py-modindex.html

下面我们来简单说下几个常用的内建模块。

### datetime：Python处理日期和时间的标准库

引入方法： `from datetime import datetime`
第一个datetime是模块，第二个datetime是类。 如果仅导入import datetime，则必须引用全名datetime.datetime。

```python
from datetime import datetime

# 获取当前时间
now = datetime.now()

# 加减时间
now_2 = now + datetime.timedelta(days=2)

# 时间转时间戳
now.timestamp()

# 时间转字符串
now.strftime('%Y-%m-%d %H:%M:%S')
```

更多使用方法，可见：[https://docs.python.org/3/library/datetime.html#module-datetime](https://docs.python.org/3/library/datetime.html#module-datetime)

### collections

Python内建的一个集合模块，提供了许多有用的集合类。

**namedtuple 创建一个自定义的tuple对象**

```python
>>> from collections import namedtuple
>>> Point = namedtuple('Point', ['x', 'y'])
>>> p = Point(1, 2)
>>> p.x
```

**deque 为了高效实现插入和删除操作的双向列表，适合用于队列和栈**

```python
>>> from collections import deque
>>> q = deque(['a', 'b', 'c'])
>>> q.append('x')
>>> q.appendleft('y')
>>> q
deque(['y', 'a', 'b', 'c', 'x'])
>>> q.pop()
>>> q
deque(['y', 'a', 'b', 'c'])
>>> q. popleft()
>>> q
deque([ 'a', 'b', 'c'])
```

**defaultdict 使用dict时，如果引用的Key不存在，就会抛出KeyError。如果希望key不存在时，返回一个默认值，就可以用defaultdict**

```python
>>> from collections import defaultdict
>>> dd = defaultdict(lambda: 'N/A')
>>> dd['key1'] = 'abc'
>>> dd['key1'] # key1存在'abc'
>>> dd['key2'] # key2不存在，返回默认值'N/A'
```

**OrderedDict： 保持Key的顺序，可以用OrderedDict(按插入序)。 OrderedDict可以实现一个FIFO（先进先出）的dict，当容量超出限制时，先删除最早添加的Key**

```python
>>> from collections import OrderedDict
>>> d = dict([('a', 1), ('b', 2), ('c', 3)])
>>> d # dict的Key是无序的
{'a': 1, 'c': 3, 'b': 2}
>>> od = OrderedDict([('a', 1), ('b', 2), ('c', 3)])
>>> od # OrderedDict的Key是有序的
OrderedDict([('a', 1), ('b', 2), ('c', 3)])
```

**Counter：计数器，也是dict的子类，如统计字母出现的次数，类似于Wordcount**

```python
>>> from collections import Counter
>>> c=Counter
>>> c('Programming')
Counter({'r': 2, 'm': 2, 'g': 2, 'P': 1, 'a': 1, 'o': 1, 'i': 1, 'n': 1})
```

### base64 

用64个字符来表示任意二进制数据. Base64是一种任意二进制到文本字符串的编码方法，常用于在URL、Cookie、网页中传输少量二进制数据。

### hashlib 

hashlib 提供常见的摘要算法，MD5,SHA1,SHA256,SHA512等。摘要算法又称哈希算法、散列算法。它通过一个函数，把任意长度的数据转换为一个长度固定的数据串（通常用16进制的字符串表示）。 目的是为了发现原始数据是否被人篡改过 。摘要算法之所以能指出数据是否被篡改过，就是因为摘要函数是一个单向函数，计算f(data)很容易，但通过digest反推data却非常困难。而且，对原始数据做一个bit的修改，都会导致计算出的摘要完全不同。也可以看成加密算法。

```python
import hashlib

md5 = hashlib.md5()
md5.update('how to use md5 in '.encode('utf-8'))
md5.update('python hashlib?'.encode('utf-8'))
print(md5.hexdigest())
```

注意：在pycharm上测试运行时，新建的.py文件名称不要取hashlib，因为代码中需要import hashlib，如果文件名也是hashlib就会引入当前文件(模块)，从导致出现错误。

### itertools: 提供用于操作迭代对象的函数

count()会创建一个无限的迭代器，count(1)从1开始的迭代器

```python
for n in itertools.count(1):
    print(n)
    if n==10:
        break
```

cycle()会把传入的一个序列无限重复下去

```python
for c in itertools.cycle('abc'):
    print(c)
    i+=1
    if i==10:
        break
```
repeat()负责把一个元素无限重复下去，不过如果提供第二个参数就可以限定重复次数

```python
for n in itertools.repeat('a',3):
    print(n)
```
chain()可以把一组迭代对象串联起来，形成一个更大的迭代器：

```python
for c in itertools.chain('ABC', 'XYZ'):
       print(c)    # 迭代效果：'A' 'B' 'C' 'X' 'Y' 'Z'
```
groupby()把迭代器中相邻的重复元素挑出来放在一起：

```python
>>> for key, group in itertools.groupby('AAABBBCCAAA'):
...     print(key, list(group))
...
A ['A', 'A', 'A']
B ['B', 'B', 'B']
C ['C', 'C']
A ['A', 'A', 'A
```
小结： itertools模块提供的全部是处理迭代功能的函数，它们的返回值不是list，而是Iterator，只有用for循环迭代的时候才真正计算。


## 常用第三发模块

### requests

专门为人类提供的url处理库，使用起来更方便、更符合人类的思考方式。

官方地址：http://www.python-requests.org/en/master/

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
### psutil 

Python 中常用的运维工具库，可获取机器的CPU、内存、硬盘等硬件信息。而且可以跨平台使用，支持Linux／UNIX／OSX／Windows等，是系统管理员和运维不可或缺的必备模块。

官方文档：https://github.com/giampaolo/psutil


## 参考 

- [廖雪峰Python教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)