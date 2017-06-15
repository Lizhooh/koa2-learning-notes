
## Koa2 Router
与 Express 一样，Koa2 也有路由，同样的保持了整洁的 API。

> 先来看看 Express 的路由

```js
app.get('/', (req, res, next) => {
    res.send('<h1>Index</h1>');
});

app.get('/hello/:name', (req, res, next) => {
    const name = req.params.name;
    res.send(`<h1>Hello, ${name}!</h1>`);
});
```

> 在来看看 Koa2 的路由

```js
const router = require('koa-router')();

router.get('/', async (ctx, next) => {
    ctx.body = '<h1>Index</h1>';
});

router.get('/hello/:name', async (ctx, next) => {
    const name = ctx.params.name;
    ctx.body = `<h1>Hello, ${name}!</h1>`;
});

app.use(router.routes());
```

最后发现，他们之间基本没什么差异。

## 安装

```js
npm install --save koa-router@latest
```

## 简单示例

```js
const koa = require('koa2');
const router = require('koa-router')();
const bodyParser = require('koa-bodyparser');
const colors = require('colors');
const app = new koa();

router.get('/hello/:name', async (ctx, next) => {
    const name = ctx.params.name;
    ctx.body = `<h1>Hello, ${name}!</h1>`;
});

router.get('/', async (ctx, next) => {
    ctx.body = '<h1>Index</h1>';
});

// add router middleware:
app.use(bodyParser());
app.use(router.routes());
app.listen(3333, _ => {
    console.log(
        '\n\trun node server in:',
        `http://127.0.0.1:3333\n`.green
    );
});
```

## 模块化
> 在 Express 可以这样进行模块化路由：

```js
// index.js
    // 装载所有子路由
    app.use('/',     require('./route/index'));
    app.use('/user', require('./route/user'));

// route/index.js
    const router = require('express').Router();

    router.get('/',      (req, res, next) => { /* ... */ })
          .get('/home',  (req, res, next) => { /* ... */ })
          .get('/about', (req, res, next) => { /* ... */ })

    module.exports = router;

// route/user.js
    const router = require('express').Router();

    router.get('/',      (req, res, next) => { /* ... */ })
          .get('/login', (req, res, next) => { /* ... */ })

    module.exports = router;
```

> 而在 Koa2 中，需要这样写：

```js
// index.js
    // 装载所有子路由
    const index = require('./route/index');
    const user  = require('./route/user');

    let router = new Router()
    router.use('/',     index.routes())
          .use('/user', user.routes());

    // 加载路由中间件
    app.use(router.routes());

// route/index.js
    const router = new require('koa-router')();

    router.get('/',      async (ctx) => { /*   */ })
          .get('/home',  async (ctx) => { /*   */ })
          .get('/about', async (ctx) => { /*   */ })

    module.exports = router;

// route/user.js
    const router = new require('koa-router')();

    router.get('/',      async (ctx) => { /*   */ })
          .get('/login', async (ctx) => { /*   */ })

    module.exports = router;
```


## 前缀 prefixes
为 url 添前缀，从而减少点代码：

```js
const router = new Router({
    prefix: '/users'
});

router.get('/',    ...); // responds to "/users"
router.get('/:id', ...); // responds to "/users/:id"
```

## 参数 parameters

```js
router.get('/:category/:title', async (ctx, next) => {
    console.log(this.params);
    // => { category: 'programming', title: 'how-to-node' }
});
```

## 重定向 redirect

```js
// 接受所有类型， get, post, put, delete ...
router.all('/login', async (ctx, next) => {
    ctx.redirect('/sign-in');
    ctx.status = 301;
});

app.use(router.routes())
    // redirect 是 router 方法，从而要使用 router 的中间件
   .use(router.allowedMethods());
```

## 参考资料
> koa-router: https://github.com/alexmingoia/koa-router
