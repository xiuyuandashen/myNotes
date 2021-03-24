# Spring Data

> 对于数据访问层，无论是SQL(关系型数据库)还是NOSQL(非关系型数据库)，Spring Boot 底层都是采用Spring Data 的方式进行统一处理。

> Spring Data 也是Spring中与Spring Boot ，Spring Cloud 等其名的知名项目。



| `spring-boot-starter-data-cassandra`          | Starter for using Cassandra distributed database and Spring Data Cassandra |
| --------------------------------------------- | ------------------------------------------------------------ |
| `spring-boot-starter-data-cassandra-reactive` | Starter for using Cassandra distributed database and Spring Data Cassandra Reactive |
| `spring-boot-starter-data-couchbase`          | Starter for using Couchbase document-oriented database and Spring Data Couchbase |
| `spring-boot-starter-data-couchbase-reactive` | Starter for using Couchbase document-oriented database and Spring Data Couchbase Reactive |
| `spring-boot-starter-data-elasticsearch`      | Starter for using Elasticsearch search and analytics engine and Spring Data Elasticsearch |
| `spring-boot-starter-data-jdbc`               | Starter for using Spring Data JDBC                           |
| `spring-boot-starter-data-jpa`                | Starter for using Spring Data JPA with Hibernate             |
| `spring-boot-starter-data-ldap`               | Starter for using Spring Data LDAP                           |
| `spring-boot-starter-data-mongodb`            | Starter for using MongoDB document-oriented database and Spring Data MongoDB |
| `spring-boot-starter-data-mongodb-reactive`   | Starter for using MongoDB document-oriented database and Spring Data MongoDB Reactive |
| `spring-boot-starter-data-neo4j`              | Starter for using Neo4j graph database and Spring Data Neo4j |
| `spring-boot-starter-data-r2dbc`              | Starter for using Spring Data R2DBC                          |
| `spring-boot-starter-data-redis`              | Starter for using Redis key-value data store with Spring Data Redis and the Lettuce client |
| `spring-boot-starter-data-redis-reactive`     | Starter for using Redis key-value data store with Spring Data Redis reactive and the Lettuce client |
| `spring-boot-starter-data-rest`               | Starter for exposing Spring Data repositories over REST using Spring Data REST |
| `spring-boot-starter-data-solr`               | Starter for using the Apache Solr search platform with Spring Data Solr |

##  springBoot 默认数据源

```yaml
spring:
  datasource:
    username: root
    password: feng10.10
    url: jdbc:mysql://localhost:3306/zlfsql?characterEncoding=utf8&serverTimezone=UTC
    driver-class-name: com.mysql.cj.jdbc.Driver

```

```java
@SpringBootTest
class SpringdataApplicationTests {

    @Autowired
    DataSource dataSource;

    @Test
    void contextLoads() throws SQLException {
		//class com.zaxxer.hikari.HikariDataSource
        System.out.println(dataSource.getClass());
        final Connection connection = dataSource.getConnection();
        System.out.println(connection);
        connection.close();
    }

}
```

`class com.zaxxer.hikari.HikariDataSource` 默认数据源

简单查询一下

```java
@Controller
public class UserController {
    @Autowired
    JdbcTemplate jdbcTemplate;// package org.springframework.jdbc.core

    @RequestMapping("/userList")
    @ResponseBody
    public  List<Map<String , Object>> userList(){

        String sql = "select *from user";
        final List<Map<String, Object>> mapList = jdbcTemplate.queryForList(sql);
        return mapList;
    }
}
```

![image-20200622164223951](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200622164223951.png)

数据源自动配置类 `DataSourceAutoConfiguration`

```java
/**
 * {@link EnableAutoConfiguration Auto-configuration} for {@link DataSource}.
 *
 * @author Dave Syer
 * @author Phillip Webb
 * @author Stephane Nicoll
 * @author Kazuki Shimizu
 * @since 1.0.0
 */
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass({ DataSource.class, EmbeddedDatabaseType.class })
@ConditionalOnMissingBean(type = "io.r2dbc.spi.ConnectionFactory")
@EnableConfigurationProperties(DataSourceProperties.class)
@Import({ DataSourcePoolMetadataProvidersConfiguration.class, DataSourceInitializationConfiguration.class })
public class DataSourceAutoConfiguration {

	@Configuration(proxyBeanMethods = false)
	@Conditional(EmbeddedDatabaseCondition.class)
	@ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
	@Import(EmbeddedDataSourceConfiguration.class)
	protected static class EmbeddedDatabaseConfiguration {

	}

	@Configuration(proxyBeanMethods = false)
	@Conditional(PooledDataSourceCondition.class)
	@ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
    // 默认有5种数据源
	@Import({ DataSourceConfiguration.Hikari.class, DataSourceConfiguration.Tomcat.class,
			DataSourceConfiguration.Dbcp2.class, DataSourceConfiguration.Generic.class,
			DataSourceJmxConfiguration.class })
	protected static class PooledDataSourceConfiguration { //池数据源配置

	}

	/**
	 * {@link AnyNestedCondition} that checks that either {@code spring.datasource.type}
	 * is set or {@link PooledDataSourceAvailableCondition} applies.
	 */
    //汇总数据源条件
	static class PooledDataSourceCondition extends AnyNestedCondition {

		PooledDataSourceCondition() {
			super(ConfigurationPhase.PARSE_CONFIGURATION);
		}

		@ConditionalOnProperty(prefix = "spring.datasource", name = "type") //数据源类型
		static class ExplicitType {

		}

		@Conditional(PooledDataSourceAvailableCondition.class)
		static class PooledDataSourceAvailable {

		}

	}

	/**
	 * {@link Condition} to test if a supported connection pool is available.
	 */
    //合并数据源可用条件
	static class PooledDataSourceAvailableCondition extends SpringBootCondition {

		@Override
		public ConditionOutcome getMatchOutcome(ConditionContext context, AnnotatedTypeMetadata metadata) {
			ConditionMessage.Builder message = ConditionMessage.forCondition("PooledDataSource");
			if (DataSourceBuilder.findType(context.getClassLoader()) != null) {
				return ConditionOutcome.match(message.foundExactly("supported DataSource"));
			}
			return ConditionOutcome.noMatch(message.didNotFind("supported DataSource").atAll());
		}

	}

	/**
	 * {@link Condition} to detect when an embedded {@link DataSource} type can be used.
	 * If a pooled {@link DataSource} is available, it will always be preferred to an
	 * {@code EmbeddedDatabase}.
	 */
    //嵌入式数据库条件
	static class EmbeddedDatabaseCondition extends SpringBootCondition {

		private static final String DATASOURCE_URL_PROPERTY = "spring.datasource.url"; //就是yaml配置中的数据

		private final SpringBootCondition pooledCondition = new PooledDataSourceCondition();

		@Override
		public ConditionOutcome getMatchOutcome(ConditionContext context, AnnotatedTypeMetadata metadata) {
			ConditionMessage.Builder message = ConditionMessage.forCondition("EmbeddedDataSource");
			if (hasDataSourceUrlProperty(context)) {
				return ConditionOutcome.noMatch(message.because(DATASOURCE_URL_PROPERTY + " is set"));
			}
			if (anyMatches(context, metadata, this.pooledCondition)) {
				return ConditionOutcome.noMatch(message.foundExactly("supported pooled data source"));
			}
			EmbeddedDatabaseType type = EmbeddedDatabaseConnection.get(context.getClassLoader()).getType();
			if (type == null) {
				return ConditionOutcome.noMatch(message.didNotFind("embedded database").atAll());
			}
			return ConditionOutcome.match(message.found("embedded database").items(type));
		}

		private boolean hasDataSourceUrlProperty(ConditionContext context) {
			Environment environment = context.getEnvironment();
			if (environment.containsProperty(DATASOURCE_URL_PROPERTY)) {
				try {
					return StringUtils.hasText(environment.getProperty(DATASOURCE_URL_PROPERTY));
				}
				catch (IllegalArgumentException ex) {
					// Ignore unresolvable placeholder errors
				}
			}
			return false;
		}

	}

}

```



## Druid数据源

[Druid Maven依赖](https://mvnnrepository.com/artifact/com.alibaba/druid)

[Druid github 首页](https://github.com/alibaba/druid/wiki/首页)

我们在application.yaml中指定数据源为`com.alibaba.druid.pool.DruidDataSource `

```yaml
spring:
  datasource:
    username: root
    password: feng10.10
    url: jdbc:mysql://localhost:3306/zlfsql?characterEncoding=utf8&serverTimezone=UTC
    driver-class-name: com.mysql.cj.jdbc.Driver
    ## 指定数据源为druid
    type: com.alibaba.druid.pool.DruidDataSource 
    
    # 下面为连接池的补充设置，应用到上面所有数据源中
    # 初始化大小，最小，最大
    initialSize: 5
    minIdle: 5
    maxActive: 20
    # 配置获取连接等待超时的时间
    maxWait: 60000
    # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
    timeBetweenEvictionRunsMillis: 60000
    # 配置一个连接在池中最小生存的时间，单位是毫秒
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    # 打开PSCache，并且指定每个连接上PSCache的大小
    poolPreparedStatements: true
    maxPoolPreparedStatementPerConnectionSize: 25
    # 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
    filters: stat,wall,logback
    # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
    # 合并多个DruidDataSource的监控数据
    useGlobalDataSourceStat: true

```

而在默认的配置类中 ，没有连接池的补充设置！`DataSourceProperties`配置类中没有druid连接池的一些配置

```java
@ConfigurationProperties(prefix = "spring.datasource") 
public class DataSourceProperties implements BeanClassLoaderAware, InitializingBean {

	private ClassLoader classLoader;

	/**
	 * Name of the datasource. Default to "testdb" when using an embedded database.
	 */
	private String name;

	/**
	 * Whether to generate a random datasource name.
	 */
	private boolean generateUniqueName = true;

	/**
	 * Fully qualified name of the connection pool implementation to use. By default, it
	 * is auto-detected from the classpath.
	 */
	private Class<? extends DataSource> type;

	/**
	 * Fully qualified name of the JDBC driver. Auto-detected based on the URL by default.
	 */
	private String driverClassName;

	/**
	 * JDBC URL of the database.
	 */
	private String url;

	/**
	 * Login username of the database.
	 */
	private String username;

	/**
	 * Login password of the database.
	 */
	private String password;

	/**
	 * JNDI location of the datasource. Class, url, username and password are ignored when
	 * set.
	 */
	private String jndiName;

	/**
	 * Initialize the datasource with available DDL and DML scripts.
	 */
	private DataSourceInitializationMode initializationMode = DataSourceInitializationMode.EMBEDDED;

	/**
	 * Platform to use in the DDL or DML scripts (such as schema-${platform}.sql or
	 * data-${platform}.sql).
	 */
	private String platform = "all";

	/**
	 * Schema (DDL) script resource references.
	 */
	private List<String> schema;

	/**
	 * Username of the database to execute DDL scripts (if different).
	 */
	private String schemaUsername;

	/**
	 * Password of the database to execute DDL scripts (if different).
	 */
	private String schemaPassword;

	/**
	 * Data (DML) script resource references.
	 */
	private List<String> data;

	/**
	 * Username of the database to execute DML scripts (if different).
	 */
	private String dataUsername;

	/**
	 * Password of the database to execute DML scripts (if different).
	 */
	private String dataPassword;

	/**
	 * Whether to stop if an error occurs while initializing the database.
	 */
	private boolean continueOnError = false;

	/**
	 * Statement separator in SQL initialization scripts.
	 */
	private String separator = ";";

	/**
	 * SQL scripts encoding.
	 */
	private Charset sqlScriptEncoding;

	private EmbeddedDatabaseConnection embeddedDatabaseConnection = EmbeddedDatabaseConnection.NONE;

	private Xa xa = new Xa();

	private String uniqueName;

```

我们可以写一个配置类 ， 这个方式就相当于，把我们上面的这个 **DataSourceProperties**配置类，换成了 **DruidDataSource**类

```java
@Configuration
public class DruidConfig {
	//将yaml中的spring.datasource中的属性注入到 DruidDataSource类中，然后加入bean
    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource dataSource(){
        return new DruidDataSource();
    }
}
```

###  Druid后台监控

```java
@Configuration
public class DruidConfig {

    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource dataSource(){
        return new DruidDataSource();
    }

    /**
     * 配置Druid监控
     *
     * @return StatViewServlet
     */
    @Bean
    public ServletRegistrationBean servletRegistrationBean() {
        ServletRegistrationBean<StatViewServlet> bean = new ServletRegistrationBean<>(new StatViewServlet(), "/druid/*");
        Map<String, String> map = new HashMap<>();
        //访问的用户名密码
        map.put(StatViewServlet.PARAM_NAME_USERNAME, "xiuyuandashen");
        map.put(StatViewServlet.PARAM_NAME_PASSWORD, "123456");
        //允许访问的ip，默认是所有ip
        map.put(StatViewServlet.PARAM_NAME_ALLOW, "");
        //禁止访问的ip
        map.put(StatViewServlet.PARAM_NAME_DENY, "192.168.1.1");
        bean.setInitParameters(map);
        return bean;
    }

    /**
     * 配置一个监控的filter
     *
     * @return WebStatFilter
     */
    @Bean
    public FilterRegistrationBean filterRegistrationBean() {
        FilterRegistrationBean<WebStatFilter> bean = new FilterRegistrationBean<>();
        bean.setFilter(new WebStatFilter());
        Map<String, String> map = new HashMap<>();
        //移除这些监听
        map.put(WebStatFilter.PARAM_NAME_EXCLUSIONS, "*.js,*.css,/druid/*,*.gif,*.jpg,*.png");
        bean.setInitParameters(map);
        //拦截所有请求，全部都要走druid监听
        bean.setUrlPatterns(Collections.singletonList("/*"));
        return bean;
    }

}
```

![image-20200623084848976](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200623084848976.png)

![image-20200623084913497](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200623084913497.png)

## 整合Mybatis

```xml
<!-- 整合Mybatis 和 SpringBoot-->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
```

Mybatis的属性配置类

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package org.mybatis.spring.boot.autoconfigure;

import java.io.IOException;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Properties;
import org.apache.ibatis.session.Configuration;
import org.apache.ibatis.session.ExecutorType;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.context.properties.NestedConfigurationProperty;
import org.springframework.core.io.Resource;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import org.springframework.core.io.support.ResourcePatternResolver;

@ConfigurationProperties(
    prefix = "mybatis"
)
public class MybatisProperties {
    public static final String MYBATIS_PREFIX = "mybatis";
    private String configLocation; //配置位置
    private String[] mapperLocations; //映射器位置
    private String typeAliasesPackage; //类型别名包
    private String typeHandlersPackage; //类型处理程序包
    private boolean checkConfigLocation = false; //检查配置位置
    private ExecutorType executorType; //执行者类型
    private Properties configurationProperties; //配置属性
    @NestedConfigurationProperty 
    private Configuration configuration; //配置

    public MybatisProperties() {
    }

    public String getConfigLocation() {
        return this.configLocation;
    }

    public void setConfigLocation(String configLocation) {
        this.configLocation = configLocation;
    }

    /** @deprecated */
    @Deprecated
    public String getConfig() {
        return this.configLocation;
    }

    /** @deprecated */
    @Deprecated
    public void setConfig(String config) {
        this.configLocation = config;
    }

    public String[] getMapperLocations() {
        return this.mapperLocations;
    }

    public void setMapperLocations(String[] mapperLocations) {
        this.mapperLocations = mapperLocations;
    }

    public String getTypeHandlersPackage() {
        return this.typeHandlersPackage;
    }

    public void setTypeHandlersPackage(String typeHandlersPackage) {
        this.typeHandlersPackage = typeHandlersPackage;
    }

    public String getTypeAliasesPackage() {
        return this.typeAliasesPackage;
    }

    public void setTypeAliasesPackage(String typeAliasesPackage) {
        this.typeAliasesPackage = typeAliasesPackage;
    }

    public boolean isCheckConfigLocation() {
        return this.checkConfigLocation;
    }

    public void setCheckConfigLocation(boolean checkConfigLocation) {
        this.checkConfigLocation = checkConfigLocation;
    }

    public ExecutorType getExecutorType() {
        return this.executorType;
    }

    public void setExecutorType(ExecutorType executorType) {
        this.executorType = executorType;
    }

    public Properties getConfigurationProperties() {
        return this.configurationProperties;
    }

    public void setConfigurationProperties(Properties configurationProperties) {
        this.configurationProperties = configurationProperties;
    }

    public Configuration getConfiguration() {
        return this.configuration;
    }

    public void setConfiguration(Configuration configuration) {
        this.configuration = configuration;
    }

    public Resource[] resolveMapperLocations() {
        ResourcePatternResolver resourceResolver = new PathMatchingResourcePatternResolver();
        List<Resource> resources = new ArrayList();
        if (this.mapperLocations != null) {
            String[] var3 = this.mapperLocations;
            int var4 = var3.length;

            for(int var5 = 0; var5 < var4; ++var5) {
                String mapperLocation = var3[var5];

                try {
                    Resource[] mappers = resourceResolver.getResources(mapperLocation);
                    resources.addAll(Arrays.asList(mappers));
                } catch (IOException var8) {
                }
            }
        }

        return (Resource[])resources.toArray(new Resource[resources.size()]);
    }
}
```



一个简单的配置

```yaml
spring:
  datasource:
    username: root
    password: feng10.10
    url: jdbc:mysql://localhost:3306/zlfsql?characterEncoding=utf8&serverTimezone=UTC
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource

    # 下面为连接池的补充设置，应用到上面所有数据源中
    # 初始化大小，最小，最大
    initialSize: 5
    minIdle: 5
    maxActive: 20
    # 配置获取连接等待超时的时间
    maxWait: 60000
    # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
    timeBetweenEvictionRunsMillis: 60000
    # 配置一个连接在池中最小生存的时间，单位是毫秒
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    # 打开PSCache，并且指定每个连接上PSCache的大小
    poolPreparedStatements: true
    maxPoolPreparedStatementPerConnectionSize: 25
    # 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
    filters: stat,wall,logback
    # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
    # 合并多个DruidDataSource的监控数据
    useGlobalDataSourceStat: true


mybatis:
  # 映射器位置
  mapper-locations: classpath:mybatis/mapper/*.xml
  # 实体类包的别名
  type-aliases-package: com.zlf.springdata.entity
  # 配置位置
  #config-location:
  #
```

**Mapper**

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.zlf.springdata.Dao.UserDao">
    <select id="queryUserList" resultType="User">
        select *from user;
    </select>
</mapper>
```

Dao

```java
package com.zlf.springdata.Dao;

import com.zlf.springdata.entity.User;
import org.apache.ibatis.annotations.Mapper;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
@Mapper //让springboot可以扫描到它 也可以在 springboot程序入口处 @MapperScan("com.zlf.springdata.Dao") 如下
public interface UserDao {

    List<User> queryUserList();
    
}
```

```java
@SpringBootApplication
@MapperScan("com.zlf.springdata.Dao")
public class SpringdataApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringdataApplication.class, args);
    }

}
```

**Controller**

```java
@Controller
public class UserController {


    @Autowired
    private UserDao userDao;

    @RequestMapping("/userList")
    @ResponseBody
    public  List<User> userList(){
        final List<User> users = userDao.queryUserList();
        return users;

    }
}
```

## 事务

**@Transactional注解**

*@Transactional属性*

 

| 属性                   | 类型                               | 描述                                   |
| ---------------------- | ---------------------------------- | -------------------------------------- |
| value                  | String                             | 可选的限定描述符，指定使用的事务管理器 |
| propagation            | enum: Propagation                  | 可选的事务传播行为设置                 |
| isolation              | enum: Isolation                    | 可选的事务隔离级别设置                 |
| readOnly               | boolean                            | 读写或只读事务，默认读写               |
| timeout                | int (in seconds granularity)       | 事务超时时间设置                       |
| rollbackFor            | Class对象数组，必须继承自Throwable | 导致事务回滚的异常类数组               |
| rollbackForClassName   | 类名数组，必须继承自Throwable      | 导致事务回滚的异常类名字数组           |
| noRollbackFor          | Class对象数组，必须继承自Throwable | 不会导致事务回滚的异常类数组           |
| noRollbackForClassName | 类名数组，必须继承自Throwable      | 不会导致事务回滚的异常类名字数组       |

*用法*

@Transactional 可以作用于接口、接口方法、类以及类方法上。当作用于类上时，该类的所有 public 方法将都具有该类型的事务属性，同时，我们也可以在方法级别使用该标注来覆盖类级别的定义。

虽然 @Transactional 注解可以作用于接口、接口方法、类以及类方法上，但是 Spring 建议不要在接口或者接口方法上使用该注解，因为这只有在使用基于接口的代理时它才会生效。另外， @Transactional 注解应该只被应用到 public 方法上，这是由 Spring AOP 的本质决定的。如果你在 protected、private 或者默认可见性的方法上使用 @Transactional 注解，这将被忽略，也不会抛出任何异常。

默认情况下，只有来自外部的方法调用才会被AOP代理捕获，也就是，类内部方法调用本类内部的其他方法并不会引起事务行为，即使被调用方法使用@Transactional注解进行修饰。