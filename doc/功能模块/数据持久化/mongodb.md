
## Mongodb
MongoDB 是一个基于分布式文件存储的数据库。由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。
其次 MongoDB 是一个 NOSQL，与 Mysql 这些 SQL 数据库有着不一样的地方。

![](../../../resource/mongodb.jpg)

在 Nodejs 上使用 Mongodb 可以使用官方的模块：`mongodb`，也可以使用第三方优秀的模块：`mongoose`。

关于`mongoose`，请参考官方文档：http://www.nodeclass.com/api/mongoose.html

**安装：**

```bash
npm install --save mongodb
```

## 基本使用
下面的示例，展示了 mongodb 在 Nodejs 上的基本使用：

```js
const mongodb = require('mongodb');

// 连接 mongodb server API: new mongodb.Server(host, port, [options]);
const dbServer = new mongodb.Server('127.0.0.1', '27017');

// 选择 db API: new mongodb.Db(dataName, dbServer, [options]);
const db = new mongodb.Db('test', dbServer);

// 打开 db
db.open((err, db) => {

    // 访问 集合
    db.collection('user', (err, collection) => {

        // 插入数据
        collection.insert({ username: '小明', age: 20 })
            .then(res => {
                console.log(res);
            });

        // 查找数据
        collection.find({ username: '小明' })
            .then(res => {
                console.log(res);
            });

        // 更新数据
        collection.update({ username: '小明' }, { username: '新小明' }, { upsert: true })
            .then(res => {
                console.log(res);
            });

        // 删除数据
        collection.remove({ username: '新小明' })
            .then(res => {
                console.log(res);
            });

        // 关闭 db
        db.close();
    });
});
```

注意，注意，现在 mongodb native 提供了 Promise/async/await 的写法，这是一件非常好的事情。
如果没有回调传递给它，MongoClient连接方法返回一个Promise。
参考：https://github.com/mongodb/node-mongodb-native/pull/1531

```js

```

## Mongoose
当然，官方的 mongodb 驱动可能并不太好用，这时候可以选择更优秀的 mongodb 驱动，比如 Mongoose。

关于 Mongoose 请参考：[学习 Mongoose 的使用](http://me.lizhooh.com/2017/04/08/MongoDB/%E5%AD%A6%E4%B9%A0%20Mongoose%20%E7%9A%84%E4%BD%BF%E7%94%A8%20/)

## Mongodb 可视化客户端

推荐两个 Mongodb 可视化客户端：
- mongobooster:  https://mongobooster.com/ [翻墙]
- mongoclient:  https://www.mongoclient.com/


## 参考资料
> http://mongodb.github.io/node-mongodb-native/2.2/api/
