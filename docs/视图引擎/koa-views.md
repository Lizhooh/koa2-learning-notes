
## View
传统的后端开发都会有一个视图引擎，当然 koa2 也不例外。

## koa-views
`koa-views`是一个视图引擎的中间件，提供类似`express.views`的功能。

**安装：**

```js
npm install --save koa-views
npm install --save ejs
```

**示例目录：**

```js
├── package.json
├── index.js
└── views
    └── index.ejs
```

**用法：**

```js
const Koa = require('koa')
const views = require('koa-views')
const path = require('path')
const app = new Koa()

// 加载模板引擎
app.use(views(path.join(__dirname, './views'), {
    extension: 'ejs'
}))

app.use(async ctx => {
    await ctx.render('index', {
        title: 'hello koa2',
    });
})

app.listen(3000);
```

## 参考资料
- koa-views: https://github.com/queckezz/koa-views
