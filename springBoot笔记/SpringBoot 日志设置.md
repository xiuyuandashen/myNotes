#  日志

**SpringBoot默认整合的日志框架是slf4j+logback**

**slf4j是对众多日志框架抽取一个标准，而logback是一种实现。**

![image-20200708201905814](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200708201905814.png)

**springboot中推荐用的就是logback。**



## 日志的基础配置设置



```yaml
logging:
  file:
    name: e://logs/all.log # 日志文件名称（例如，`myapp.log`）。 名称可以是当前目录的确切位置或相对。
  level:  #日志级别 debug/info/warn/error 默认未info 日志级别严重性映射
    root: info # root 设置全局的输入级别，root代表全局
```

![image-20200708201955115](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200708201955115.png)



## 更精细的化控制日志的输出

比如每天一个日志文件，最多保存30个日志文件.

**我们需要个logback.xml（名称固定）的配置文件来代替application.yaml/properties的配置。**

- 直接把logback.xml导入到resouces中

- 然后将application.yaml/properties 关于日志的配置注释

- SpringBoot会自动采用logback.xml中的配置

- logback.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <configuration>
      <!--1，定义日志保存的路径-->
      <property name="LOG_HOME" value="e://logs//" />
      <!--2，定义一个控制台输出器，名为console-->
      <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
          <!--按pattern指定的格式输出日志，编码为UTF-8-->
          <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
              <pattern>%d{yyyy-MM-dd HH:mm:ss} %-5level [%thread]  %logger{30} - %msg%n</pattern>
              <charset>UTF-8</charset>
          </encoder>
      </appender>
      <!--3，定义一个日滚动的日志文件-->
      <appender name="file" class="ch.qos.logback.core.rolling.RollingFileAppender">
          <!--按pattern指定的格式输出日志，编码为UTF-8-->
          <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
              <pattern>%d{yyyy-MM-dd HH:mm:ss} %-5level [%thread]  %logger{30} - %msg%n</pattern>
              <charset>UTF-8</charset>
          </encoder>
          <!-- 定义保存的文件名 -->
          <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
              <fileNamePattern>${LOG_HOME}/sprintboot_%d{yyyy-MM-dd}.log</fileNamePattern>
              <!--日志最多保存30天-->
              <maxHistory>30</maxHistory>
          </rollingPolicy>
      </appender>
      <!-- 定义日志全局最低输出级别是INFO，同时向控制台和日滚动文件输出 -->
      <root level="INFO">
          <appender-ref ref="console" />
          <appender-ref ref="file" />
      </root>
  </configuration>
  ```

  

![image-20200708203157462](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200708203157462.png)



[大佬的博客](https://zhuanlan.zhihu.com/p/58411217)