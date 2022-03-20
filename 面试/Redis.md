# Redis 面试题

## 说一下你在项目中的redis的应用场景？

按照数据类型来

- String

  绑定ip地址，可以记录ip地址的操作。

- Hash

  存储用户信息【id，name，age】
   Hset(key,field,value)
   Hset(userKey,id,101)
   当我修改用户信息某一项属性的时候，可以直接取出单一的值。
   不建议使用String类型是因为，在反序列化时，会全部序列化出来，会增加IO次数，降低性能。

- List
   实现最新消息的排行，
   还可以利用List的push命令，将任务存在list集合中，同时使用另一个命令，将任务从集合中取出[pop]。
   Redis — List 数据类型来模拟消息队列。【电商中的秒杀就可以采用这种方式来完成一个秒杀活动】

- Set
   特殊之处：可以自动排重（非重复）。比如说微博中将每个人的好友存在集合(Set)中，
   这样求两个人的共通好友的操作。我们只需要求交集即可。

- Zset (SortedSet)
   以某一个条件为权重，进行排序。 京东：商品详情的时候，都会有一个综合排名，还可以按照价格进行排名。

  

## Redis实现简单分布式锁

### 1.加锁原理

加锁实际上就是在redis中，给Key键设置一个值，为避免死锁，并给定一个过期时间。

```
SET lock_key random_value NX PX 5000
```

值得注意的是：
`random_value` 是客户端生成的唯一的字符串。
`NX` 代表只在键不存在时，才对键进行设置操作。
`PX 5000` 设置键的过期时间为5000毫秒。

这样，如果上面的命令执行成功，则证明客户端获取到了锁。

### 2.解锁原理

解锁的过程就是将Key键删除。但也不能乱删，不能说客户端1的请求将客户端2的锁给删除掉。这时候`random_value`的作用就体现出来。

为了保证解锁操作的原子性，我们用LUA脚本完成这一操作。先判断当前锁的字符串是否与传入的值相等，是的话就删除Key，解锁成功。

```lua
if redis.call('get',KEYS[1]) == ARGV[1] then 
   return redis.call('del',KEYS[1]) 
else
   return 0 
end
```

###  3.实现

首先，我们在pom文件中，引入Jedis。在这里，笔者用的是最新版本，注意由于版本的不同，API可能有所差异。



```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.0.1</version>
</dependency>
```

加锁的过程很简单，就是通过SET指令来设置值，成功则返回；否则就循环等待，在timeout时间内仍未获取到锁，则获取失败。



```Java
@Service
public class RedisLock {

    Logger logger = LoggerFactory.getLogger(this.getClass());

    private String lock_key = "redis_lock"; //锁键

    protected long internalLockLeaseTime = 30000;//锁过期时间

    private long timeout = 999999; //获取锁的超时时间

    
    //SET命令的参数 
    SetParams params = SetParams.setParams().nx().px(internalLockLeaseTime);

    @Autowired
    JedisPool jedisPool;

    
    /**
     * 加锁
     * @param id
     * @return
     */
    public boolean lock(String id){
        Jedis jedis = jedisPool.getResource();
        Long start = System.currentTimeMillis();
        try{
            for(;;){
                //SET命令返回OK ，则证明获取锁成功
                String lock = jedis.set(lock_key, id, params);
                if("OK".equals(lock)){
                    return true;
                }
                //否则循环等待，在timeout时间内仍未获取到锁，则获取失败
                long l = System.currentTimeMillis() - start;
                if (l>=timeout) {
                    return false;
                }
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }finally {
            jedis.close();
        }
    }
}
```

解锁我们通过`jedis.eval`来执行一段LUA就可以。将锁的Key键和生成的字符串当做参数传进来。



```java
    /**
     * 解锁
     * @param id
     * @return
     */
    public boolean unlock(String id){
        Jedis jedis = jedisPool.getResource();
        String script =
                "if redis.call('get',KEYS[1]) == ARGV[1] then" +
                        "   return redis.call('del',KEYS[1]) " +
                        "else" +
                        "   return 0 " +
                        "end";
        try {
            Object result = jedis.eval(script, Collections.singletonList(lock_key), 
                                    Collections.singletonList(id));
            if("1".equals(result.toString())){
                return true;
            }
            return false;
        }finally {
            jedis.close();
        }
    }
```

最后，我们可以在多线程环境下测试一下。我们开启1000个线程，对count进行累加。调用的时候，关键是唯一字符串的生成。这里，笔者使用的是`Snowflake`算法。



```java
@Controller
public class IndexController {

    @Autowired
    RedisLock redisLock;
    
    int count = 0;
    
    @RequestMapping("/index")
    @ResponseBody
    public String index() throws InterruptedException {

        int clientcount =1000;
        CountDownLatch countDownLatch = new CountDownLatch(clientcount);

        ExecutorService executorService = Executors.newFixedThreadPool(clientcount);
        long start = System.currentTimeMillis();
        for (int i = 0;i<clientcount;i++){
            executorService.execute(() -> {
            
                //通过Snowflake算法获取唯一的ID字符串
                String id = IdUtil.getId();
                try {
                    redisLock.lock(id);
                    count++;
                }finally {
                    redisLock.unlock(id);
                }
                countDownLatch.countDown();
            });
        }
        countDownLatch.await();
        long end = System.currentTimeMillis();
        logger.info("执行线程数:{},总耗时:{},count数为:{}",clientcount,end-start,count);
        return "Hello";
    }
}
```

至此，单节点Redis的分布式锁的实现就已经完成了。比较简单，但是问题也比较大，最重要的一点是，锁不具有可重入性。

## Redis 持久化

[转载自](https://www.cnblogs.com/itdragon/p/7906481.html)

Redis 有两种持久化方案，RDB （Redis DataBase）和 AOF （Append Only File）。如果你想快速了解和使用RDB和AOF，可以直接跳到文章底部看总结。本章节通过配置文件，触发快照的方式，恢复数据的操作，命令操作演示，优缺点来学习 Redis 的重点知识**持久化**。

### RDB 详解

RDB 是 Redis 默认的持久化方案。在指定的时间间隔内，执行指定次数的写操作，则会将内存中的数据写入到磁盘中。即在指定目录下生成一个dump.rdb文件。Redis 重启会通过加载dump.rdb文件恢复数据。

#### 从配置文件了解RDB

打开 redis.conf 文件，找到 SNAPSHOTTING 对应内容
1 RDB核心规则配置（重点）

```
save <seconds> <changes>
# save ""
save 900 1
save 300 10
save 60 10000
```

解说：save <指定时间间隔> <执行指定次数更新操作>，满足条件就将内存中的数据同步到硬盘中。官方出厂配置默认是 900秒内有1个更改，300秒内有10个更改以及60秒内有10000个更改，则将内存中的数据快照写入磁盘。
若不想用RDB方案，可以把 save "" 的注释打开，下面三个注释。

2 指定本地数据库文件名，一般采用默认的 dump.rdb

```
dbfilename dump.rdb
```

3 指定本地数据库存放目录，一般也用默认配置

```
dir ./
```

4 默认开启数据压缩

```
rdbcompression yes
```

解说：配置存储至本地数据库时是否压缩数据，默认为yes。Redis采用LZF压缩方式，但占用了一点CPU的时间。若关闭该选项，但会导致数据库文件变的巨大。建议开启。

#### 触发RDB快照

1 在指定的时间间隔内，执行指定次数的写操作
2 执行save（阻塞， 只管保存快照，其他的等待） 或者是bgsave （异步）命令
3 执行flushall 命令，清空数据库所有数据，意义不大。
4 执行shutdown 命令，保证服务器正常关闭且不丢失任何数据，意义...也不大。

#### 通过RDB文件恢复数据

**将dump.rdb 文件拷贝到redis的安装目录的bin目录下，重启redis服务即可**。在实际开发中，一般会考虑到物理机硬盘损坏情况，选择备份dump.rdb 。可以从下面的操作演示中可以体会到。

#### RDB 的优缺点

优点：
1 **适合大规模的数据恢复。**
2 **如果业务对数据完整性和一致性要求不高，RDB是很好的选择。**

缺点：
1 **数据的完整性和一致性不高，因为RDB可能在最后一次备份时宕机了**。
2 备份时占用内存，因为**Redis 在备份时会独立创建一个子进程，将数据写入到一个临时文件（此时内存中的数据是原来的两倍哦）**，**最后再将临时文件替换之前的备份文件**。
所以Redis 的持久化和数据的恢复要选择在夜深人静的时候执行是比较合理的。

#### 操作演示

```xml
[root@itdragon bin]# vim redis.conf
save 900 1
save 120 5
save 60 10000
[root@itdragon bin]# ./redis-server redis.conf
[root@itdragon bin]# ./redis-cli -h 127.0.0.1 -p 6379
127.0.0.1:6379> keys *
(empty list or set)
127.0.0.1:6379> set key1 value1
OK
127.0.0.1:6379> set key2 value2
OK
127.0.0.1:6379> set key3 value3
OK
127.0.0.1:6379> set key4 value4
OK
127.0.0.1:6379> set key5 value5
OK
127.0.0.1:6379> set key6 value6
OK
127.0.0.1:6379> SHUTDOWN
not connected> QUIT
[root@itdragon bin]# cp dump.rdb dump_bk.rdb
[root@itdragon bin]# ./redis-server redis.conf
[root@itdragon bin]# ./redis-cli -h 127.0.0.1 -p 6379
127.0.0.1:6379> FLUSHALL 
OK
127.0.0.1:6379> keys *
(empty list or set)
127.0.0.1:6379> SHUTDOWN
not connected> QUIT
[root@itdragon bin]# cp dump_bk.rdb  dump.rdb
cp: overwrite `dump.rdb'? y
[root@itdragon bin]# ./redis-server redis.conf
[root@itdragon bin]# ./redis-cli -h 127.0.0.1 -p 6379
127.0.0.1:6379> keys *
1) "key5"
2) "key1"
3) "key3"
4) "key4"
5) "key6"
6) "key2"
```

第一步：vim 修改持久化配置时间，120秒内修改5次则持久化一次。
第二步：重启服务使配置生效。
第三步：分别set 5个key，过两分钟后，在bin的当前目录下会自动生产一个dump.rdb文件。（set key6 是为了验证shutdown有触发RDB快照的作用）
第四步：将当前的dump.rdb 备份一份（模拟线上工作）。
第五步：执行FLUSHALL命令清空数据库数据（模拟数据丢失）。
第六步：重启Redis服务，恢复数据.....咦？？？？( ′◔ ‸◔`)。数据是空的？？？？这是因为FLUSHALL也有触发RDB快照的功能。
第七步：将备份的 dump_bk.rdb 替换 dump.rdb 然后重新Redis。

注意点：**SHUTDOWN 和 FLUSHALL 命令都会触发RDB快照**，这是一个坑，请大家注意。

其他命令：

- keys * 匹配数据库中所有 key
- save 阻塞触发RDB快照，使其备份数据
- FLUSHALL 清空整个 Redis 服务器的数据(几乎不用)
- SHUTDOWN 关机走人（很少用）

------

### AOF 详解

AOF ：Redis 默认不开启。它的出现是为了弥补RDB的不足（数据的不一致性），所以它采用日志的形式来记录每个**写操作**，并**追加**到文件中。Redis 重启的会根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

#### 从配置文件了解AOF

打开 redis.conf 文件，找到 APPEND ONLY MODE 对应内容
1 redis 默认关闭，开启需要手动把no改为yes

```
appendonly yes
```

2 指定本地数据库文件名，默认值为 appendonly.aof

```
appendfilename "appendonly.aof"
```

3 指定更新日志条件

```
# appendfsync always
appendfsync everysec
# appendfsync no
```

解说：
always：同步持久化，每次发生数据变化会立刻写入到磁盘中。性能较差当数据完整性比较好（慢，安全）
everysec：出厂默认推荐，每秒异步记录一次（默认值,最多丢失1秒的数据）
no：不同步

4 配置重写触发机制

```
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
```

解说：当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发。一般都设置为3G，64M太小了。

#### 触发AOF快照

根据配置文件触发，可以是每次执行触发，可以是每秒触发，可以不同步。

#### 根据AOF文件恢复数据

正常情况下，将appendonly.aof 文件拷贝到redis的安装目录的bin目录下，重启redis服务即可。但在实际开发中，可能因为某些原因导致appendonly.aof 文件格式异常，从而导致数据还原失败，可以通过命令redis-check-aof --fix appendonly.aof 进行修复 。从下面的操作演示中体会。

#### AOF的重写机制

前面也说到了，AOF的工作原理是将写操作追加到文件中，文件的冗余内容会越来越多。所以聪明的 Redis 新增了重写机制。当AOF文件的大小超过所设定的阈值时，Redis就会对AOF文件的内容压缩。

**重写的原理：Redis 会fork出一条新进程，读取内存中的数据，并重新写到一个临时文件中。**并没有读取旧文件（你都那么大了，我还去读你？？？ o(ﾟДﾟ)っ傻啊！）。**最后替换旧的aof文件。**

**触发机制：当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发。这里的“一倍”和“64M” 可以通过配置文件修改。**

#### AOF 的优缺点

优点：**数据的完整性和一致性更高**
缺点：**因为AOF记录的内容多，文件会越来越大，数据恢复也会越来越慢。**

#### 操作演示

```
[root@itdragon bin]# vim appendonly.aof
appendonly yes
[root@itdragon bin]# ./redis-server redis.conf
[root@itdragon bin]# ./redis-cli -h 127.0.0.1 -p 6379
127.0.0.1:6379> keys *
(empty list or set)
127.0.0.1:6379> set keyAOf valueAof
OK
127.0.0.1:6379> FLUSHALL 
OK
127.0.0.1:6379> SHUTDOWN
not connected> QUIT
[root@itdragon bin]# ./redis-server redis.conf
[root@itdragon bin]# ./redis-cli -h 127.0.0.1 -p 6379
127.0.0.1:6379> keys *
1) "keyAOf"
127.0.0.1:6379> SHUTDOWN
not connected> QUIT
[root@itdragon bin]# vim appendonly.aof
fjewofjwojfoewifjowejfwf
[root@itdragon bin]# ./redis-server redis.conf
[root@itdragon bin]# ./redis-cli -h 127.0.0.1 -p 6379
Could not connect to Redis at 127.0.0.1:6379: Connection refused
not connected> QUIT
[root@itdragon bin]# redis-check-aof --fix appendonly.aof 
'x              3e: Expected prefix '*', got: '
AOF analyzed: size=92, ok_up_to=62, diff=30
This will shrink the AOF from 92 bytes, with 30 bytes, to 62 bytes
Continue? [y/N]: y
Successfully truncated AOF
[root@itdragon bin]# ./redis-server redis.conf
[root@itdragon bin]# ./redis-cli -h 127.0.0.1 -p 6379
127.0.0.1:6379> keys *
1) "keyAOf"
```

第一步：修改配置文件，开启AOF持久化配置。
第二步：重启Redis服务，并进入Redis 自带的客户端中。
第三步：保存值，然后模拟数据丢失，关闭Redis服务。
第四步：重启服务，发现数据恢复了。（额外提一点：有教程显示FLUSHALL 命令会被写入AOF文件中，导致数据恢复失败。我安装的是redis-4.0.2没有遇到这个问题）。
第五步：修改appendonly.aof，模拟文件异常情况。
第六步：重启 Redis 服务失败。这同时也说明了，**RDB和AOF可以同时存在，且优先加载AOF文件。**
第七步：校验appendonly.aof 文件。重启Redis 服务后正常。

补充点：aof 的校验是通过 **redis-check-aof 文件**，那么rdb 的校验是不是可以通过 redis-check-rdb 文件呢？？？

### 总结

1. Redis 默认开启RDB持久化方式，在指定的时间间隔内，执行指定次数的写操作，则将内存中的数据写入到磁盘中。
2. RDB 持久化适合大规模的数据恢复但它的数据一致性和完整性较差。
3. Redis 需要手动开启AOF持久化方式，默认是每秒将写操作日志追加到AOF文件中。
4. AOF 的数据完整性比RDB高，但记录内容多了，会影响数据恢复的效率。
5. Redis 针对 AOF文件大的问题，提供重写的瘦身机制。
6. 若只打算用Redis 做缓存，可以关闭持久化。
7. 若打算使用Redis 的持久化。建议RDB和AOF都开启。其实RDB更适合做数据的备份，留一后手。AOF出问题了，还有RDB。

到这里Redis 的持久化就介绍完了，有什么不对的地方可以指出。

