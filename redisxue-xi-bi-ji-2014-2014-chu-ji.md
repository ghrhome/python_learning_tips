**阅读目录**

* [1. Redis是什么、特点、优势](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_label0)
* [2. redis安装（Linux）、启动、退出、设置密码、远程连接](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_label1)
* [3. Reis key](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_label2)
* [4. Redis数据类型](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_label3)
* [5. Redis HyperLogLog](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_label4)
* [6. Redis 发布订阅](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_label5)
* [7. Redis事务](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_label6)
* [8. Redis脚本](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_label7)
* [9. 数据备份与恢复](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_label8)
* [10. 数据库操作](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_label9)
* [11. 实际小应用](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_label10)
* [12. 关闭持久化](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_label11)

[回到顶部](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_labelTop)

### 1. Redis是什么、特点、优势

Redis是一个开源的使用C语言编写、开源、支持网络、可基于内存亦可持久化的日志型、高性能的Key-Value数据库，并提供多种语言的API。

它通常被称为**数据结构服务器**，因为值（value）可以是 字符串\(String\)、哈希\(Map\)、 列表\(list\)、集合\(sets\) 和 有序集合\(sorted sets\)等类型。

Redis 与其他 key - value 缓存产品有以下三个特点：

* Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用。
* Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
* Redis支持数据的备份，即master-slave模式的数据备份。

Redis优势

* 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
* 丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
* 原子 – Redis的所有操作都是原子性的，同时Redis还支持对几个操作全并后的原子性执行。
* 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

[回到顶部](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_labelTop)

### 2. redis安装（Linux）、启动、退出、设置密码、远程连接

**2.1 安装redis**

下载redis安装包（如：redis-2.8.17.tar.gz）

```
tar -zxvf redis-2.8.17.tar.gz
cd redis-2.8.17
make
sudo make install
```

**2.2 后台启动服务端**

```
nohup redis-server &
```

**注**：redis-server默认启动端口是6379，没有密码

如果不使用默认配置文件，启动时可以加上配置文件

```
nohup redis-server  ~/soft/redis-2.8.17/redis.conf &
```

**2.3 启动客户端、验证**

```
127.0.0.1:6379> ping
PONG

127.0.0.1:6379> set var "hello world"
OK
127.0.0.1:6379> get var
"hello world"
```

**2.4 退出**

关闭redis-server

```
redis-cli shutdown
```

例子

```
$ps -ef | grep redis
root     23422 19813  0 10:59 pts/5    00:00:08 redis-server *:6379

$sudo redis-cli  shutdown
[23422] 05 Mar 12:11:29.301 # User requested shutdown...
[23422] 05 Mar 12:11:29.301 * Saving the final RDB snapshot before exiting.
[23422] 05 Mar 12:11:29.314 * DB saved on disk
[23422] 05 Mar 12:11:29.314 # Redis is now ready to exit, bye bye...
[1]+  Done                    sudo redis-server  (wd: ~/soft/redis-2.10.3)
(wd now: ~/soft/redis-2.8.17)

$ps -ef | grep redis
jihite 30563 19813  0 12:11 pts/5    00:00:00 grep redis
```

**注**：如果设置上**密码**后，单纯的redis-cli是关不掉的，必须加上ip、port、passwd

```
sudo redis-cli -h host -p port -a passwd shutdown
```

退出客户端

```
localhost:6379> QUIT
```

**2.5 设立密码**

打开redis.conf找到requirepass，去掉默认，修改

```
requirepass yourpassword
```

验证密码的正确性

```
localhost:6379> auth jihite
OK
```

**2.6 远程连接**

需要已经安装redis，可以使用redis-cli命令

```
redis-cli -h host -p port -a password
```

**2.7 查看redis-server统计信息**

```
INFO
```

### **3. Reis key**

Redis是key-value的数据库，Redis的键用于管理Redis的键，基本语法是

```
COMMAND KEY_NAME
```

```
localhost:6379> SET var redis
OK
localhost:6379> GET var
"redis"
localhost:6379> DEL var
(integer) 1
localhost:6379> GET var
(nil)
```

**注**：redis命令不区分大小写，所以get var和GET var是等价的

| 序号 | Redis keys命令及描述 |
| :--- | :--- |
| 1 | DEL key 该命令用于在 key 存在是删除 key。 |
| 2 | DUMP key  序列化给定 key ，并返回被序列化的值。 |
| 3 | EXISTS key  检查给定 key 是否存在。 |
| 4 | EXPIRE key seconds 为给定 key 设置过期时间。 |
| 5 | EXPIREAT key timestamp  EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间。 不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳\(unix timestamp\)。 |
| 6 | PEXPIRE key milliseconds  设置 key 的过期时间亿以毫秒计。 |
| 7 | PEXPIREAT key milliseconds-timestamp  设置 key 过期时间的时间戳\(unix timestamp\) 以毫秒计 |
| 8 | KEYS pattern  查找所有符合给定模式\( pattern\)的 key 。例如keys \* 返回所有的key |
| 9 | MOVE key db  将当前数据库的 key 移动到给定的数据库 db 当中。 |
| 10 | PERSIST key  移除 key 的过期时间，key 将持久保持。 |
| 11 | PTTL key  以毫秒为单位返回 key 的剩余的过期时间。 |
| 12 | TTL key  以秒为单位，返回给定 key 的剩余生存时间\(TTL, time to live\)。 |
| 13 | RANDOMKEY  从当前数据库中随机返回一个 key 。 |
| 14 | RENAME key newkey  修改 key 的名称 |
| 15 | RENAMENX key newkey  仅当 newkey 不存在时，将 key 改名为 newkey 。 |
| 16 | TYPE key  返回 key 所储存的值的类型。 |

[回到顶部](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_labelTop)

### 4. Redis数据类型

Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset\(sorted set：有序集合\)。

**4.1 String（字符串）**

* 是Redis最基本的数据类型，可以理解成与Memcached一模一样的类型，一个key对应一个value
* 二进制安全的。意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象 
* 一个键最大能存储
  **512MB**

例子

```
127.0.0.1:6379> set var "String type"
OK
127.0.0.1:6379> get var
"String type"
```

说明：利用set给变量var赋值“String type”；利用get获得变量var的值

**4.2 Hash（哈希）**

* 是一个键值对集合
* 是一个string类型的field和value的映射表，hash特别适合用于存储对象

hset,hget例子

```
127.0.0.1:6379> hget set1 name
"jihite"
127.0.0.1:6379> hget set1 score
"100"
127.0.0.1:6379> hset set2 name jihite2
(integer) 1
127.0.0.1:6379> hset set2 score 110
(integer) 1
127.0.0.1:6379> hget set1 name 
"jihite"
```

hset&hget一次只能往哈希结构里面插入一个键值对，如果插入多个可以用hmset&hmget

hmset, hmget例子

```
127.0.0.1:6379> HMSET var:1  name jihite school pku
OK

127.0.0.1:6379> HGETALL var:1
1) "name"
2) "jihite"
3) "school"
4) "pku"
```

说明

var:1是键值，每个 hash 可以存储 232 - 1 键值对（40多亿）

HMSET用于建立hash对象，HGETALL用于获取hash对象

hset v.s. hmset操作对比

```
127.0.0.1:6379> hset set5 name1 jihite1 name2 jihite2 name3 jihite3
(error) ERR wrong number of arguments for 'hset' command
127.0.0.1:6379> hmset set5 name1 jihite1 name2 jihite2 name3 jihite3
OK
127.0.0.1:6379> hget set5 name1
"jihite1"
127.0.0.1:6379> hmget set5 name1
1) "jihite1"
127.0.0.1:6379> hmget set5 name1 name2
1) "jihite1"
2) "jihite2"
127.0.0.1:6379> hget set5 name1 name2
(error) ERR wrong number of arguments for 'hget' command
```

**4.3 LIST（列表）**

例子

```
127.0.0.1:6379> lpush lvar 1
(integer) 1
127.0.0.1:6379> lpush lvar a
(integer) 2
127.0.0.1:6379> lpush lvar ab
(integer) 3

127.0.0.1:6379> lrange lvar 0 1
1) "ab"
2) "a"
127.0.0.1:6379> lrange lvar 0 10
1) "ab"
2) "a"
3) "1"
127.0.0.1:6379> lrange lvar 2 2
1) "1"
```

说明

lpush往列表的前边插入；lrange后面的数字是范围（闭区间）

列表最多可存储 232 - 1 元素 \(4294967295, 每个列表可存储40多亿\)。

**4.4 Set\(集合\)**

Redis的Set是string类型的无序集合。

集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O\(1\)

例子

```
127.0.0.1:6379> sadd setvar redis
(integer) 1
127.0.0.1:6379> sadd setvar mongodb
(integer) 1
127.0.0.1:6379> sadd setvar mongodb
(integer) 0
127.0.0.1:6379> sadd setvar rabbitmq
(integer) 1
127.0.0.1:6379> smembers setvar
1) "rabbitmq"
2) "redis"
3) "mongodb"
```

说明

set往集合中插入元素，smembers列举出集合中的元素

成功插入返回1；错误插入返回0，例子中mongodb第二次插入时，因已经存在，故插入失败。

**4.5 zset\(sorted sete：有序集合\)**

zset和set一样也是String类型的集合，且不允许元素重复

zset和set不同的地方在于zset关联一个double类型的分数，redis通过分数对集合中的元素排序

zset的元素是唯一的，但是分数是可以重复的

例子

```
127.0.0.1:6379> zadd zvar 1 redis
(integer) 1
127.0.0.1:6379> zadd zvar 1 redis
(integer) 0
127.0.0.1:6379> zadd zvar 2 redis
(integer) 0
127.0.0.1:6379> 
127.0.0.1:6379> zadd zvar 2 mongo
(integer) 1
127.0.0.1:6379> zadd zvar 0 rabbitmq
(integer) 1
127.0.0.1:6379>  
127.0.0.1:6379> ZRANGEBYSCORE zvar 0 1000
1) "rabbitmq"
2) "mongo"
3) "redis"
127.0.0.1:6379> 
127.0.0.1:6379> 
127.0.0.1:6379> zadd zvar -2 celery
(integer) 1
127.0.0.1:6379> ZRANGEBYSCORE zvar 0 1000
1) "rabbitmq"
2) "mongo"
3) "redis"
127.0.0.1:6379> ZRANGEBYSCORE zvar -3 1000
1) "celery"
2) "rabbitmq"
3) "mongo"
4) "redis"
```

说明

成功插入返回1，否则返回0。插入已存在元素失败--返回0

分数为float（可正、负、0）

[回到顶部](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_labelTop)

### 5. Redis HyperLogLog

Redis HyperLogLog是用来做基数统计的算法。优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。

在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基 数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

**注**：因为HyperLogLog只会根据输入元素来计算基数，而不会存储输入元素本身，因此不会返回输入的各个元素。

基数是什么？ 对于\["abc", "abc", "2", "3"\]，基数是\["abc", "2", "3"\]，个数是3.

例子

```
localhost:6379> pfadd jsh redis
(integer) 1
localhost:6379> pfadd jsh redis
(integer) 0
localhost:6379> pfadd jsh mongodb
(integer) 1
localhost:6379> pfadd jsh rabbitmq
(integer) 1
localhost:6379> pfcount jsh
(integer) 3
localhost:6379> pfadd jsh2 redis
(integer) 1
localhost:6379> pfadd jsh2 a
(integer) 1
localhost:6379> pfcount jsh2
(integer) 2

localhost:6379> pfmerge jsh jsh2
OK
localhost:6379> pfcount jsh
(integer) 4
localhost:6379> pfcount jsh2
(integer) 2
```

说明：

* pfadd key ele \[ele2 ...\]：添加指定元素到HyperLogLog中，
* pfcount key: 返回给定HyperLogLog的基数估算值
* pfmerge destkey srckey \[srckey2....\]：讲多个HyperLogLog合并到一个第一个HyperLogLog中

[回到顶部](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_labelTop)

### 6. Redis 发布订阅

Redis 发布订阅\(pub/sub\)是一种消息通信模式：发送者\(pub\)发送消息，订阅者\(sub\)接收消息。

Redis 客户端可以订阅任意数量的频道。

原理：下图展示了三个客户端client1, client2, client5订阅了频道channel1

![](https://images2015.cnblogs.com/blog/408927/201603/408927-20160305185510471-267771771.png)

当有新消息通过PUBLISH发送给channel1时，这时候channel1就会把消息同时发布给订阅者

![](https://images2015.cnblogs.com/blog/408927/201603/408927-20160305185517752-1445845542.png)

例子

创建订阅频道redisChat

```
localhost:6379> subscribe redisChat
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "redisChat"
3) (integer) 1
```

打开几个客户端，订阅channel redisChat

```
localhost:6379> psubscribe redisChat
Reading messages... (press Ctrl-C to quit)
1) "psubscribe"
2) "redisChat"
3) (integer) 1
```

然后给channel redisChat发送消息“Hello World”

```
localhost:6379> publish redisChat "Hello World"
(integer) 1
```

客户端会收到消息

```
Reading messages... (press Ctrl-C to quit)
1) "pmessage"
2) "redisChat"
3) "redisChat"
4) "Hello World"
```

### 7. Redis事务

事务是一个单独的操作集合，事务中的命令有顺序，是一个原子操作（事务中的命令要么全部执行，要么全部不执行），执行一个事务中的命令时不会被其他命令打断。

一个事务从开始到结束经过以下三个阶段：

* 开始事务
* 命令入队
* 执行事务

例子

```
localhost:6379> MULTI
OK
localhost:6379> set name jihite
QUEUED
localhost:6379> get name
QUEUED
localhost:6379> sadd language "c++" "python" "java"
QUEUED
localhost:6379> smembers language
QUEUED
localhost:6379> exec
1) OK
2) "jihite"
3) (integer) 3
4) 1) "java"
   2) "python"
   3) "c++"
```

说明：事务以MULTI开始，以EXEC结束

### 8. Redis脚本

Redis 脚本使用 Lua 解释器来执行脚本。执行脚本的常用命令为 **EVAL**。基本语法

```
EVAL script numkeys key [key ...] arg [arg ...]
```

例子

```
localhost:6379> EVAL "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second
1) "key1"
2) "key2"
3) "first"
4) "second"
```

### **9. 数据备份与恢复**

数据备份

```
localhost:6379> save
OK
```

改命令会在redis的安装目录中创建文件dump.rdb，并把数据保存在该文件中。

查看redis的安装目录

```
localhost:6379> config get dir
1) "dir"
2) "/home/jihite/soft/redis-2.8.17"
```

数据恢复

只需将备份文件dump.rdb拷贝到redis的安装目录即可

### 10. 数据库操作

Redis中，一共有16个数据库，分别是0~15，一般情况下，进入数据库默认编号是0，如果我们要进入指定数据库，可以用select语句

切换到编号为3的数据库

```
localhost:6379> select 3
OK
localhost:6379[3]>
```

查看数据库中所有的键值

```
localhost:6379[1]> set a 1
OK
localhost:6379[1]> set  b 2
OK
localhost:6379[1]> keys *
1) "b"
2) "a"
```

返回当前数据库中所有key的数目:　　dbsize 

删除当前数据库中的所有key: 　　flushdb    

清空所有数据库中的所有key: 　　flushall

把当前数据库中的key转移到指定数据库：move a aim\_db，例：

```
localhost:6379[1]> set z sss
OK
localhost:6379[1]> move z 0
(integer) 1
localhost:6379[1]> select 0
OK
localhost:6379> get z
"sss"
```

### 11. 实际小应用

http://www.aboutyun.com/thread-12613-1-1.html 

[回到顶部](https://www.cnblogs.com/kaituorensheng/p/5244347.html#_labelTop)



### 12. 关闭持久化

数据持久化是Redis不同于其他缓存的一个特性，具有明显的有点。但如不希望持久化数据，只作为普通的缓存用，如memcache

方法：

修改配置文件，改完后重启。

```
#save 900 1  
#save 300 10  
#save 60 10000  
```

或执行操作命令

```
CONFIG SET save ""
```

执行命令后，无需重启即可生效

  








