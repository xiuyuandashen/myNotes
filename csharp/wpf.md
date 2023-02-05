# WPF

## WPF布局基础

### Grid

Gird是一个组织行列布局的容器控件

```xaml
<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <Grid>
        <!--
        Grid.RowDefinitions 用来定义Grid中的行布局  RowDefinition 表示一行
        RowDefinition 中 witdh height 可以设置宽度和高度 比如 height=auto 表示自适应  如果该行没有元素 auto就会隐藏该行
        Grid.ColumnDefinitions 用来定义Grid中的列布局 ColumnDefinition 表示一列
        ColumnDefinition 中 witdh height 可以设置宽度和高度 比如 witdh=auto 表示自适应  如果该列没有元素 auto就会隐藏该列
		Height=“2*” 表示占用2份
        -->
        <Grid.RowDefinitions>
            <RowDefinition Height="100"></RowDefinition>
            <RowDefinition></RowDefinition>
            <RowDefinition></RowDefinition>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition></ColumnDefinition>
            <ColumnDefinition ></ColumnDefinition>
            <ColumnDefinition ></ColumnDefinition>
        </Grid.ColumnDefinitions>

        <!--
            Grid.Row="0" 表示元素在第一行
            Grid.RowSpan="2" 表示元素Grid.Row开始跨越2行
            Grid.Column="2" 表示元素在第三列
            Grid.ColumnSpan="1" 表示元素从Grid.Column跨越1列（相当于没跨域）
        -->
        <Border Grid.Row="0" Grid.RowSpan="2" Background="Red" Grid.Column="2" Grid.ColumnSpan="1"></Border>
    </Grid>
</Window>

```

![image-20230205225456588](D:/note/myNotes/csharp/wpf.assets/image-20230205225456588.png)

### StackPanel

