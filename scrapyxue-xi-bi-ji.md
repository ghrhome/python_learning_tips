# scrapy学习笔记

scrapy是python最有名的爬虫框架之一，可以很方便的进行web抓取，并且提供了很强的定制型，这里记录简单学习的过程和在实际应用中会遇到的一些常见问题

## 一、安装

在安装scrapy之前有一些依赖需要安装，否则可能会安装失败，scrapy的选择器依赖于`lxml`，还有`Twisted`网络引擎，下面是ubuntu下安装的过程

### 1. linux下安装

```
# 1. 安装xml依赖库
$ sudo apt-get install libxml2 libxml2-dev
$ sudo apt-get install libxslt1-dev
$ sudo apt-get install python-libxml2

# 2. 安装lxml
$ sudo pip install lxml

# 3. 安装Twisted（版本可以换成最新的），用pip也可以，如果失败的话下载源码安装，如下
$ wget https://pypi.python.org/packages/6b/23/8dbe86fc83215015e221fbd861a545c6ec5c9e9cd7514af114d1f64084ab/Twisted-16.4.1.tar.bz2#md5=c6d09bdd681f538369659111f079c29d
$ tar xjf Twisted-16.4.1.tar.bz2
$ cd Twisted-16.4.1
$ sudo python setup.py install

# 3. 安装scrapy
$ sudo pip install scrapy
```

> [http://lxml.de/installation.html](http://lxml.de/installation.html)

### 2. Mac下安装

```
# 安装xml依赖库
$ xcode-select —install

# 其实相关依赖pip会自动帮我们装上
$ pip install scrapy
```

mac下安装有时候会失败，建议使用`virtualenv`安装在独立的环境下，可以减少一些问题，因为mac系统自带python，例如一些依赖库依赖的一些新的版本，而升级新版本会把旧版本卸载掉，卸载可能会有权限的问题

## 二、基本使用

### 1. 初始化scrapy项目

我们可以使用命令行初始化一个项目

```
$ scrapy startproject tutorial
```

> [这里](https://scrapy-chs.readthedocs.io/zh_CN/0.24/topics/commands.html)可以查看scrapy更多其他的命令

初始化完成后，我们得到下面目录结构

```
scrapy.cfg:         项目的配置文件
tutorial/:          该项目的python模块, 在这里添加代码
    items.py:       项目中的item文件
    pipelines.py:   项目中的pipelines文件.
    settings.py:    项目全局设置文件.
    spiders/        爬虫模块目录

```

我们先看一下scrapy的处理流程  
![](http://jbcdn2.b0.upaiyun.com/2016/10/c24dade2ab6bc0143a2abc9d271136d0.png)

scrapy由下面几个部分组成

* `spiders`
  ：爬虫模块，负责配置需要爬取的数据和爬取规则，以及解析结构化数据
* `items`
  ：定义我们需要的结构化数据，使用相当于
  `dict`
* `pipelines`
  ：管道模块，处理spider模块分析好的结构化数据，如保存入库等
* `middlewares`
  ：中间件，相当于钩子，可以对爬取前后做预处理，如修改请求header，url过滤等

我们先来看一个例子，在`spiders`目录下新建一个模块`DmozSpider.py`

