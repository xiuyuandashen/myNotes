再安装了node.js还有Vue之后。

使用vscode控制台创建项目 **vue create 项目名称(全小写)**

##  创建项目



选择手动配置，下面是需要的功能，Babel是编译必选的。空格选择/去除，回车进入下一步

![image-20200627204333930](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200627204333930.png)

问你是不是需要单独的配置文件，或者直接将配置直接写在package.json

![image-20200627204608498](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200627204608498.png)

问你是否保存当前的选项，下次就可以直接选择该配置

![image-20200627204755485](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200627204755485.png)

等下载编译之后

![image-20200627205051567](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200627205051567.png)

## 可视化创建项目

 在控制台输入 `vue ui` 就可以进入可视化界面创建项目

![image-20200627211541713](C:\Users\郑大人\AppData\Roaming\Typora\typora-user-images\image-20200627211541713.png)



## 项目结构



> ├── README.md // 项目说明文档
>
> ├── node_modules // 依赖包目录
>
> ├── build // webpack相关配置文件（都已配置好，一般无需再配置）
>
> │       ├── build.js  //生成环境构建
>
> │       ├── check-versions.js  //版本检查（node，npm）
>
> │       ├── dev-client.js    //开发服务器热重载 （实现页面的自动刷新） 
>
> │       ├── dev-server.js    //构建本地服务器（npm run dev）
>
> │       ├── utils.js         // 构建相关工具
>
> │       ├── vue-loader.conf.js   //csss 加载器配置
>
> │       ├── webpack.base.conf.js    //webpack基础配置
>
> │       ├── webpack.dev.conf.js     // webpack开发环境配置
>
> │       └── webpack.prod.conf.js     //webpack生产环境配置
>
> ├── config // vue基本配置文件（可配置监听端口，打包输出等）
>
> │       ├── dev.env.js // 项目开发环境配置
>
> │       ├── index.js //   项目主要配置（包括监听端口、打包路径等）
>
> │       └── prod.env.js // 生产环境配置
>
> ├── index.html // 项目入口文件
>
> ├── package-lock.json // npm5 新增文件，优化性能
>
> ├── package.json // 项目依赖包配置文件
> ├── src // 项目核心文件（存放我们编写的源码文件）
>
> │       ├── App.vue // 根组件        
> │       ├── assets // 静态资源（样式类文件、如css，less，和一些外部的js文件）
>
> │       │       └── css  //样式
> │       │       └── font  //字体
> │       │       └── images  //图片
> │       ├── components // 组件目录
>
> │       │       └── Hello.vue // 测试组件
>
> │       ├── main.js // 入口js文件
>
> │       └── router // 路由配置文件夹
>
> │       └── index.js // 路由配置文件
>
> └── static // 静态资源目录（一般存放图片类）

### assets和static文件夹的区别

assets目录中的文件会被webpack处理解析为模块依赖，只支持相对路径形式。例如，在 `<img src="./logo.png">`
和 `background: url(./logo.png)`中，"./logo.png" 是相对的资源路径，将由Webpack解析为模块依赖。

static/ 目录下的文件并不会被Webpack处理：它们会直接被复制到最终的打包目录（默认是dist/static）下。必须使用绝对路径引用这些文件，这是通过在 config.js 文件中的 build.assetsPublicPath 和 build.assetsSubDirectory 连接来确定的。

任何放在 static/ 中文件需要以绝对路径的形式引用：/static/[filename]。

在我们实际的开发中，总的来说：static放不会变动的文件 assets放可能会变动的文件。

## require()

用于引入模块、 `JSON`、或本地文件。 可以从 `node_modules` 引入模块。 可以使用相对路径（例如 `./`、 `./foo`、 `./bar/baz`、 `../foo`）引入本地模块或 JSON 文件，路径会根据 [`__dirname`](http://nodejs.cn/s/etUQhi) 定义的目录名或当前工作目录进行处理。

例如 ：`require('./assets/img/1.jpeg')`

## scoped

<style scoped> 
    .list{
        width: 300px;
        height: 700px;
        background-color: skyblue;
    }
    .list ul li{
        display: flex;
    }
    .list ul li img{
        width: 80px;
        height: 80px;
    }
</style>

`scoped`可达到类似组件私有化、样式设置“作用域”的效果。但是慎用