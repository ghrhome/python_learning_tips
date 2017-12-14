# python操作MongoDB

下载python中MongoDB的驱动程序

```
pip install pymongo
```

然后确保MongoDB已经安装且可以正常运行，去官网下载相应版本：https://www.mongodb.com/

```
mkdir -p /home/tools
cd/home/tools
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.4.2.tgz
```

解压文件并修改目录名

```
tar -zxvf mongodb-linux-x86_64-3.4.2.tgz
mv mongodb-linux-x86_64-3.4.2 mongodb3.4.2

ln -s mongodb_3.4.2 mongodb
```

MongoDB 的可执行文件位于 bin 目录下，所以可以将其添加到 **PATH** 路径中

```
export PATH=/home/tools/mongodb/bin:$PATH
```

MongoDB的数据存储在data目录的db目录下，但是这个目录在安装过程不会自动创建，所以你需要手动创建data目录，并在data目录中创建db目录。

```
mkdir -p /data/db
```

在mongo安装目录中的bin目录执行mongod命令来启动mongdb服务

```
./mongod --dbpath /data/db
```

如果想进入MongoDB后台管理

```
./mongo
```

```
# ./mongo
MongoDB shell version v3.4.2
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.4.2
Welcome to the MongoDB shell.
.....................
times number of files.
2017-03-12T01:15:04.901+0800 I CONTROL  [initandlisten] 
>

```

使用用户 admin 使用密码 123456 连接到本地的 MongoDB 服务上

```
>
 mongodb://admin:123456@localhost/

... 
```

创建数据库（如果数据库不存在，则创建数据库，否则切换到指定数据库）

```
... use test

2017-03-12T01:34:04.361+0800 E QUERY    [thread1] SyntaxError: missing ; before statement @(shell):3:4
```

```
> use test
switched to db test
> db
test
> show dbs    #查看所有数据库
admin  0.000GB
local  0.000GB
```

python操作mongodb

连接mongodb

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-

from pymongo import MongoClient

conn = MongoClient('192.168.0.113', 27017)
db = conn.mydb  #连接mydb数据库，没有则自动创建
my_set = db.test_set　　#使用test_set集合，没有则自动创建
```

```
my_set.insert({"name":"zhangsan","age":18})
#或
my_set.save({"name":"zhangsan","age":18})
```

插入多条

```
#添加多条数据到集合中
users=[{"name":"zhangsan","age":18},{"name":"lisi","age":20}]  
my_set.insert(users) 
#或
my_set.save(users)
```

查询数据（查询不到则返回None）

```
#查询全部
for i in my_set.find():
    print(i)
#查询name=zhangsan的
for i in my_set.find({"name":"zhangsan"}):
    print(i)
print(my_set.find_one({"name":"zhangsan"}))
```

更新数据

```
my_set.update(
   <query>,    #查询条件
   <update>,    #update的对象和一些更新的操作符
   {
     upsert: <boolean>,    #如果不存在update的记录，是否插入
     multi: <boolean>,        #可选，mongodb 默认是false,只更新找到的第一条记录
     writeConcern: <document>    #可选，抛出异常的级别。
   }
)
```

把上面插入的数据内的age改为20

```
my_set.update({"name":"zhangsan"},{'$set':{"age":20}})
```

删除数据

```
my_set.remove(
   <query>,    #（可选）删除的文档的条件
   {
     justOne: <boolean>,    #（可选）如果设为 true 或 1，则只删除一个文档
     writeConcern: <document>    #（可选）抛出异常的级别
   }
)
```

```
#删除name=lisi的全部记录
my_set.remove({'name': 'zhangsan'})

#删除name=lisi的某个id的记录
id = my_set.find_one({"name":"zhangsan"})["_id"]
my_set.remove(id)

#删除集合里的所有记录
db.users.remove()　
```

mongodb的条件操作符

```
#    (>)  大于 - $gt
#    (<)  小于 - $lt
#    (>=)  大于等于 - $gte
#    (<= )  小于等于 - $lte
```

```
#例：查询集合中age大于25的所有记录
for i in my_set.find({"age":{"$gt":25}}):
    print(i)
```

type\(判断类型\)

```
#找出name的类型是String的
for i in my_set.find({'name':{'$type':2}}):
    print(i)
```

类型队对照列表

```
Double    1     
String    2     
Object    3     
Array    4     
Binary data    5     
Undefined    6    已废弃
Object id    7     
Boolean    8     
Date    9     
Null    10     
Regular Expression    11     
JavaScript    13     
Symbol    14     
JavaScript (with scope)    15     
32-bit integer    16     
Timestamp    17     
64-bit integer    18     
Min key    255    Query with -1.
Max key    127
```

排序

　　在MongoDB中使用sort\(\)方法对数据进行排序，sort\(\)方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序，-1为降序。

```
for i in my_set.find().sort([("age",1)]):
    print(i)
```

limit和skip

```
#limit()方法用来读取指定数量的数据
#skip()方法用来跳过指定数量的数据
#下面表示跳过两条数据后读取6条
for i in my_set.find().skip(2).limit(6):
    print(i)
```

IN

```
#找出age是20、30、35的数据
for i in my_set.find({"age":{"$in":(20,30,35)}}):
    print(i)
```

OR

```
#找出age是20或35的记录
for i in my_set.find({"$or":[{"age":20},{"age":35}]}):
    print(i)
```

all

```
'''
dic = {"name":"lisi","age":18,"li":[1,2,3]}
dic2 = {"name":"zhangsan","age":18,"li":[1,2,3,4,5,6]}

my_set.insert(dic)
my_set.insert(dic2)'''
for i in my_set.find({'li':{'$all':[1,2,3,4]}}):
    print(i)
#查看是否包含全部条件
#输出：{'_id': ObjectId('58c503b94fc9d44624f7b108'), 'name': 'zhangsan', 'age': 18, 'li': [1, 2, 3, 4, 5, 6]}
```

push/pushAll

```
my_set.update({'name':"lisi"}, {'$push':{'li':4}})
for i in my_set.find({'name':"lisi"}):
    print(i)
#输出：{'li': [1, 2, 3, 4], '_id': ObjectId('58c50d784fc9d44ad8f2e803'), 'age': 18, 'name': 'lisi'}

my_set.update({'name':"lisi"}, {'$pushAll':{'li':[4,5]}})
for i in my_set.find({'name':"lisi"}):
    print(i)
#输出：{'li': [1, 2, 3, 4, 4, 5], 'name': 'lisi', 'age': 18, '_id': ObjectId('58c50d784fc9d44ad8f2e803')}
```

pop/pull/pullAll

```
#pop
#移除最后一个元素(-1为移除第一个)
my_set.update({'name':"lisi"}, {'$pop':{'li':1}})
for i in my_set.find({'name':"lisi"}):
    print(i)
#输出：{'_id': ObjectId('58c50d784fc9d44ad8f2e803'), 'age': 18, 'name': 'lisi', 'li': [1, 2, 3, 4, 4]}

#pull （按值移除）
#移除3
my_set.update({'name':"lisi"}, {'$pop':{'li':3}})

#pullAll （移除全部符合条件的）
my_set.update({'name':"lisi"}, {'$pullAll':{'li':[1,2,3]}})
for i in my_set.find({'name':"lisi"}):
    print(i)
#输出：{'name': 'lisi', '_id': ObjectId('58c50d784fc9d44ad8f2e803'), 'li': [4, 4], 'age': 18}
```

多级路径元素操作

　　先插入一条数据

```
dic = {"name":"zhangsan",
       "age":18,
       "contact" : {
           "email" : "1234567@qq.com",
           "iphone" : "11223344"}
       }
my_set.insert(dic)
```

```
#多级目录用. 连接
for i in my_set.find({"contact.iphone":"11223344"}):
    print(i)
#输出：{'name': 'zhangsan', '_id': ObjectId('58c4f99c4fc9d42e0022c3b6'), 'age': 18, 'contact': {'email': '1234567@qq.com', 'iphone': '11223344'}}

result = my_set.find_one({"contact.iphone":"11223344"})
print(result["contact"]["email"])
#输出：1234567@qq.com

#多级路径下修改操作
result = my_set.update({"contact.iphone":"11223344"},{"$set":{"contact.email":"9999999@qq.com"}})
result1 = my_set.find_one({"contact.iphone":"11223344"})
print(result1["contact"]["email"])
#输出：9999999@qq.com
```

还可以对数组用索引操作

```
dic = {"name":"lisi",
       "age":18,
       "contact" : [
           {
           "email" : "111111@qq.com",
           "iphone" : "111"},
           {
           "email" : "222222@qq.com",
           "iphone" : "222"}
       ]}
my_set.insert(dic)
```

```
#查询
result1 = my_set.find_one({"contact.1.iphone":"222"})
print(result1)
#输出：{'age': 18, '_id': ObjectId('58c4ff574fc9d43844423db2'), 'name': 'lisi', 'contact': [{'iphone': '111', 'email': '111111@qq.com'}, {'iphone': '222', 'email': '222222@qq.com'}]}

#修改
result = my_set.update({"contact.1.iphone":"222"},{"$set":{"contact.1.email":"222222@qq.com"}})
print(result1["contact"][1]["email"])
#输出：222222@qq.com
```

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
from pymongo import MongoClient

settings = {
    "ip":'192.168.0.113',   #ip
    "port":27017,           #端口
    "db_name" : "mydb",    #数据库名字
    "set_name" : "test_set"   #集合名字
}

class MyMongoDB(object):
    def __init__(self):
        try:
            self.conn = MongoClient(settings["ip"], settings["port"])
        except Exception as e:
            print(e)
        self.db = self.conn[settings["db_name"]]
        self.my_set = self.db[settings["set_name"]]

    def insert(self,dic):
        print("inser...")
        self.my_set.insert(dic)

    def update(self,dic,newdic):
        print("update...")
        self.my_set.update(dic,newdic)

    def delete(self,dic):
        print("delete...")
        self.my_set.remove(dic)

    def dbfind(self,dic):
        print("find...")
        data = self.my_set.find(dic)
        for result in data:
            print(result["name"],result["age"])

def main():
    dic={"name":"zhangsan","age":18}
    mongo = MyMongoDB()
    mongo.insert(dic)
    mongo.dbfind({"name":"zhangsan"})

    mongo.update({"name":"zhangsan"},{"$set":{"age":"25"}})
    mongo.dbfind({"name":"zhangsan"})

    mongo.delete({"name":"zhangsan"})
    mongo.dbfind({"name":"zhangsan"})

if __name__ == "__main__":
    main()

demo
```









