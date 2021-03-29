# Nacos

Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。

Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。 Nacos 是构建以“服务”为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。



# Nacos官方文档

[官方文档](https://nacos.io/zh-cn/docs/what-is-nacos.html)



# 安装nacos

[下载地址](https://github.com/alibaba/nacos/releases)

解压后，打开 /bin/startup.cmd 即可启动nacos。

<img src="./images/startNacos.png" width="100%">

打开 localhost:8848/nacos/index.html 

使用 账号：nacos 密码：nacos 即可登录

<img src="./images/LoginNacos.png" width="100%">

<img src="./images/页面.png" width="100%">



# 服务注册

## 依赖

在service-parent层导入 服务注册 的依赖,这样在该模块底下的子模块都可以用到（version在根模块中定义了版本）这里用的是

`<cloud-alibaba.version>2.1.0.RELEASE</cloud-alibaba.version>`

```xml
<!--    服务注册    -->
<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

## 配置

在要使用nacos的service的application.yaml 中配置 服务发现的配置，这样该服务就可以找到 注册中心 的

```yaml
Spring:
  application:
    name: service-edu
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 # nacos 服务地址
```



在要注册的服务的启动类上 添加 Spring Cloud 原生注解 @EnableDiscoveryClient 开启服务注册发现功能

```java
@SpringBootApplication
//这样她就会扫描有引入包的为com.zlf.* 的 组件 比如 common下的service-base的配置他的包前缀也是com.zlf 当他引入时就会扫描到
@ComponentScan(basePackages = {"com.zlf"}) //不加这个只能扫描 EduApplication 所在包以及之下的所有配置
@EnableDiscoveryClient // 这样就有了服务注册的能力了 通过 Spring Cloud 原生注解 @EnableDiscoveryClient 开启服务注册发现功能
public class EduApplication {
    public static void main(String[] args) {
        SpringApplication.run(EduApplication.class,args);
    }
}
```



然后启动该服务，即可载nacos网站上看到该服务了

<img src="./images/服务注册.png" width="100%">

<img src="./images/服务注册2.png" width="100%">