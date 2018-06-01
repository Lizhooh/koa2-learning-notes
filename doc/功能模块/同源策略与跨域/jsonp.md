
## Jsonp
一个众所周知的问题，Ajax 直接请求普通文件存在跨域无权限访问的问题，甭管你是静态页面、动态网页、web 服务、WCF，只要是跨域请求，一律不准。
不过我们又发现，Web 页面上调用 js 文件时则不受是否跨域的影响（不仅如此，我们还发现凡是拥有`src`这个属性的标签都拥有跨域的能力，比如`<script>、<img>、<iframe>`

Jsonp 就是为了解决这个问题而出现的，使用 Jsonp 需要前后端的配合。

## 原生实现
Jsonp 跨域输出的数据是可执行的`JavaScript`代码，而不是 json 文本
- ctx 输出的类型应该是`'text/javascript'`
- ctx 输出的内容为可执行的返回数据`JavaScript`代码字符串

需要有回调函数名 callbackName，前端获取后会通过动态执行 JavaScript 代码字符，获取里面的数据。

如果需要自己实现的话，主要是：
- 获取回调函数名称
- 装载数据，拼接函数调用

```js
const jsonp = (resData) => {
    // 获取 jsonp 的 callback
    const callbackName = this.query.callback || 'callback'

    // jsonp 的 script 字符串，这是套路写法，一个自调用函数（即时函数）
    const jsonpStr = `;${callbackName}(${JSON.stringify(resData)})`

    // 用 text/javascript，让请求支持跨域获取
    this.type = 'text/javascript';

    // 输出 jsonp 字符串
    this.body = jsonpStr;
};

module.exports = () => async (ctx, next) => {
    ctx.jsonp = jsonp.bind(ctx); // 挂载在 ctx 下，this 指向 ctx
    await next();
}
```

使用：

```js
// ... 省略部分代码
const jsonp = require('./jsonp');

router.get('getData', async ctx => {
    ctx.jsonp({
        success: true,
        data: {
            text: 'this is a jsonp api',
            time: new Date().getTime(),
        }
    })
});

app
    .use(jsonp())           // 使用
    .use(router.routes());
```

此时，前端应该使用 jsonp 函数，比如 JQuery 的 `$.jsonp()`

## koa-jsonp
`koa-jsonp` 是一个 jsonp 中间件。


**安装**

```js
npm install --save koa-jsonp
```

**用法：**

```js
const jsonp = require('koa-jsonp');

router.get('/user', async ctx => {
	ctx.body = { name: '小明' };  // 直接写 JSON，剩下的交给中间件
})

app
    .use(jsonp())
    .use(router.routes());
```

## 参考资料
- https://github.com/kilianc/koa-jsonp
