# Swagger

##  Swagger 配置

[查看别人的博客](https://blog.csdn.net/u012702547/article/details/88775298)

Swagger 的访问地址是 /swagger-ui.html

```java
/**
 * Swagger2 配置
 */
@Configuration
@EnableSwagger2
public class SwaggerConfig {

   	//配置了Swagger 的Docket 的Bean实例
    @Bean
    public Docket docket1(){

        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())//作者信息
                .enable(true) //enable 是否启动Swagger ，若为false 则不能在浏览器访问 Swagger
                .pathMapping("/")
                .select()
                //RequestHandlerSelectors,配置扫描接口的方式
                    //basePackage ：指定要扫描的包
                    // any  ：扫描全部
                    //none  ：不扫描
                    //withClassAnnotation ：扫描类上的注解，参数是一个注解的反射对象 例如 RestController.class
                    //withMethodAnnotation ：扫描方法上的注解 例如 GetMapping.class
                .apis(RequestHandlerSelectors.basePackage("com.controller"))
                //paths() 过滤什么路径
                .build(); //建立

    }

    //配置Swapper信息
    public ApiInfo apiInfo(){
        //作者的信息
        final Contact contact = new Contact("xiuyuan","作者的网页","1556450877@qq.com");
        return new ApiInfo("Swagger入门"
                ,"学习SpringBoot"
                ,"v1.0"
                ,"http://www.baidu.com"
                ,contact
                ,"Apache 2.0"
                , "...."
                ,new ArrayList<>());
    }
}
```



协同开发，Swapper分组

创建新的Docket  Bean实例，并且设定组名 groupName(),

```java
/**
 * Swagger2 配置
 */
@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket docket2(){
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("A");
    }
    @Bean
    public Docket docket3(){
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("B");
    }
    @Bean
    public Docket docket4(){
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("C");
    }
    //配置了Swagger 的Docket 的Bean实例
    @Bean
    public Docket docket1(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())//作者信息
                .groupName("秀元")
                .enable(true) //enable 是否启动Swagger ，若为false 则不能在浏览器访问 Swagger
                .pathMapping("/")
                .select()
                //RequestHandlerSelectors,配置扫描接口的方式
                    //basePackage ：指定要扫描的包
                    // any  ：扫描全部
                    //none  ：不扫描
                    //withClassAnnotation ：扫描类上的注解，参数是一个注解的反射对象 例如 RestController.class
                    //withMethodAnnotation ：扫描方法上的注解 例如 GetMapping.class
                .apis(RequestHandlerSelectors.basePackage("com.controller"))
                //paths() 过滤什么路径
                .build(); //建立

    }

    //配置Swapper信息
    public ApiInfo apiInfo(){
        //作者的信息
        final Contact contact = new Contact("xiuyuan","作者的网页","1556450877@qq.com");
        return new ApiInfo("Swagger入门"
                ,"学习SpringBoot"
                ,"v1.0"
                ,"http://www.baidu.com"
                ,contact
                ,"Apache 2.0"
                , "...."
                ,new ArrayList<>());
    }
}
```

![image-20200630201035189](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200630201035189.png)



## 接口注解

@Api  注解可以用来标记当前Controller的功能。
@ApiOperation 注解用来标记一个方法的作用。
@ApiImplicitParam 注解用来描述一个参数，可以配置参数的中文含义，也可以给参数设置默认值，这样在接口测试的时候可以避免手动输入。
如果有多个参数，则需要使用多个 @ApiImplicitParam 注解来描述，多个 @ApiImplicitParam 注解需要放在一个 @ApiImplicitParams注解中。
需要注意的是，@ApiImplicitParam 注解中虽然可以指定参数是必填的，但是却不能代替@RequestParam(required = true)，前者的必填只是在Swagger2框架内必填，抛弃了Swagger2，这个限制就没用了，所以假如开发者需要指定一个参数必填，@RequestParam(required = true)注解还是不能省略。
如果参数是一个对象（例如上文的更新接口），对于参数的描述也可以放在实体类中。

```java
@ApiModel 
public class User {
    @ApiModelProperty(value = "姓名")
    private String name;
    @ApiModelProperty(value = "密码")
    private String password;
    @ApiModelProperty(value = "年龄")
    private Integer age;
    //getter/setter 必须得有 get 和set方法 不然无法显示用户的信息
}

```

![image-20200630202347363](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200630202347363.png)