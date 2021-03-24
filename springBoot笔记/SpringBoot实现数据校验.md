

[参考博客](https://zhuanlan.zhihu.com/p/60017249)

## **1，为什么要做后端的数据校验**

前端的js校验可以涵盖大部分的校验职责，如用户名唯一性，生日格式，邮箱格式校验等等常用的校验。

但是为了避免用户绕过浏览器，使用http工具直接向后端请求一些违法数据，服务端的数据校验也是必要的，可以防止脏数据落到数据库中

## **2，JSR303/JSR-349，hibernate validation，spring validation之间的关系**

2.1，JSR303是一项标准,JSR-349是其的升级版本，添加了一些新特性，他们规定一些校验规范即校验注解，如@Null，@NotNull，@Pattern，

他们位于javax.validation.constraints包下，只提供规范不提供实现。

2.2，hibernate validation是对这个规范的实践，他提供了相应的实现，并增加了一些其他校验注解，如@Email，@Length，@Range等等，

他们位于org.hibernate.validator.constraints包下。

2.3，spring为了给开发者提供便捷，对hibernate validation进行了二次封装，

显示校验validated bean时，你可以使用spring validation或者hibernate validation，

而spring validation另一个特性，便是其在springmvc模块中添加了自动校验，并将校验信息封装进了特定的类中。

## **3，如何使用**

我们只需要引入web依赖，web模块使用了hibernate-validation，并且databind模块也提供了相应的数据绑定功能。

依赖

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.hibernate.validator</groupId>
            <artifactId>hibernate-validator</artifactId>
        </dependency>
```

**3.1 给实体类添加注解**

```java
public class User {
    @NotNull
    private String name;

    @Pattern(regexp = "^1(3|4|5|7|8)\\d{9}$",message = "手机号码格式错误")
    @NotBlank(message = "手机号码不能为空")
    private String phone;

    @Email(message = "邮箱格式不正确")
    private String email;

    @Min(18)
    private Integer age;
}
```

**3.2 给Controller的方法添加注解@Valid,表示该对象需要进行校验**

，**需要注意的是@Valid 和 BindingResult 是一 一对应的，如果有多个@Valid，那么每个@Valid后面都需要添加BindingResult用于接收bean中的校验信息**

**如果@Valid校验未通过其实会报 BindException的异常**

```java
@RestController
@RequestMapping("user")
public class UserController {

    @PostMapping("add")
    public ResultBean add(@Valid User user,BindingResult bindingResult){
        if(bindingResult.hasErrors()){
            List<FieldError> fieldErrors =
                    bindingResult.getFieldErrors();
            for (FieldError fieldError : fieldErrors) {
                String message = fieldError.getDefaultMessage();
                String field = fieldError.getField();
                //这样就可以罗列出有问题的字段和错误提示信息
                System.out.println(field+":"+message);
            }
            return new ResultBean("400","提交的参数信息有误！");
        }
        return new ResultBean("200","ok");
    }
}
```

## 3.3 添加拦截器，做统一控制（对BindException的异常进行拦截统一处理）

上边每个方法都需要加@Valid 和  BindingResult 太繁琐了。

```java
@ControllerAdvice
public class GlobalExceptionHandler {
    // ResultBean 自定义的实体类，用来储存code和错误提示

    @ExceptionHandler(BindException.class)
    @ResponseBody
    public ResultBean validationErrorHandler(BindException ex) throws JsonProcessingException {
        //1.此处先获取BindingResult
        BindingResult bindingResult = ex.getBindingResult();
        //2.获取错误信息
        List<FieldError> fieldErrors = bindingResult.getFieldErrors();
        //3.组装异常信息
        Map<String,String> message = new HashMap<>();
        for (FieldError fieldError : fieldErrors) {
            message.put(fieldError.getField(),fieldError.getDefaultMessage());
        }
        //4.将map转换为JSON
        ObjectMapper objectMapper = new ObjectMapper();
        String json = objectMapper.writeValueAsString(message);
        //5.返回错误信息
        return new ResultBean("400",json);
    }
}
```

## **4，还有哪些常用注解**

4.1 JSR提供的标准注解

```text
@Null   被注释的元素必须为 null    
@NotNull    被注释的元素必须不为 null    
@AssertTrue     被注释的元素必须为 true    
@AssertFalse    被注释的元素必须为 false    
@Min(value)     被注释的元素必须是一个数字，其值必须大于等于指定的最小值    
@Max(value)     被注释的元素必须是一个数字，其值必须小于等于指定的最大值    
@DecimalMin(value)  被注释的元素必须是一个数字，其值必须大于等于指定的最小值    
@DecimalMax(value)  被注释的元素必须是一个数字，其值必须小于等于指定的最大值    
@Size(max=, min=)   被注释的元素的大小必须在指定的范围内    
@Digits (integer, fraction)     被注释的元素必须是一个数字，其值必须在可接受的范围内    
@Past   被注释的元素必须是一个过去的日期    
@Future     被注释的元素必须是一个将来的日期    
@Pattern(regex=,flag=)  被注释的元素必须符合指定的正则表达式 
```

4.2，Hibernate Validator提供的校验注解

```text
@NotBlank(message =)   验证字符串非null，且长度必须大于0    
@Email  被注释的元素必须是电子邮箱地址    
@Length(min=,max=)  被注释的字符串的大小必须在指定的范围内    
@NotEmpty   被注释的字符串的必须非空    
@Range(min=,max=,message=)  被注释的元素必须在合适的范围内
```