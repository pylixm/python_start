# 第十一章 爬虫结果保存文件


<!-- TOC -->

- [第十一章 爬虫结果保存文件](#第十一章-爬虫结果保存文件)
  - [文件处理](#文件处理)
    - [读取文件](#读取文件)
    - [写文件](#写文件)
    - [with 关键字](#with-关键字)
  - [将爬取结构保存文件](#将爬取结构保存文件)

<!-- /TOC -->

之前我们的爬虫代码是直接将爬取结果保存到字典，也就是在内存中，当程序执行完成后，我们无法再次获取这快内存的内容，造成结果的丢失。这一章呢，我们来改进下我们的爬虫代码，将结果保存到文件，方便我们的查看。

在改写代码之前，先让我们来看下`Python`中的文件处理如何的。

## 文件处理

`Python`中使用内建函数`open()`来实现文件的读写。

### 读取文件

```python
f = open('/home/test.txt', 'r')
# 文件路径：/home/test.text ，支持绝对路径和相对路径
# 模式'r'：表示读取文本文件，编码为utf-8。读取其他编码可指定具体编码 open('C:\\test.txt','r', encoding='GBK')。
# 模式'rb': 表示读取二进制文件，如图片、视频等，可使用此模式。
f.read()
# 读取全部文件内容
f.readline()
# 按行读取，返回第一行
f.readlines()
# 按行读取，返回所有行数据的一个列表
f.close()
# 关闭文件，文件的打开会占用系统资源，且系统规定的文件打开数是有限的。
```

**知识点：**
- 使用`open`函数时，路径和模式必填。
- `f` 文件对象被读取后，内容将清空。
- 读取文件后，需要关闭，否则会占用系统资源和系统打开文件数。

### 写文件

Python 通过模式来区分是读取文件还是写文件。

```python
f = open('/home/test.txt', 'w')
# 文件路径: '/home/test.txt'，文件名自拟
# 模式 'w': 表示写文本文件，编码为utf-8
# 模式 'wb': 表示写二进制文件
f.write('内容')
# 一次写入全部内容，不分行
f.writelines(['第一部分','第二部分'])
# 一次写入多个元素，参数为列表，不分行
f.close()
# 关闭文件，保证所有数据完整写入自盘。
```

除了 `r` 和`w` 还有很多模式，如下：

![](/images/open_model.png)


### with 关键字

Python 针对这种用完之后需要关闭的对象，提供了`with`关键字。在代码结束后，自动的调用`close()`方法，将对象释放。

```python
with open('/home/test.txt', 'w') as f:
    f.write('内容')
```

在执行完`with`内的代码后，会自动关闭文件。这样，避免了我们因忘记关闭造成的系统资源浪费。


## 将爬取结构保存文件

```python
# -*- coding:utf-8 -*-
"""
简单爬虫：将结果保存到文件中
requests docs: http://www.python-requests.org/en/master/
BeautifulSoup docs: https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/
"""
import requests
import bs4


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

    def parse_resp(slef, resp):
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

    def save_txt(self, ret=[]):
        """
        保存 csv 结果
        :param ret:
        :return:
        """
        if ret:
            with open('post_list.txt', 'ab') as file:
                for item in ret:
                    line = '{0} {1}'.format(item['title'], item['href'])
                    file.write(line.encode() + b'\n')

    def save_csv(self, ret=[]):
        """
        保存 csv 结果
        :param ret:
        :return:
        """
        if ret:
            with open('post_list.csv', 'ab') as file:
                for item in ret:
                    line = '{0},{1}'.format(item['title'], item['href'])
                    file.write(line.encode('GBK') + b'\n')

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
            self.save_csv(ret)
            self.save_txt(ret)


if __name__ == '__main__':
    spider = MySpider()
    spider.start()

```