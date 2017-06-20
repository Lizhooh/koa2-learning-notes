
## session
session 可以自己实现或者通过第三方中间件实现。

session 操作一般需要有以下需求：
- 如果 session 数据量很小，可以直接存在内存中。
- 如果 session 数据量很大，则需要存储介质存放 session 数据。

## session 认证
基于`session`和`cookie`的后端用户认证方法：

- 在登录页面输入用户名与密码，单击按钮登录，将会把**用户名**和**密码**传给 API 服务器，服务器通过验证后就会将当前用户名（唯一 id）加入`session.user`里，并返回登录状态信息。

- 在登录时会创建一个新的`session`，并产生一个`sid`，这个`sid`将会通过请求响应存储在浏览器`cookie`的`sid`里，下次（前端）发出请求时设置`cookie`携带这个字段，此时服务器通过`sid`映射到`sid`来找到存储中的`session`数据关联起来。

- 在已经登录过一次后，会维持一个会话状态（一般为 30 分钟），通过`sid`映射到`sid`，来找到`session.user`中的数据从而判断，用户是否登录过。如果发现会话生命周期结束了，那就把`session.user`置空或删除，并返回超时登录页面。

- 在注销时，也是通过`sid`来找到`session.user`信息，然后把`session.user`置空或删除。

- 而`session`是后端维护的一个`stroe`，用来储存`session`的数据（数据库存储，内存缓存）。

## 自定义简单的 session 中间件
下面在 Nodejs 里，根据上面的方法，自定义一个简单的 session 中间件。

```js
// 唯一 ID
const uuid = require('uuid');

// 储存 session 的信息
const STROE = [];

const session = (req, res, next) => {
    const sid = req.cookie.sid;
    let _session = { user: null, sid: null };

    if(sid) {
        // 如果存在 sid 着寻找 session 信息
        const index = STROE.find(i => i.sid === sid);
        index >= 0 && (_session = STROE[index]);
    }

    req.session = {
        ..._session,
        create(user) {
            // 添加 session 信息
            const sid = uuid.v4();
            this.user = user;
            this.sid = sid;
            // 添加到 cookie 里
            req.cookie.sid = sid;
            STROE.push({ user, sid });
        },
        destroy() {
            // 删除 session 信息
            const index = STROE.find(i => i.sid === this.sid);
            this.user = null;
            this.sid = null;
            STROE.splice(index, 1);
        },
    };
    next();
};

module.exports = session;
```

## koa-session
`koa-session`是 koa 的 session 中间件，类似`express-session`一样。
它的存储方式是存在**内存**里的，如果你想存储在`MysSQL/MongoDb/Redis`，那就自己寻找相关中间件或者根据需求自己封装中间件。

**安装：**

```js
npm install --save koa-session
```

**用法：**

```js
const session = require('koa-session');

app.keys = ['some secret hurr'];

const CONFIG = {
    key: 'koa:sess',             /** (string) cookie key (default is koa:sess) */
    maxAge: 1000 * 60 * 60 * 24, /** (number) maxAge in ms (default is 1 days) */
    overwrite: true,             /** (boolean) can overwrite or not (default true) */
    httpOnly: true,              /** (boolean) httpOnly or not (default true) */
    signed: true,                /** (boolean) signed or not (default true) */
};

app.use(session(CONFIG, app));
```

## 参考资料
> session:  https://github.com/koajs/session