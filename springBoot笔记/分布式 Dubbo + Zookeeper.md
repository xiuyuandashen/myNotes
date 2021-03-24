#  分布式 Dubbo + Zookeeper + SpringBoot

##  单体架构

![image-20200709151845014](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200709151845014.png)



## RPC

RPC（Remote Procedure Call）远程过程调用，简单的理解是一个节点请求另一个节点提供的服务。

**远程过程调用带来的新问题**

在远程调用时，我们需要执行的函数体是在远程的机器上的，也就是说，Multiply是在另一个进程中执行的。这就带来了几个新问题：

1. **Call ID映射**。我们怎么告诉远程机器我们要调用Multiply，而不是Add或者FooBar呢？在本地调用中，函数体是直接通过函数指针来指定的，我们调用Multiply，编译器就自动帮我们调用它相应的函数指针。但是在远程调用中，函数指针是不行的，因为两个进程的地址空间是完全不一样的。所以，在RPC中，所有的函数都必须有自己的一个ID。这个ID在所有进程中都是唯一确定的。客户端在做远程过程调用时，必须附上这个ID。然后我们还需要在客户端和服务端分别维护一个 {函数 <--> Call ID} 的对应表。两者的表不一定需要完全相同，但相同的函数对应的Call ID必须相同。当客户端需要进行远程调用时，它就查一下这个表，找出相应的Call ID，然后把它传给服务端，服务端也通过查表，来确定客户端需要调用的函数，然后执行相应函数的代码。
2. **序列化和反序列化**。客户端怎么把参数值传给远程的函数呢？在本地调用中，我们只需要把参数压到栈里，然后让函数自己去栈里读就行。但是在远程过程调用时，客户端跟服务端是不同的进程，不能通过内存来传递参数。甚至有时候客户端和服务端使用的都不是同一种语言（比如服务端用C++，客户端用Java或者Python）。这时候就需要客户端把参数先转成一个字节流，传给服务端后，再把字节流转成自己能读取的格式。这个过程叫序列化和反序列化。同理，从服务端返回的值也需要序列化反序列化的过程。
3. **网络传输**。远程调用往往用在网络上，客户端和服务端是通过网络连接的。所有的数据都需要通过网络传输，因此就需要有一个网络传输层。网络传输层需要把Call ID和序列化后的参数字节流传给服务端，然后再把序列化后的调用结果传回客户端。只要能完成这两者的，都可以作为传输层使用。因此，它所使用的协议其实是不限的，能完成传输就行。尽管大部分RPC框架都使用TCP协议，但其实UDP也可以，而gRPC干脆就用了HTTP2。Java的Netty也属于这层的东西。

**RPC的两个核心模块：通讯 、序列化**

[Dubbo官方文档](http://dubbo.apache.org/zh-cn/docs/user/preface/)



## 什么是Dubbo

1，什么是Dubbo

Dubbo是阿里巴巴在2011年开源的分布式服务框架，是服务化治理的核心框架。 Dubbo主要提供3个功能，远程接口调用，负载均衡和容错，服务注册和服务发现

目前，已经是Apache的开源项目

[http://dubbo.apache.orgdubbo.apache.org](https://link.zhihu.com/?target=http%3A//dubbo.apache.org)

2，Dubbo的架构

![img](https://pic3.zhimg.com/80/v2-d51a77e30fd7478662f5afcb534dc6bf_720w.jpg)

3，工作细节说明（来自dubbo官网）

节点角色说明（掌握）

> Provider 暴露服务的服务提供方
> Consumer 调用远程服务的服务消费方
> Registry 服务注册与发现的注册中心
> Monitor 统计服务的调用次数和调用时间的监控中心
> Container 服务运行容器

调用关系说明

> \1. 服务容器负责启动，加载，运行服务提供者。
> \2. 服务提供者在启动时，向注册中心注册自己提供的服务。
> \3. 服务消费者在启动时，向注册中心订阅自己所需的服务。
> \4. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
> \5. 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
> \6. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

Dubbo 架构具有以下几个特点

连通性

> · 注册中心负责服务地址的注册与查找，相当于目录服务，服务提供者和消费者只在启动时与注册中心交互，注册中心不转发请求，压力较小
> · 监控中心负责统计各服务调用次数，调用时间等，统计先在内存汇总后每分钟一次发送到监控中心服务器，并以报表展示
> · 服务提供者向注册中心注册其提供的服务，并汇报调用时间到监控中心，此时间不包含网络开销
> · 服务消费者向注册中心获取服务提供者地址列表，并根据负载算法直接调用提供者，同时汇报调用时间到监控中心，此时间包含网络开销
> · 注册中心，服务提供者，服务消费者三者之间均为长连接，监控中心除外
> · 注册中心通过长连接感知服务提供者的存在，服务提供者宕机，注册中心将立即推送事件通知消费者
> · **注册中心和监控中心全部宕机，不影响已运行的提供者和消费者，消费者在本地缓存了提供者列表**
> · 注册中心和监控中心都是可选的，服务消费者可以直连服务提供者

健状性

> · 监控中心宕掉不影响使用，只是丢失部分采样数据
> · 数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务
> · 注册中心对等集群，任意一台宕掉后，将自动切换到另一台
> · 注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯
> · 服务提供者无状态，任意一台宕掉后，不影响使用
> · 服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复

伸缩性

> 注册中心为对等集群，可动态增加机器部署实例，所有客户端将自动发现新的注册中心
> 服务提供者无状态，可动态增加机器部署实例，注册中心将推送新的服务提供者信息给消费者

## 安装Dubbo 和 Zookeeper 



![image-20200709161712977](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200709161712977.png)

### 首先安装Zookeeper 注册中心

![image-20200708160323110](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200708160323110.png)

在./conf 下 将zoo_sample.cfg 命名未 zoo.cfg，并对其配置

```bash
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
## 数据的储存位置
dataDir=D:\\apache-zookeeper-3.6.1-bin\\apache-zookeeper-3.6.1-bin\\data
# the port at which the clients will connect
## 端口
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the 
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1

## Metrics Providers
#
# https://prometheus.io Metrics Exporter
#metricsProvider.className=org.apache.zookeeper.metrics.prometheus.PrometheusMetricsProvider
#metricsProvider.httpPort=7000
#metricsProvider.exportJvmInfo=true
## 这个得加上
admin.serverPort=8888

```

然后在./bin/

![image-20200708160658069](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200708160658069.png)

服务器启动成功

![image-20200708160734918](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200708160734918.png)

客户端启动成功

![image-20200708160721246](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200708160721246.png)

### Dubbo-admin

首先下载Dubbo-admin

**dubbo-admin是一个监控管理后台，可以查看我们注册了哪些服务，哪些服务被消费了~**

https://github.com/apache/dubbo-admin/tree/master

然后解压 

用cmd进行打包成 jar ：在下面这个路径下 

![image-20200708161133862](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200708161133862.png)

```bash
mvn clean package -Dmaven.test.skip=true
```

打包成功后在目录target下会出现jar包：对应三个项目都会有jar

![image-20200708161111846](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200708161111846.png)

首先我们需要打开Zookeeper的服务器和客户端，然后

我们只需要在cmd中，使用java -jar dubbo...jar 就可以使用。

有与该jar包的源文件中端口是7001，所以我们在浏览器中访问 http://localhost:7001/

账号：root 密码 root

![image-20200708161404985](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200708161404985.png)

### Dubbo

Dubbo 只是一个jar包，我们在项目中引入即可。

##  springBoot 集成 Dubbo + Zookeeper

首先先开启 Zookeeper注册中心 和 dubbo-admin 可视化页面

###  生产者 （提供服务的）

**首先需要创建接口** 

同个生产者需要继承相同的接口，消费者也需要导入接口的依赖

![image-20200710233858974](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200710233858974.png)



![image-20200710233944655](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200710233944655.png)





生产者

![image-20200710234151117](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200710234151117.png)

**如果多个生产者提供相同的服务，那么服务的继承的接口要一致**



#### 依赖

```xml
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>0.2.0</version>
</dependency>

以及导入interface模块 ，这样才能继承接口
<dependency>
    <groupId>com</groupId>
    <artifactId>springboot-interface</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```



#### 配置Dubbo 

```properties
server.port=8002


dubbo.application.name=dubbo-springboot-provider ## 定义应用的名称
dubbo.registry.protocol=zookeeper  ## 定义服务注册中心
dubbo.registry.address=127.0.0.1:2181 ## 定义服务注册中心的ip地址
dubbo.protocol.port=22222 ## 用dubbo协议在22222端口暴露服务
```

#### 给Service加@Service注解（dubbo提供的@Service）

```java
@Service //dubbo 的Service
public class UserServiceImpl implements IUserService {
    @Override
    public String hello(String name) {

        return "String Boot hello:"+name;
    }
}
```

#### 启动类加上@EnableDubbo注解

```java
@SpringBootApplication
@EnableDubbo  //开启注解式的Dubbo发布服务
public class SpringbootProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootProviderApplication.class, args);
    }

}
```



###  消费者

![image-20200710001452503](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200710001452503.png)

#### 依赖

```xml
<!--dubbo + Zookeeper 依赖-->
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>0.2.0</version>
</dependency>

<!--  接口的依赖 ，可以通过maven导入其他模块-->
<dependency>
    <groupId>com</groupId>
    <artifactId>springboot-interface</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

####  配置Dubbo

```properties
dubbo.application.name=dubbo-springboot-consumer ## 定义应用的名称
dubbo.registry.protocol=zookeeper  ## 定义服务注册中心
dubbo.registry.address=127.0.0.1:2181  ## 定义服务注册中心的ip地址
```

#### Controller层调用生产者提供的方法

```java
package com.springbootconsumer.controller;

import com.alibaba.dubbo.config.annotation.Reference;
import com.springbootprovider.service.IUserService;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("user")
public class UserController {

    @Reference //通过 @Reference 注解来 代替原先的@Autowried注解，
    private IUserService userService;

    @RequestMapping("hello")
    public String hello(String name){

      return  userService.hello(name);

    }
}
```

#### 启动类加上@EnableDubbo注解

```java
@SpringBootApplication
@EnableDubbo
public class SpringbootConsumerApplication {

    public static void main(String[] args) {
        
        SpringApplication.run(SpringbootConsumerApplication.class, args);
    }

}
```



###  效果

![image-20200710002228893](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200710002228893.png)

![image-20200710002307272](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200710002307272.png)

![image-20200710002329477](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200710002329477.png)



##  相关博客

https://zhuanlan.zhihu.com/p/58523066



# dubbo的负载均衡配置

使用Dubbo，我们可以非常方便地做服务的水平扩展，集群部署。

那么问题来了，Dubbo的负载均衡策略是怎么样的？

Dubbo为我们提供了4种策略，默认是随机策略。

4种策略分别如下：

## Random LoadBalance

> **随机**，按权重设置随机概率。
> 在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。

## RoundRobin LoadBalance

> **轮询**，按公约后的权重设置轮询比率。
> 存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。

## LeastActive LoadBalance

> **最少活跃调用数**，相同活跃数的随机，活跃数指调用前后计数差。
> 使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大。

## ConsistentHash LoadBalance

> **一致性 Hash**，相同参数的请求总是发到同一提供者。
> 当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动。
> 算法参见：[http://en.wikipedia.org/wiki/Consistent_hashing](https://link.zhihu.com/?target=http%3A//en.wikipedia.org/wiki/Consistent_hashing)
> 缺省只对第一个参数 Hash，如果要修改，请配置 `<dubbo:parameter key="hash.arguments" value="0,1" />`
> 缺省用 160 份虚拟节点，如果要修改，请配置 `<dubbo:parameter key="hash.nodes" value="320" />`



**实验步骤：**

1，服务提供方开启多个实例

2，消费方通过设置负载均衡策略，重复调用服务接口，观察其执行结果。

3，注意要将服务提供方部署在多个服务器，不同的ip，才可以看到正确的效果

调用方程序：

```java
/**
 * @author huangguizhao
 */
@Controller
@RequestMapping("user")
public class UserController {
    
    @Reference(loadbalance="roundrobin")
    private IUserService userService;

    @RequestMapping("hello")
    @ResponseBody
    public String hello(){
        return userService.hello();
    }

}
```

通过多次访问接口测试接口：

[http://localhost:8080/user/hellolocalhost:8080](https://link.zhihu.com/?target=http%3A//localhost%3A8080/user/hello)

也可以在配置文件统一配置

```yaml
dubbo：
  provider:
    loadbalance: roundrobin
```

之前配置文件的配置方式：

<dubbo:referenceinterface="..."loadbalance="roundrobin" />



# dubbo 的超时容错机制

![image-20200710191718461](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200710191718461.png)

## 1 超时机制是一种保护机制。

> 假设A服务调用B服务，而B服务出现了问题，响应迟缓，这个时候如果没有超时机制，那么A服务会消耗大量的线程去调用B服务，最终可能导致A服务资源耗尽，从而服务A宕机，如果还有其他服务C调用了A服务，则会出现连锁反应，出现服务雪崩的效应。

## 2 集群容错机制

> 是指当A服务调用B服务超时后，Dubbo默认会执行重试的机制，尝试去调用集群的其他机器，默认是重试两次，即加上第一次调用，总共是三次。

## 3 配置

超时机制+容错机制的配置：

1. 支持以服务为单位和以方法为单位进行配置，方法的优先级高于服务
2. 也支持在服务提供方和服务消费方配置，服务消费方优先级高于服务提供方

服务消费方配置

```text
@Reference(timeout = 2000)
private IProductApi productApi;

//配置文件的方式
dubbo
  consumer:
    timeout: 3000
    retries: 6 //重试次数
```

服务提供方配置

```text
@Service(timeout = 2000)

//配置文件的方式
dubbo
  provider:
    timeout: 1000
```

集群容错的模式

可以在 `@Reference(cluster = "failfast")` //快速失败

1，Failover Cluster 默认的模式，失败自动切换，当出现失败，重试其他服务器。 **（适合读操作，类似读取操作）**

通常用于读操作，但重试会带来更长延迟。可设置retries属性，规定重试的次数。下面这种就适合 Failover

![image-20200710194048298](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200710194048298.png)

2，Failfast Cluster 快速失败，只发起一次调用，失败立即报错。 **（适合写操作，如写入数据库）**

通常用于非幂等性的写操作，避免造成重复的数据。下面这张图就适合用 Failfast，这样就不会重复调用

![image-20200710193748306](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200710193748306.png)

![image-20200710225130158](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200710225130158.png)

```text
<dubbo:service cluster="failfast" />
<dubbo:reference cluster="failfast" />
```

**在实际使用时，大多数都是使用 Failover 失败自动切换，当出现失败，重试其他服务器，这种方式。**

但是写入操作时，我们需要将接口改成幂等性接口。

怎么将接口变成幂等的

可以添加唯一性约束（对数据库表进行设置）。

## 结论

**1、一般不轻易改变超时时间**

**2、容错模式一般选择第一种（Failover）**

**3、保证接口的幂等性。**



# Dubbo 的多版本配置

Dubbo提供多版本的配置，方便我们做服务的灰度发布，或者是解决不兼容的问题。

当一个接口实现，出现不兼容升级时，可以用版本号过渡，版本号不同的服务相互间不引用。

可以按照以下的步骤进行版本迁移：

1. 在低压力时间段，先升级一半提供者为新版本
2. 再将所有消费者升级为新版本
3. 然后将剩下的一半提供者升级为新版本

老版本服务提供者配置：

```java
/**
 * @author huangguizhao
 */
@Service(version = "1.0.0")
public class UserServiceImpl implements IUserService{

    @Override
    public String hello() {
        System.out.println("service hello...");
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "hello";
    }
}
```

新版本服务提供者配置：

```java
/**
 * @author huangguizhao
 */
@Service(version = "2.0.0")
public class UserServiceImpl2 implements IUserService{

    @Override
    public String hello() {
        System.out.println("service new hello...");
        return "hello";
    }
}
```

老版本服务消费者配置：

```java
@Reference(loadbalance="roundrobin",cluster = "failover",version = "1.0.0")
private IUserService userService;
```

新版本服务消费者配置：

```java
@Reference(loadbalance="roundrobin",cluster = "failover",version = "2.0.0")
private IUserService userService;
```

如果不需要区分版本，可以按照以下的方式配置 ：

```java
@Reference(loadbalance="roundrobin",cluster = "failover",version = "*")
private IUserService userService;
```



# Dubbo 的本地存根

在服务调用之前做一些处理逻辑。例如参数的判断什么的。

**场景：**

当我们执行远程服务调用时，客户端通常只剩下接口，而实现全在服务器端，但提供方有些时候想在客户端也执行部分逻辑，比如：提前验证参数，调用失败后伪造容错数据等等，此时就需要在 API 中带上 Stub，客户端生成 Proxy 实例，会把 Proxy 通过构造函数传给 Stub，然后把 Stub 暴露给用户，Stub 可以决定要不要去调 Proxy。

![img](https://pic2.zhimg.com/80/v2-5f243f76584c8218b493d2b73db4f58d_720w.jpg)

**开发步骤：**

**1，在我们的dubbo-interface，开发服务对应的本地存根**

```java
/**
 * @author huangguizhao
 */
public class UserServiceStub implements IUserService {

    private final IUserService userService;

    //构造函数传入真正的远程代理对象
    public UserServiceStub(IUserService userService){
        this.userService = userService;
    }

    @Override
    public String hello(String name) {
        if(name == null || "".equals(name)){
            return "validate param";
        }
        return userService.hello(name);
    }
}
```

2，在dubbo-consumer，服务引用中，设置存根

```java
@Reference(stub = "com.hgz.springbootdubboconsumer.stub.UserServiceStub") //调用存根的service
private IUserService userService;
```

3，调用服务，观察执行结果

