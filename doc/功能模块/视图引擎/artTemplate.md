
## art-template
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

## Koa2 use art-template

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

// koa-art-template 默认自带 koa-views 的功能，所以不需要 views 了
render(app, {
    root: path.join(__dirname, 'views'),     // views 路径
    extname: '.html',
    debug: process.env.NODE_ENV !== 'production'
});

app.use(async function (ctx) {
    await ctx.render('index', {              // views/index.html
        name: '小明'
    });
});

app.listen(8080);
```

**html 文件：**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <h2>`{{ name }}`</h2>
</body>
</html>
 ```

## 参考资料
- https://aui.github.io/art-template/