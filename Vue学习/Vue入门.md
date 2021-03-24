# Vue初入

##  Vue 学习阶段引入

[Vue官方文档](https://cn.vuejs.org/v2/guide/installation.html)

<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

之后会有编译打包的方式。

## Vue 初入案例

```html
<div id="app">
    	{{a}}
        <h1>{{b}}</h1>
    </div>
    <script>
        new Vue({
            el: "#app",
            data: {
                a: "hello world",
                b: "hello Vue"
            }

        })
    </script>
```

上边 `el`表示要绑定的对象，`data`是需要双向绑定的数据。而`data`中的数据通过`{{}}`来进行显示 ，相当于使用了`innerHTML`。

即时运行，即时编译！双向绑定。

[Vue API](https://cn.vuejs.org/v2/api/)