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

**2 . 简单的redis操作**

redis连接实例是线程安全的，可以直接将redis连接实例设置为一个全局变量，直接使用。如果需要另一个Redis实例（or Redis数据库）时，就需要重新创建redis连接实例来获取一个新的连接。同理，python的redis没有实现select命令。

```
>>> import redis
>>> r = redis.Redis(host='localhost',port=6379,db=0)
>>> r.set('guo','shuai')
True
>>> r.get('guo')
'shuai'
>>> r['guo']            
'shuai'
>>> r.keys()
['guo']
>>> r.dbsize()         #当前数据库包含多少条数据       
1L
>>> r.delete('guo')
1
>>> r.save()               #执行“检查点”操作，将数据写回磁盘。保存时阻塞
True
>>> r.get('guo');
>>> r.flushdb()        #清空r中的所有数据
True
```

**3. pipeline操作**

管道（pipeline）是redis在提供单个请求中缓冲多条服务器命令的基类的子类。它通过减少服务器-客户端之间反复的TCP数据库包，从而大大提高了执行批量命令的功能。

```
>>> p = r.pipeline() --创建一个管道
>>> p.set('hello','redis')
>>> p.sadd('faz','baz')
>>> p.incr('num')
>>> p.execute()
[True, 1, 1]
>>> r.get('hello')
'redis'
```

管道的命令可以写在一起，如：

```
>>> p.set('hello','redis').p.sadd('faz','baz').incr('num').execute()
```

默认的情况下，管道里执行的命令可以保证执行的原子性，执行pipe = r.pipeline\(transaction=False\)可以禁用这一特性。

**4. 应用场景 – 页面点击数**

《Redis Cookbook》对这个经典场景进行详细描述。假定我们对一系列页面需要记录点击次数。例如论坛的每个帖子都要记录点击次数，而点击次数比回帖的次数的多得多。如果使用关系数据库来存储点击，可能存在大量的行级锁争用。所以，点击数的增加使用redis的INCR命令最好不过了。

当redis服务器启动时，可以从关系数据库读入点击数的初始值（1237这个页面被访问了34634次）

```
>>> r.set("visit:1237:totals",34634)
True
```

```
>>> r.incr("visit:1237:totals")
34635
>>> r.incr("visit:1237:totals")
34636
```

每当有一个页面点击，则使用INCR增加点击数即可。

页面载入的时候则可直接获取这个值

```
>>> r.get ("visit:1237:totals")
'34636'
```

**5. 使用hash类型保存多样化对象**

当有大量类型文档的对象，文档的内容都不一样时，（即“表”没有固定的列），可以使用hash来表达。

```
>>> r.hset('users:jdoe', 'name', "John Doe")
1L
>>> r.hset('users:jdoe', 'email', 'John@test.com')
1L
>>> r.hset('users:jdoe', 'phone', '1555313940')
1L
>>> r.hincrby('users:jdoe', 'visits', 1)
1L
>>> r.hgetall('users:jdoe')
{'phone': '1555313940', 'name': 'John Doe', 'visits': '1', 'email': 'John@test.com'}
>>> r.hkeys('users:jdoe')
['name', 'email', 'phone', 'visits']
```

**6. 应用场景 – 社交圈子数据**

在社交网站中，每一个圈子\(circle\)都有自己的用户群。通过圈子可以找到有共同特征（比如某一体育活动、游戏、电影等爱好者）的人。当一个用户加入一个或几个圈子后，系统可以向这个用户推荐圈子中的人。

我们定义这样两个圈子,并加入一些圈子成员。

```
>>> r.sadd('circle:game:lol','user:debugo')
1
>>> r.sadd('circle:game:lol','user:leo')
1
>>> r.sadd('circle:game:lol','user:Guo')
1
>>> r.sadd('circle:soccer:InterMilan','user:Guo')
1
>>> r.sadd('circle:soccer:InterMilan','user:Levis')
1
>>> r.sadd('circle:soccer:InterMilan','user:leo')
1
```

\#获得某一圈子的成员

```
>>> r.smembers('circle:game:lol')
set(['user:Guo', 'user:debugo', 'user:leo'])
redis> smembers circle:jdoe:family  
```

可以使用集合运算来得到几个圈子的共同成员：

```
>>> r.sinter('circle:game:lol', 'circle:soccer:InterMilan')
set(['user:Guo', 'user:leo'])
>>> r.sunion('circle:game:lol', 'circle:soccer:InterMilan')
set(['user:Levis', 'user:Guo', 'user:debugo', 'user:leo'])
```

**7. 应用场景 – 实时用户统计**

[Counting Online Users with Redis](http://flask.pocoo.org/snippets/71/)

介绍了这个方法。当我们需要在页面上显示当前的在线用户时，就可以使用Redis来完成了。首先获得当前时间（以Unix timestamps方式）除以60，可以基于这个值创建一个key。然后添加用户到这个集合中。当超过你设定的最大的超时时间，则将这个集合设为过期；而当需要查询当前在线用户的时候，则将最后N分钟的集合交集在一起即可。由于redis连接对象是线程安全的，所以可以直接使用一个全局变量来表示。

```
import time
from redis import Redis
from datetime import datetime
ONLINE_LAST_MINUTES = 5
redis = Redis()

def mark_online(user_id):         #将一个用户标记为online
    now = int(time.time())        #当前的UNIX时间戳
    expires = now + (app.config['ONLINE_LAST_MINUTES'] * 60) + 10    #过期的UNIX时间戳
    all_users_key = 'online-users/%d' % (now // 60)        #集合名，包含分钟信息
    user_key = 'user-activity/%s' % user_id                
    p = redis.pipeline()
    p.sadd(all_users_key, user_id)                         #将用户id插入到包含分钟信息的集合中
    p.set(user_key, now)                                   #记录用户的标记时间
    p.expireat(all_users_key, expires)                     #设定集合的过期时间为UNIX的时间戳
    p.expireat(user_key, expires)
    p.execute()

def get_user_last_activity(user_id):        #获得用户的最后活跃时间
    last_active = redis.get('user-activity/%s' % user_id)  #如果获取不到，则返回None
    if last_active is None:
        return None
    return datetime.utcfromtimestamp(int(last_active))

def get_online_users():                     #获得当前online用户的列表
    current = int(time.time()) // 60        
    minutes = xrange(app.config['ONLINE_LAST_MINUTES'])
    return redis.sunion(['online-users/%d' % (current - x)        #取ONLINE_LAST_MINUTES分钟对应集合的交集
                         for x in minutes])
```

































