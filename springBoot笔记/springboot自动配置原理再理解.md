#  springboot自动配置

## application.yaml如何写入配置

每个xxxAutoConfiguration 都是容器中的一个组件，最后都要加入到容器中；用他们来做自动配置。

每个自动配置类都可以进行自动装配功能，因为有`@EnableConfigurationProperties({xxxProperties.class})`注解来自动配置属性。

一但这个配置类生效；这个配置类就会给容器中添加各种组件；这些组件的属性是从对应的Properties类中获取的，这些Properties类里面的每个属性又和配置文件绑定的(通过`@ConfigurationProperties(prefix = "xxx")`从application.yaml中获取）。

所有在配置文件中能配置的属性都是在xxxProperties类中封装的属性；配置文件能配置什么就可以参照某个功能对应的xxxProperties类。

例如：

**这是一个自动配置类**

```java
import ...

//表示这是一个配置类
@Configuration(
        proxyBeanMethods = false
)
//自动配置属性：ServerProperties
@EnableConfigurationProperties({ServerProperties.class})

//Spring的底层注解：根据不同的条件，来判断当前配置或者类是否生效
@ConditionalOnWebApplication(
        type = Type.SERVLET
)
@ConditionalOnClass({CharacterEncodingFilter.class})
@ConditionalOnProperty(
        prefix = "server.servlet.encoding",
        value = {"enabled"},
        matchIfMissing = true
)
public class HttpEncodingAutoConfiguration {
    private final Encoding properties;

    public HttpEncodingAutoConfiguration(ServerProperties properties) {
        this.properties = properties.getServlet().getEncoding();
    }

    @Bean
    @ConditionalOnMissingBean
    public CharacterEncodingFilter characterEncodingFilter() {
        CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
        filter.setEncoding(this.properties.getCharset().name());
        filter.setForceRequestEncoding(this.properties.shouldForce(org.springframework.boot.web.servlet.server.Encoding.Type.REQUEST));
        filter.setForceResponseEncoding(this.properties.shouldForce(org.springframework.boot.web.servlet.server.Encoding.Type.RESPONSE));
        return filter;
    }

    @Bean
    public HttpEncodingAutoConfiguration.LocaleCharsetMappingsCustomizer localeCharsetMappingsCustomizer() {
        return new HttpEncodingAutoConfiguration.LocaleCharsetMappingsCustomizer(this.properties);
    }

    static class LocaleCharsetMappingsCustomizer implements WebServerFactoryCustomizer<ConfigurableServletWebServerFactory>, Ordered {
        private final Encoding properties;

        LocaleCharsetMappingsCustomizer(Encoding properties) {
            this.properties = properties;
        }

        public void customize(ConfigurableServletWebServerFactory factory) {
            if (this.properties.getMapping() != null) {
                factory.setLocaleCharsetMappings(this.properties.getMapping());
            }

        }

        public int getOrder() {
            return 0;
        }
    }
}

```

**对应的Properties属性类**（由于代码过多，仅展示一小部分）

```java
//这个就是从application.yaml中映射属性值到该类中
@ConfigurationProperties(
    prefix = "server",
    ignoreUnknownFields = true
)
public class ServerProperties {
    private Integer port;
    private InetAddress address;
    @NestedConfigurationProperty
    private final ErrorProperties error = new ErrorProperties();
    private ServerProperties.ForwardHeadersStrategy forwardHeadersStrategy;
    private String serverHeader;
    private DataSize maxHttpHeaderSize = DataSize.ofKilobytes(8L);
    private Shutdown shutdown;
    @NestedConfigurationProperty
    private Ssl ssl;
    @NestedConfigurationProperty
    private final Compression compression;
    @NestedConfigurationProperty
    private final Http2 http2;
    private final ServerProperties.Servlet servlet;
    private final ServerProperties.Tomcat tomcat;
    private final ServerProperties.Jetty jetty;
    private final ServerProperties.Netty netty;
    private final ServerProperties.Undertow undertow;

    public ServerProperties() {
        this.shutdown = Shutdown.IMMEDIATE;
        this.compression = new Compression();
        this.http2 = new Http2();
        this.servlet = new ServerProperties.Servlet();
        this.tomcat = new ServerProperties.Tomcat();
        this.jetty = new ServerProperties.Jetty();
        this.netty = new ServerProperties.Netty();
        this.undertow = new ServerProperties.Undertow();
    }
... ...
```

```yaml
server:
  port: 1314
```

上边application.yaml中相当于设置了`ServerProperties`类中的`port`属性为`1314`。



![image-20200617224614852](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200617224614852.png)



## 总结

1. SpringBoot启动会加载大量的自动配置类

2. 我们看我们需要的功能有没有在springboot默认写好的自动配置类中

3. 我们再来看这个自动配置类中到底配置的哪些组件；（只要我们要用的组件存在其中，我们就不需要再手动配置了）

4. 给容器中自动配置类添加组件时，就会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可。

   xxxAutoConfiguration：自动配置类；给容器中添加组件。

   xxxProperties：封装配置文件中的相关的属性。

```yaml
server:
  port: 1314
# debug设置为true，将在控制台显示springboot自动装配的日志 用来查看哪些自动配置类生效了，哪些没有生效。
debug: true
```

![image-20200617232905078](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200617232905078.png)

![image-20200617232945446](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200617232945446.png)