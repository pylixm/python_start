
<!-- TOC -->

- [课前阅读](#课前阅读)
- [什么是网络爬虫](#什么是网络爬虫)
- [爬虫的基本构成](#爬虫的基本构成)
- [实现一个简单的爬虫](#实现一个简单的爬虫)
- [参考](#参考)

<!-- /TOC -->

## 课前阅读

- [正则表达式](http://www.runoob.com/regexp/regexp-syntax.html)
- [http基础知识]()--《Python爬虫开发与项目实战》第二章内容
- [Python3-urlib模块使用](https://blog.csdn.net/duxu24/article/details/77414298)


从本章开始，我们进入我们的实战部分，在实战中继续巩固和学习Python技术。之前我们学习了Python的基础语法、高级数据结构、控制流、函数和模块，接下来我们使用我们学习的知识来实现一个Python中一个基本的而且使用比较广泛的爬虫项目。

在开始项目之前，我们先来了解一些基础知识。

## 什么是网络爬虫

>网络爬虫（英语：web crawler），也叫网络蜘蛛（spider），是一种用来自动浏览万维网的网络机器人。其目的一般为编纂网络索引。
>-- 摘自<wiki百科>

简单来说，就是模拟人浏览互联网上的信息，并自动提取保存信息的程序系统。网络爬虫常常被各搜索引擎用来抓取互联网上的内容，用来更新自己的网站内容及对其他网站的索引。它也是许多内容聚合类网站常常使用的工具，来抓取各热点内容，聚合展示以获取用户流量。

大型的网络爬虫往往是非常复杂的，它有着复杂的架构设计以保证它的运行速度，复杂的爬取策略以保证爬取内容的准确性。爬取下来的海量数据的保存及快速的查询，从中提取有价值的内容，这些都是大型的网络爬虫需要考虑的问题。

我们接下来，通过带领大家构建一个简单的爬虫，来逐渐的学习和巩固Python的基础知识，并使该爬虫具有一定的实际项目使用价值。至于更复杂，更大型的爬虫项目系统，大家感兴趣的可继续的深入学习研究。

## 爬虫的基本构成

首先，我们来分析下人浏览一个网页的过程：
- 1、在浏览器地址输入网址地址打开网页
- 2、通过索引链接或菜单找到我们需要的信息
- 3、阅读信息，获取需要的信息

针对以上过程，我们来抽象一下，分别为:
- 1、网站请求
- 2、内容搜索或解析
- 3、内容的获取保存

根据我们的分析，我们爬虫的结构便呼之欲出了。

- 首先，我们需要一个请求网站地址的功能模块，可以返回网站的内容。
- 其次，我们需要一个下载和分析网站内容的模块，来查找我们需要的信息。
- 最后，需要一个保存信息的模块，用来将我们的信息保存。

针对于多个网络地址URL的情况，我们还需要一个功能模块来记录已经处理过和未处理的URL。

总结以上分析，我们爬虫的基本构成如下：

![](https://ws1.sinaimg.cn/large/8697aaedly1ftdrg8jbqcj20t40ai78a.jpg)

接下来，我们由简入繁，慢慢来搭建这样一个爬虫。

## 实现一个简单的爬虫

通过课前的阅读学习，默认你已经有一定的正则、html和urllib库的使用基础，这里不再对这些做赘述。

首先，我们使用Python来实现一个最简单的爬虫。我们来爬取下“程序员头条”这个网站的“人工智能”菜单下的文章标题和地址url。

实现代码如下：

```python
# -*- coding:utf-8 -*-
from urllib import request
import re


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
    # 1、请求功能模块 和下载功能模块
    req = request.Request(path)
    req.add_header('User-Agent',
                   'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36')
    try:
        resp = request.urlopen(req)
    except Exception as err:
        print(err)
        return ret

    if resp.status == 200:
        # 2、解析功能模块
        for line in resp.read().decode().split('\n'):
            post_dict = {}
            re_title = re.search('<a target="_blank" rel="external" title=".*? href="(.*?)">(.*?)<\/a>',
                              line)
            if re_title:
                post_dict['href'] = 'https://toutiao.io{0}'.format(re_title.group(1)).replace('posts', 'k')
                post_dict['title'] = re_title.group(2)
            # 3、保存功能
            if post_dict:
                ret.append(post_dict)

    return ret

```

**代码分析：**
- 首先，函数体开头做了参数为空的判断。
- 使用urlib包中request模块，来实现了url的请求和下载。这里注意我们使用的是python3.6版本，在Python2.x中，urllib的功能分为urllib和urllib2，要处理带请求头的请求时需要联合使用。
- 接下来，我们分析了返回的html每行代码，把文章的标题和url使用正则表达式给提取出来。
- 最后，我们把提取出来的数据保存到了我们事先准备好的列表中。

以上便是一个简单的爬虫程序了，程序虽然简单但是各个功能模块还是都有的。


## 参考

- [https://zh.wikipedia.org/wiki/%E7%B6%B2%E8%B7%AF%E7%88%AC%E8%9F%B2](https://zh.wikipedia.org/wiki/%E7%B6%B2%E8%B7%AF%E7%88%AC%E8%9F%B2)