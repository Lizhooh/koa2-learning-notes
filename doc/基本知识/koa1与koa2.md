

### koa 1
koa 1 使用 generator 实现异步，代码看起来像同步的：

```js
const koa = require('koa');
const app = koa();

app.use('/home', function *() {
    yield doReadFile1();
    const data = yield doReadFile2();
    this.body = data;
});

app.listen(3000);
```

### koa 2
koa 团队并没有止步于 koa 1，他们非常超前地基于 ES7 开发了 koa2，和 koa 1 相比，koa2 完全使用 Promise 并配合 async 来实现异步。

```js
const Koa = require('koa');
const app = new Koa();

app.use('/home', async ctx => {
    await doReadFile1();
    const data = await doReadFile2();
    ctx.body = data;
});

app.listen(3000);
```

### 从 koa 1 到 koa2 的转换
从 koa 1 到 koa2 的转换，只需要把 generator 换成 async 即可。




