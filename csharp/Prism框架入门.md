# Prism框架

## 介绍

Prism（Prism Library for WPF，前身为Composite Application Guidance for WPF and Silverlight）是由 Microsoft 模式与实践团队开发的一个开源框架，旨在简化构建复杂、灵活、可维护的 WPF（Windows Presentation Foundation）应用程序。Prism 提供了一组强大的工具和指南，以帮助开发人员采用面向模块的开发方法，使应用程序易于扩展和维护。



主要有以下功能：

1. MVVM（Model-View-ViewModel）模式：Prism 强烈鼓励使用 MVVM 模式来设计 WPF 应用程序。MVVM 是一种设计模式，可以将用户界面（View）与业务逻辑和数据（ViewModel）分离，从而实现代码的解耦和测试的可行性。
2. 模块化开发：Prism 支持模块化开发，将应用程序分解成独立的模块。每个模块可以独立开发、测试和部署，并且可以在运行时动态加载和卸载。这样的模块化设计使应用程序更易于维护和扩展。
3. 导航和区域：Prism 提供了强大的导航和区域功能，帮助开发人员管理应用程序中的不同界面和视图的显示和切换。区域是在主界面中预留的空白区域，可以用于显示不同的模块或视图。
4. 事件聚合器：Prism 提供了一个事件聚合器，用于解耦不同模块之间的通信。通过事件聚合器，一个模块可以发布事件，而其他模块可以订阅这些事件，实现松耦合的通信。
5. 命令：Prism 提供了强大的 DelegateCommand 和 CompositeCommand，用于将用户交互抽象成命令，使代码更加可维护和可测试。
6. 依赖注入：Prism 鼓励使用依赖注入容器，如 Unity 或 DryIoc，来实现对象的创建和解析，以实现松耦合和可测试性。
7. 异常处理：Prism 提供了异常处理机制，用于捕获和处理应用程序中的异常，从而提高应用程序的稳定性和可靠性。
8. 测试：Prism 的设计和架构使得应用程序易于进行单元测试、集成测试和自动化测试。

Prism 是一个功能强大、成熟稳定的框架，适用于构建大型、复杂的 WPF 应用程序。它的设计理念和推荐做法可以帮助开发人员遵循最佳实践，使代码更易于理解、扩展和维护。

除了 WPF 版本的 Prism，还有针对 UWP（Universal Windows Platform）和 Xamarin 的 Prism 版本，使开发人员可以在不同的平台上共享代码和设计思想。



## 安装

### VS2022安装Prism框架插件

> 打开VS2022 打开扩展，搜搜 Prism，选择 `Prism Template Pack`，这个插件可以快速构建Prism

<img src="Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230724222211610.png" alt="image-20230724222211610"  />



创建新项目，选择IOC框架类型

![image-20230724224116291](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230724224116291.png)



### 项目结构

> `App.xaml.cs`继承自PrismApplication，最终还是继承`System.Windows。Application`,只是在它的基础上扩展了功能

![image-20230724224206775](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230724224206775.png)



![image-20230724224536725](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230724224536725.png)



## Prism 区域介绍

> 区域是在主界面中预留的空白区域，可以用于显示不同的模块或视图
>
> 例如一个空白控件，点击不同的按钮展示不同的页面



### 自动绑定视图模型

`prism:ViewModelLocator.AutoWireViewModel="True"`这个指令可以自动找到并绑定对应的ViewModel

> 下面的代码（MainWindow.xaml）会自动找到对应的MainWindowViewModel.cs 作为数据绑定的上下文对象

```xaml
<Window x:Class="PrismTest1.Views.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:prism="http://prismlibrary.com/"
        prism:ViewModelLocator.AutoWireViewModel="True"
        Title="{Binding Title}" Height="350" Width="525" >
    <Grid>
        <ContentControl prism:RegionManager.RegionName="ContentRegion" />
    </Grid>
</Window>



```

**需要以下的项目结构**

Views目录下都是视图，ViewModels下都是视图模型



![image-20230725195731069](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725195731069.png)



### 使用区域

#### 注册区域

```xaml
<!--
prism:RegionManager.RegionName 注册一个区域
-->
<ContentControl prism:RegionManager.RegionName="{Binding RegionName}" Grid.Column="1" />
```

#### 使用区域导航

```csharp
private void Open(string obj)
{
    // 首先通过IRegionManager接口获取到全局定义的可用区域
    // 往这个区域动态设置内容
    // 动态设置的内容就是通过依赖注入的形式
    // obj 就是在App中注入的导航视图的名称
    regionManager.Regions[RegionName].RequestNavigate(obj);
}
```

#### 注册视图容器IOC

```csharp
// 在App下进行注册导航视图模型
protected override void RegisterTypes(IContainerRegistry containerRegistry)
{
    // 注册 IOC 导航模型
    containerRegistry.RegisterForNavigation<ModelAWindow>("ModelA");
    containerRegistry.RegisterForNavigation<ModelBWindow>("ModelB");
}
```

![image-20230725204416385](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725204416385.png)



#### 完整代码



对应的MainWindowViewModel如下

```csharp
using Prism.Commands;
using Prism.Mvvm;
using Prism.Regions;
using System;
using static ImTools.ImMap;

namespace PrismTest1.ViewModels
{
    public class MainWindowViewModel : BindableBase
    {
        private string _title = "Prism Application";
        // 区域名称
        private string regionName = "ContentRegion";
        private readonly IRegionManager regionManager;
		// 导航命令
        public DelegateCommand<string> OpenCommand { get; set; }

        public string Title
        {
            get { return _title; }
            set { SetProperty(ref _title, value); }
        }

        public string RegionName
        {
            get => regionName; set
            {
                SetProperty(ref regionName, value);
            }
        }


        public MainWindowViewModel(IRegionManager regionManager)
        {
            this.regionManager = regionManager;
            OpenCommand = new DelegateCommand<string>(Open);
        }

        private void Open(string obj)
        {
            // 首先通过IRegionManager接口获取到全局定义的可用区域
            // 往这个区域动态设置内容
            // 动态设置的内容就是通过依赖注入的形式
            // obj 就是在App中注入的导航视图的名称
            regionManager.Regions[RegionName].RequestNavigate(obj);
        }
    }
}

```



对应的MainWindow.xaml代码

```xaml
<Window x:Class="PrismTest1.Views.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:prism="http://prismlibrary.com/"
        prism:ViewModelLocator.AutoWireViewModel="True"
        Title="{Binding Title}" Height="350" Width="525" >
    <Grid>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="3*" MinWidth="200" MaxWidth="300"></ColumnDefinition>
            <ColumnDefinition Width="7*"></ColumnDefinition>
        </Grid.ColumnDefinitions>

        <StackPanel>
            <Button Content="Model A" Command="{Binding OpenCommand}" CommandParameter="ModelA"></Button>
            <Button Content="Model B" Command="{Binding OpenCommand}" CommandParameter="ModelB"></Button>
        </StackPanel>
        <!-- prism:RegionManager.RegionName 设置区域名称（注册区域） -->        
        <ContentControl prism:RegionManager.RegionName="{Binding RegionName}" Grid.Column="1" />
    </Grid>
</Window>

```



#### 效果



![image-20230725210830207](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725210830207.png)

![image-20230725210846640](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725210846640.png)



## Prism 模块化介绍

> Prism 支持模块化开发，将应用程序分解成独立的模块。每个模块可以独立开发、测试和部署，并且可以在运行时动态加载和卸载。这样的模块化设计使应用程序更易于维护和扩展

### 创建模块

创建普通WPF项目，引入`Prism.DryIoc`,创建一个Module类，继承`IModule`，进行视图的IOC注册

![image-20230725230819850](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725230819850.png)



![image-20230725230924514](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725230924514.png)

创建Module类

```csharp
using ModuleA.Views;
using Prism.Ioc;
using Prism.Modularity;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ModuleA
{
    public class ModuleAProfile : IModule
    {
        public void OnInitialized(IContainerProvider containerProvider)
        {
            
        }

        public void RegisterTypes(IContainerRegistry containerRegistry)
        {
            // 注册导航 ModelA
            containerRegistry.RegisterForNavigation<ModelA>();
        }
    }
}

```



项目结构

![image-20230725232531592](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725232531592.png)



### 使用模块化

在Prism项目中使用这些模块，有多种方式

1. 直接引入模块的项目依赖，然后再App中进行添加模块
2. 只用配置目录的方式（需要将模块的dll放入该目录下）
3. 使用App.config文件、xml文件等多种方式进行动态引入



#### 直接引入模块依赖进行添加模块使用

这种方式需要**引入模块依赖**

```csharp
public partial class App
    {
        protected override Window CreateShell()
        {
            return Container.Resolve<MainWindow>();
        }

        protected override void RegisterTypes(IContainerRegistry containerRegistry)
        {

        }

        protected override void ConfigureModuleCatalog(IModuleCatalog moduleCatalog)
        {
            // 添加模块
            moduleCatalog.AddModule<ModuleAProfile>();
            moduleCatalog.AddModule<ModuleBProfile>();
            base.ConfigureModuleCatalog(moduleCatalog);
        }
    }
```







![image-20230725232055988](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725232055988.png)

![image-20230725232750830](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725232750830.png)



**上述的PrismModuleExample1，用的与上节区域介绍一样的代码（MainWindow相关代码）**



效果

![image-20230725233021685](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725233021685.png)

![image-20230725233033460](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725233033460.png)



#### 使用配置路径放置模块DLL方式进行模块使用

> 这种方式可以一次配置后，不需要修改代码进行动态配置（只需要在对应的目录下放置DLL即可）

创建一个目录放置模块DLL，设置成始终复制

![image-20230725233841817](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725233841817.png)



![image-20230725233807527](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725233807527.png)





```cs

using Prism.Ioc;
using Prism.Modularity;
using PrismModuleExample1.Views;
using System.Windows;

namespace PrismModuleExample1
{
    /// <summary>
    /// Interaction logic for App.xaml
    /// </summary>
    public partial class App
    {
        protected override Window CreateShell()
        {
            return Container.Resolve<MainWindow>();
        }

        protected override void RegisterTypes(IContainerRegistry containerRegistry)
        {

        }

        //protected override void ConfigureModuleCatalog(IModuleCatalog moduleCatalog)
        //{
        //    // 添加模块
        //    moduleCatalog.AddModule<ModuleAProfile>();
        //    moduleCatalog.AddModule<ModuleBProfile>();
        //    base.ConfigureModuleCatalog(moduleCatalog);
        //}
		
        protected override IModuleCatalog CreateModuleCatalog()
        {
            // 读取Modules的DLL添加对应模块
            return new DirectoryModuleCatalog() { ModulePath = @"./Modules"};
        }
    }
}


```

效果也是一样的

![image-20230725234013831](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230725234013831.png)





## Prism 导航介绍

1. 每次导航是否建立新的对象或者使用原来的对象
2. 传递参数
3. 导航拦截
4. 导航日志



视图模型可以实现`INavigationAware`接口 或者`IConfirmNavigationRequest`，`IConfirmNavigationRequest`是`INavigationAware`的升级版，多了拦截的功能



### 注册导航

```csharp
public class ModuleAModule : IModule
{
    public void OnInitialized(IContainerProvider containerProvider)
    {

    }

    public void RegisterTypes(IContainerRegistry containerRegistry)
    {
        // 注册ViewA导航并绑定上下文对象为ViewAViewModel
        containerRegistry.RegisterForNavigation<ViewA, ViewAViewModel>();
    }
}
```



### 导航传递参数



```csharp
private void Open(string obj)
{
    // 首先通过IRegionManager接口获取到全局定义的可用区域
    // 往这个区域动态设置内容
    // 动态设置的内容就是通过依赖注入的形式
    // obj 就是在App中注入的导航视图的名称


    // 传递参数给视图模型
    NavigationParameters parameters = new NavigationParameters();

    parameters.Add("Title", $"我是视图{obj}");

    regionManager.Regions[RegionName].RequestNavigate(obj,parameters);
}
```



### 视图模型实现INavigationAware接口



下述代码中，**IsNavigationTarget**用来判断是否使用原来的实例，**OnNavigatedTo**导航进来时调用，**OnNavigatedFrom**导航离开时调用



```csharp
using Prism.Commands;
using Prism.Mvvm;
using Prism.Regions;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ModuleA.ViewModels
{
    public class ViewAViewModel : BindableBase,INavigationAware
    {
        private string _message;
        public string Message
        {
            get { return _message; }
            set { SetProperty(ref _message, value); }
        }

        public ViewAViewModel()
        {
            Message = "View A from your Prism Module";
        }

        /// <summary>
        /// 当导航进来时调用
        /// </summary>
        /// <param name="navigationContext"></param>
        public void OnNavigatedTo(NavigationContext navigationContext)
        {
            // 获取入参
            if(navigationContext.Parameters.ContainsKey("Title"))
                Message = navigationContext.Parameters.GetValue<string>("Title");
        }

        /// <summary>
        /// 每次重新导航的时候，是否重用原来的实例
        /// </summary>
        /// <param name="navigationContext"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        public bool IsNavigationTarget(NavigationContext navigationContext)
        {
            // 每次导航都是新的对象
            return false;
        }

        /// <summary>
        /// 当导航离开时调用
        /// </summary>
        /// <param name="navigationContext"></param>
        /// <exception cref="NotImplementedException"></exception>
        public void OnNavigatedFrom(NavigationContext navigationContext)
        {
            // 判断即将导航的页面是否为ViewB
            if (navigationContext.Uri.OriginalString.Equals("ViewB"))
            {
                Console.WriteLine("去ViewB");
            }
        }
    }
}

```



### 视图模型实现IConfirmNavigationRequest接口，来进行导航拦截



```csharp
using Prism.Commands;
using Prism.Mvvm;
using Prism.Regions;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows;

namespace ModuleB.ViewModels
{
    public class ViewBViewModel : BindableBase,IConfirmNavigationRequest
    {
        private string _message;
        public string Message
        {
            get { return _message; }
            set { SetProperty(ref _message, value); }
        }

        public ViewBViewModel()
        {
            Message = "View B from your Prism Module";
        }

        public void OnNavigatedTo(NavigationContext navigationContext)
        {
            // 获取入参
            if (navigationContext.Parameters.ContainsKey("Title"))
                Message = navigationContext.Parameters.GetValue<string>("Title");
        }

        public bool IsNavigationTarget(NavigationContext navigationContext)
        {
            return false;
        }

        public void OnNavigatedFrom(NavigationContext navigationContext)
        {
            
        }

        /// <summary>
        /// 离开导航时调用（拦截），可取消导航，需要实现IConfirmNavigationRequest接口，该接口继承自INavigationAware
        /// </summary>
        /// <param name="navigationContext"></param>
        /// <param name="continuationCallback"></param>
        public void ConfirmNavigationRequest(NavigationContext navigationContext, Action<bool> continuationCallback)
        {
            bool result = true;

            // 弹窗确认是否导航至其他页面
            if(MessageBox.Show($"确认是否导航至{navigationContext.Uri.OriginalString}?","温馨提示",MessageBoxButton.YesNo) == MessageBoxResult.No)
            {
                // 取消导航
                result = false;
            }

            // 回传结果，是否允许导航到其他页面
            continuationCallback(result);
        }
    }
}

```



效果

![image-20230727220222515](Prism%E6%A1%86%E6%9E%B6%E5%85%A5%E9%97%A8.assets/image-20230727220222515.png)



### 导航日志



使用 `IRegionNavigationJournal`来实现导航日志



> 创建导航日志

每次导航回调给Journal赋值

```csharp
// 导航日志
private IRegionNavigationJournal journal;


private void Open(string obj)
{
    // 首先通过IRegionManager接口获取到全局定义的可用区域
    // 往这个区域动态设置内容
    // 动态设置的内容就是通过依赖注入的形式
    // obj 就是在App中注入的导航视图的名称


    // 传递参数给视图模型
    NavigationParameters parameters = new NavigationParameters();

    parameters.Add("Title", $"我是视图{obj}");

    // 添加导航回调
    regionManager.Regions[RegionName].RequestNavigate(obj, (navigationCallbackResult) =>
            {
                // 如果导航成功
                if ((bool)navigationCallbackResult.Result)
                {
                    // 给导航日志赋值
                    journal = navigationCallbackResult.Context.NavigationService.Journal;
                }

            },parameters);
}

```



> 返回上一步



```csharp
/// <summary>
/// 返回上一步
/// </summary>
private void Back()
{
    // 允许返回上一步
    if (journal.CanGoBack)
    {
        journal.GoBack();
    }
}
```



> 返回下一步



```csharp
/// <summary>
/// 返回下一步
/// </summary>
private void Forward()
{
    // 允许返回下一步
    if (journal.CanGoForward)
    {
        journal.GoForward();
    }
}
```



> 完整代码



```csharp
using Prism.Commands;
using Prism.Mvvm;
using Prism.Regions;
using System;
using System.Linq;

namespace PrismNavigationExample.ViewModels
{
    public class MainWindowViewModel : BindableBase
    {
        private string _title = "Prism Application";
        // 区域名称
        private string regionName = "ContentRegion";
        private readonly IRegionManager regionManager;

        // 导航命令
        public DelegateCommand<string> OpenCommand { get; set; }

        // 返回上一步命令
        public DelegateCommand BackCommand { get; set; }

        // 返回下一步命令
        public DelegateCommand ForwardCommand { get; set; } 


        // 导航日志
        private IRegionNavigationJournal journal;

        public string Title
        {
            get { return _title; }
            set { SetProperty(ref _title, value); }
        }

        public string RegionName
        {
            get => regionName; set
            {
                SetProperty(ref regionName, value);
            }
        }


        public MainWindowViewModel(IRegionManager regionManager)
        {
            this.regionManager = regionManager;
            OpenCommand = new DelegateCommand<string>(Open);
            BackCommand = new DelegateCommand(Back);
            ForwardCommand = new DelegateCommand(Forward);
        }

        /// <summary>
        /// 返回下一步
        /// </summary>
        private void Forward()
        {
            // 允许返回下一步
            if (journal.CanGoForward)
            {
                journal.GoForward();
            }
        }

        /// <summary>
        /// 返回上一步
        /// </summary>
        private void Back()
        {
            // 允许返回上一步
            if (journal.CanGoBack)
            {
                journal.GoBack();
            }
        }

        private void Open(string obj)
        {
            // 首先通过IRegionManager接口获取到全局定义的可用区域
            // 往这个区域动态设置内容
            // 动态设置的内容就是通过依赖注入的形式
            // obj 就是在App中注入的导航视图的名称


            // 传递参数给视图模型
            NavigationParameters parameters = new NavigationParameters();

            parameters.Add("Title", $"我是视图{obj}");

            // 添加导航回调
            regionManager.Regions[RegionName].RequestNavigate(obj, (navigationCallbackResult) =>
            {
                // 如果导航成功
                if ((bool)navigationCallbackResult.Result)
                {
                    // 给导航日志赋值
                    journal = navigationCallbackResult.Context.NavigationService.Journal;
                }

            },parameters);
        }

    }
}

```

