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

* 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
* 丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
* 原子 – Redis的所有操作都是原子性的，同时Redis还支持对几个操作全并后的原子性执行。
* 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

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
| 2 | DUMP key  序列化给定 key ，并返回被序列化的值。 |
| 3 | EXISTS key  检查给定 key 是否存在。 |
| 4 | EXPIRE key seconds 为给定 key 设置过期时间。 |
| 5 | EXPIREAT key timestamp  EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间。 不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳\(unix timestamp\)。 |
| 6 | PEXPIRE key milliseconds  设置 key 的过期时间亿以毫秒计。 |
| 7 | PEXPIREAT key milliseconds-timestamp  设置 key 过期时间的时间戳\(unix timestamp\) 以毫秒计 |
| 8 | KEYS pattern  查找所有符合给定模式\( pattern\)的 key 。例如keys \* 返回所有的key |
| 9 | MOVE key db  将当前数据库的 key 移动到给定的数据库 db 当中。 |
| 10 | PERSIST key  移除 key 的过期时间，key 将持久保持。 |
| 11 | PTTL key  以毫秒为单位返回 key 的剩余的过期时间。 |
| 12 | TTL key  以秒为单位，返回给定 key 的剩余生存时间\(TTL, time to live\)。 |
| 13 | RANDOMKEY  从当前数据库中随机返回一个 key 。 |
| 14 | RENAME key newkey  修改 key 的名称 |
| 15 | RENAMENX key newkey  仅当 newkey 不存在时，将 key 改名为 newkey 。 |
| 16 | TYPE key  返回 key 所储存的值的类型。 |

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





















