

## POST 请求
对于 POST 请求的处理，Koa2 没有封装获取参数的方法，需要通过解析上下文 context 中的原生 node.js 请求对象 req，将 POST 表单数据解析成 query。
**注意：** ctx.request 是 context 经过封装的请求对象，ctx.req 是 context 提供的 node.js 原生 HTTP 请求对象，同理 ctx.response 是 context 经过封装的响应对象，ctx.res 是 context 提供的 node.js 原生 HTTP 请求对象。


下面介绍两个中间件： `koa-body`, `koa-bodyparser`

## koa-body
`koa-body`全功能 koa boby 解析器中间件，支持 multipart，urlencoded，json 请求主体，提供与 Express bodyParser 相同的功能，提供文件上传功能。

**安装：**

```js
npm install --save koa-body@2
```

**用法：**

```js
const koaBody = require('koa-boby');

router.get('/', async ctx => {
    const data = ctx.request.body;

    ctx.body = { data };
});

app.use(koaBody());
app.use(router.routes());
```

## koa-bodyparser
`koa-bodyparser` 与 koa-boby 差不多，用法基本一致。

**安装：**

```js
npm install koa-bodyparser@2 --save
```

**用法：**

```js
const bodyparser = require('koa-bodyparser');

router.get('/', async ctx => {
    const data = ctx.request.body;

    ctx.body = { data };
});

app.use(bodyparser());
app.use(router.routes());
```


## 参考资料
> koa: http://koa.bootcss.com/

> koa-bodyparser: https://github.com/koajs/bodyparser

> koa-body: https://github.com/dlau/koa-body

