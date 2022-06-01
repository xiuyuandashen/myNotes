# mongo

## mongo安装

```bash
# 拉去docker mongo镜像
docker pull mongo:latest

# 创建和启动容器
docker run -idt --restart=always -p 27017:27017 --name mymongo -v /home/mongo/data/db:/data/db -d mongo
# 进入容器
docker exec -it mymongo /bin/bash
# 进入容器后，执行mongo进入mongo
mongo
# 查看数据库
show dbs;
```

![image-20220321001121597](D:\笔记\mongo\mongo.assets\image-20220321001121597.png)



## mongo 基础概念

不管我们学习什么数据库都应该学习其中的基础概念，在mongodb中基本的概念是文档、集合、数据库，下面我们挨个介绍。

下表将帮助您更容易理解Mongo中的一些概念：

![image-20220321001613108](D:\笔记\mongo\mongo.assets\image-20220321001613108.png)

通过下图实例，我们也可以更直观的的了解Mongo中的一些概念：

![image-20220321002113083](D:\笔记\mongo\mongo.assets\image-20220321002113083.png)

### 数据库

一个mongodb中可以建立多个数据库

```bash
# 1、 Help查看命令提示 
db.help();
# 2、 切换/创建数据库 如果数据库不存在，则创建数据库，否则切换到指定数据库
use test
# 3、 查询所有数据库 
show dbs;
# 4、 删除当前使用数据库 
db.dropDatabase();
# 5、 查看当前使用的数据库 
db.getName();
# 6、 显示当前db状态 
db.stats();
# 7、 当前db版本 
db.version();
# 8、 查看当前db的链接机器地址 
db.getMongo〇;

```



### 文档

文档是一组键值(key-value)对(即BSON)。MongoDB 的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系型数据库有很大的区别，也是 MongoDB 非常突出的特点。

下表列出了 RDBMS 与 MongoDB 对应的术语：

![image-20220321003724923](D:\笔记\mongo\mongo.assets\image-20220321003724923.png)

**需要注意的是：**

1、文档中的键/值对是有序的。

2、文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。

3、MongoDB区分类型和大小写。

4、MongoDB的文档不能有重复的键。

5、文档的键是字符串。除了少数例外情况，键可以使用任意UTF-8字符。

**文档键命名规范：**

1、键不能含有\0 (空字符)。这个字符用来表示键的结尾。

2、.和$有特别的意义，只有在特定环境下才能使用。

3、以下划线"_"开头的键是保留的(不是严格要求的)。



### 集合

集合就是 MongoDB 文档组，类似于 RDBMS （关系数据库管理系统：Relational Database Management System)中的表格。

集合存在于数据库中，集合没有固定的结构，这意味着你在对集合可以插入不同格式和类型的数据，但通常情况下我们插入集合的数据都会有一定的关联性。

常用命令：

```bash
# 1、 创建一个集合（table)
db.createCollection( "collName");
# 2、 得到指定名称的集合（table )
db.getCollection("user");

```



### MongoDB 数据类型

下表为MongoDB中常用的几种数据类型：

![image-20220321094201095](D:\笔记\mongo\mongo.assets\image-20220321094201095.png)

![image-20220321101109302](D:\笔记\mongo\mongo.assets\image-20220321101109302.png)

## 适用场景

### 适用场景

1、网站数据：Mongo非常适合实时的插入，更新与查询，并具备网站实时数据存储所需的复制及高度伸缩性。

2、缓存：由于性能很高，Mongo也适合作为信息基础设施的缓存层。在系统重启之后，由Mongo搭建的持久化缓存层可以避免下层的数据源过载。

3、大尺寸，低价值的数据：使用传统的关系型数据库存储一些数据时可能会比较昂贵， 在此之前，很多时候程序员往往会选择传统的文件进行存储。

4、高伸缩性的场景：Mongo非常适合由数十或数百台服务器组成的数据库。Mongo的路线图中已经包含对Map Reduce弓摩的内置支持。

5、用于对象及 JSON数据的存储：Mongo的BSON数据格式非常适合文档化格式的存储 及查询。

### 不适用场景

1、高度事务性的系统：例如银行或会计系统。传统的关系型数据库目前还是更适用于需要大量原子性复杂事务的应用程序。

2、传统的商业智能应用：针对特定问题的BI数据库会对产生高度优化的查询方式。对于此类应用，数据仓库可能是更合适的选择。



## MongoDB 入门

### 常用操作

#### insert

```bash
# 查看当前所在数据库
db.getName() # 或者 db
# 在user集合中插入一条数据
db.user.save({name:'xiuyuan',sex:'1'})
# 查看集合数据
db.user.find()

```

![image-20220321104935830](D:\笔记\mongo\mongo.assets\image-20220321104935830.png)

_id组合

Objectld是、id”的默认类型。Objectld使用12字节的存储空间，每个字节二位十六进制数字， 是一个24位的字符串

![image-20220321104945507](D:\笔记\mongo\mongo.assets\image-20220321104945507.png)

1. 时间戳：时间不断变化的。

2. 机器：主机的唯_标识码。通常是机器主机名的散列值，这样可以确保不同主机 生成不同的Objectld ,不产生冲突。

3. PID:为了确保在同一台机器上并发的多个进程产生的Objectld是唯一的，所以加上进程标识符(PID)。

4. 计数器：前9个字节保证了同一秒钟不同机器不同进程产生的Objectld是唯一的。 后3个字节就是一个自动增加的计数器，确保相同进程同一秒产生的Objectld也是不一样。同一秒最多允许每个进程拥有IS 777 2托个不同的Objectld。