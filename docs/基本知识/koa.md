

## koa
koa 是由 Express 原班人马打造的，致力于成为一个更小、更富有表现力、更健壮的 Web 框架。

Koa 包含了像 `content-negotiation`（内容协商）、`cache freshness`（缓存刷新）、`proxy support`（代理支持）和 `redirection`（重定向）等常用任务方法。
与提供庞大的函数支持不同，Koa 只包含很小的一部分，因为 Koa 并不绑定任何中间件。

## hello world
下面是 koa2 的 hello world，里面有很多与 Express 相似的地方，包括 app.use，app.listen 等。

```js
const Koa = require('koa');
const app = new Koa();

app.use(async ctx => {
    ctx.body = 'Hello World';
});

app.listen(3000);
```

## Context（上下文）
和 Express 不同， Koa 并没有封装更多了功能。
Koa Context 将 node 的 request 和 response 对象封装在一个单独的对象里面，其为编写 web 应用和 API 提供了很多有用的方法。

`ctx.app`: 应用实例引用。（在编写中间件时，非常有用处）

**其中：**

- `ctx.req/res`: 这是 Nodejs 原生的 request/response 对象
- `ctx.request/response`: 这是 Koa 的封装 request/response 对象

Koa 不支持 直接调用底层 res 进行响应处理。请避免使用以下 node 属性：

- res.statusCode
- res.writeHead()
- res.write()
- res.end()

以下访问器和别名与 Request 等价：

比如：`ctx.header` 等价于 `crx.request.header`

- ctx.header
- ctx.method
- ctx.method =
- ctx.url
- ctx.url =
- ctx.originalUrl
- ctx.path
- ctx.path =
- ctx.query
- ctx.query =
- ctx.querystring
- ctx.querystring =
- ctx.host
- ctx.hostname
- ctx.fresh
- ctx.stale
- ctx.socket
- ctx.protocol
- ctx.secure
- ctx.ip
- ctx.ips
- ctx.subdomains
- ctx.is()
- ctx.accepts()
- ctx.acceptsEncodings()
- ctx.acceptsCharsets()
- ctx.acceptsLanguages()
- ctx.get()

以下访问器和别名与 Response 等价：

比如：`ctx.body` 等价于 `ctx.response.body`

- ctx.body
- ctx.body =
- ctx.status
- ctx.status =
- ctx.length =
- ctx.length
- ctx.type =
- ctx.type
- ctx.headerSent
- ctx.redirect()
- ctx.attachment()
- ctx.set()
- ctx.remove()
- ctx.lastModified =
- ctx.etag =


## 安装
安装 koa2

```bash
npm install --save koa@2
```

## 结束
好了，到了这里 koa 基本知识，就只有这些了。Koa 只封装了 req 与 res 对象，把更多的留给了第三方中间件去处理，这也是 Koa 的魅力所在。


## 参考资料
- http://koajs.com/