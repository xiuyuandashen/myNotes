#  Shiro 整合 SpringBoot

##  shiro主要有三大功能模块

1. Subject：主体，一般指用户。

2. SecurityManager：安全管理器，管理所有Subject，可以配合内部安全组件。(类似于SpringMVC中的DispatcherServlet)

3. Realms：用于进行权限信息的验证，一般需要自己实现。

**shiro架构图**

![image-20200629142750768](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200629142750768.png)

```
Subject 用户
SecurityManager 管理所有用户
Realm   连接数据
```

## Shiro入门

### Shiro 配置

按步骤：

1. 先创建一个 Realms 类 继承自 AuthorizingRealm 类 实现它两个方法 授权 `doGetAuthorizationInfo`  和 认证 `doGetAuthenticationInfo`

   ```java
   import com.entity.User;
   import com.service.impl.UserServiceImpl;
   import org.apache.shiro.SecurityUtils;
   import org.apache.shiro.authc.*;
   import org.apache.shiro.authz.AuthorizationInfo;
   import org.apache.shiro.authz.SimpleAuthorizationInfo;
   import org.apache.shiro.realm.AuthorizingRealm;
   import org.apache.shiro.subject.PrincipalCollection;
   import org.apache.shiro.subject.Subject;
   import org.springframework.beans.factory.annotation.Autowired;
   
   //自定义的UserRealm
   public class UserRealm  extends AuthorizingRealm {
   
       @Autowired
       UserServiceImpl userService;
       //授权
       @Override
       protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
           System.out.println("执行了==>  授权");
           // 创建 简单授权信息类
           final SimpleAuthorizationInfo  authorizationInfo= new SimpleAuthorizationInfo();
   
           //获得当前的用户 当前的用户首先得被存入  简单身份验证信息类 构造函数的第一个参数中
           final Subject subject = SecurityUtils.getSubject();
           User currentUser = (User)subject.getPrincipal();
           //添加当前用户 字符串权限
           authorizationInfo.addStringPermission(currentUser.getPerms());
           return authorizationInfo;
       }
   
       //认证
       @Override
       protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
           System.out.println("执行了==> 认证 ");
           UsernamePasswordToken token  =  (UsernamePasswordToken)authenticationToken;
           // 用户名，密码 从数据库中取
           User user = userService.qureyUserByName(token.getUsername());
   
           if(user == null){
               return null; //抛出异常 UnknownAccountException 未知账户异常
           }
           //密码认证 由 shiro 来完成,加密了
           //返回 简单身份验证信息类
           return new SimpleAuthenticationInfo(user,user.getPassword(),"");
       }
   }
   ```

2. 创建一个Shiro的配置类，里面向spring容器注入三个Bean 分别是:

   1. `ShiroFilterFactoryBean `: 用来设置 过滤器，以及权限，登录页，未授权跳转页等等.(需要给方法传入安全管理器 `DefaultWebSecurityManager`对象)
   2. `DefaultWebSecurityManager`:  DefaultWebSecurityManager类主要定义了设置subjectDao，获取会话模式，设置会话模式，设置会话管理器，是否是http会话模式等操作，它继承了DefaultSecurityManager类，实现了WebSecurityManager接口。
   3. Realms 自定义的类

```java
import at.pollux.thymeleaf.shiro.dialect.ShiroDialect;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.HashMap;
import java.util.LinkedHashMap;
import java.util.Map;

@Configuration
public class ShiroConfig {

    //ShiroFilterFactoryBean :3
    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("SecurityManager")DefaultWebSecurityManager defaultWebSecurityManager){
        final ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        // 设置安全管理器
        bean.setSecurityManager(defaultWebSecurityManager);
        //添加shiro的内置过滤器  拦截
            /**
             * anon:无需认证即可访问
             * authc：必须认证才可以访问
             * user：必须拥有记住我 功能才可以用
             * perms：拥有对某个资源的权限才能访问
             * role：拥有某个角色权限才能访问
             */
        //拦截
        Map<String,String> filterMap = new LinkedHashMap<>();
        filterMap.put("/add","authc"); //也可支持通配符 *
        filterMap.put("/update","authc");

        //授权 perms[a:b] 必须是a ，且有权限b
        filterMap.put("/add","perms[user:add]");
        filterMap.put("/update","perms[user:update]");

        //设置过滤器链定义图
        bean.setFilterChainDefinitionMap(filterMap);

        //设置登录页面,即如果没有权限，就会跳转至登录页面
        bean.setLoginUrl("/toLogin");

        //设置未授权跳转页面的url
        bean.setUnauthorizedUrl("/unauthorized");

        return bean;
    }
    //DefaultWebSecurityManager:2
    @Bean(name = "SecurityManager")
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
        final DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
        //关联 UserRealm
        defaultWebSecurityManager.setRealm(userRealm);

        return defaultWebSecurityManager;
    }
    //创建 realm 对象 ，需要自定义 :1

    @Bean(name = "userRealm")
    public UserRealm getRealm(){
        return new UserRealm();
    }

}
```

登录

```java
@RequestMapping("/login")
    public String login(@RequestParam(name = "username") String username
            ,@RequestParam(name = "password") String password
            ,Model model){
        //获取当前用户
        final Subject subject = SecurityUtils.getSubject();
        //封装用户的登录数据
        final UsernamePasswordToken token = new UsernamePasswordToken(username, password);

        try {
            //执行登录的方法 ，如果没有异常就ok了
            //执行登录时，会进入Realm中认证
            subject.login(token);
            return "index";
        } catch (AuthenticationException e) {
            model.addAttribute("msg","用户名或密码错误!");
            return "login";
        }

    }
```

注销

```java
	/**
     * 退出用户
     * @return
     */
    @RequestMapping("/logout")
    public String logout(){
        final Subject subject = SecurityUtils.getSubject();
        //退出用户
        subject.logout();
        return "index";
    }
```





##  Shiro整合 Thymeleaf

依赖

```xml
<!-- https://mvnrepository.com/artifact/com.github.theborakompanioni/thymeleaf-extras-shiro -->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>

```

整合配置

Shiro整合 Thymeleaf 需要在配置类中加入一个Bean 到 spring容器，这个类是 `ShiroDialect`

```java
@Configuration
public class ShiroConfig {
    
    //用来整合 thymeleaf
    @Bean
    public ShiroDialect getShiroDialect(){
        return new ShiroDialect();
    }

    //ShiroFilterFactoryBean :3
    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("SecurityManager")DefaultWebSecurityManager defaultWebSecurityManager){
        final ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        // 设置安全管理器
        bean.setSecurityManager(defaultWebSecurityManager);
        //添加shiro的内置过滤器  拦截
            /**
             * anon:无需认证即可访问
             * authc：必须认证才可以访问
             * user：必须拥有记住我 功能才可以用
             * perms：拥有对某个资源的权限才能访问
             * role：拥有某个角色权限才能访问
             */
        //拦截
        Map<String,String> filterMap = new LinkedHashMap<>();
        filterMap.put("/add","authc"); //也可支持通配符 *
        filterMap.put("/update","authc");

        //授权 perms[a:b] 必须是a ，且有权限b
        filterMap.put("/add","perms[user:add]");
        filterMap.put("/update","perms[user:update]");

        //设置过滤器链定义图
        bean.setFilterChainDefinitionMap(filterMap);

        //设置登录页面,即如果没有权限，就会跳转至登录页面
        bean.setLoginUrl("/toLogin");

        //设置未授权跳转页面的url
        bean.setUnauthorizedUrl("/unauthorized");

        return bean;
    }
    //DefaultWebSecurityManager:2
    @Bean(name = "SecurityManager")
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
        final DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
        //关联 UserRealm
        defaultWebSecurityManager.setRealm(userRealm);

        return defaultWebSecurityManager;
    }
    //创建 realm 对象 ，需要自定义 :1

    @Bean(name = "userRealm")
    public UserRealm getRealm(){
        return new UserRealm();
    }

  
}
```

html

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org"
xmlns:shiro="http://www.thymeleaf.org/thymeleaf-extras-shiro"> <!-- shiro 整合 thymeleaf 的提示-->
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
<h1>首页</h1>
<p><a href="/toLogin">登录</a></p>
<p><a href="/logout">退出</a></p>
<hr>

<div shiro:hasPermission="user:add"> <!--判断权限-->
    <a th:href="@{/add}">add</a>
</div>
<div shiro:hasPermission="user:update">
    <a th:href="@{/update}">update</a>
</div>

</body>
</html>
```





