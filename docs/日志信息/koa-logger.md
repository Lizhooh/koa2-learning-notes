
## koa-logger
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

koa-logger 并不支持把日志输出到文件里，如果想把日志输出到文件里，可以使用 `PM2` 把部署 Nodejs 应用，把标准输出流写进文件里，参考目录 `PM2`。

## 小结
`koa-logger` 只是一个很小的日志信息模块，它只输出到控制台。在下一节里将介绍使用更加强大的日志模块：`log4j`。