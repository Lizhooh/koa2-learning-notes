

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

router.post('/', async ctx => {
    const data = ctx.request.body;

    ctx.body = { data };
});

app.use(koaBody());
app.use(router.routes());
```

koa-body 还能支持文件上传，它的实现依赖于 formidable。

**koa-body 的配置项：**

- patchNode {Boolean} 修补程序请求正文到节点 ctx.req，默认 false
- patchKoa {Boolean} 修补程序请求正文到 Koa's ctx.request，默认 true
- jsonLimit {String | Integer} JSON 主体的字节（整数）的限制，默认值 1mb
- formLimit {String | Integer}表单主体的字节（整数）的限制，默认值 56kb
- textLimit {String | Integer}文本正文的字节（整数）的限制，默认 56kb
- encoding {String} 设置传入表单字段的编码，默认值 utf-8
- multipart {Boolean} 解析 multipart，默认 false
- urlencoded {Boolean} 解析 urlencoded body，默认 true
- text {Boolean} 解析文本正文，默认 true
- json {Boolean} 解析 json，默认 true
- formidable {Object} 传递给强大的多部分解析器的选项
- onError {Function} 自定义错误句柄，如果抛出错误，可以自定义响应 - onError（错误，上下文），默认将抛出
- strict {Boolean} 如果启用，请不要解析 GET，HEAD，DELETE请求，默认 true

## koa-bodyparser
`koa-bodyparser` 与 koa-boby 差不多，用法基本一致。

**安装：**

```js
npm install koa-bodyparser@2 --save
```

**用法：**

```js
const bodyparser = require('koa-bodyparser');

router.post('/', async ctx => {
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

