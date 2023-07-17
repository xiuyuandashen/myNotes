## MateriaDesignXAML

### 启动项目

[官网地址]([MaterialDesignInXAML/MaterialDesignInXamlToolkit at v3.2.0 (github.com)](https://github.com/MaterialDesignInXAML/MaterialDesignInXamlToolkit/tree/v3.2.0))

这边我们选择3.20版本（更高级版本下载后无法运行会报错缺失net462）

下载后进行编译

进入目录 `MaterialDesignInXamlToolkit-3.2.0/.paket`下执行命令`.\paket.exe install`，进行打包编译，然后到`MaterialDesignInXamlToolkit-3.2.0`下使用vs2019打开`MaterialDesignToolkit.Wpf.slnf`

<img src="%E4%BD%BF%E7%94%A8MateriaDesignXAML-UI%E6%A1%86%E6%9E%B6-WPF.assets/image-20230717234609886.png" alt="image-20230717234609886" style="zoom:50%;" />

选择MaterialDesignDemo作为启动项目

<img src="%E4%BD%BF%E7%94%A8MateriaDesignXAML-UI%E6%A1%86%E6%9E%B6-WPF.assets/image-20230717234758191.png" alt="image-20230717234758191" style="zoom:50%;" />

启动后，可以使用该案例项目作为例子进行学习

![image-20230717234927848](%E4%BD%BF%E7%94%A8MateriaDesignXAML-UI%E6%A1%86%E6%9E%B6-WPF.assets/image-20230717234927848.png)



![image-20230717234945655](%E4%BD%BF%E7%94%A8MateriaDesignXAML-UI%E6%A1%86%E6%9E%B6-WPF.assets/image-20230717234945655.png)



### 创建项目并引入Material Design

创建WPF项目，使用NuGet进行安装依赖 `Install-Package MaterialDesignThemes`,或者使用可视化页面进行安装

![image-20230717235141163](%E4%BD%BF%E7%94%A8MateriaDesignXAML-UI%E6%A1%86%E6%9E%B6-WPF.assets/image-20230717235141163.png)



安装后在App.xaml中进行引入样式字典

> 这是在MaterialDesignDemo项目中拷贝出来了

```xaml
<Application x:Class="WpfTrainingPractice.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:WpfTrainingPractice"
             xmlns:materialDesign="http://materialdesigninxaml.net/winfx/xaml/themes"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <!-- This is the current way to setup your app's initial theme -->
                <materialDesign:BundledTheme BaseTheme="Inherit" PrimaryColor="DeepPurple" SecondaryColor="Lime" />

                <!-- If you would prefer to use your own colors there is an option for that as well -->
                <!--<materialDesign:CustomColorTheme BaseTheme="Light" PrimaryColor="Aqua" SecondaryColor="DarkGreen" />-->

                <!-- You can also use the built in theme dictionaries as well
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Light.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignColors;component/Themes/Recommended/Primary/MaterialDesignColor.DeepPurple.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignColors;component/Themes/Recommended/Accent/MaterialDesignColor.Lime.xaml" />
                -->

                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Button.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.CheckBox.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.ListBox.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.PopupBox.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.RadioButton.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.TextBlock.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.ToggleButton.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Defaults.xaml" />

                <!--<ResourceDictionary Source="pack://application:,,,/ShowMeTheXAML.AvalonEdit;component/Themes/xamldisplayer.xaml" />-->
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Application.Resources>
</Application>

```

然后在页面中可以直接使用了

![image-20230717235431401](%E4%BD%BF%E7%94%A8MateriaDesignXAML-UI%E6%A1%86%E6%9E%B6-WPF.assets/image-20230717235431401.png)
