

## cookie
Koa2 自带 cookie 的处理，并不需要第三方中间件的支持。

它有两个主要的 API：

- `ctx.cookies.get`(name, [options])
    signed: 如果为 true，表示请求时 cookie 需要进行签名

- `ctx.cookies.set`(name, value, [options])
    signed: 是否要做签名。
    expires: cookie 有效期时间。
    path: cookie 的路径，默认为 `/`。
    domain: cookie 的域。
    secure: false 表示 cookie 通过 HTTP 协议发送，true 表示 cookie 通过 HTTPS 发送。
    httpOnly: true 表示 cookie 只能通过 HTTP 协议发送。

## 基本使用

```js
router.get('/user', async ctx => {
    // 获取 cookie
    const usercookie = ctx.cookies.get('user');

    if(usercookie === undefined) {
        // 设置 cookies
        ctx.cookies.set('user', JSON.stringify({
            id: 1,
            name: 'xiaoming',
        }));
    }
});
```

## 参考资料
- http://koa.bootcss.com/