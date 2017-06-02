
### koa-logger
在`Express`里使用的是`morgan`这个中间件，而在 Koa2 里使用的是`koa-logger`

<!-- more -->
## koa-logger
`koa-logger`是 Koa2 的一个日志信息中间件。

**安装：**

```shell
npm install --save koa-logger
```

**使用：**

```js
const logger = require('koa-logger');
const app = new Koa();

// 建议把 logger 放在所有的中间件之间
app.use(logger());
```

## 参考资料
> https://github.com/koajs/logger