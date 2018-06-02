## koa-static-cache
`koa-static-cache` 是一个静态资源**缓存**的中间件，与 `koa-static` 有一定的差别（说实话，看名字就知道有什么差别了）。

与 `koa-static` 的差别：
- koa-static 不会把数据缓存在内存里，而 koa-static-cache 会把数据缓存在内存里。
- 默认情况下将静态数据存储在**内存**中，也可以选择存储在磁盘里。
- 在初始化时缓存资源，需要重新启动进程以更新静态资源。
- 使用 MD5 哈希值作为 ETag。
- 如果存在于磁盘上，则使用 .gz 文件，如 nginx gzip_static 模块。

注意：默认缓存在内存里。

**安装：**

```js
npm install --save koa-static-cache
```

**用法：**

```js
const path = require('path')
const staticCache = require('koa-static-cache')

app.use(staticCache(
    path.join(__dirname, 'public'), {
        maxAge: 365 * 24 * 60 * 60
    }
))
```

## 参考资料
- https://github.com/koajs/static-cache