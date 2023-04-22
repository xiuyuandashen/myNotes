# DotNet6.0集成xxxjob



## 使用docker部署XxxJob平台

```bash
docker run --link mysql5.7:db  -e PARAMS="--spring.datasource.url=jdbc:mysql://db:3306/xxl_job?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai --spring.datasource.username=root --spring.datasource.password=123456" -p 8997:8080 -v /tmp:/data/applogs --name xxl-job-admin  -d xuxueli/xxl-job-admin:2.4.0 
```

上述 `--link mysql5.7:db`mysql5.7是mysql容器名称，链接到xxl-job-admin中作为通讯使用

`-e PARAMS=“”`,这是向程序传递参数

xxxjob默认密码是admin/123456

详细请看[官方文档](https://www.xuxueli.com/xxl-job/)

- 在这之前需要去githubxxxjob[源码](https://github.com/xuxueli/xxl-job/)那边获取sql脚本，然后导入到mysql容器中



## Net6.0集成xxxJob

1. 在NuGet安装DotXxlJob.Core

2. 在Program.cs中注册配置、注入Job任务

   1. 注入配置的代码，这边是对IServiceCollection、IApplicationBuilder进行扩展，实现自动注入xxxJob任务，以及配置xxxJob中间件

   ```c#
   using DotXxlJob.Core;
   using NetCoreWebApi.Job;
   using System.Reflection;
   
   namespace NetCoreWebApi.Aspect
   {
       public static class DependencyInjectionExtersion
       {
           /// <summary>
           /// 自动注入xxxJob任务
           /// </summary>
           /// <param name="services"></param>
           /// <param name="configurationSection"></param>
           /// <returns></returns>
           public static IServiceCollection AddxxlJobs(this IServiceCollection services, IConfiguration configurationSection)
           {
               //注入配置
               services.AddXxlJobExecutor(configurationSection);
               //获取所有JobHandle任务
               var types = GetSubClassType(typeof(AbstractJobHandler));
               var methods = typeof(ServiceCollectionServiceExtensions).GetMethods();
               MethodInfo method = methods.FirstOrDefault(m => m.Name == "AddSingleton" && m.IsGenericMethod && m.GetGenericArguments().Length == 2);
               // 自动注册Job,请勿手动注册
               foreach (var type in types)
               {
                   var registerMethod = method.MakeGenericMethod(new Type[] { typeof(IJobHandler), type });
                   registerMethod.Invoke(services, new object[] { services });
               }
               services.AddAutoRegistry();
               return services;
           }
           public static List<Type> GetSubClassType(Type baseType)
           {
               var allTypes = typeof(Program).Assembly.GetTypes();
               return allTypes.Where(t => t.BaseType != null && t.BaseType.Equals(baseType)).ToList();
           }
   
   
           public static IApplicationBuilder UseXxlJobExecutor(this IApplicationBuilder @this)
           {
               // 注册XxxJob中间件
               return @this.UseMiddleware<XxlJobExecutorMiddleware>();
           }
       }
   }
   
   ```

   2. XxlJobExecutorMiddleware中间件

   ```c#
   using DotXxlJob.Core;
   
   namespace NetCoreWebApi.Aspect
   {
       /// <summary>
       /// xxxJob 中间件配置
       /// </summary>
       public class XxlJobExecutorMiddleware
       {
           private readonly IServiceProvider _provider;
           private readonly RequestDelegate _next;
           private readonly XxlRestfulServiceHandler _rpcService;
           public XxlJobExecutorMiddleware(IServiceProvider provider, RequestDelegate next)
           {
               this._next = next;
               this._provider = provider;
               this._rpcService = _provider.GetRequiredService<XxlRestfulServiceHandler>();
           }
   
           public async Task Invoke(HttpContext httpContext)
           {
               string contentType = httpContext.Request.ContentType;
       
               if ("POST".Equals(httpContext.Request.Method, StringComparison.OrdinalIgnoreCase)
                   && !string.IsNullOrEmpty(contentType)
                   && contentType.ToLower().StartsWith("application/json"))
               {
       
                   await _rpcService.HandlerAsync(httpContext.Request, httpContext.Response);
       
                   return;
               }
               await _next(httpContext);
           }
       }
   }
   ```
   
   3.  在Program.cs中使用
   
      ```c#
       #region 配置xxxjob
      //builder.Services.Configure<XxlJobExecutorOptions>(builder.Configuration.GetSection("xxlJob"));
      //builder.Services.AddXxlJobExecutor(builder.Configuration);
      //builder.Services.AddAutoRegistry();
      //注册所有job任务
      builder.Services.AddxxlJobs(builder.Configuration);
      #endregion
      
      
          
      #region 启用XxlExecutor,注册XxxJob中间件
      app.UseXxlJobExecutor();
      #endregion
          
      app.UseRouting();
      app.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.ProcessMetrices("/metrics");
      });
      app.Run();
      ```
   
      

3. 编写Job任务

   主要是继承`AbstractJobHandler`类，实现对应的方法以及使用 `JobHandler`特性并且指名JOB任务名称

   ```c#
   using DotXxlJob.Core;
   using DotXxlJob.Core.Model;
   
   namespace NetCoreWebApi.Job
   {
       [JobHandler(name: "demoJobHandler1")]
       public class DemoJobHandler : AbstractJobHandler
       {
           public void Dispose()
           {
               throw new NotImplementedException();
           }
   
           public override Task<ReturnT> Execute(JobExecuteContext context)
           {
               context.JobLogger.Log("receive demo job handler,parameter:{0}", context.JobParameter);
               Console.WriteLine("job Invoke...");
               return Task.FromResult(ReturnT.SUCCESS);
           }
       }
   }
   ```

   

 4. 配置文件

    appsettings.Development.json

    ```
    {
      "Logging": {
        "LogLevel": {
          "Default": "Information",
          "Microsoft.AspNetCore": "Warning"
        }
      },
      //xxxjob配置 
      "xxlJob": {
      	// adminAddresses 是xxxjob平台的地址
        "adminAddresses": "http://10.8.23.77:8997/xxl-job-admin",
        // 配置的执行器名称
        "appName": "xxl-job-executor-dotnet",
        "specialBindAddress": "10.8.23.77",
        "port": 5199,
        "autoRegistry": true,
        "accessToken": "",
        "logRetentionDays": 30
      }
    }
    
    ```

## 在xxxjob平台注册

1. 添加执行器

2. 添加任务

   
