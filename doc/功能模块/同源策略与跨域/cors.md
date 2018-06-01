
## 跨域请求

开启跨域请求是指开启 cors，而不是 jsonp。
在 Express 上开启跨域请求：

```js
// 开启跨域请求
router.all('/*', (req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
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

而在 Koa 里可以使用原生或`koa-cors`

## 原生

其实和上面一样，下面直接把它封装成中间件。

```js
module.exprots = async (ctx, next) => {
    ctx.header["Access-Control-Allow-Origin"]   = "*";
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
