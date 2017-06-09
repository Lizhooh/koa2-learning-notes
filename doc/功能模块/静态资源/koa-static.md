## 静态资源
像 Express 一样，Koa2 需要静态资源的中间件。

一个 http 请求访问 web 服务静态资源，一般会经历三个步骤：

- 访问文本，例如 js，css，png，jpg，gif
- 访问静态目录
- 找不到资源，抛出404错误

<!-- more -->
而在 Express 里最直接的方式是：

```js
app.get('/public/*', (req, res, next) => {
    const url = req.url;
    res.sendFile(req.url);
});
```

而在 Koa2 里一般都是选择第三方中间件：`koa-static`

## koa-static
`koa-static` 是 koa 里的中间件，类似 express-static

**安装：**

```js
npm install --save koa-static
```

**用法：**

```js
const static = require('koa-static');

app.use(static(path.join(__dirname, 'public')));
```

## koa-static-cache
`koa-static-cache` 是一个静态资源缓存的中间件，与`koa-static`有一定的差别。

与`koa-static`的差别：
- 默认情况下将静态数据存储在内存中，也可以选择存储在磁盘里
- 在初始化时缓存资源，需要重新启动进程以更新静态资源
- 使用 MD5 哈希值作为 ETag
- 如果存在于磁盘上，则使用 .gz 文件，如 nginx gzip_static 模块

**安装：**

```js
npm install --save koa-static-cache
```

**用法：**

```js
var path = require('path')
var staticCache = require('koa-static-cache')

app.use(staticCache(
    path.join(__dirname, 'public'), {
        maxAge: 365 * 24 * 60 * 60
    }
))
```

## 参考资料

> koa-static: https://github.com/koajs/static


> koa-static-cache: https://github.com/koajs/static-cache

