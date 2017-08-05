
## Redis
Redis 是一个开源的使用 ANSI C 语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value 数据库。Redis 是一个高性能的 key-value 数据库。redis 的出现，很大程度补偿了 memcached 这类 key/value 存储的不足，在部 分场合可以对关系数据库起到很好的补充作用。

**性能：**
- 测试完成了 50 个并发执行 100000 个请求
- 设置和获取的值是一个 256 字节字符串
- Linux box是运行Linux 2.6,这是X3320 Xeon 2.5 ghz
- 文本执行使用 loopback 接口 (127.0.0.1)
- 结果: 读的速度是 110000 次 /s,写的速度是 81000 次 /s

**应用场景：**
- 缓存
- 任务队列
- 网站统计
- 数据过期处理
- 分布式集群架构中的 session 分离

本文并不会讲述 redis 的基础语法，而是直接讲述 redis 在 Nodejs 上的使用方式。

## ioredis
`ioredis` 是 Nodejs 的一个 `Redis` 模块，有着与 `Redis` 一模一样的 API 操作。

**它有几个特点：**

- 全功能。它支持 Cluster，Sentinel，Pipelining 以及 Lua 脚本和 Pub / Sub（在二进制消息的支持下）。
- 高性能。
- 令人愉快的 API 它适用于 Node 回调和 Promise。
- 转换命令参数和回复。
- 透明键前缀。
- 抽象 Lua 脚本，允许您定义自定义命令。
- 支持二进制数据。
- 支持 TLS。
- 支持离线队列和准备检查。
- 支持 ES6 类型，如 Map 和 Set。
- 支持 GEO 命令（Redis 3.2 Unstable）。
- 复杂的错误处理策略。

**安装：**

```bash
npm install --save ioredis
```

### 基本使用
下面介绍，它的基本使用，其实使用起来非常简单。

```js
const Redis = require('ioredis');
// 默认是 http://127.0.0.1:6379， 0 号数据库
const redis = new Redis();

//  计算运行时间
const timing = async (name = 'test', cb) => {
    console.time(name);
    typeof cb === 'function' && await cb();
    console.timeEnd(name);
};

// set
timing('set', _ => {
    redis.set('foo', 'bar');
    redis.get('foo').then(res => console.log(res));
});

// del
timing('del', _ => {
    redis.del('foo').then(res => console.log(res));
});

// sadd [set 集合]
timing('sadd', _ => {
    redis.sadd('seta', 1, 3, 5, 7).then(res => console.log(res));
    redis.sadd('setb', [1, 3, 5, 7]).then(res => console.log(res));
});

// lpush [list 列表]
timing('lpush', _ => {
    redis.lpush('list', [1, 2, 3]).then(res => console.log(res));
    redis.lrange('list', [0, -1]).then(res => console.log(res));
});

// 选择数据库 [序号]
timing('select', _ => {
    // 选择 1 号数据库，然后查询 1 号数据库的 keys
    redis.select(1).then(res => {
        redis.set('name', 'xiaoming');
        redis.keys('*').then(res => console.log(res));
    });
});

// 事务
timing('basic', async () => {
    redis.multi().set('a', 1).set('b', 2).exec();
});

// 管道
redis.pipeline().get('a').get('b').exec().then(res => {
    console.log(res);
});
```

可以看出，在 redis 命令行里怎样使用，在这里就怎样使用，API 基本是一致。

### 连接 Redis
连接`Redis`，默认是连接到`http://127.0.0.1:6379`。

```js
new Redis()                     // Connect to 127.0.0.1:6379
new Redis(6380)                 // 127.0.0.1:6380
new Redis(6379, '192.168.1.1')  // 192.168.1.1:6379
new Redis('/tmp/redis.sock')
new Redis({
    port: 6379,                  // Redis port
    host: '127.0.0.1',           // Redis host
    family: 4,                   // 4 (IPv4) or 6 (IPv6)
    password: 'auth',            // 密码
    db: 0,                       // 数据库号
})

// 使用 redis 协议
new Redis('redis://:authpassword@127.0.0.1:6380/4')
```

### 发布与订阅
以下是发布/订阅 API 的简单示例。以下程序打开两个客户端连接。它使用一个连接订阅一个频道，并与另一个发布到该频道：

```js
timing('sub/pub', _ => {
    // 发布
    const pub = new Redis();

    // 订阅 news 与 music
    redis.subscribe('news', 'music', (err, count) => {
        // 现在我们订阅了'新闻'和'音乐'通道
        // count 表示我们当前订阅的频道数
        pub.publish('news', 'Hello world!');
        pub.publish('music', 'Hello again!');
    });

    // 监听 message 事件
    redis.on('message', (channel, message) => {
        // 接收信息世界，你好！从频道新闻
        // 再次收到消息，你好！从声道音乐
        console.log('Receive message %s from channel %s', message, channel);

        // 把 news 退订了
        redis.unsubscribe('news');
        // redis.unsubscribe('news', 'music');
    });

    // 还有一个名为'messageBuffer'的事件，它与'message'相同
    // 它返回缓冲区而不是字符串
    redis.on('messageBuffer', (channel, message) => {
        // Both `channel` and `message` are buffers.
    });
});
```

相应的有：

```js
// 订阅与给定模式相匹配的所有频道
redis.psubscribe('pat?ern', function (err, count) {});
redis.punsubscribe(...arg);
redis.on('pmessage', function (pattern, channel, message) {});
redis.on('pmessageBuffer', function (pattern, channel, message) {});
```

当客户端发出 `subscribe` 或 `psubscribe` 时，该连接被置于“订户”模式。在这一点上，只有修改订阅集的命令是有效的。（其他命令无效）
订阅集为空时，连接将恢复为常规模式。

### 处理二进制数据
参考下面代码：

```js
redis.set('buf', new Buffer('buf'));
```

## 参考资料
> https://github.com/luin/ioredis


