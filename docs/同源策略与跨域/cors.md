
## 跨域请求
[跨域资源共享（CORS）](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS) 机制允许 Web 应用服务器进行跨域访问控制，从而使跨域数据传输得以安全进行。浏览器支持在 API 容器中（例如 XMLHttpRequest 或 Fetch ）使用 CORS，以降低跨域 HTTP 请求所带来的风险。

开启跨域请求，需要在 http header 里设置属性来告诉浏览器，后端允许接收来自不同域名的请求。

开启 CORS 有一定的风险，默认在开启状态下是全部接受来自所有域名或端口的请求。并且开启 CORS 后，在前端使用 XMLHttpRequest 或 Fetch 请求时会有两个请求，第一个是浏览器自动发送的，第二个尝试用户发的请求。浏览器会发送一个确定的请求，确定是否开启了跨域请求，这个请求的方法是 OPTIONS。

## 开启

在 Express 上开启跨域请求：

```js
// 开启跨域请求
router.all('/*', (req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");     // host 白名单
    res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
    res.header("Access-Control-Allow-Methods", "PUT,POST,GET,DELETE,OPTIONS");
    res.header("Content-Type", "application/json;charset=utf-8");
    next();
})
```

或者用第三方中间件：

```js
const cors = require('cors');
// 开启跨域请求
router.all('/*', cors());
```

而在 Koa 里可以使用原生或 `koa2-cors`。

## 原生

其实和上面一样，下面直接把它封装成中间件。

```js
module.exprots = async (ctx, next) => {
    ctx.header["Access-Control-Allow-Origin"]   = "*";   // host 白名单
    ctx.header["Access-Control-Allow-Headers"]  = "Origin, X-Requested-With, Content-Type, Accept";
    ctx.header["Access-Control-Allow-Methods"]  = "PUT,POST,GET,DELETE,OPTIONS";
    ctx.header["Content-Type"]                  = "application/json;charset=utf-8";
    await next();
});
```


## koa2-cors
`koa2-cors`是 koa2 的一个中间件，其实里面的代码也和上面的差不多。

**安装：**

```
npm install --save koa2-cors
```

**用法：**

```js
const cors = require('koa2-cors');

// 只在特定路径下开启
app.use('/api/*', cors());
```


## 参考资料
- https://github.com/zadzbw/koa2-cors
