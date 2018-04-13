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

* `spiders`：爬虫模块，负责配置需要爬取的数据和爬取规则，以及解析结构化数据
* `items`：定义我们需要的结构化数据，使用相当于`dict`
* `pipelines`：管道模块，处理spider模块分析好的结构化数据，如保存入库等
* `middlewares`：中间件，相当于钩子，可以对爬取前后做预处理，如修改请求header，url过滤等

我们先来看一个例子，在`spiders`目录下新建一个模块`DmozSpider.py`

```
import scrapy

class DmozSpider(scrapy.Spider):
    # 必须定义
    name = "dmoz"
    # 初始urls
    start_urls = [
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
    ]

    # 默认response处理函数
    def parse(self, response):
        # 把结果写到文件中
        filename = response.url.split("/")[-2]
        with open(filename, 'wb') as f:
            f.write(response.body)
```

打开终端进入根目录，执行下面命令

```
$ scrapy crawl dmoz
```

爬虫开始爬取start\_urls定义的url，并输出到文件中，最后输出爬去报告，会输出爬取得统计结果

```
2016-09-13 10:36:43 [scrapy] INFO: Spider opened
2016-09-13 10:36:43 [scrapy] INFO: Crawled 0 pages (at 0 pages/min), scraped 0 items (at 0 items/min)
2016-09-13 10:36:43 [scrapy] DEBUG: Telnet console listening on 127.0.0.1:6023
2016-09-13 10:36:44 [scrapy] DEBUG: Crawled (200) <GET http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/> (referer: None)
2016-09-13 10:36:45 [scrapy] DEBUG: Crawled (200) <GET http://www.dmoz.org/Computers/Programming/Languages/Python/Books/> (referer: None)
2016-09-13 10:36:45 [scrapy] INFO: Closing spider (finished)
2016-09-13 10:36:45 [scrapy] INFO: Dumping Scrapy stats:
{'downloader/request_bytes': 548,
 'downloader/request_count': 2,
 'downloader/request_method_count/GET': 2,
 'downloader/response_bytes': 16179,
 'downloader/response_count': 2,
 'downloader/response_status_count/200': 2,
 'finish_reason': 'finished',
 'finish_time': datetime.datetime(2016, 9, 13, 2, 36, 45, 585113),
 'log_count/DEBUG': 3,
 'log_count/INFO': 7,
 'response_received_count': 2,
 'scheduler/dequeued': 2,
 'scheduler/dequeued/memory': 2,
 'scheduler/enqueued': 2,
 'scheduler/enqueued/memory': 2,
 'start_time': datetime.datetime(2016, 9, 13, 2, 36, 43, 935790)}
2016-09-13 10:36:45 [scrapy] INFO: Spider closed (finished)
```

这里我们完成了简单的爬取和保存的操作，会在根目录生成两个文件`Resources`和`Books`

### 2. 通过代码运行爬虫

每次进入控制台运行爬虫还是比较麻烦的，而且不好调试，我们可以通过`CrawlerProcess`通过代码运行爬虫，新建一个模块`run.py`

```
from scrapy.crawler import CrawlerProcess
from scrapy.utils.project import get_project_settings
 
from spiders.DmozSpider import DmozSpider
 
# 获取settings.py模块的设置
settings = get_project_settings()
process = CrawlerProcess(settings=settings)
 
# 可以添加多个spider
# process.crawl(Spider1)
# process.crawl(Spider2)
process.crawl(DmozSpider)
 
# 启动爬虫，会阻塞，直到爬取完成
process.start()
```

> 参考：
>
> [http://doc.scrapy.org/en/latest/topics/practices.html\#run-scrapy-from-a-script](http://doc.scrapy.org/en/latest/topics/practices.html#run-scrapy-from-a-script)









