# Nosql概述

## 为什么要用Nosql

由于现在处于互联网的大数据时代，从前的单机的程序已经无法满足日益增长的数据量。尤其是超大规模的高并发的社区！暴露出来很多难以克服的问题。传统的关系型数据库有些力不从心了。且很多数据类型用户的个人信息，社交网络，地理位置。这些数据类型不需要一个固定的格式！不需要多余的操作就可以横向扩展。类似Map<String,Object> 使用键值对来控制。

Nosql是非关系型数据库。

> Nosql特点

1. 方便扩展（数据之间没有关系，很好扩展）
2. 大数据高性能（Redis 一秒写8万次，读取11万次，Nosql的缓存记录级，是一种细粒度的缓存，性能会比较高。
3. 数据类型是多样型的！（不需要事先设计数据库！随取随用）
4. 传统的RDBMS 和 NoSql
   - 传统的 RDBMS
     - 结构化组织
     - SQL
     - 数据和关系都存在单独的表中 row col
     - 操作数据，数据定义语言
     - 严格的一致性
     - 基础的事务
     - ... ...
   - NoSql
     - 不仅仅是数据
     - 没有固定的查询语言
     - 键值对存储，列存储，文档存储，图形数据库（社交关系）
     - 最终一致性
     - CAP定理 和 BASE （异地多活）
     - 高性能、高可用、高可扩
     - ... ...

> 了解大数据的 3V 和 3高

大数据时代的 3V ：主要是描述问题的

1、海量Velume

2、多样Variety

3、实时Velocity

大数据时代的 3高：主要是对程序的要求

1、高并发   

2、高可扩

3、高性能

真正在公司中的实践：NoSql + RDBMS 一起使用才是最强的。

敏捷开发、极限编程

## NoSql的四大分类

**KV键值对**：

- 新浪： redis
- 美团：redis + Tair
- 阿里、百度：redis + memecache

**文档型数据库（bson格式 和json一样）**

- MongoDB （一般必须要掌握）
  - MongoDB 是基于分布式存储的的数据库，C++编写的，主要来存储大量的文档
  - MongoDB 是一个介于关系型数据与非关系型的数据库之间的中间产品！MongoDB 是非关系型数据库中功能最丰富，最像关系型数据库的！
- ConthDB

**列存储数据库**

- HBase
- 分布式文件系统

**图形数据库**

- Neo4j、InfoGrid

[数据库类型详解](https://blog.csdn.net/uiuan00/article/details/103005496)

## Redis入门

> Redis是什么？

Redis（Remote Dictionary Server )，即远程字典服务！

是一个开源的使用ANSI [C语言](https://baike.baidu.com/item/C语言)编写、支持网络、可基于内存亦可持久化的日志型、Key-Value[数据库](https://baike.baidu.com/item/数据库/103728)，并提供多种语言的API。

免费 和 开源！是当下最热门的NoSql技术之一！也被人们称为结构化数据库。

> Redis能干什么？

1、内存存储、持久化、内存中是断电即失，所以说持久化很重要（rdb、aof）

2、效率高，可以用于高速缓存

3、发布订阅系统

4、地图信息分析

5、计时器、计数器（浏览量！）

6 、... ...

> 特性

1、多样性的数据

2、持久化

3、集群

4、事务

... ...

> 学习中需要用到的东西

1、官网 https://redis.io/

2、中文网 http://www.redis.cn/

​		中文开发手册 https://www.php.cn/manual/view/36359.html

3、下载地址 ：通过官网即可

![image-20200702233710052](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200702233710052.png)



注意：windows在Github上下载（停更很久了！）

不建议在windows搭建Redis---- 推荐linux上学习

###  Windows 下安装

1、下载

![image-20200702235224375](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200702235224375.png)

2、启动

![image-20200702235358143](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200702235358143.png)

3、连接数据库

![image-20200702235443570](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200702235443570.png)



![image-20200702235726818](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200702235726818.png)

记住一句话，Windows下使用确实简单，但是Redis 推荐我们去linux去开发使用。

### Linux下安装

#### 1、去官网下载安装包 `redis-6.0.5.tar.gz`

#### 2、解压Redis安装包  在 /opt 

![image-20200703003019173](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703003019173.png)





#### 3、进入解压后的文件，可以看到redis的配置文件

![image-20200703003215345](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703003215345.png)

#### 4、基本的环境安装

```bash
yum install gcc-c++
```

```bash
make #安装下载环境
```

![image-20200703142840584](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703142840584.png)

安装过程中会出现错误，极可能是gcc版本太低了 yum自带的gcc 是4.8.5版本。。。

[解决方案](http://www.144d.com/post-673.html)

```bash
make install
```

![image-20200703144725382](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703144725382.png)

#### 5、 redis 默认的安装路径是`/usr/local/bin/`

![image-20200703145101554](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703145101554.png)

#### 6、拷贝原生的配置文件到，`/usr/local/bin/我们自己创建的目录下` 

![image-20200703145400980](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703145400980.png)

这样我们修改配置文件只需要修改我们目录下的即可。之后我们将使用redis.conf 配置文件进行启动。

#### 7、redis默认不是后台启动的，我们需要修改配置文件！

```bash
vim redis.conf
```

![image-20200703145806545](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703145806545.png)

#### 8、通过配置文件启动redis

![image-20200703150005758](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703150005758.png)

#### 9、使用redis客户端进行连接

![image-20200703150137906](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703150137906.png)

```bash
redis-cli -p 6379 # -p 表示端口号
```

![image-20200703150257049](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703150257049.png)

#### 10、查看redis 的进程是否开启

![image-20200703150621393](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703150621393.png)

#### 11、如何关闭redis服务 

```bash
shutdown # 关机
exit  #退出
```

![image-20200703150800202](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703150800202.png)

![image-20200703150838980](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703150838980.png)

### 测试性能

**redis-benchamark** 是一个压力测试工具！

官方自带的性能测试工具。

redis-benchamark 命令。

redis 性能测试工具可选参数如下所示：

| 序号 | 选项      | 描述                                       | 默认值    |
| :--- | :-------- | :----------------------------------------- | :-------- |
| 1    | **-h**    | 指定服务器主机名                           | 127.0.0.1 |
| 2    | **-p**    | 指定服务器端口                             | 6379      |
| 3    | **-s**    | 指定服务器 socket                          |           |
| 4    | **-c**    | 指定并发连接数                             | 50        |
| 5    | **-n**    | 指定请求数                                 | 10000     |
| 6    | **-d**    | 以字节的形式指定 SET/GET 值的数据大小      | 2         |
| 7    | **-k**    | 1=keep alive 0=reconnect                   | 1         |
| 8    | **-r**    | SET/GET/INCR 使用随机 key, SADD 使用随机值 |           |
| 9    | **-P**    | 通过管道传输 <numreq> 请求                 | 1         |
| 10   | **-q**    | 强制退出 redis。仅显示 query/sec 值        |           |
| 11   | **--csv** | 以 CSV 格式输出                            |           |
| 12   | **-l**    | 生成循环，永久执行测试                     |           |
| 13   | **-t**    | 仅运行以逗号分隔的测试命令列表。           |           |
| 14   | **-I**    | Idle 模式。仅打开 N 个 idle 连接并等待。   |           |

我们来简单测试一下：

```bash
# 测试：100个并发连接 1并发/100000请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000 ## 在redis客户端开启的情况下
```

![image-20200703161927593](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703161927593.png)

如何分析这些数据：

![image-20200703162912335](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703162912335.png)

### 基础的知识

redis默认有16个数据库 ：redis.conf 配置文件中写出

redis命令不分大小写

![image-20200703163625919](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703163625919.png)

默认使用第0个数据库

可以使用`select` 进行切换数据库

```bash
127.0.0.1:6379> select 3 ## 切换数据库
OK
127.0.0.1:6379[3]> dbsize ## 数据库大小
(integer) 0
127.0.0.1:6379[3]> 
```

![image-20200703164228328](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703164228328.png)

可以发现，在一个数据库中存值，在另外一个数据库中无法发现。

```bash
127.0.0.1:6379[3]> keys * # 查看当前数据库所有的key
1) "name"
127.0.0.1:6379[3]> 
```

清空当前数据库 `flushdb`

```bash
127.0.0.1:6379[3]> flushdb ## 清空当前数据库
OK
127.0.0.1:6379[3]> keys *
(empty array)

```

清空所有数据库 `flushall`

```bash
127.0.0.1:6379> flushall ## 清空所有数据库
OK
127.0.0.1:6379> keys *
(empty array)

```

> Redis 是单线程的！6版本一下。。

明白Redis是很快的，官方表示，Redis是基于内存操作，CPU不是Redis的性能瓶颈。Redis的性能瓶颈是根据机器的内存和网络带宽，既然可以使用单线程来实现，就使用单线程。

Redis是c语言写的，官方提供的数据是100000+ 的QPS，完全不比同样是使用key-value的Memecache差！

#### **Redis为什么单线程还那么快？**

1、误区1：高性能的服务器一定是多线程的？

2、误区2：多线程（CPU上下文会切换）一定比单线程效率高！

​		先对CPU>内存>硬盘的速度要有所了解

**核心**：redis是将所有的数据全部放在内存中的，所以说使用单线程去操作效率就是最高的，多线程（CPU上下文会切换：耗时的操作！！），对于内存系统来说，如果没有上下文切换效率就是最高的！多次读写都在一个cpu上的，在内存情况下，这个就是最佳的方案！

## 五大类型

> 官方文档

![image-20200703171917287](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703171917287.png)

翻译：Redis是一种开放源代码（BSD许可）的内存中数据结构存储，用作`数据库`，`缓存`和`消息中间件`。它支持数据结构，例如字符串，哈希，列表，集合，带范围查询的排序集合，位图，超级日志，带有半径查询和流的地理空间索引。 Redis具有内置的复制，Lua脚本，LRU驱逐，事务和不同级别的磁盘持久性，并通过Redis Sentinel和Redis Cluster自动分区提供高可用性。

### Redis-Key

`Redis-Key的基本命令`

```bash
127.0.0.1:6379> set name xiuyuandashen ## 设置一个key 为name ，value为xiuyuandashen
OK
127.0.0.1:6379> get name ## 获取个key值
"xiuyuandashen"
127.0.0.1:6379> KEYS * ## 查看所有的key
1) "name"
127.0.0.1:6379> EXISTS name ## 判断当前key是否存在
(integer) 1
127.0.0.1:6379> EXISTS nams2 
(integer) 0
127.0.0.1:6379> move name 1 ## 移动这个key 到 1数据库
(integer) 1
127.0.0.1:6379> EXISTS name
(integer) 0
127.0.0.1:6379> select 1 ## 切换到 1数据库
OK
127.0.0.1:6379[1]> EXISTS name  # 判断key是否存在 1 是存在 0不存在
(integer) 1
127.0.0.1:6379> EXPIRE name 10  ## 设置key的过期 时间为10秒  单位是秒
(integer) 1
127.0.0.1:6379> get name 
(nil) ## 不存在
127.0.0.1:6379> ttl name ## 查看key 的剩余的过期时间 当为-2时代表过期了 -1表示永不过期
(integer) -2
127.0.0.1:6379> TYPE age ## 查看当前key 的类型
string
```

> 后期如果又不会的命令可以查看官网的官方文档 http://www.redis.cn/documentation.html

![image-20200703175506178](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200703175506178.png)









### String（字符串）

**90%的java程序员使用redis只会一个String类型！**

```bash
##############################################################################################
127.0.0.1:6379> set key1 v1 ## 设置值
OK
127.0.0.1:6379> get key1 ## 获得值
"v1"
127.0.0.1:6379> keys *  ## 查看所有的key
1) "key1"
127.0.0.1:6379> EXISTS key1 ## 判断某个key是否存在
(integer) 1
127.0.0.1:6379> APPEND key1 "hello world" # 给当前key追加字符串内容，如果当前key不存在则相当于set key
(integer) 13
127.0.0.1:6379> get key1
"v1hello world"
127.0.0.1:6379> STRLEN key1  ## 获取当前key的字符串长度
(integer) 13
127.0.0.1:6379> 
##############################################################################################
127.0.0.1:6379> set views 0 
OK
127.0.0.1:6379> get views
"0"
127.0.0.1:6379> incr views ## 当前key的值自增 1
(integer) 1
127.0.0.1:6379> incr views 
(integer) 2
127.0.0.1:6379> get views
"2"
127.0.0.1:6379> decr views ## 当前key的值自减 1 
(integer) 1
127.0.0.1:6379> decr views
(integer) 0
127.0.0.1:6379> get views
"0"
127.0.0.1:6379> incrby views 10 ## 当前key的值增加  10 ，指定增量
(integer) 10
127.0.0.1:6379> get views
"10"
127.0.0.1:6379> decrby views 5 ## 当前key的值减 5 ，指定减量
(integer) 5
127.0.0.1:6379> get views
"5"
127.0.0.1:6379> 
##############################################################################################
# 字符串范围 range 截取
127.0.0.1:6379> set key1 "hello,xiuyuandashen"
OK
127.0.0.1:6379> get key1
"hello,xiuyuandashen"
127.0.0.1:6379> GETRANGE key1 0 3  # 截取字符串 [0,3]
"hell"
127.0.0.1:6379> GETRANGE key1 0 -1	# 获取全部字符串，和 get key 是一样的
"hello,xiuyuandashen"
127.0.0.1:6379> 

# 替换 ！
127.0.0.1:6379> set key2 abcdefg
OK
127.0.0.1:6379> get key2
"abcdefg"
127.0.0.1:6379> SETRANGE key2 1 xx # 替换指定位置开始的字符串 ！
(integer) 7
127.0.0.1:6379> get key2
"axxdefg"
##############################################################################################
# setex （set with expire） #设置key 并且设置过期时间
# setnx	（set if no exist）# 不存在当前key才设置成功，如果存在key则设置失败
127.0.0.1:6379> setex key2 30 "hello ,xiuyuan" # 设置一个key 30秒后过期 value 是 “hello，xiuyuan”
OK
127.0.0.1:6379> ttl key2 # 查看还有多少秒过期
(integer) 26
127.0.0.1:6379> ttl key2
(integer) 24
127.0.0.1:6379> ttl key2 
(integer) -2  # 过期了
127.0.0.1:6379> get key2
(nil)
127.0.0.1:6379> setnx mykey "hello,key" # 设置一个key，但key重复（存在）时，设置失败
(integer) 1
127.0.0.1:6379> get mykey
"hello,key"
127.0.0.1:6379> setnx mykey "hello" # 因为key已经存在，设置失败
(integer) 0 # 失败
127.0.0.1:6379> get mykey 
"hello,key"
##############################################################################################
# mset 批量设置key-value 
# mget 批量获取 value
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3 k4 v4 # 同时设置多个k v 对
OK
127.0.0.1:6379> keys *
1) "k3"
2) "k4"
3) "k2"
4) "k1"
127.0.0.1:6379> mget k1 k2 k3 k4 # 同时获取多个key值
1) "v1"
2) "v2"
3) "v3"
4) "v4"
127.0.0.1:6379> msetnx k1 v1 k5 v5 # msetnx 是一个原子性的操作，要么一起成功，要么一起失败！,如果其中一个key本身存在，即该操作全部失败
(integer) 0
127.0.0.1:6379> get k5
(nil)

# 对象
set user:1 {name:xiuyuan,age:18} #设置一个user:1对象 值为json字符保存一个对象

# 这里的 key是一个巧妙的设计： user:{id}:{filed} ,如此设计在Redis中是完全ok的！

127.0.0.1:6379> mset user:1:name xiuyuan user:1:age 18
OK
127.0.0.1:6379> mget user:1:name user:1:age 
1) "xiuyuan"
2) "18"
127.0.0.1:6379> set user:2 {name:xiuyuandashen,age:19}
OK
127.0.0.1:6379> get user:2
"{name:xiuyuandashen,age:19}"
##############################################################################################
# getset 先get再set
127.0.0.1:6379> getset db redis # 如果不存在key，则返回nil，并设置key的值
(nil)
127.0.0.1:6379> get db
"redis"
127.0.0.1:6379> getset db mongodb # 如果存在key，获取原来的值，并设置新的值
"redis"
127.0.0.1:6379> get db
"mongodb"

```

数据结构是相同的！

String类似的使用场景：value除了是我们的字符串还可以是我们的数字！

- 计数器
- 统计多单位的数量
- 粉丝数
- 对象缓存存储

#### 数据结构

> String的数据结构为简单动态字符串(Simple Dynamic String,缩写SDS)。是可以修改的字符串，内部结构实现上类似于Java的ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配

![image-20220410210209902](D:\笔记\redis\Redis.assets\image-20220410210209902.png)

如图中所示，内部为当前字符串实际分配的空间capacity一般要高于实际字符串长度len。**当字符串长度小于1M时，扩容都是加倍现有的空间，如果超过1M，扩容时一次只会多扩1M的空间。需要注意的是字符串最大长度为512M**。



### List（列表）

单键多值。

基本的数据类型，列表。

在redis中，我们可以把list玩成，栈、队列、阻塞队列！ 

所有的list命令都是以`l`开头的

```bash
127.0.0.1:6379> LPUSH list one ## 将一个值或多个值插入到列表的头部（左）
(integer) 1
127.0.0.1:6379> LPUSH list tow
(integer) 2
127.0.0.1:6379> lpush list three
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1 # 获取列表的所有元素
1) "three"
2) "tow"
3) "one"
127.0.0.1:6379> lrange list 0 1 # 获取列表的第1、2个元素（通过区间获取具体的值）
1) "three"
2) "tow"
127.0.0.1:6379> lrange list 0 0 
1) "three"
127.0.0.1:6379> RPUSH list xiuyuan ## 将一个值或多个值插入到列表的尾部(右)
(integer) 4
127.0.0.1:6379> lrange list 0 -1 # 获取列表的所有元素
1) "three"
2) "tow"
3) "one"
4) "xiuyuan"
##############################################################################################
# LPOP 移除列表的第一个元素
# RPOP 移除列表的最后一个元素
127.0.0.1:6379> LRange list 0 -1
1) "three"
2) "tow"
3) "one"
4) "xiuyuan"
127.0.0.1:6379> lpop list ## 移除列表的第一个元素（左
"three"
127.0.0.1:6379> LRange list 0 -1
1) "tow"
2) "one"
3) "xiuyuan"
127.0.0.1:6379> RPOP list ## 移除列表的最后一个元素（右
"xiuyuan"
127.0.0.1:6379> LRange list 0 -1
1) "tow"
2) "one"
127.0.0.1:6379> 
##############################################################################################
# Lindex 获取列表的下标对应的值
127.0.0.1:6379> LRange list 0 -1
1) "tow"
2) "one"
127.0.0.1:6379> lindex list 0 # 通过下标获取 list 中的某一个值
"tow"
127.0.0.1:6379> lindex list 1 # 通过下标获取 list 中的某一个值
"one"
##############################################################################################
# Llen 获取列表的长度
127.0.0.1:6379> LPUSH list one tow three 
(integer) 3
127.0.0.1:6379> llen list
(integer) 3
127.0.0.1:6379> 
##############################################################################################
# 移除指定的值
# Lrem 移除指定的值
127.0.0.1:6379> lRange list 0 -1
1) "three"
2) "three"
3) "tow"
4) "one"
127.0.0.1:6379> Lrem list 2 three # 移除列表中指定个数的value ，精确匹配
(integer) 2
127.0.0.1:6379> lRange list 0 -1
1) "tow"
2) "one"
127.0.0.1:6379> lrem list 1 one # 移除列表中指定个数的value ，精确匹配
(integer) 1
127.0.0.1:6379> lRange list 0 -1
1) "tow"
##############################################################################################
# ltrim 修剪 截取
127.0.0.1:6379> rpush mylist hello hello1 hello2 hell3
(integer) 4
127.0.0.1:6379> ltrim mylist 1 2 ## 通过下标截取指定的长度 （截取 下标 [1 , 2] 的元素）
OK
127.0.0.1:6379> lrange mylist 0 -1
1) "hello1"
2) "hello2"
127.0.0.1:6379> 
##############################################################################################
# rpoplpush 移除列表最后一个元素，将他移动到新的列表
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "hello1"
3) "hello2"
127.0.0.1:6379> rpoplpush mylist myotherlist # 移除列表最后一个元素，将他移动到新的列表
"hello2"
127.0.0.1:6379> lrange mylist 0 -1 # 查看原来的列表 
1) "hello"
2) "hello1"
127.0.0.1:6379> lrange myotherlist 0 -1 # 查看目标的列表，确实存在该值！
1) "hello2"
##############################################################################################
# lset 修改列表指定位置（下标）的值，如果该位置不存在值，则报错
127.0.0.1:6379> EXISTS list # 判断是否有 key
(integer) 0
127.0.0.1:6379> lset list 0 item1 # 设置下标0 的值为item1，由于list不存在，设置失败
(error) ERR no such key
127.0.0.1:6379> lpush list item1 # 
(integer) 1
127.0.0.1:6379> lrange list 0 0 
1) "item1"
127.0.0.1:6379> lset list 0 value1 # 如果存在，更新下标的值
OK
127.0.0.1:6379> lrange list 0 0 
1) "value1"
127.0.0.1:6379> lset list 3 item3 # 如果不存在，则会报错
(error) ERR index out of range
127.0.0.1:6379> 
##############################################################################################
# linsert 在列表指定具体值的前/后插入值

127.0.0.1:6379> Rpush mylist hello world
(integer) 2
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "world"
127.0.0.1:6379> linsert mylist before "world" "redis" # 在指定值(world)之前插入一个值(redis)
(integer) 3
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "redis"
3) "world"
127.0.0.1:6379> linsert mylist after "hello" "java" # 在指定值(hello)之后插入一个值(java)
(integer) 4
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "java"
3) "redis"
4) "world"
127.0.0.1:6379> 

```

> 小结

- list实际上就是一个链表，before after left right 都可以插入值
- 如果key不存在，创建新的链表
- 如果key存在，新增内容
- 如果移除了所有值，空链表，也代表不存在！**值在键在，值光键亡**
- 在两边插入或者改动值，效率最高！中间元素效率会低点。

消息排队！ 消息队列

队列（Lpush，Rpop），栈（Lpush，Lpop）

#### 数据结构

List的数据结构为快速链表quickList。

首先在列表元素较少的情况下会使用一块连续的内存存储，这个结构是ziplist，也即是压缩列表。它将所有的元素紧挨着一起存储，分配的是一块连续的内存。

当数据量比较多的时候才会改成quicklist。因为普通的链表需要的附加指针空间太大，会比较浪费空间。比如这个列表里存的只是int类型的数据，结构上还需要两个额外的指针prev和next。

![image-20220410220804221](D:\笔记\redis\Redis.assets\image-20220410220804221.png)

Redis将链表和ziplist结合起来组成了quicklist。也就是将多个ziplist使用双向指针串起来使用。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。

### Set（集合）

`set`中的值是不能重复的。`无序不重复集合`

set的命令都是`s`开头的

Redis的Set是string类型的无序集合。它底层其实是一个value为null的hash表，所以添加，删除，查找的**复杂度都是****O(1)**。

```bash
##############################################################################################
127.0.0.1:6379> sadd myset hello # 指定set集合中添加元素
(integer) 1
127.0.0.1:6379> sadd myset xiuyuandashen 
(integer) 1
127.0.0.1:6379> smembers myset # 查看指定set中所有的元素
1) "xiuyuandashen"
2) "hello"
127.0.0.1:6379> sismember myset hello # 判断某个元素是否在指定set中
(integer) 1
127.0.0.1:6379> sismember myset world
(integer) 0
##############################################################################################
127.0.0.1:6379> scard myset # 获取指定set的内容元素的个数（长度）
(integer) 2
127.0.0.1:6379> sadd myset xiuyuandashen2
(integer) 1
127.0.0.1:6379> scard myset # 获取指定set的内容元素的个数（长度）
(integer) 3
127.0.0.1:6379> 
##############################################################################################
# srem 移除指定set集合中一个或者多个指定元素
127.0.0.1:6379> srem myset xiuyuandashen xiuyuandashen2 # 移除指定set的指定元素
(integer) 2
127.0.0.1:6379> smembers myset
1) "hello"
127.0.0.1:6379> 
##############################################################################################
# set 是无序不重复集合
# SRANDMEMBER 随机获取指定集合中的指定个数的元素
127.0.0.1:6379> sadd myset 1 2 3 4 5 6 7 8
(integer) 8
127.0.0.1:6379> smembers myset
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
6) "6"
7) "7"
8) "8"
127.0.0.1:6379> SRANDMEMBER myset 1 # 随机获取一个元素
1) "8"
127.0.0.1:6379> SRANDMEMBER myset 2 # 随机获取2个元素
1) "8"
2) "5"
127.0.0.1:6379> SRANDMEMBER myset 5 # 随机获取5个元素
1) "7"
2) "3"
3) "8"
4) "4"
5) "1"
127.0.0.1:6379> SRANDMEMBER myset # 随机获取1个元素
"8"
127.0.0.1:6379> SRANDMEMBER myset # 随机获取1个元素
"1"
127.0.0.1:6379> 
##############################################################################################
# 随机删除指定set集合中的元素
# Spop  随机删除指定set集合中的指定个数的元素
127.0.0.1:6379> smembers myset
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
6) "6"
7) "7"
8) "8"
127.0.0.1:6379> Spop myset 1 # 随机删除set集合中的一个元素
1) "1"
127.0.0.1:6379> Spop myset # 随机删除set集合中的一个元素
"6"
127.0.0.1:6379> Spop myset 8 # 随机删除set集合中的8个元素，若元素数目不够，那就有多少移除多少
1) "2"
2) "3"
3) "4"
4) "5"
5) "7"
6) "8"
127.0.0.1:6379> 
##############################################################################################
# smove 将一个指定的值，移动到另外一个set集合中
127.0.0.1:6379> sadd myset1 hello world java python redis
(integer) 5
127.0.0.1:6379> sadd myset2 php spring springboot
(integer) 3
127.0.0.1:6379> smove myset1 myset2 java # 将myset1中的 java 移动到myset2中
(integer) 1
127.0.0.1:6379> smembers myset1
1) "python"
2) "world"
3) "hello"
4) "redis"
127.0.0.1:6379> smembers myset2
1) "java"
2) "springboot"
3) "php"
4) "spring"
127.0.0.1:6379> 
##############################################################################################
# 微博，b站 共同关注！（交集）
#数字集合类：
# - 差集 sdiff
# - 交集 sinter
# - 并集 sunion
127.0.0.1:6379> sadd set1 a b c d 
(integer) 4
127.0.0.1:6379> sadd set2 c e f
(integer) 3
127.0.0.1:6379> sdiff set1 set2 # set1 中与set2 的差集
1) "a"
2) "b"
3) "d"
127.0.0.1:6379> sinter set1 set2 # set1中set2 的交集 共同好友就可以这样实现
1) "c"
127.0.0.1:6379> SUNION set1 set2 # set1与set2 的并集 
1) "b"
2) "a"
3) "c"
4) "e"
5) "f"
6) "d"

```

微博，A用户将所有的关注的人放在一个set集合中！将他的粉丝也放到一个集合中。

共同关注，共同爱好，二度好友，推荐好友（六度分割理论）



### Hash（哈希）

Map 集合，key-Map集合。本质上和String类型没有太大区别，还是一个简单的key-value！

Hash的命令都是以`H`开头的

```bash
##############################################################################################
127.0.0.1:6379> hset myhash field1 xiuyuandashen # 在一个Hash中设置一个具体的key-value
(integer) 1
127.0.0.1:6379> hget myhash field1 # 在一个Hash中获取一个具体的key的值
"xiuyuandashen"
127.0.0.1:6379> hmset myhash field1 xiuyuan field2 hello field3 world # 设置多个具体的key-value
OK
127.0.0.1:6379> hmget myhash field1 field2 field3 # 在一个Hash中获取多个key的值
1) "xiuyuan"
2) "hello"
3) "world"
127.0.0.1:6379> hgetall myhash # 获取在一个Hash中的所有的key和value
1) "field1" #key
2) "xiuyuan"#value
3) "field2" #key
4) "hello"	#value
5) "field3"	#key
6) "world"	#value
127.0.0.1:6379> 
127.0.0.1:6379> hdel myhash field1 field3 #删除hash指定的key（一个或多个）
(integer) 2
127.0.0.1:6379> hgetall myhash
1) "field2"
2) "hello"
127.0.0.1:6379> 
##############################################################################################
# hlen 获取hash的key的个数（字段数量）
127.0.0.1:6379> hgetall myhash
1) "field2"
2) "hello"
3) "key1"
4) "a"
5) "key2"
6) "b"
7) "key3"
8) "99"
127.0.0.1:6379> hlen myhash #  获取hash的key的个数（字段数量）
(integer) 4
##############################################################################################
# hexists 判断hash中key是否存在
127.0.0.1:6379> hexists myhash key2 # 判断hash中key是否存在
(integer) 1
127.0.0.1:6379> hexists myhash key10 # 判断hash中key是否存在
(integer) 0
127.0.0.1:6379> 
##############################################################################################
# hkeys 只获取所有的key
# hvals 只获取所有的value
127.0.0.1:6379> hkeys myhash # 获取所有的key
1) "field2"
2) "key1"
3) "key2"
4) "key3"
127.0.0.1:6379> hvals myhash # 获取所有的value
1) "hello"
2) "a"
3) "b"
4) "99"
127.0.0.1:6379> 
##############################################################################################
# Hincrby 自增
# hsetnx 如果key不存在设置成功，存在设置失败
127.0.0.1:6379> Hincrby myhash key3 1 # key对应的value 自增 1
(integer) 100
127.0.0.1:6379> hsetnx myhash test1 hello  # 如果key不存在设置成功，存在设置失败
(integer) 1
127.0.0.1:6379> hsetnx myhash test1 world # 如果key不存在设置成功，存在设置失败
(integer) 0
127.0.0.1:6379> 
##############################################################################################
# 用来存储对象很方便
127.0.0.1:6379> hmset user:1 name xiuyuandashen age 18 
OK
127.0.0.1:6379> hkeys user:1
1) "name"
2) "age"
127.0.0.1:6379> hvals user:1
1) "xiuyuandashen"
2) "18"
127.0.0.1:6379> hset user:1 age 30
(integer) 0
127.0.0.1:6379> hvals user:1
1) "xiuyuandashen"
2) "30"
127.0.0.1:6379> 
```

**hash更适合于对象的存储，String更加适合字符串的存储**

### Zset（有序集合）

在set的基础上，增加了一个值 ，对比：set k1 v1 ， Zset k1 score v1

```bash
127.0.0.1:6379> zadd myset 1 one  # 添加一个值
(integer) 1
127.0.0.1:6379> zadd myset 2 tow 3 three # 添加多个值 
(integer) 2
127.0.0.1:6379> zrange myset 0 -1 # 显示所有元素
1) "one"
2) "tow"
3) "three"
127.0.0.1:6379> 
##############################################################################################
# 排序如何实现
# zrangebyscore 升序
# zrevrangebyscore  降序
# zrevrange 反转显示的所有元素
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf # 显示所有用户 从小到大 -inf表示负无穷 +inf表示正无穷
1) "xiaohong"
2) "xiaoming"
3) "zhangsan"
127.0.0.1:6379> ZRANGEBYSCORE salary 0 5000 withscores #显示所有用户，从小到大（升序） 并且附带成绩（比较因素）工资不大于 5000的
1) "xiaohong"
2) "2500"
3) "xiaoming"
4) "5000"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf withscores #显示所有用户，从小到大 并且附带成绩（比较因素）
1) "xiaohong"
2) "2500"
3) "xiaoming"
4) "5000"
5) "zhangsan"
6) "10000"
127.0.0.1:6379> 
127.0.0.1:6379> zrevrange salary 0 -1 # 反转显示的所有元素
1) "zhangsan"
2) "xiaoming"
127.0.0.1:6379> zrevrange salary 0 -1 withscores # 反转显示的所有元素 并且附带成绩（比较因素）
1) "zhangsan"
2) "10000"
3) "xiaoming"
4) "5000"
127.0.0.1:6379> zrevrangebyscore myset +inf -inf withscores  # 降序排列显示所有元素 并且附带成绩（比较因素）
1) "java"
2) "4"
3) "redis"
4) "3"
5) "world"
6) "2"
7) "hello"
8) "1"
127.0.0.1:6379> 

##############################################################################################
# 移除元素 Zrem
127.0.0.1:6379> zrange salary 0 -1
1) "xiaohong"
2) "xiaoming"
3) "zhangsan"
127.0.0.1:6379> zrem salary xiaohong # 移除有序集合中的指定元素
(integer) 1
127.0.0.1:6379> zrange salary 0 -1
1) "xiaoming"
2) "zhangsan"
127.0.0.1:6379> 
##############################################################################################
# zcard 获取有序集合元素个数
127.0.0.1:6379> zcard salary # 获取有序集合元素个数
(integer) 2
##############################################################################################
# zcount 获取指定区间的成员数量
127.0.0.1:6379> zadd myset 1 hello 2 world 3 redis 4 java
(integer) 4
127.0.0.1:6379> zcount myset 1 3 # 获取指定区间的成员数量 [1,3]
(integer) 3
127.0.0.1:6379> zcount myset 1 1 # 获取指定区间的成员数量 [1,1]
(integer) 1
127.0.0.1:6379> zcount myset 4 10 # 获取指定区间的成员数量 [4,10]
(integer) 1
127.0.0.1:6379> 


```

案例思路：set 排序，存储班级成绩表，工资表排序。

普通消息 1  重要消息  2 ，带权重进行判断

排行榜实现。。

### 三种特殊的数据类型

#### geospatial 地理位置

朋友的定位，附近的人，打车距离计算。

Redis的Geo

可以查询一些测试数据： http://www.hao828.com/chaxun/zhongguochengshijingweidu/

>  只有六个命令

![image-20200705143438843](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200705143438843.png)

> geoadd  添加地理位置  longitude   经度  latitude 纬度  member 名称
>
> GEOADD key longitude latitude member [longitude latitude member ...]

```bash
# geoadd 添加地理位置 
# 规则， 南极和北极无法添加，我们一般会下载城市数据，直接通过java程序导入
# 有效经度从-180到180度。
# 有效纬度从 -85.05112878 到 85.05112878 度。
127.0.0.1:6379> geoadd china:city 116.40 39.90 beijing
(integer) 1
127.0.0.1:6379> geoadd china:city 121.47 31.23 shanghai 106.50 29.53 chongqi 114.05 22.52 shengzheng
(integer) 3

```

> geopos 获取指定geo的经度和唯独
>
> GEOPOS key member [member ...]

```bash
127.0.0.1:6379> geopos china:city beijing # 获取一个城市的经纬度
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
127.0.0.1:6379> geopos china:city beijing shanghai  # 获取多个城市的经纬度
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
2) 1) "121.47000163793563843"
   2) "31.22999903975783553"
127.0.0.1:6379> 

```

> feodist   返回排序集合表示的地理空间索引中两个成员之间的距离。
>
> ```javascript
> GEODIST key member1 member2 [unit] 
> unit 表示单位 m 米 km 千米 mi 英里 ft 英尺
> ```

```bash
127.0.0.1:6379> geodist china:city beijing shanghai # 默认单位是 m 查看北京和上海的直线距离
"1067378.7564"
127.0.0.1:6379> 127.0.0.1:6379> geodist china:city beijing shanghai km # 设置单位为 km 查看北京和上海的直线距离
"1067.3788"
127.0.0.1:6379> 
```

> georadius   radius 半径   返回指定的经纬度的半径范围的所有元素
>
> ```javascript
> GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count] [ASC|DESC] [STORE key] [STOREDIST key]
> 该命令有选择地使用以下选项返回附加信息：
> 
> WITHDIST：还要返回指定中心返回物品的距离。距离以与指定为命令的半径参数的单位相同的单位返回。
> 
> WITHCOORD：还返回匹配项目的经度，纬度坐标。
> 
> WITHHASH：还以52位无符号整数的形式返回项目的原始 geohash 编码的有序集合分数。这只对低级别的黑客或调试很有用，对于普通用户来说这很有趣。
> 
> 该命令的默认设置是返回未排序的项目。使用以下两个选项可以调用两种不同的排序方法：
> 
> ASC：将返回的项目从最近的到最远的，相对于中心排序。
> 
> DESC：从最远到最近的相对于中心的返回项目排序。
> 
> 默认情况下会返回所有匹配的项目。通过使用 COUNT <count>选项，可以将结果限制为前 N 个匹配项。但是请注意，在内部，命令需要执行与匹配指定区域的项目数量成比例的努力，因此，COUNT即使只返回几个结果，使用非常小的选项查询非常大的区域也可能会很慢。另一方面，COUNT如果通常只使用第一个结果，则可以成为减少带宽使用的非常有效的方法。
> ```

```bash
127.0.0.1:6379> georadius china:city 110 30 1000 km
1) "chongqi"
2) "shengzheng"
127.0.0.1:6379> georadius china:city 110 30 1000 km withcoord withdist count 1
1) 1) "chongqi"
   2) "341.9374"
   3) 1) "106.49999767541885376"
      2) "29.52999957900659211"
127.0.0.1:6379> georadius china:city 110 30 1000 km withcoord withdist count 2
1) 1) "chongqi"
   2) "341.9374"
   3) 1) "106.49999767541885376"
      2) "29.52999957900659211"
2) 1) "shengzheng"
   2) "924.6408"
   3) 1) "114.04999762773513794"
      2) "22.5200000879503861"
127.0.0.1:6379> 

```

> georadiusbymember 于上边的georadius类似 ，唯一的区别在于，它不是以查询区域的中心为经度和纬度值，而是采用已存在于有序集合所代表的地理空间索引内的成员的名称。指定成员的位置用作查询的中心。
>
> ```javascript
> GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count] [ASC|DESC] [STORE key] [STOREDIST key]
> ```

```bash
127.0.0.1:6379> GEORADIUSBYMEMBER china:city beijing 500 km withcoord
1) 1) "beijing"
   2) 1) "116.39999896287918091"
      2) "39.90000009167092543"
127.0.0.1:6379> GEORADIUSBYMEMBER china:city beijing 1000 km  withdist
1) 1) "beijing"
   2) "0.0000"
127.0.0.1:6379> GEORADIUSBYMEMBER china:city chongqi 1000 km  withdist
1) 1) "chongqi"
   2) "0.0000"
127.0.0.1:6379> GEORADIUSBYMEMBER china:city chongqi 10000 km  withdist
1) 1) "chongqi"
   2) "0.0000"
2) 1) "shengzheng"
   2) "1084.4275"
3) 1) "shanghai"
   2) "1447.6737"
4) 1) "beijing"
   2) "1464.0708"
127.0.0.1:6379> 
```

> geohash  返回表示地理空间索引（使用 GEOADD 添加元素）的排序集值中一个或多个元素位置的有效 Geohash 字符串。
>
> ```javascript
> GEOHASH key member [member ...]
> ```

```bash
# 将二维的经纬度表示成一维的字符串，如果两个字符串越接近，距离越近
127.0.0.1:6379> geohash china:city beijing
1) "wx4fbxxfke0"
127.0.0.1:6379> geohash china:city beijing shanghai
1) "wx4fbxxfke0"
2) "wtw3sj5zbj0"
127.0.0.1:6379> 

```

> geo 底层的实现就用Zset！ 我们可用Zset 来操作它

```bash
127.0.0.1:6379> zrange china:city 0 -1 # 用Zset 显示 所有元素
1) "chongqi"
2) "shengzheng"
3) "shanghai"
4) "beijing"
127.0.0.1:6379> zrem china:city chongqi # 用Zset 删除 一个元素
(integer) 1
127.0.0.1:6379> zrange china:city 0 -1
1) "shengzheng"
2) "shanghai"
3) "beijing"
127.0.0.1:6379> 

```



#### hyperloglogs

> 什么是基数？ [https://baike.baidu.com/item/%E5%9F%BA%E6%95%B0/4260?fr=aladdin](https://baike.baidu.com/item/基数/4260?fr=aladdin)

> 简介

Redis 2.8.9 版本更新了 Hyperloglog 数据结构

Redis Hyperloglog 基数统计算法！

优点：占用的内存是固定，2^64不同的元素的基数，只需要12kb的内存！如果从内存的角度来比较的话，Hyperloglog 是首选！

**网页的UV（一个人访问一个网站多次，但只能算一次）**

传统的实现方式，set保存用户的id，然后就可以统计set中元素的数量作为标准判断！

这个方式如果保存大量的用户id，就会比较麻烦！我们的目的是为了计数，而不是保存用户id！

使用 hyperloglogs 有0.81%的错误率，但是用来统计UV，可以忽略不计！

> 实例
>
> pfadd 将所有元素参数添加到以指定为第一个参数的变量名称存储的HyperLogLog数据结构中
>
> pfcount 返回HyperLogLog数据结构中的不重复元素数量（基数） 
>
> pfmerge 将多个 HyperLogLog 值合并为一个唯一  第一个key作为合并结果集（不重复元素）

```bash
127.0.0.1:6379> pfadd mykey a b c d e f g h i j
(integer) 1
127.0.0.1:6379> pfcount mykey
(integer) 10
127.0.0.1:6379> pfadd mykey2 i j k l m n a b c
(integer) 1
127.0.0.1:6379> pfcount mykey2
(integer) 9
127.0.0.1:6379> pfmerge mykey3 mykey mykey2
OK
127.0.0.1:6379> pfcount mykey3
(integer) 14
127.0.0.1:6379> 

```

如果允许容错，使用 hyperloglog

如果不允许容错，不要使用 hyperloglog，使用set或者自己的数据类型

#### bitmaps

> 位存储

统计用户信息，活跃，不活跃！登录，未登录！打卡，365天打卡！两个状态的，都可以使用bitmaps存储！

Bitmaps 位图，数据结构！都是操作二进制来进行记录，就只有0和1 两个状态！

例如：使用bitmaps 来记录，周一到周日的打卡情况

```bash
# setbit 
# getbit
127.0.0.1:6379> setbit sign 0 1  # 记录周一打卡情况
(integer) 0
127.0.0.1:6379> setbit sign 1 0  # 记录周二打卡情况
(integer) 0
127.0.0.1:6379> setbit sign 2 0
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 4 1
(integer) 0
127.0.0.1:6379> setbit sign 5 0
(integer) 0
127.0.0.1:6379> setbit sign 6 0
(integer) 0
127.0.0.1:6379> getbit sign 3 # 获取周四打卡情况
(integer) 1 # 已打卡
127.0.0.1:6379> getbit sign 6 # 获取周日打卡情况
(integer) 0 # 未打卡

#  bitcount  统计情况  
127.0.0.1:6379> bitcount sign # 统计这周打卡情况
(integer) 3
127.0.0.1:6379> bitcount sign 0 4 # 统计周一到周五的打卡情况
(integer) 3
127.0.0.1:6379> bitcount sign 5 6 # 统计周末的打卡情况
(integer) 0
127.0.0.1:6379> 

```



## 事务

要么同时成功，要么同时失败，这个就是原子性！

Redis 事务本质：一组命令的集合！一个事务中的所有命令都会被序列化，在事务执行过程中，会按照顺序执行！

一次性、顺序性、排他性！执行一系列的命令！

```bash
----- 队列 set set set 执行--------
```

**Redis事务没有隔离级别的概念！**

所有的命令在事务中，并没有直接被执行！只有发起执行命令的时候才会执行！Exec

**Redis单条命令是保证原子性的，但是Redis事务不保证原子性！**

Redis的事务：

- 开启事务（multi）
- 命令入队（）
- 执行事务（exec）

锁：redis可以实现乐观锁！

> 正常执行事务！ 

```bash
127.0.0.1:6379> multi # 开启事务
OK
## 命令入队
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> exec # 执行事务
1) OK
2) OK
3) "v2"
4) OK

```

> 放弃事务

```bash
127.0.0.1:6379> multi # 开启事务
OK
## 命令入队
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> discard # 放弃事务
OK
127.0.0.1:6379> get k1 
(nil) # 获取不到

```

> 编译型异常（代码有问题！命令有误！），事务中所有的命令都不会被执行

```bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set   # 错误的命令
(error) ERR wrong number of arguments for 'set' command 
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> exec # 执行事务报错
(error) EXECABORT Transaction discarded because of previous errors. 
127.0.0.1:6379> get k3 # 所有的命令都不会被执行
(nil)
```

>运行时异常（例如 1/0），如果事务队列中存在语法性，那么执行命令的时候，其他命令可以正常执行，错误命令抛出异常！

```bash
127.0.0.1:6379> set k1 "xiuyuan" 
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> incr k1 # 不是数字 不能 +1 执行时会报错
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> exec
1) (error) ERR value is not an integer or out of range # 运行时异常
2) OK # 但是其他命令依旧可以执行 
3) OK # 但是其他命令依旧可以执行 
127.0.0.1:6379> get k2  # 但是其他命令依旧可以执行 
"v2"
127.0.0.1:6379> get k3 
"v3"
127.0.0.1:6379> 

```

> 监控！ Watch 实现乐观锁

悲观锁：

- 很悲观，认为什么时候都会出问题，无论什么时候都会加锁！

乐观锁：

- 很乐观，认为什么时候都不会出问题，所有不会上锁！更新数据的时候去判断一下，在此期间是否有其他人（线程）修改过这个数据，version！
- 获取version（数据版本）
- 更新的时候比较version

> Redis监测测试

正常执行成功！

```bash
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money # 监视 money对象
OK
127.0.0.1:6379> multi # 事务正常结束，数据期间没有发生变动，这个时候就正常执行成功！
OK
127.0.0.1:6379> decrby money 20
QUEUED
127.0.0.1:6379> incrby out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
127.0.0.1:6379> 

```

测试多线程修改值，使用watch 可以当作redis的乐观锁操作！

```bash
127.0.0.1:6379> watch money # 监视 money
OK 
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 10
QUEUED
127.0.0.1:6379> incrby out 10
QUEUED
127.0.0.1:6379> exec # 执行之前，另外一个线程修改了我们的值（set money 1000），这个时候就会导致事务执行失败！
(nil)
127.0.0.1:6379> unwatch # 如果发现事务执行失败，就先解锁
OK
127.0.0.1:6379> watch money # 获取最新的值（version），再次监视
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 10
QUEUED
127.0.0.1:6379> incrby out 10
QUEUED
127.0.0.1:6379> exec # 比对最新的值（version）是否发生变化，如果未发生变化，则执行成功，否则执行失败！
1) (integer) 990
2) (integer) 30
127.0.0.1:6379> 
```







## Jedis

我们要使用java 来操作Redis

>  什么是Jedis？是Redis官方推荐的java连接开发工具！使用java 操作Redis 中间件！如果你要使用java操作redis，那么一定要对jedis 十分熟悉！

> 测试

1、导入依赖

```xml
<dependencies>
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.2.0</version>
        </dependency>
<!--           fastjson-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.70</version>
        </dependency>
    </dependencies>
```

2、编码测试

- 连接数据库
- 操作命令
- 断开连接

```java
package com;

import redis.clients.jedis.Jedis;

public class TestPing {

    public static void main(String[] args) {
        //1、创建 Jedis 对象
        final Jedis jedis = new Jedis("127.0.0.1", 6379);
        // Jedis 所有命令都就是我们之前学过的所有指令
        System.out.println(jedis.ping());
    }
}

```

### 常用的Api

就在前面五大类型！

**redis中的指令，在java中的jedis对象直接调用同名的方法即可！**

### 事务

```java
package com;

import com.alibaba.fastjson.JSONObject;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.Transaction;

public class TestTx {
    public static void main(String[] args) {
        final Jedis jedis = new Jedis("127.0.0.1", 6379);
        final JSONObject jsonObject = new JSONObject();
        jedis.flushDB(); //清空数据
        jsonObject.put("hello","world");
        jsonObject.put("name","xiuyuandashen");
        final String result = jsonObject.toJSONString();
        final Transaction multi = jedis.multi();//开启事务
        // jedis.watch(result); //监控对象
        try{
            multi.set("user1",result);
            multi.set("user2",result);
            int i = 1/0;
            multi.exec(); //执行事务
        }catch (Exception e){
            multi.discard(); //放弃事务
            e.printStackTrace();
        }finally {
            System.out.println(jedis.get("user1"));
            System.out.println(jedis.get("user2"));
            jedis.close();//关闭连接
        }
    }
}
```



## SpringBoot 整合

SpringBoot 操作数据：springData jpa jdbc mongodb redis 

SpringData 也是和SpringBoot 齐名的项目！

说明：在SpringBoot2.x之后，原来使用的是`Jedis`被替换为了`lettuce`！

![image-20200706130847368](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200706130847368.png)

![image-20200706130902515](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200706130902515.png)

`jedis：采用的直连，多个线程操作的话是不安全的，如果想要避免不安全，使用jedis pool连接池 ！更像 BIO模式。`

`lettuce：采用netty，实例可以在多个线程中进行共享，不存在线程不安全的情况！可以减少线程数据了，更像 NIO模式。`

源码分析：

```java

@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(RedisOperations.class)
@EnableConfigurationProperties(RedisProperties.class)
@Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
public class RedisAutoConfiguration {

	@Bean
	@ConditionalOnMissingBean(name = "redisTemplate") //我们可以自己写javaConfig  redisTemplate 方法替换这个默认的方法
	public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
			throws UnknownHostException {
        //默认的RedisRemplate 没有过多的设置，redis对象 都需要进行序列化！
        // 两个泛型 <Object, Object> ，我们后面应该需要强制转换 <String, Object>
		RedisTemplate<Object, Object> template = new RedisTemplate<>();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}

	@Bean
	@ConditionalOnMissingBean //由于String类型是我们最常使用的一个类型，所有单独提出来一个bean
	public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory)
			throws UnknownHostException {
		StringRedisTemplate template = new StringRedisTemplate();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}

}

```

> 整合测试

1、导入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

2、配置连接

```properties
## 配置redis
spring.redis.host=127.0.0.1
spring.redis.port=6379
# spring.redis.lettuce. 配置连接池的相关内容 用lettuce 如果用redis的可能默认不生效
```

3、测试

```java
package com.springboot_redis;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.connection.RedisConnection;
import org.springframework.data.redis.core.RedisTemplate;

@SpringBootTest
class SpringbootRedisApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    void contextLoads() {
        // redisTemplate 操作不同的类型，api与redis指令是一致的
        // opsForValue() 操作字符串 String 类型
        // opsForList() 操作list列表 类型 总之 与redis中一致
        // opsForSet() ...
        // opsForZSet() ...
        // opsForHash() ...
        // opsForGeo() ...
        // opsForHyperLogLog() ...
        redisTemplate.opsForValue().set("name","xiuyuandashen");
        redisTemplate.opsForList();
        redisTemplate.opsForSet();
        redisTemplate.opsForZSet();
        redisTemplate.opsForHash();
        redisTemplate.opsForGeo();
        redisTemplate.opsForHyperLogLog();

        //除了基本的操作，我们常用的方法都可以通过redisTemplate操作，比如事务，和基本的crud

        //获取redis连接对象
        //final RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
        //connection.flushDb();
        //connection.flushAll();
        //connection.zAdd("myZset".getBytes(),10,"hello".getBytes());

        System.out.println(redisTemplate.opsForValue().get("name"));

    }

}

```

![image-20200706133552259](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200706133552259.png)

![image-20200706133807398](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200706133807398.png)

我们可能需要自己写一个javaConfig 替换掉原来的redis 的默认配置

![image-20200706143112671](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200706143112671.png)

默认有以上多种序列化方式



我们来编写一个自己的RedisTemplate

```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
            throws UnknownHostException {
        //我们为了自己开发方便，一般直接使用 <String,Object>
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);

        //Json序列化配置
        final Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        final ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);
        // String 的序列化
        final StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        //key 采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        //hash 的key也采用String的序列化的方式
        template.setHashKeySerializer(stringRedisSerializer);
        //value序列化方式采用jackson
        template.setValueSerializer(jackson2JsonRedisSerializer);
        // hash的value序列化方式采用 jackson
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();

        return template;
    }
}
```

在上边测试的代码中会发现使用redis命令会比较麻烦没有那么方便，下面是封装的RedisUtils工具类

###  RedisUtil

```java
package com.utils;

import org.springframework.data.redis.connection.DataType;
import org.springframework.data.redis.core.Cursor;
import org.springframework.data.redis.core.ScanOptions;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.data.redis.core.ZSetOperations.TypedTuple;

import java.util.Collection;
import java.util.Date;
import java.util.List;
import java.util.Map;
import java.util.Map.Entry;
import java.util.Set;
import java.util.concurrent.TimeUnit;

/**
 * Redis工具类*/
public class RedisUtil {
    private StringRedisTemplate redisTemplate;

    public void setRedisTemplate(StringRedisTemplate redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    public StringRedisTemplate getRedisTemplate() {
        return this.redisTemplate;
    }

    /** -------------------key相关操作--------------------- */

    /**
     * 删除key
     * 
     * @param key
     */
    public void delete(String key) {
        redisTemplate.delete(key);
    }

    /**
     * 批量删除key
     * 
     * @param keys
     */
    public void delete(Collection<String> keys) {
        redisTemplate.delete(keys);
    }

    /**
     * 序列化key
     * 
     * @param key
     * @return
     */
    public byte[] dump(String key) {
        return redisTemplate.dump(key);
    }

    /**
     * 是否存在key
     * 
     * @param key
     * @return
     */
    public Boolean hasKey(String key) {
        return redisTemplate.hasKey(key);
    }

    /**
     * 设置过期时间
     * 
     * @param key
     * @param timeout
     * @param unit
     * @return
     */
    public Boolean expire(String key, long timeout, TimeUnit unit) {
        return redisTemplate.expire(key, timeout, unit);
    }

    /**
     * 设置过期时间
     * 
     * @param key
     * @param date
     * @return
     */
    public Boolean expireAt(String key, Date date) {
        return redisTemplate.expireAt(key, date);
    }

    /**
     * 查找匹配的key
     * 
     * @param pattern
     * @return
     */
    public Set<String> keys(String pattern) {
        return redisTemplate.keys(pattern);
    }

    /**
     * 将当前数据库的 key 移动到给定的数据库 db 当中
     * 
     * @param key
     * @param dbIndex
     * @return
     */
    public Boolean move(String key, int dbIndex) {
        return redisTemplate.move(key, dbIndex);
    }

    /**
     * 移除 key 的过期时间，key 将持久保持
     * 
     * @param key
     * @return
     */
    public Boolean persist(String key) {
        return redisTemplate.persist(key);
    }

    /**
     * 返回 key 的剩余的过期时间
     * 
     * @param key
     * @param unit
     * @return
     */
    public Long getExpire(String key, TimeUnit unit) {
        return redisTemplate.getExpire(key, unit);
    }

    /**
     * 返回 key 的剩余的过期时间
     * 
     * @param key
     * @return
     */
    public Long getExpire(String key) {
        return redisTemplate.getExpire(key);
    }

    /**
     * 从当前数据库中随机返回一个 key
     * 
     * @return
     */
    public String randomKey() {
        return redisTemplate.randomKey();
    }

    /**
     * 修改 key 的名称
     * 
     * @param oldKey
     * @param newKey
     */
    public void rename(String oldKey, String newKey) {
        redisTemplate.rename(oldKey, newKey);
    }

    /**
     * 仅当 newkey 不存在时，将 oldKey 改名为 newkey
     * 
     * @param oldKey
     * @param newKey
     * @return
     */
    public Boolean renameIfAbsent(String oldKey, String newKey) {
        return redisTemplate.renameIfAbsent(oldKey, newKey);
    }

    /**
     * 返回 key 所储存的值的类型
     * 
     * @param key
     * @return
     */
    public DataType type(String key) {
        return redisTemplate.type(key);
    }

    /** -------------------string相关操作--------------------- */

    /**
     * 设置指定 key 的值
     * @param key
     * @param value
     */
    public void set(String key, String value) {
        redisTemplate.opsForValue().set(key, value);
    }

    /**
     * 获取指定 key 的值
     * @param key
     * @return
     */
    public String get(String key) {
        return redisTemplate.opsForValue().get(key);
    }

    /**
     * 返回 key 中字符串值的子字符
     * @param key
     * @param start
     * @param end
     * @return
     */
    public String getRange(String key, long start, long end) {
        return redisTemplate.opsForValue().get(key, start, end);
    }

    /**
     * 将给定 key 的值设为 value ，并返回 key 的旧值(old value)
     * 
     * @param key
     * @param value
     * @return
     */
    public String getAndSet(String key, String value) {
        return redisTemplate.opsForValue().getAndSet(key, value);
    }

    /**
     * 对 key 所储存的字符串值，获取指定偏移量上的位(bit)
     * 
     * @param key
     * @param offset
     * @return
     */
    public Boolean getBit(String key, long offset) {
        return redisTemplate.opsForValue().getBit(key, offset);
    }

    /**
     * 批量获取
     * 
     * @param keys
     * @return
     */
    public List<String> multiGet(Collection<String> keys) {
        return redisTemplate.opsForValue().multiGet(keys);
    }

    /**
     * 设置ASCII码, 字符串'a'的ASCII码是97, 转为二进制是'01100001', 此方法是将二进制第offset位值变为value
     * 
     * @param key 位置
     * @param value
     *            值,true为1, false为0
     * @return
     */
    public boolean setBit(String key, long offset, boolean value) {
        return redisTemplate.opsForValue().setBit(key, offset, value);
    }

    /**
     * 将值 value 关联到 key ，并将 key 的过期时间设为 timeout
     * 
     * @param key
     * @param value
     * @param timeout
     *            过期时间
     * @param unit
     *            时间单位, 天:TimeUnit.DAYS 小时:TimeUnit.HOURS 分钟:TimeUnit.MINUTES
     *            秒:TimeUnit.SECONDS 毫秒:TimeUnit.MILLISECONDS
     */
    public void setEx(String key, String value, long timeout, TimeUnit unit) {
        redisTemplate.opsForValue().set(key, value, timeout, unit);
    }

    /**
     * 只有在 key 不存在时设置 key 的值
     * 
     * @param key
     * @param value
     * @return 之前已经存在返回false,不存在返回true
     */
    public boolean setIfAbsent(String key, String value) {
        return redisTemplate.opsForValue().setIfAbsent(key, value);
    }

    /**
     * 用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始
     * 
     * @param key
     * @param value
     * @param offset
     *            从指定位置开始覆写
     */
    public void setRange(String key, String value, long offset) {
        redisTemplate.opsForValue().set(key, value, offset);
    }

    /**
     * 获取字符串的长度
     * 
     * @param key
     * @return
     */
    public Long size(String key) {
        return redisTemplate.opsForValue().size(key);
    }

    /**
     * 批量添加
     * 
     * @param maps
     */
    public void multiSet(Map<String, String> maps) {
        redisTemplate.opsForValue().multiSet(maps);
    }

    /**
     * 同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在
     * 
     * @param maps
     * @return 之前已经存在返回false,不存在返回true
     */
    public boolean multiSetIfAbsent(Map<String, String> maps) {
        return redisTemplate.opsForValue().multiSetIfAbsent(maps);
    }

    /**
     * 增加(自增长), 负数则为自减
     * 
     * @param key
     * @return
     */
    public Long incrBy(String key, long increment) {
        return redisTemplate.opsForValue().increment(key, increment);
    }

    /**
     * 
     * @param key
     * @return
     */
    public Double incrByFloat(String key, double increment) {
        return redisTemplate.opsForValue().increment(key, increment);
    }

    /**
     * 追加到末尾
     * 
     * @param key
     * @param value
     * @return
     */
    public Integer append(String key, String value) {
        return redisTemplate.opsForValue().append(key, value);
    }

    /** -------------------hash相关操作------------------------- */

    /**
     * 获取存储在哈希表中指定字段的值
     * 
     * @param key
     * @param field
     * @return
     */
    public Object hGet(String key, String field) {
        return redisTemplate.opsForHash().get(key, field);
    }

    /**
     * 获取所有给定字段的值
     * 
     * @param key
     * @return
     */
    public Map<Object, Object> hGetAll(String key) {
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * 获取所有给定字段的值
     * 
     * @param key
     * @param fields
     * @return
     */
    public List<Object> hMultiGet(String key, Collection<Object> fields) {
        return redisTemplate.opsForHash().multiGet(key, fields);
    }

    public void hPut(String key, String hashKey, String value) {
        redisTemplate.opsForHash().put(key, hashKey, value);
    }

    public void hPutAll(String key, Map<String, String> maps) {
        redisTemplate.opsForHash().putAll(key, maps);
    }

    /**
     * 仅当hashKey不存在时才设置
     * 
     * @param key
     * @param hashKey
     * @param value
     * @return
     */
    public Boolean hPutIfAbsent(String key, String hashKey, String value) {
        return redisTemplate.opsForHash().putIfAbsent(key, hashKey, value);
    }

    /**
     * 删除一个或多个哈希表字段
     * 
     * @param key
     * @param fields
     * @return
     */
    public Long hDelete(String key, Object... fields) {
        return redisTemplate.opsForHash().delete(key, fields);
    }

    /**
     * 查看哈希表 key 中，指定的字段是否存在
     * 
     * @param key
     * @param field
     * @return
     */
    public boolean hExists(String key, String field) {
        return redisTemplate.opsForHash().hasKey(key, field);
    }

    /**
     * 为哈希表 key 中的指定字段的整数值加上增量 increment
     * 
     * @param key
     * @param field
     * @param increment
     * @return
     */
    public Long hIncrBy(String key, Object field, long increment) {
        return redisTemplate.opsForHash().increment(key, field, increment);
    }

    /**
     * 为哈希表 key 中的指定字段的整数值加上增量 increment
     * 
     * @param key
     * @param field
     * @param delta
     * @return
     */
    public Double hIncrByFloat(String key, Object field, double delta) {
        return redisTemplate.opsForHash().increment(key, field, delta);
    }

    /**
     * 获取所有哈希表中的字段
     * 
     * @param key
     * @return
     */
    public Set<Object> hKeys(String key) {
        return redisTemplate.opsForHash().keys(key);
    }

    /**
     * 获取哈希表中字段的数量
     * 
     * @param key
     * @return
     */
    public Long hSize(String key) {
        return redisTemplate.opsForHash().size(key);
    }

    /**
     * 获取哈希表中所有值
     * 
     * @param key
     * @return
     */
    public List<Object> hValues(String key) {
        return redisTemplate.opsForHash().values(key);
    }

    /**
     * 迭代哈希表中的键值对
     * 
     * @param key
     * @param options
     * @return
     */
    public Cursor<Entry<Object, Object>> hScan(String key, ScanOptions options) {
        return redisTemplate.opsForHash().scan(key, options);
    }

    /** ------------------------list相关操作---------------------------- */

    /**
     * 通过索引获取列表中的元素
     * 
     * @param key
     * @param index
     * @return
     */
    public String lIndex(String key, long index) {
        return redisTemplate.opsForList().index(key, index);
    }

    /**
     * 获取列表指定范围内的元素
     * 
     * @param key
     * @param start
     *            开始位置, 0是开始位置
     * @param end
     *            结束位置, -1返回所有
     * @return
     */
    public List<String> lRange(String key, long start, long end) {
        return redisTemplate.opsForList().range(key, start, end);
    }

    /**
     * 存储在list头部
     * 
     * @param key
     * @param value
     * @return
     */
    public Long lLeftPush(String key, String value) {
        return redisTemplate.opsForList().leftPush(key, value);
    }

    /**
     * 
     * @param key
     * @param value
     * @return
     */
    public Long lLeftPushAll(String key, String... value) {
        return redisTemplate.opsForList().leftPushAll(key, value);
    }

    /**
     * 
     * @param key
     * @param value
     * @return
     */
    public Long lLeftPushAll(String key, Collection<String> value) {
        return redisTemplate.opsForList().leftPushAll(key, value);
    }

    /**
     * 当list存在的时候才加入
     * 
     * @param key
     * @param value
     * @return
     */
    public Long lLeftPushIfPresent(String key, String value) {
        return redisTemplate.opsForList().leftPushIfPresent(key, value);
    }

    /**
     * 如果pivot存在,再pivot前面添加
     * 
     * @param key
     * @param pivot
     * @param value
     * @return
     */
    public Long lLeftPush(String key, String pivot, String value) {
        return redisTemplate.opsForList().leftPush(key, pivot, value);
    }

    /**
     * 
     * @param key
     * @param value
     * @return
     */
    public Long lRightPush(String key, String value) {
        return redisTemplate.opsForList().rightPush(key, value);
    }

    /**
     * 
     * @param key
     * @param value
     * @return
     */
    public Long lRightPushAll(String key, String... value) {
        return redisTemplate.opsForList().rightPushAll(key, value);
    }

    /**
     * 
     * @param key
     * @param value
     * @return
     */
    public Long lRightPushAll(String key, Collection<String> value) {
        return redisTemplate.opsForList().rightPushAll(key, value);
    }

    /**
     * 为已存在的列表添加值
     * 
     * @param key
     * @param value
     * @return
     */
    public Long lRightPushIfPresent(String key, String value) {
        return redisTemplate.opsForList().rightPushIfPresent(key, value);
    }

    /**
     * 在pivot元素的右边添加值
     * 
     * @param key
     * @param pivot
     * @param value
     * @return
     */
    public Long lRightPush(String key, String pivot, String value) {
        return redisTemplate.opsForList().rightPush(key, pivot, value);
    }

    /**
     * 通过索引设置列表元素的值
     * 
     * @param key
     * @param index
     *            位置
     * @param value
     */
    public void lSet(String key, long index, String value) {
        redisTemplate.opsForList().set(key, index, value);
    }

    /**
     * 移出并获取列表的第一个元素
     * 
     * @param key
     * @return 删除的元素
     */
    public String lLeftPop(String key) {
        return redisTemplate.opsForList().leftPop(key);
    }

    /**
     * 移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
     * 
     * @param key
     * @param timeout
     *            等待时间
     * @param unit
     *            时间单位
     * @return
     */
    public String lBLeftPop(String key, long timeout, TimeUnit unit) {
        return redisTemplate.opsForList().leftPop(key, timeout, unit);
    }

    /**
     * 移除并获取列表最后一个元素
     * 
     * @param key
     * @return 删除的元素
     */
    public String lRightPop(String key) {
        return redisTemplate.opsForList().rightPop(key);
    }

    /**
     * 移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
     * 
     * @param key
     * @param timeout
     *            等待时间
     * @param unit
     *            时间单位
     * @return
     */
    public String lBRightPop(String key, long timeout, TimeUnit unit) {
        return redisTemplate.opsForList().rightPop(key, timeout, unit);
    }

    /**
     * 移除列表的最后一个元素，并将该元素添加到另一个列表并返回
     * 
     * @param sourceKey
     * @param destinationKey
     * @return
     */
    public String lRightPopAndLeftPush(String sourceKey, String destinationKey) {
        return redisTemplate.opsForList().rightPopAndLeftPush(sourceKey,
                destinationKey);
    }

    /**
     * 从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
     * 
     * @param sourceKey
     * @param destinationKey
     * @param timeout
     * @param unit
     * @return
     */
    public String lBRightPopAndLeftPush(String sourceKey, String destinationKey,
            long timeout, TimeUnit unit) {
        return redisTemplate.opsForList().rightPopAndLeftPush(sourceKey,
                destinationKey, timeout, unit);
    }

    /**
     * 删除集合中值等于value得元素
     * 
     * @param key
     * @param index
     *            index=0, 删除所有值等于value的元素; index>0, 从头部开始删除第一个值等于value的元素;
     *            index<0, 从尾部开始删除第一个值等于value的元素;
     * @param value
     * @return
     */
    public Long lRemove(String key, long index, String value) {
        return redisTemplate.opsForList().remove(key, index, value);
    }

    /**
     * 裁剪list
     * 
     * @param key
     * @param start
     * @param end
     */
    public void lTrim(String key, long start, long end) {
        redisTemplate.opsForList().trim(key, start, end);
    }

    /**
     * 获取列表长度
     * 
     * @param key
     * @return
     */
    public Long lLen(String key) {
        return redisTemplate.opsForList().size(key);
    }

    /** --------------------set相关操作-------------------------- */

    /**
     * set添加元素
     * 
     * @param key
     * @param values
     * @return
     */
    public Long sAdd(String key, String... values) {
        return redisTemplate.opsForSet().add(key, values);
    }

    /**
     * set移除元素
     * 
     * @param key
     * @param values
     * @return
     */
    public Long sRemove(String key, Object... values) {
        return redisTemplate.opsForSet().remove(key, values);
    }

    /**
     * 移除并返回集合的一个随机元素
     * 
     * @param key
     * @return
     */
    public String sPop(String key) {
        return redisTemplate.opsForSet().pop(key);
    }

    /**
     * 将元素value从一个集合移到另一个集合
     * 
     * @param key
     * @param value
     * @param destKey
     * @return
     */
    public Boolean sMove(String key, String value, String destKey) {
        return redisTemplate.opsForSet().move(key, value, destKey);
    }

    /**
     * 获取集合的大小
     * 
     * @param key
     * @return
     */
    public Long sSize(String key) {
        return redisTemplate.opsForSet().size(key);
    }

    /**
     * 判断集合是否包含value
     * 
     * @param key
     * @param value
     * @return
     */
    public Boolean sIsMember(String key, Object value) {
        return redisTemplate.opsForSet().isMember(key, value);
    }

    /**
     * 获取两个集合的交集
     * 
     * @param key
     * @param otherKey
     * @return
     */
    public Set<String> sIntersect(String key, String otherKey) {
        return redisTemplate.opsForSet().intersect(key, otherKey);
    }

    /**
     * 获取key集合与多个集合的交集
     * 
     * @param key
     * @param otherKeys
     * @return
     */
    public Set<String> sIntersect(String key, Collection<String> otherKeys) {
        return redisTemplate.opsForSet().intersect(key, otherKeys);
    }

    /**
     * key集合与otherKey集合的交集存储到destKey集合中
     * 
     * @param key
     * @param otherKey
     * @param destKey
     * @return
     */
    public Long sIntersectAndStore(String key, String otherKey, String destKey) {
        return redisTemplate.opsForSet().intersectAndStore(key, otherKey,
                destKey);
    }

    /**
     * key集合与多个集合的交集存储到destKey集合中
     * 
     * @param key
     * @param otherKeys
     * @param destKey
     * @return
     */
    public Long sIntersectAndStore(String key, Collection<String> otherKeys,
            String destKey) {
        return redisTemplate.opsForSet().intersectAndStore(key, otherKeys,
                destKey);
    }

    /**
     * 获取两个集合的并集
     * 
     * @param key
     * @param otherKeys
     * @return
     */
    public Set<String> sUnion(String key, String otherKeys) {
        return redisTemplate.opsForSet().union(key, otherKeys);
    }

    /**
     * 获取key集合与多个集合的并集
     * 
     * @param key
     * @param otherKeys
     * @return
     */
    public Set<String> sUnion(String key, Collection<String> otherKeys) {
        return redisTemplate.opsForSet().union(key, otherKeys);
    }

    /**
     * key集合与otherKey集合的并集存储到destKey中
     * 
     * @param key
     * @param otherKey
     * @param destKey
     * @return
     */
    public Long sUnionAndStore(String key, String otherKey, String destKey) {
        return redisTemplate.opsForSet().unionAndStore(key, otherKey, destKey);
    }

    /**
     * key集合与多个集合的并集存储到destKey中
     * 
     * @param key
     * @param otherKeys
     * @param destKey
     * @return
     */
    public Long sUnionAndStore(String key, Collection<String> otherKeys,
            String destKey) {
        return redisTemplate.opsForSet().unionAndStore(key, otherKeys, destKey);
    }

    /**
     * 获取两个集合的差集
     * 
     * @param key
     * @param otherKey
     * @return
     */
    public Set<String> sDifference(String key, String otherKey) {
        return redisTemplate.opsForSet().difference(key, otherKey);
    }

    /**
     * 获取key集合与多个集合的差集
     * 
     * @param key
     * @param otherKeys
     * @return
     */
    public Set<String> sDifference(String key, Collection<String> otherKeys) {
        return redisTemplate.opsForSet().difference(key, otherKeys);
    }

    /**
     * key集合与otherKey集合的差集存储到destKey中
     * 
     * @param key
     * @param otherKey
     * @param destKey
     * @return
     */
    public Long sDifference(String key, String otherKey, String destKey) {
        return redisTemplate.opsForSet().differenceAndStore(key, otherKey,
                destKey);
    }

    /**
     * key集合与多个集合的差集存储到destKey中
     * 
     * @param key
     * @param otherKeys
     * @param destKey
     * @return
     */
    public Long sDifference(String key, Collection<String> otherKeys,
            String destKey) {
        return redisTemplate.opsForSet().differenceAndStore(key, otherKeys,
                destKey);
    }

    /**
     * 获取集合所有元素
     * 
     * @param key
     * @return
     */
    public Set<String> setMembers(String key) {
        return redisTemplate.opsForSet().members(key);
    }

    /**
     * 随机获取集合中的一个元素
     * 
     * @param key
     * @return
     */
    public String sRandomMember(String key) {
        return redisTemplate.opsForSet().randomMember(key);
    }

    /**
     * 随机获取集合中count个元素
     * 
     * @param key
     * @param count
     * @return
     */
    public List<String> sRandomMembers(String key, long count) {
        return redisTemplate.opsForSet().randomMembers(key, count);
    }

    /**
     * 随机获取集合中count个元素并且去除重复的
     * 
     * @param key
     * @param count
     * @return
     */
    public Set<String> sDistinctRandomMembers(String key, long count) {
        return redisTemplate.opsForSet().distinctRandomMembers(key, count);
    }

    /**
     * 
     * @param key
     * @param options
     * @return
     */
    public Cursor<String> sScan(String key, ScanOptions options) {
        return redisTemplate.opsForSet().scan(key, options);
    }

    /**------------------zSet相关操作--------------------------------*/
    
    /**
     * 添加元素,有序集合是按照元素的score值由小到大排列
     * 
     * @param key
     * @param value
     * @param score
     * @return
     */
    public Boolean zAdd(String key, String value, double score) {
        return redisTemplate.opsForZSet().add(key, value, score);
    }

    /**
     * 
     * @param key
     * @param values
     * @return
     */
    public Long zAdd(String key, Set<TypedTuple<String>> values) {
        return redisTemplate.opsForZSet().add(key, values);
    }

    /**
     * 
     * @param key
     * @param values
     * @return
     */
    public Long zRemove(String key, Object... values) {
        return redisTemplate.opsForZSet().remove(key, values);
    }

    /**
     * 增加元素的score值，并返回增加后的值
     * 
     * @param key
     * @param value
     * @param delta
     * @return
     */
    public Double zIncrementScore(String key, String value, double delta) {
        return redisTemplate.opsForZSet().incrementScore(key, value, delta);
    }

    /**
     * 返回元素在集合的排名,有序集合是按照元素的score值由小到大排列
     * 
     * @param key
     * @param value
     * @return 0表示第一位
     */
    public Long zRank(String key, Object value) {
        return redisTemplate.opsForZSet().rank(key, value);
    }

    /**
     * 返回元素在集合的排名,按元素的score值由大到小排列
     * 
     * @param key
     * @param value
     * @return
     */
    public Long zReverseRank(String key, Object value) {
        return redisTemplate.opsForZSet().reverseRank(key, value);
    }

    /**
     * 获取集合的元素, 从小到大排序
     * 
     * @param key
     * @param start
     *            开始位置
     * @param end
     *            结束位置, -1查询所有
     * @return
     */
    public Set<String> zRange(String key, long start, long end) {
        return redisTemplate.opsForZSet().range(key, start, end);
    }

    /**
     * 获取集合元素, 并且把score值也获取
     * 
     * @param key
     * @param start
     * @param end
     * @return
     */
    public Set<TypedTuple<String>> zRangeWithScores(String key, long start,
            long end) {
        return redisTemplate.opsForZSet().rangeWithScores(key, start, end);
    }

    /**
     * 根据Score值查询集合元素
     * 
     * @param key
     * @param min
     *            最小值
     * @param max
     *            最大值
     * @return
     */
    public Set<String> zRangeByScore(String key, double min, double max) {
        return redisTemplate.opsForZSet().rangeByScore(key, min, max);
    }

    /**
     * 根据Score值查询集合元素, 从小到大排序
     * 
     * @param key
     * @param min
     *            最小值
     * @param max
     *            最大值
     * @return
     */
    public Set<TypedTuple<String>> zRangeByScoreWithScores(String key,
            double min, double max) {
        return redisTemplate.opsForZSet().rangeByScoreWithScores(key, min, max);
    }

    /**
     * 
     * @param key
     * @param min
     * @param max
     * @param start
     * @param end
     * @return
     */
    public Set<TypedTuple<String>> zRangeByScoreWithScores(String key,
            double min, double max, long start, long end) {
        return redisTemplate.opsForZSet().rangeByScoreWithScores(key, min, max,
                start, end);
    }

    /**
     * 获取集合的元素, 从大到小排序
     * 
     * @param key
     * @param start
     * @param end
     * @return
     */
    public Set<String> zReverseRange(String key, long start, long end) {
        return redisTemplate.opsForZSet().reverseRange(key, start, end);
    }

    /**
     * 获取集合的元素, 从大到小排序, 并返回score值
     * 
     * @param key
     * @param start
     * @param end
     * @return
     */
    public Set<TypedTuple<String>> zReverseRangeWithScores(String key,
            long start, long end) {
        return redisTemplate.opsForZSet().reverseRangeWithScores(key, start,
                end);
    }

    /**
     * 根据Score值查询集合元素, 从大到小排序
     * 
     * @param key
     * @param min
     * @param max
     * @return
     */
    public Set<String> zReverseRangeByScore(String key, double min,
            double max) {
        return redisTemplate.opsForZSet().reverseRangeByScore(key, min, max);
    }

    /**
     * 根据Score值查询集合元素, 从大到小排序
     * 
     * @param key
     * @param min
     * @param max
     * @return
     */
    public Set<TypedTuple<String>> zReverseRangeByScoreWithScores(
            String key, double min, double max) {
        return redisTemplate.opsForZSet().reverseRangeByScoreWithScores(key,
                min, max);
    }

    /**
     * 
     * @param key
     * @param min
     * @param max
     * @param start
     * @param end
     * @return
     */
    public Set<String> zReverseRangeByScore(String key, double min,
            double max, long start, long end) {
        return redisTemplate.opsForZSet().reverseRangeByScore(key, min, max,
                start, end);
    }

    /**
     * 根据score值获取集合元素数量
     * 
     * @param key
     * @param min
     * @param max
     * @return
     */
    public Long zCount(String key, double min, double max) {
        return redisTemplate.opsForZSet().count(key, min, max);
    }

    /**
     * 获取集合大小
     * 
     * @param key
     * @return
     */
    public Long zSize(String key) {
        return redisTemplate.opsForZSet().size(key);
    }

    /**
     * 获取集合大小
     * 
     * @param key
     * @return
     */
    public Long zZCard(String key) {
        return redisTemplate.opsForZSet().zCard(key);
    }

    /**
     * 获取集合中value元素的score值
     * 
     * @param key
     * @param value
     * @return
     */
    public Double zScore(String key, Object value) {
        return redisTemplate.opsForZSet().score(key, value);
    }

    /**
     * 移除指定索引位置的成员
     * 
     * @param key
     * @param start
     * @param end
     * @return
     */
    public Long zRemoveRange(String key, long start, long end) {
        return redisTemplate.opsForZSet().removeRange(key, start, end);
    }

    /**
     * 根据指定的score值的范围来移除成员
     * 
     * @param key
     * @param min
     * @param max
     * @return
     */
    public Long zRemoveRangeByScore(String key, double min, double max) {
        return redisTemplate.opsForZSet().removeRangeByScore(key, min, max);
    }

    /**
     * 获取key和otherKey的并集并存储在destKey中
     * 
     * @param key
     * @param otherKey
     * @param destKey
     * @return
     */
    public Long zUnionAndStore(String key, String otherKey, String destKey) {
        return redisTemplate.opsForZSet().unionAndStore(key, otherKey, destKey);
    }

    /**
     * 
     * @param key
     * @param otherKeys
     * @param destKey
     * @return
     */
    public Long zUnionAndStore(String key, Collection<String> otherKeys,
            String destKey) {
        return redisTemplate.opsForZSet()
                .unionAndStore(key, otherKeys, destKey);
    }

    /**
     * 交集
     * 
     * @param key
     * @param otherKey
     * @param destKey
     * @return
     */
    public Long zIntersectAndStore(String key, String otherKey,
            String destKey) {
        return redisTemplate.opsForZSet().intersectAndStore(key, otherKey,
                destKey);
    }

    /**
     * 交集
     * 
     * @param key
     * @param otherKeys
     * @param destKey
     * @return
     */
    public Long zIntersectAndStore(String key, Collection<String> otherKeys,
            String destKey) {
        return redisTemplate.opsForZSet().intersectAndStore(key, otherKeys,
                destKey);
    }

    /**
     * 
     * @param key
     * @param options
     * @return
     */
    public Cursor<TypedTuple<String>> zScan(String key, ScanOptions options) {
        return redisTemplate.opsForZSet().scan(key, options);
    }
}
```



## Redis.conf 详解

## Redis持久化

## Redis发布订阅

## Redis主从复制

## Redis 缓存穿透和雪崩



