<!-- TOC -->

- [常用设计模式](#常用设计模式)
    - [单例模式](#单例模式)
    - [工厂模式](#工厂模式)
- [参考](#参考)

<!-- /TOC -->

## 常用设计模式

设计模式是经过总结、优化的，对我们经常会碰到的一些编程问题的可重用解决方案。一个设计模式并不像一个类或一个库那样能够直接作用于我们的代码。反之，设计模式更为高级，它是一种必须在特定情形下实现的一种方法模板。设计模式不会绑定具体的编程语言。一个好的设计模式应该能够用大部分编程语言实现(如果做不到全部的话，具体取决于语言特性)。最为重要的是，设计模式也是一把双刃剑，如果设计模式被用在不恰当的情形下将会造成灾难，进而带来无穷的麻烦。然而如果设计模式在正确的时间被用在正确地地方，它将是你的救星。

下面介绍下，常用的设计模式和Python的实现。

### 单例模式

**意图：**

保证一个类仅有一个实例，并提供一个访问它的全局访问点。

**适用性：**

当类只能有一个实例而且客户可以从一个众所周知的访问点访问它时。
当这个唯一实例应该是通过子类化可扩展的，并且客户应该无需更改代码就能使用一个扩展的实例时。

**实现**

```python
#!/usr/bin/python
#coding:utf8
'''
Singleton
'''
 
class Singleton(object):
    ''''' A python style singleton '''
 
    def __new__(cls, *args, **kw):
        if not hasattr(cls, '_instance'):
            org = super(Singleton, cls)
            cls._instance = org.__new__(cls, *args, **kw)
        return cls._instance
 
 
if __name__ == '__main__':
    class SingleSpam(Singleton):
        def __init__(self, s):
            self.s = s
 
        def __str__(self):
            return self.s
 
 
    s1 = SingleSpam('spam')
    print id(s1), s1
    s2 = SingleSpam('spa')
    print id(s2), s2
    print id(s1), s1
```

### 工厂模式

**意图：**

定义一个用于创建对象的接口，让子类决定实例化哪一个类。Factory Method 使一个类的实例化延迟到其子类。

**适用性：**

当一个类不知道它所必须创建的对象的类的时候。
当一个类希望由它的子类来指定它所创建的对象的时候。
当类将创建对象的职责委托给多个帮助子类中的某一个，并且你希望将哪一个帮助子类是代理者这一信息局部化的时候。

```python
#!/usr/bin/python
#coding:utf8
'''
Factory Method
'''
 
class ChinaGetter(object):
    """A simple localizer a la gettext"""
    def __init__(self):
        self.trans = dict(dog=u"小狗", cat=u"小猫")
 
    def get(self, msgid):
        """We'll punt if we don't have a translation"""
        try:
            return self.trans[msgid]
        except KeyError:
            return str(msgid)
 
 
class EnglishGetter(object):
    """Simply echoes the msg ids"""
    def get(self, msgid):
        return str(msgid)
 
 
def get_localizer(language="English"):
    """The factory method"""
    languages = dict(English=EnglishGetter, China=ChinaGetter)
    return languages[language]()
 
# Create our localizers
e, g = get_localizer("English"), get_localizer("China")
# Localize some text
for msgid in "dog parrot cat bear".split():
    print(e.get(msgid), g.get(msgid))
```

更多设计模式，大家可参考《精通Python设计模式》一书，书中详细介绍了16中设计模式，从生活实例出发到代码实现。


## 参考

- [https://www.cnblogs.com/Liqiongyu/p/5916710.html](https://www.cnblogs.com/Liqiongyu/p/5916710.html)