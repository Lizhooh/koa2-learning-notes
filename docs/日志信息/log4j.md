
## Log4j
开发过程中，日志记录是必不可少的事情，尤其是生产系统中经常无法调试，因此日志就成了重要的调试信息来源。
Log4j 是 Apache 的一个开源项目，一个很出名的日志管理模块。现在可以在 Nodejs 里使用 log4js 来管理后端日志。

Log4j 的使用非常简单，只需要做一下简单的配置即可。

首先安装 Log4j：`npm install log4js`。

```js
const log4js = require('log4js');

// 一些配置
log4js.configure({
    appenders: {
        log: {
            type: 'file',                       // 日志类型，有 type、console 等
            filename: './logs/log/info.log',    // 日志输出的文件位置
        },
        api: {
            type: 'file',
            filename: './logs/log/api.log',
        },
        console: {
            type: 'console',                    // 会输出控制台哦
        }
    },
    categories: {
        api: { appenders: ['api'], level: 'info' },
        // 会文件记录而输出控制台
        log: { appenders: ['log', 'console'], level: 'info' },
        default: { appenders: ['log', 'console'], level: 'info' }
    }
});
```

<b>说明：</b>在这里定义了几个日志分类，包括了：`api`，`log`，`default`。其中 level 指的是日志的等级，低于某个等级就不会记录了。例如：`log.debug('xxx')`，在 info 等级下，这段日志是不会被记录的。在定义日志器的时候，`type` 用于指定日志数据的重定向输出，`file` 表示的是把日志信息输出到文件里，`console` 表示的是把日志信息输出到控制台里。分类 `api` 只用了一个日志器，而 `log` 用了两个日志器，可以同时输出到文件和控制台。

当需要使用时，先是取一个分类，如果输出内容。


```js
const apilog = log4js.getLogger('api');

apilog.log('xxx');
```

这时，会在 `api.log` 里看到日志信息。

```js
[2018-05-16T14:28:36.036] [INFO] api - xxx
```

## 集成到 Koa 里

集成到 Koa 里，可以自己写中间件也可以使用第三方的中间。有一个现成的 koa-log4 中间件，可以使用。

```js
const klog = require('koa-log4');

// 日志
router.use(klog.koaLogger(klog.getLogger('log'), {
    format: `":remote-addr" :method(:status) :url :content-lengthb`
}));

// [2018-05-16T14:30:54.035] [INFO] log - "::ffff:127.0.0.1" GET(200) /api/v0/ 15b
```

不过这个中间件的写的比较简单，格式设置很少，并且只能把日志输出到控制台。
笔者还是建议自己打造更强大的 log4js koa 中间件。
