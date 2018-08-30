
<!-- TOC -->

- [Scrapy 简介](#scrapy-简介)
    - [安装](#安装)
    - [架构介绍](#架构介绍)
- [Scrapy 基本使用](#scrapy-基本使用)
    - [第一步，创建项目](#第一步创建项目)
    - [第二步，编写Spider](#第二步编写spider)
    - [第三步，定义item](#第三步定义item)
    - [第四步，构建 Item pipeline 持久化到文件](#第四步构建-item-pipeline-持久化到文件)
    - [第五步，使用Item pipeline 持久化到数据库](#第五步使用item-pipeline-持久化到数据库)
- [参考](#参考)

<!-- /TOC -->


## Scrapy 简介

Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架。 可以应用在包括数据挖掘，信息处理或存储历史数据等一系列的程序中。

Scrapy 使用了 Twisted 异步网络框架来处理网络通讯，结构清晰明了，并且包含了各种中间件接口，可以灵活的完成各种需求。


### 安装 

Scrapy 是使用Python开发，属于Python的第三方包。它的安装和其他三方包没什么区别。当前Scrapy 最新版本为`1.5`，支持python2.7 和python3.4+版本的python。

**Linux/Mac**

在linux 和 Mac 系统下，可使用 pip安装。

```python
pip install scrapy 
```

**windows**

在windows上安装的话，需要按照的依赖包比较多。官方建议直接使用 [Anaconda](https://docs.anaconda.com/anaconda/) 或 [Miniconda](https://conda.io/docs/user-guide/install/index.html) ，通过[conda-forge](https://conda-forge.org/)包来安装，这样可以解决各种因为window缺少包而引起的问题。

```python
conda install -c conda-forge scrapy
```

### 架构介绍 

Scrapy 架构如下图：

![](https://ws1.sinaimg.cn/large/8697aaedly1fue5k6ydd5j212w0q4myc.jpg)

Scrapy中的数据流由执行引擎控制，其过程如下:

- 引擎打开一个网站(open a domain)，找到处理该网站的Spider并向该spider请求第一个要爬取的URL(s)。
- 引擎从Spider中获取到第一个要爬取的URL并在调度器(Scheduler)以Request调度。
- 引擎向调度器请求下一个要爬取的URL。
- 调度器返回下一个要爬取的URL给引擎，引擎将URL通过下载中间件(请求(request)方向)转发给下载器(Downloader)。
- 一旦页面下载完毕，下载器生成一个该页面的Response，并将其通过下载中间件(返回(response)方向)发送给引擎。
- 引擎从下载器中接收到Response并通过Spider中间件(输入方向)发送给Spider处理。
- Spider处理Response并返回爬取到的Item及(跟进的)新的Request给引擎。
- 引擎将(Spider返回的)爬取到的Item给Item Pipeline，将(Spider返回的)Request给调度器。
- (从第二步)重复直到调度器中没有更多地request，引擎关闭该网站。

Scrapy 架构中各组件大致功能如下：

**Scrapy 引擎**
引擎负责控制数据流在系统中所有组件中流动，并在相应动作发生时触发事件。 详细内容查看下面的数据流(Data Flow)部分。

**调度器(Scheduler)**
调度器从引擎接受request并将他们入队，以便之后引擎请求他们时提供给引擎。

**下载器(Downloader)**
下载器负责获取页面数据并提供给引擎，而后提供给spider。

**Spiders**
Spider是Scrapy用户编写用于分析response并提取item(即获取到的item)或额外跟进的URL的类。 每个spider负责处理一个特定(或一些)网站。 更多内容请看 Spiders 。

**Item Pipeline**
Item Pipeline负责处理被spider提取出来的item。典型的处理有清理、 验证及持久化(例如存取到数据库中)。 更多内容查看 Item Pipeline 。

**下载器中间件(Downloader middlewares)**
下载器中间件是在引擎及下载器之间的特定钩子(specific hook)，处理Downloader传递给引擎的response。 其提供了一个简便的机制，通过插入自定义代码来扩展Scrapy功能。

**Spider中间件(Spider middlewares)**
Spider中间件是在引擎及Spider之间的特定钩子(specific hook)，处理spider的输入(response)和输出(items及requests)。 其提供了一个简便的机制，通过插入自定义代码来扩展Scrapy功能。

各组件功能简单可总结如下，大多数情况下我们只需要定义其中的`Spider`和`ItemPipline`模块即可需求。

![](https://ws1.sinaimg.cn/large/8697aaedly1fue5c9sky2j20vg0k846q.jpg)


## Scrapy 基本使用

我们已经把scrapy安装好了，并了解了它的基本架构和数据流程。接下来，我们使用scrapy来改写之前的爬虫项目，来熟悉它的各组件的使用方法。

### 第一步，创建项目

scrapy 提供了一些命令行工具，可直接生成项目代码。我们可直接使用如下命令来生成项目代码。

```python
scrapy startproject v6_scrapy 
```
会生成如下代码：

![](https://ws1.sinaimg.cn/large/8697aaedly1fue63ipdyzj20c40awjsc.jpg)

```
scrapy.cfg 项目部署文件
v6_scrapy/spiders 爬虫Spiders模块存放目录
v6_scrapy/items.py 项目中的item文件
v6_scrapy/pipelines.py 项目中的Pipelines文件
v6_scrapy/settings.py 项目中的配置文件

```

### 第二步，编写Spider 

在sipders目录中，添加我们的爬虫文件`toutiao_spider.py`，内容如下：

```python
# -*- coding:utf-8 -*-
import scrapy


class ToutiaoSpider(scrapy.Spider):
    name = 'toutiao'
    start_urls = [
        'https://toutiao.io/c/ai?page=1',
    ]

    def parse(self, response):
        """
        实现html解析
        :param response:
        :return:
        """
        papers = response.xpath('//a[@rel="external"]')
        for paper in papers:
            title = paper.xpath('./@title').extract()[0]
            href = 'https://toutiao.io%s' % paper.xpath('./@href').extract()[0]
        
        print(title, href)
```

爬虫模块包含一个爬虫类，该类负责爬取网页的内容，并解析返回的html内容，从中提取我们需要的数据。爬虫类继承`scrapy.Spider`类，有以下截个属性和方法：

- `name` spider 的名字，用于区分爬虫类。
- `start_urls` spider 启动时，进行爬取的入口url列表。当没有制定特定的URL时，spider将从该列表中开始进行爬取。 因此，第一个被获取到的页面的URL将是该列表之一。 后续的URL将会从获取到的数据中提取。
- `parse` 当response没有指定回调函数时，该方法是Scrapy处理下载的response的默认方法。`parse` 负责处理response并返回处理的数据以及跟进的URL。 Spider 对其他的Request的回调函数也有相同的要求。

在完成之后，执行如下代码启动爬虫：

```bash
scrapy crawl toutiao
```

会看到我们需要抓取的东西被打印出来。

大家注意到代码中有个`xpath()`的用法，这是scrapy自己的一套数据提取机制，称为`selector`，他们通过特定的[XPath](http://www.w3.org/TR/xpath)和[CSS](http://www.w3.org/TR/selectors) 表达式来查询和提取html中的数据。

Selector 对象主要有4种方法：

- `xpath(query)` 传入`XPath`表达式，返回该表达式所对应的多有节点的selector list 列表。
- `css(query)`传入`CSS`表达式，返回该表达式所对应的多有节点的selector list 列表。
- `extrac()` 序列化该节点为Unicode字符串并返回list列表。
- `re(regex)` 根据传入的正则表达式提取数据，返回Unicode字符串列表。

使用如下：

```python
scrapy.Selector(response).re('<a rel="next" href="(\S*)">下一页 ›</a>')
scrapy.Selector(response).xpath('//title/text()')
scrapy.Selector(response).css('title::text').extract()[0]

# xpath和css 可简写为：
reponse.xpath()
reponse.css()
```

### 第三步，定义item

scrapy 使用Item类来结构化数据，以方便对数据的操作。Item 类是一个简单的容器，用来暂存被抓取到的数据，它提供了类似字典的API操作，很多操作类似字典。它需要继承自`scrapy.Item`， 代码如下：

```python
class ToutiaoItem(scrapy.Item):
    title = scrapy.Field()
    href = scrapy.Field()
```

它的操作如下：
- 新建
```python
item = ToutiaoItem(title='深度学习在推荐系统上的应用', href='https://toutiao.io/k/pmd2v1')
```
- 获取字段

```python
print(item['title'])
```

- 获取键值

```python
print(item.keys())
print(item.values())
```

- Item 复制

```python
item1 = ToutiaoItem(item)
item2 = item.copy()
```

- dict 与item转化

```python
dict_item = dict(item)
item = ToutiaoItem({'title':'深度学习在推荐系统上的应用', 'href':'https://toutiao.io/k/pmd2v1'})
```

spider 的`parse`方法可改写为:

```python
     def parse(self, response):
        """
        实现html解析
        :param response:
        :return:
        """
        papers = response.xpath('//a[@rel="external"]')
        for paper in papers:
            title = paper.xpath('./@title').extract()[0]
            href = 'https://toutiao.io%s' % paper.xpath('./@href').extract()[0]
            item = ToutiaoItem({'title': title, 'href': href})
            yield item

```

我们使用`yield`关键字，将`parse`方法变为一个生成器，优化了代码，减少了数据资源占用。

>说明，yield关键字和生成器，后面会讲到。大家可暂理解为一个高性能的列表对象即可。


### 第四步，构建 Item pipeline 持久化到文件

Item pipeline 是scrapy数据流的最后一步，它的主要功能有以下几点：

- 清理HTML数据
- 验证爬取数据的合法性
- 查重并丢弃
- 将爬取到的数据做持久化处理

我们今天使用到的便是持久化处理。

每个Item pipeline 是一个独立的类，它必须实现`process_item(self, item, spider)`方法，pipeline的每个组件会调用该方法，它必须返回一个item对象，或者抛出DropItem异常，被丢弃的Item将不会被之后的pipeline组件处理。代码如下：

我们看下如何将爬取到的数据保存到文件，代码如下：

```python

class V6ScrapyFilePipeline(object):

    def __init__(self):
        self.file = open('toutiao.json', 'wb')

    def process_item(self, item, spider):
        if item['title']:
            line = json.dumps(dict(item))+"\n"
            self.file.write(line.encode())
            return item
        else:
            raise DropItem('在[%s]item中，没有title关键字'%item)

```

我们定义完Item pipeline后，还需要激活它，到`settings.py`配置中，添加如下配置：

```python
ITEM_PIPELINES = {
   'v6_scrapy.pipelines.V6ScrapyFilePipeline': 1,
}
```

`ITEM_PIPELINES`是一个字典，key为pipeline的路径，value为整数值，pipeline会安装这个整数值由低到高顺序执行。该整整数值一般在0到1000之间。

`settings.py`中配置的pipelines会应用于所有的spider类，我们也可以单独为某个spider类配置自己的pipeline。如下：

```python
class MysqlSpider(scrapy.Spider):

    custom_settings = {
        'ITEM_PIPELINES':{
            'v6_scrapy.pipelines.V6ScrapyFilePipeline': 1,
    }

```

此时，我们再去启动爬虫：

```python
scrapy crawl toutiao
```

我们会看到，生成了一个数据文件`toutiao.json`，我们的数据成功保存下来。

其实，scrapy 内建了一些存储的pipeline，我们可以直接通过命令行工具来时用，例如可直接将数据保存为csv文件，可执行如下命令：

```bash
scrapy crawl toutiao -o toutiao.csv
```
更多内建pipeline，可参考[这里]()

### 第五步，使用Item pipeline 持久化到数据库

在settings.py 配置文件中添加mysql的链接信息。

```python
# db configrations
MYSQL_HOST = 'localhost'
MYSQL_PORT = 3306
MYSQL_DB = 'spider'
MYSQL_USER = 'root'
MYSQL_PWD = 'root'
```

代码如下：

```python

class V6ScrapyDBPipeline(object):

    def __init__(self, host, port, db, user, pwd):
        self.host = host
        self.port = port
        self.db = db
        self.user = user
        self.pwd = pwd

    @classmethod
    def from_crawler(cls, crawler):
        """
        钩子函数，会调用它来初始化Pipeline
        :param crawler:
        :return:
        """
        return cls(
            host=crawler.settings.get('MYSQL_HOST'),
            port=crawler.settings.get('MYSQL_PORT'),
            db=crawler.settings.get('MYSQL_DB'),
            user=crawler.settings.get('MYSQL_USER'),
            pwd=crawler.settings.get('MYSQL_PWD')
        )

    def open_spider(self, spider):
        """
        钩子函数，spider创建时调用
        :param spider:
        :return:
        """
        spider.logger.info('[%s]：create db connection.' % spider.name)
        self.conn = pymysql.connect(host=self.host, user=self.user, password=self.pwd,
                 database=self.db, port=self.port)
        self.cursor = self.conn.cursor()

    def close_item(self, spider):
        """
        钩子函数，spider关闭时调用
        :param spider:
        :return:
        """
        spider.logger.info('[%s]：close db connection.' % spider.name)
        self.conn.close()

    def process_item(self, item, spider):
        """
        处理函数
        :param spider:
        :return:
        """
        try:
            sql = "insert into result (post_title, post_url)" \
                  "values('%s', '%s');" % (item['title'], item['href'])
            self.cursor.execute(sql)
            self.conn.commit()
            spider.logger.info('[%s]：insert db: %s' % (spider.name, sql))
        except Exception as err:
            self.conn.rollback()
            spider.logger.info('[%s]：insert db error: %s' % (spider.name, err))

```

此处我们为了更好的创建和释放资源使用了3个钩子函数。


好了，到此为止，我们的爬虫项目便完成了。这里只是简单的描述了下基本的使用方法，方便大家来理解和掌握Python的基本语法，至于更多`scrapy`模块的使用及后期的部署方法，大家可考考其官方文档和源码：

- [scrapy 项目源码](https://github.com/scrapy/scrapy)
- [官方文档英文版1.5](https://doc.scrapy.org/)

## 参考

- [xpath语法入门](http://www.runoob.com/xpath/xpath-nodes.html)
- [scrapy1.0文档](https://scrapy-chs.readthedocs.io/zh_CN/1.0/index.html)
- [scrapy简介](https://blog.csdn.net/spirit_artist/article/details/79933722)

