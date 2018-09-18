<!-- TOC -->

- [前言](#前言)
- [准备工作](#准备工作)
- [发布](#发布)
- [参考](#参考)

<!-- /TOC -->
## 前言

Pypi是有Python软件基金会维护的Python包的公共存储库。人们可以从上边下载第三方包来使用，也可发布自己的包供别人下载使用。本节，我们来说下如何发布我们自己的包到Pypi上。

## 准备工作

- 1、编写包的安装文件`setup`，文件规定了包的版本、源文件及依赖的Python版本等等。如下实例为一个Django的扩展应用的setup：

```python
import os
from setuptools import find_packages, setup

with open(os.path.join(os.path.dirname(__file__), 'README.md')) as readme:
    README = readme.read()

# allow setup.py to be run from any path
os.chdir(os.path.normpath(os.path.join(os.path.abspath(__file__), os.pardir)))

setup(
    name='django-mdeditor',
    version='0.1.6',
    packages=find_packages(exclude=['mdeditor_demo', 'mdeditor_demo_app.*', 'mdeditor_demo_app']),
    include_package_data=True,
    license='GPL-3.0 License',
    description='A simple Django app to edit markdown text.',
    long_description=README,
    url='',
    author='pylixm',
    author_email='pyli.xm@gmail.com',
    classifiers=[
        'Environment :: Web Environment',
        'Framework :: Django',
        'Framework :: Django :: 1.7',
        'Framework :: Django :: 1.8',
        'Framework :: Django :: 1.9',
        'Framework :: Django :: 1.10',
        'Framework :: Django :: 1.11',
        'Framework :: Django :: 2.0',
        'Intended Audience :: Developers',
        'License :: OSI Approved :: GNU General Public License v3 (GPLv3)',
        'Operating System :: OS Independent',
        'Programming Language :: Python',
        # Replace these appropriately if you are stuck on Python 2.
        'Programming Language :: Python :: 2.7',
        'Programming Language :: Python :: 3.3',
        'Programming Language :: Python :: 3.4',
        'Programming Language :: Python :: 3.5',
        'Programming Language :: Python :: 3.6',
        'Topic :: Internet :: WWW/HTTP',
        'Topic :: Internet :: WWW/HTTP :: Dynamic Content'
    ],
)

```

关于如何编写setup文件，可见文档：https://docs.python.org/3/distutils/setupscript.html

- 2、增加版权信息和说明文档

- LICENSE 授权文件，比如：MIT license， APACHE license
- README.md 项目介绍和使用说明

- 3、使用Distutils进行打包

```python
python setup.py check  # 检查setup是否正确
python setup.py sdist  # 打包

```

执行完成后，会在顶层目录下生成dist目录和egg目录

- 4、注册Pypi账号，注册地址：https://pypi.org/account/register/
注册Pypitest账号，注册地址：https://test.pypi.org/
账号和密码在包发布时会录入使用，为了在发布时不再手动录入用户名和密码我也可以使用配置文件`.pypirc`。

```ini
[distutils]
index-servers =
  pypi
  pypitest

[pypi]
repository: https://upload.pypi.org/legacy/
username: your_username
password: your_password

[pypitest]
repository: https://test.pypi.org/legacy/
username: your_username
password: your_password
```


## 发布

做好准备工作后，执行如下命令：

```bash
python setup.py check
```
检测`setup.py `是否正确，若有错误则会显示，没有则说明setup.py文件格式正确。

运行如下命令，提交：

```bash
python setup.py sdist upload -r pypi
```
录入账号密码即可，发布Python包到Pypi。

之后，我们可以使用pip安装我们的包来做测试，也可登录`pypi.org`来查看我们上传的包。

## 参考

- [Distributing Python Modules](https://docs.python.org/3/distutils/index.html)
- [Packaging Python Projects](https://packaging.python.org/tutorials/packaging-projects/#uploading-your-project-to-pypi)