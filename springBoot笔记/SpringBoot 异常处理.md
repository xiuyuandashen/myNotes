# 异常处理

SpringBoot中对异常处理提供了五种处理方式

## 1.1 自定义错误页面

SpringBoot默认的处理异常的机制：SpringBoot默认的已经提供了一套处理异常的机制。一旦程序中出现异常SpringBoot会向`/error`的url发送请求。在SpringBoot中提供了一个名为`BasicErrorController` 来处理`/error`的请求，然后跳转至默认显示异常的页面来展示异常信息。

如果我们需要将所有的异常统一跳转至自定义的错误页面，需要在`resouces/templates` 目录下创建 `error.html` 页面。注意：页面名称必须是`error`。或者可以在`resouces/templates `目录下创建`error`目录，在该目录下创建`404.html`，如果出现了404异常，即跳转至`404.html`。

## 2.Controller层面上异常处理 @ExceptionHandler （只能处理在同个Controller中的异常）

@ExceptionHandler 注解，可以用来统一处理方法抛出的异常

```java
package org.springframework.web.bind.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface ExceptionHandler {
    Class<? extends Throwable>[] value() default {}; 
}
```



### 2.1 修改 Controller

```java
@Controller
public class UserController {

    @RequestMapping("/showInfo")
    public String ShowInfo(){
        String str = null;
        str.length();
        return "ok";

    }
    //当出现空指针异常时，会跳转至templates/err/nullPointerError.html
    @ExceptionHandler(value = {java.lang.NullPointerException.class} )
    public ModelAndView nullponitExceptionHandler(Exception e){
        final ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("err",e.toString());
        modelAndView.setViewName("/err/nullPointerError.html");
        return modelAndView;
    }
}
```



###  2.2 创建页面

![image-20200630134414593](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200630134414593.png)



##  3.通过 @ControllerAdvice+@ExceptionHandler 注解处理异常

在 Controller层面上通过 @ExceptionHandler 异常处理，只能局限在一个Controller中，我们使用 @ControllerAdvice 标注一个处理异常类，这个类就可以全局范围内就行使用了。

```java
/**
 * 全局异常处理类
 */
@ControllerAdvice
public class GlobalException {

    @ExceptionHandler(value = {java.lang.NullPointerException.class} )
    public ModelAndView nullponitExceptionHandler(Exception e){
        final ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("err",e.toString());
        modelAndView.setViewName("/err/nullPointerError.html");
        return modelAndView;
    }

}
```





## 4. 全局级别异常处理器 实现HandlerExceptionResolver接口

```java
public class MyHandlerExceptionResolver implements HandlerExceptionResolver {
 
    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        //可以判断 ex 是什么异常 然后做对应的处理例如 if(ex instanceof ArithmeticException) 
        System.out.println("发生全局异常!");
        ModelMap mmp=new ModelMap();
        mmp.addAttribute("ex",ex.getMessage());
        return new ModelAndView("error",mmp);
    }
 
}
```

使用方式： **只需要将该Bean加入到Spring容器，可以通过Xml配置，也可以通过注解方式加入容器;** 

​       **方法返回值不为null才有意义，如果方法返回值为null，可能异常就没有被捕获.**

缺点分析：比如这种方式全局异常处理返回JSP、velocity等视图比较方便，返回json或者xml等格式的响应就需要自己实现了.如下是我实现的发生全局异常返回JSON的简单例子.



## 5.全局异常  SimpleMappingExceptionResolver 这个好用

在 通过 @ControllerAdvice+@ExceptionHandler 注解处理异常 时，我们在类中可能需要创建好多的方法。

我们用 SimpleMappingExceptionResolver 类 处理异常，可以通过一个方法来处理多个异常。

我们看看源码 

```java
package org.springframework.web.servlet.handler;
public class SimpleMappingExceptionResolver extends AbstractHandlerExceptionResolver {

	/** The default name of the exception attribute: "exception". */
	public static final String DEFAULT_EXCEPTION_ATTRIBUTE = "exception";


	@Nullable
	private Properties exceptionMappings;

	@Nullable
	private Class<?>[] excludedExceptions;

	@Nullable
	private String defaultErrorView;

	@Nullable
	private Integer defaultStatusCode;

	private Map<String, Integer> statusCodes = new HashMap<>();

	@Nullable
	private String exceptionAttribute = DEFAULT_EXCEPTION_ATTRIBUTE;
	
    /**
	  设置异常类名称和错误视图名称之间的映射。异常类名称可以是子字符串，目前不支持通配符。
	  例如，“ ServletException”的值将匹配{@code javax.servlet.ServletException}和子类。
      请仔细考虑模式的具体程度，以及是否包括软件包信息（这不是强制性的）。例如，“异常”将几乎匹配所有内容，并且可能会隐藏其他规	则。如果“异常”旨在为所有检查的异常定义规则，则“java.lang.Exception”将是正确的。使用更常见的异常名称
      （例如“ BaseBusinessException”），就无需使用FQN。 @param映射异常模式（也可以是完全限定的类名称）作为键，而错误视图名称作为值
	 */
	public void setExceptionMappings(Properties mappings) {
		this.exceptionMappings = mappings;
	}
	......
```

 按照springBoot 的配置 javaConfig的方法，我们只需要创建一个类，在类上加上 @Configuration 注解 表示一个Spring配置类。

在类中 加入一个方法，这个方法返回我们的目标对象给我们的spring容器，即在方法上加 @Bean 注解。

```java
@Configuration
public class GlobalException2 {
    /**
     * 此方法返回值必须是  SimpleMappingExceptionResolver 对象 简单映射异常解析器
     *  SimpleMappingExceptionResolver 对象 可以 映射异常 和 页面
     * @return
     */
    @Bean
    public SimpleMappingExceptionResolver getSimpleMappingExceptionResolver(){
        SimpleMappingExceptionResolver resolver = new SimpleMappingExceptionResolver();
        //异常 视图 映射
        Properties exceptionMappings = new Properties();
        /**
         * properties.put("异常类型全名","视图名称")
         */
        exceptionMappings.put("java.lang.NullPointerException","err/nullPointerError");
        exceptionMappings.put("java.lang.ArithmeticException","err/ArithmeticException");
        resolver.setExceptionMappings(exceptionMappings);
        return resolver;
    }
}
```

![image-20200630143047561](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200630143047561.png)





![image-20200630143102002](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200630143102002.png)

![image-20200630143116570](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200630143116570.png)

#  当发生了很严重的error时（核心业务），可以将error通过邮件发送给开发者

