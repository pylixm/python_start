<!-- TOC -->

- [Python模块的管理](#python模块的管理)
- [Python虚拟环境](#python虚拟环境)
- [Python 其他发行版](#python-其他发行版)

<!-- /TOC -->
## Python模块的管理

我们在`模块`章节知道了，Python给我们提供了许多工具来管理丰富的第三方包，其中最常用的便是`pip`。

它的常用命令如下：

```bash
# 安装模块
pip install pk_name

# 查看安装列表
pip list 

# 卸载模块
pip uninstall pk_name 

# 导出安装列表
pip free 
```

## Python虚拟环境

我们在进行Python开发时，有时候需要不同版本的Python。随着项目的不同，我们安装的包也不尽相同。这样就给我们使用时，造成了很大的麻烦，对于不同版本的Python而言，我们需要不停的切换。而对于包的依赖，那可以说是相当混乱，但从Python安装的包列表中，我们无法分别项目所依赖的包。

为了解决Python依赖包的问题，Python社区中提出了一种虚拟环境的解决方案 - virtualenv工具。它将项目所依赖的Python包安装到一个单独的目录中，不同的项目使用不同的目录，实现Python依赖包的切换。这样很好的解决了，Python项目依赖包的问题。

`pyenv` 解决了Python版本的问题， 它通过修改系统环境变量，来实现修改当前环境的默认python的目的。

关于`pyenv`和`virtualenv`的配置及使用，可参阅我之前的一篇博客：[使用 pyenv + virtualenv 打造多版本python开发环境](http://www.pylixm.top/post/51/)


最近[KZ](https://www.kennethreitz.org/values)大神有发了一个模块`Pipenv`，将Python的版本和虚拟环境统一管理起来，而且还增加了对包依赖的管理。现在，该库已成为Python官方网站推荐的包管理工具。

对于它的使用，大家可参考我之前博客：[pipenv 试用过程分享](http://www.pylixm.top/post/19/)

## Python 其他发行版

Python的生态中，包的依赖一直是一个令人头疼的问题，linux还好点，像是windows用户这个问题尤为突出。随着Python的大量使用，出现了一些优秀的解决方案。其中`Acaconda`便是一个解决Python依赖包的一个发行版本，其包含了conda、Python等180多个科学包及其依赖项。

其中，`conda` 是这一发行版的包管理工具。以为改发行版携带了大量的科学计算的包，所以广泛应用在科学计算、大数据等方面的应用环境中。

**扩展阅读**

- [致Python初学者们 - Anaconda入门使用指南](https://www.jianshu.com/p/169403f7e40c#)
- [Acaconda官方文档](https://www.anaconda.com/)。

