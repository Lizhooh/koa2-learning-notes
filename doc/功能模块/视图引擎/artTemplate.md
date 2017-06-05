
### art-template
art-template 是一个渲染性能出众模板引擎，无论在 NodeJS 还是在浏览器中都可以运行。

**特性：**
- 拥有接近 JavaScript 渲染极限的的性能
- 调试友好：语法、运行时错误日志精确到模板所在行；支持支持在模板文件上打断点（Webpack Loader）
- 支持压缩输出页面中的 HTML、CSS、JS 代码
- 支持 Express、Koa、Webpack
- 支持模板继承与子模板
- 兼容 EJS、Underscore、LoDash 模板语法
- 模板编译后的代码支持在严格模式下运行
- 支持 JavaScript 语句与模板语法混合书写
- 支持自定义模板的语法解析规则
- 浏览器版本仅 6KB 大小


**安装：**

```js
npm install --save art-template@latest
```

### Koa2 use art-template

 **安装：**

 ```bash
npm install --save art-template
npm install --save koa-art-template
 ```

 **示例：**

```js
const Koa = require('koa');
const render = require('koa-art-template');
const app = new Koa();

// koa-art-template 默认自带 koa-views 的功能，所有不需要 views 了
render(app, {
    root: path.join(__dirname, 'views'),     // views 路径
    extname: '.html',
    debug: process.env.NODE_ENV !== 'production'
});

app.use(async function (ctx) {
    await ctx.render('index', {             // views/index.html
        name: '小明'
    });
});

app.listen(8080);
```

 ```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <h2>{{ name }}</h2>
</body>
</html>
 ```

### 基本使用
使用方式有 simple 与 native 两种，simple 的方式与 vuejs 类似，而 native 方式与 ejs 相似。
下面是 simple 的语法：

**输出表达式：**

```js
// 编码输出
{{ content }}

// 不编码输出
{{# content }}
```

**条件表达式：**

```js
{{if admin}}
	<p>admin</p>
{{else if code > 0}}
	<p>master</p>
{{else}}
    <p>error!</p>
{{/if}}
```

**遍历表达式：**

```js
// 无论数组或者对象都可以用 each 进行遍历。

{{each list as value index}}
    <li>{{index}} - {{value.user}}</li>
{{/each}}

// 亦可以被简写：
{{each list}}
    <li>{{$index}} - {{$value.user}}</li>
{{/each}}
```

**模板包含表达式：**

```js
// 用于嵌入子模板。
{{include 'template_name'}}

//子模板默认共享当前数据，亦可以指定数据：
{{include 'template_name' news_list name}}
```

**辅助方法：**

```js
// 使用template.helper(name, callback)注册公用辅助方法：
template.helper('dateFormat', function (date, format) {
    // ..
    return value;
});

// 模板中使用的方式：
{{time | dateFormat: 'yyyy-MM-dd hh:mm:ss'}}

// 支持传入参数与嵌套使用：
{{time | say:'cd' | ubb | link}}
```


### 参考资料
> https://aui.github.io/art-template/