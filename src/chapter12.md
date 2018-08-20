<!-- TOC -->

- [课前预备知识学习](#课前预备知识学习)
- [常用数据库驱动介绍](#常用数据库驱动介绍)
    - [Mysql](#mysql)
    - [PostgreSQL](#postgresql)
    - [SQLite](#sqlite)
- [Mysql使用举例](#mysql使用举例)
- [改写爬虫代码](#改写爬虫代码)
- [练习](#练习)
- [参考](#参考)

<!-- /TOC -->
## 课前预备知识学习
- [数据库介绍及分类](http://blog.51cto.com/13178102/2064041)
- [SQL](http://www.runoob.com/sql/sql-tutorial.html)


## 常用数据库驱动介绍

Python 支持的数据库很多，它是通过第三方的链接操作库来实现操作数据库的。Python生态中比较长用的关系型数据有：
- Mysql
- PostgreSQL
- SQLite

除了这些关系型数据库，python也支持像`MongoDB`、`Redis`等非关系型数据库，只要有相应的第三方链接驱动即可。

### Mysql

`Mysql`数据库，可以说是使用的最广泛的开源数据库，简单使用且免费，在Python生态中的使用也是非常广泛。

在Python2.x中使用的最多的链接驱动库为[Mysql-python](https://pypi.org/project/MySQL-python/1.2.5/)，它有windows版和源码版。

在Python3.x后`Mysql-python`不再支持，可使用[pymysql](https://pypi.org/project/PyMySQL/)替代，使用方法几乎一致。

### PostgreSQL

`PostgreSQL` 是功能强大的开源数据库，如果项目对数据库的性能要求严格，并想节省经费不想上`Oracle`，建议使用此数据库。`PostgreSQL`对Python应用做了许多的优化，Django的官方文档推荐使用此数据库作为项目的首选数据库。

我们在操作该数据库时，使用的最广泛的链接库为[psycopg2](https://pypi.org/project/psycopg2/)。

### SQLite 

`SQLite`是一种嵌入式数据库，它的数据库就是一个文件。由于SQLite本身是C写的，而且体积很小，所以，经常被集成到各种应用程序中，甚至在iOS和Android的App中都可以集成。在Python中，可使用该数据库作为测试数据库使用，或作为简单的数据存储使用。可使用使用`sqlite3`模块来操作，Python2.5以上自带了该模块，无需安装。

## Mysql使用举例

Python操作关系型数据库的方式大同小异，我们这里以`Mysql`为例，简单讲解下Python对数据库的操作。

```python
#!/usr/bin/python3
 
import pymysql
 
# 打开数据库连接
db = pymysql.connect("localhost","testuser","test123","TESTDB" )
 
# 使用 cursor() 方法创建一个游标对象 cursor
cursor = db.cursor()
 
# 使用 execute() 方法执行 SQL，如果表存在则删除
cursor.execute("DROP TABLE IF EXISTS EMPLOYEE")
 
# 使用预处理语句创建表
sql = """CREATE TABLE EMPLOYEE (
         FIRST_NAME  CHAR(20) NOT NULL,
         LAST_NAME  CHAR(20),
         AGE INT,  
         SEX CHAR(1),
         INCOME FLOAT )"""
 
cursor.execute(sql)

# 新增数据
sql = "INSERT INTO EMPLOYEE(FIRST_NAME, \
       LAST_NAME, AGE, SEX, INCOME) \
       VALUES ('%s', '%s', '%d', '%c', '%d' )" % \
       ('Mac', 'Mohan', 20, 'M', 2000)
try:
   # 执行sql语句
   cursor.execute(sql)
   # 执行sql语句
   db.commit()
except:
   # 发生错误时回滚
   db.rollback()

# 查询操作
sql = "SELECT FIRST_NAME,LAST_NAME FROM EMPLOYEE \
       WHERE INCOME > '%d'" % (1000)
try:
   # 执行SQL语句
   cursor.execute(sql)
   # 获取所有记录列表
   results = cursor.fetchall()
   # [[FIRST_NAME1,LAST_NAME1],[FIRST_NAME2,LAST_NAME2]]
   for row in results:
      fname = row[0]
      lname = row[1]
      age = row[2]
      sex = row[3]
      income = row[4]
       # 打印结果
      print ("fname=%s,lname=%s,age=%d,sex=%s,income=%d" % \
             (fname, lname, age, sex, income ))
except:
   print ("Error: unable to fetch data")

# 更新数据 
sql = "UPDATE EMPLOYEE SET AGE = AGE + 1 WHERE SEX = '%c'" % ('M')
try:
   # 执行SQL语句
   cursor.execute(sql)
   # 提交到数据库执行
   db.commit()
except:
   # 发生错误时回滚
   db.rollback()

# 删除数据
sql = "Delete EMPLOYEE WHERE  id = 2"
try:
   # 执行SQL语句
   cursor.execute(sql)
   # 提交到数据库执行
   db.commit()
except:
   # 发生错误时回滚
   db.rollback()

# 关闭数据库连接
db.close()

```

知识点：
- 在执行数据库操作的时候，执行完毕，一定要关闭数据库链接，否则会造成系统资源的浪费。
- 在执行UDA（update/delete/add）操作时，增加异常的判断，发生异常及时回滚操作。

## 改写爬虫代码

更加上边介绍的数据库知识来改写我们之前的爬虫代码。

```python
# -*- coding:utf-8 -*-
import requests
import bs4
import pymysql


class MySpider(object):

    def request_url(self, path='https://toutiao.io/c/ai'):
        """
        请求url
        :param path:
        :return:
        """
        if not path:
            print('path 参数不能为空')
            return None
        # 1、请求
        header = {'User-Agent':
                      'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36'}
        try:
            return requests.get(path, headers=header)
        except Exception as err:
            print(err)
            return None

    def parse_resp(self, resp):
        """
        解析返回结果
        :param resp:
        :return:
        """
        ret = []
        bsobj = bs4.BeautifulSoup(resp.content, 'lxml')
        a_list = bsobj.find_all('a', target="_blank", rel="external")
        for a in a_list:
            post_dict = {}
            post_dict['title'] = a.get_text()
            post_dict['href'] = 'https://toutiao.io{0}'.format(
                a.get('href')).replace('posts', 'k')
            ret.append(post_dict)
        return ret

    def save_db(self, ret):
        """
        保存数据到数据库中
        :param ret:
        :return:
        """
        db = pymysql.connect("localhost", "root", "root", "spider")
        cursor = db.cursor()
        try:
            with db.cursor() as cursor:
                for item in ret:
                    sql = "insert into result (post_title, post_url)" \
                        "values('%s', '%s');" % (item['title'], item['href'])
                    print(sql)
                    cursor.execute(sql)
            db.commit()
        finally:
            db.close()

    def start(self, path='https://toutiao.io/c/ai'):
        """
        主函数
        :param path:
        :return:
        """
        # 1、请求
        resp = self.request_url(path)
        print(resp.status_code)
        if resp.status_code == 200:
            # 2、解析
            ret = self.parse_resp(resp)
            # 3、保存
            self.save_db(ret)


if __name__ == '__main__':
    spider = MySpider()
    spider.start()

```

## 练习

大家可尝试将对数据库的操作抽象提取为一个工具类。

## 参考

- [http://www.runoob.com/python3/python3-mysql.html](http://www.runoob.com/python3/python3-mysql.html)
