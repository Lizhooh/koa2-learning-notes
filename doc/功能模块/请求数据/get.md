

## GET 请求
在`koa2`中，获取 GET 请求数据源头是 koa 中 request 对象中的 query 方法或 querystring 方法。

koa 有一些简化的 API，所以`ctx.query`与`ctx.request.query`是一样的

它们都有两种方式：

1. 是从上下文中直接获取
    - 请求对象 ctx.query，返回如 `{ a:1, b:2 }`
    - 请求字符串 ctx.querystring，返回如 `a=1&b=2`

2. 是从上下文的 request 对象中获取
    - 请求对象 ctx.request.query，返回如 `{ a:1, b:2 }`
    - 请求字符串 ctx.request.querystring，返回如 `a=1&b=2`


**注意：**
- `ctx.req/res`: 这是 Nodejs 原生的 request/response 对象
- `ctx.request/response`: 这是 Koa 的封装 request/response 对象

一般来说，对于 get 请求，不需要什么中间件，因为这是默认提供的功能。

对于查询字符串的解析，可以使用 Nodejs 的原生模块`querystring`

## 示例

```js
router.get('/', async ctx => {
    const query = ctx.query;
    const querystring = ctx.querystring;

    ctx.body = {
        query,
        querystring,
    };
})
```

