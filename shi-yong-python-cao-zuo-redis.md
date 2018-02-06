# 使用Python操作Redis

**1.python的redis是否实现select命令？**

**2.本文介绍了哪些Redis使用场景？**

**3.如何实现使用hash类型保存多样化对象？**

**1. 安装pyredis**

首先安装pip

```
<SHELL># apt-get install python-pip
......
<SHELL># pip install --proxy=http://172.1.2.6:8080 redis
  Downloading redis-2.9.1.tar.gz (62kB): 62kB downloaded
  Running setup.py (path:/tmp/pip_build_root/redis/setup.py) egg_info for package redis
  ......
  Successfully installed redis
  Cleaning up...
```

也可以使用easy\_install的方式来安装：

```
easy_install redis
```

或者直接编译安装：

```
wget https://pypi.python.org/packages/source/r/redis/redis-2.9.1.tar.gz
tar xvzf redis-2.9.1.tar.gz
cd redis-2.9.1
python setup.py install
```









