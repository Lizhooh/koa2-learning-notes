

## Socket.io
`Socket.io`是一个非常出名的框架，可能有的人它先是知道了`Socket.io`才了解`Nodejs`。

Socket.io 实现了实时双向的基于事件的通讯机制。旨在让各种浏览器与移动设备上实现实时 App 功能，模糊化各种传输机制。
Socket.io 是跨平台，多种连接方式自动切换，做即时通讯方面的开发很方便，而且能和 Express/Koa 提供的传统请求方式很好的结合，即可以 在同一个域名，同一个端口提供两种连接方式： request/response, websocket(flashsocket,ajax…).

Socket.io 分为`client`与`server`。client 代码根据不同平台而定，而 server 代码会根据不同的后端语言而定，比如 Java、PHP、C#、Nodejs 等。

## Socket.io for Koa Server
在 NodeJS 上创建 socket 需要依赖于 HTTP 服务器，所有首先就是要创建 HTTP 服务器。

### 初始化

安装：

```bash
npm install --save socket.io
```

先来看，使用 Express 的例子：

```js
var express = require('express'),
    app     = express(),
    server  = require('http').createServer(app),
    io      = require('socket.io')(server);

// ... some think
app.listen(3000);
```

下面是 Koa2 的例子：

```js
const Koa    = require('koa');
const app    = new Koa();
const server = require('http').createServer(app);
const io     = require('socket.io')(server);

app.listen(3000);
```

或者也可以让 Socket.io 独占一个端口：

```js
const io = require('socket.io')(4000);
```

### 监听用户连接
当前端使用 io.connect(url); 时就会触发服务器的 connection 事件

```js
// 每一个新的用户连接时，都会向控制台输出 Welcome
io.on('connection', function (socket) {
    // socket 是连接客户端的示例，后面所有对客户端的操作都通过 socket
    console.log("Welcome");
});
```

### 向客户端发送消息
加入客户端，触发了一个 login 事件。
那么，可以在服务端使用 emit 的触发事件，实际上客户端与服务端的 API 是对称的。

```js
io.on('connection', function (socket) {
    console.log("Welcome");

    // 触发客户端的 login 事件
    socket.emit("login", {
        title: 'Welcome'
    });
});
```

此时客户端的代码应该是这样的：

```js
socket.on("login", function(data) {
    console.log(data); // { title: 'Wecome' }
});
```

### 向所有连接的用户进行广播消息
这个功能类似群发消息的功能。
使用 io.sockets 对象， 对所有连接的用户进行广播。

```js
// io = require('socket.io')(server);
io.sockets.emit("login", {
    title: 'sockets'
});
```

### 给除了自己以外的客户端广播消息

```js
socket.broadcast.emit("msg", { data: "hello,everyone" });
```

### 分组 - 分房间

```js
socket.on('group1', function (data) {
    socket.join('group1');
});

socket.on('group2',function(data){
    socket.join('group2');
});
```

### 监听客户端断开连接
监听 disconnect 事件, 在 socket 对象里有一个 唯一的属性 socket.id，这是每个客户端的唯一标识，可以使用它来判断是那个客户端端口连接。

```js
io.sockets.on('disconnect', function(socket) {
    // id 是唯一标识
    console.log(socket.id);
});
```

### 获取连接的客户端 socket

```js
io.sockets.clients().forEach(function (socket) {
    //.....
});
```

### 关闭 socket.io 服务
> io.close();

## Server 中的 socket 对象
`socket` 是与浏览器客户端进行交互的基础类。
应该注意的 socket 不直接与实际的底层 TCP / IP 有关 socket，它只是该类的名称。


### socket API
- `socket.id` (String) 会话的唯一标识符，来自底层 Client。

- `socket.rooms` (Object) 一个字符串的哈希标识房间的客户端，房间名称索引。

- `socket.client` (Client) 对基础客户端对象的引用。

- `socket.conn` (engine.Socket) 参照客户端底层传输连接（engine.io Socket对象）。这允许访问 IO 传输层，它仍然（主要是）抽象实际的TCP/IP套接字。

- `socket.request` (Request)

- `socket.use(fn)` (Function) 注册一个中间件，它是一个函数，为每个传入的数据包执行和接收作为参数的数据包。

    ```js
        var io = require('socket.io')();

        io.on('connection', function(socket){
            socket.use(function(packet, next){
                if (packet.doge === true) return next();
                next(new Error('Not a doge error'));
            });
        });
    ```

- `socket.send([...args][, ack])` (Socket) 发送消息事件。

- `socket.emit(eventName[, ...args][, ack])` (Socket) 将事件发送到由字符串名称标识的套接字。可以包含任何其他参数。所有可序列化的数据结构的支持，包括缓冲区。

    `ack` 是可选的，它用于将被调用与客户端的答应。

    ```js
        socket.emit('hello', 'world');
        socket.emit('with-binary', 1, '2', { 3: '4', 5: new Buffer(6) });

        client.emit('ferret', 'tobi', function (data) {
            console.log(data);      // data will be 'woot'
        });

        // the client code
        // client.on('ferret', function (name, fn) {
        /       console.log(name);  // name is tobi
        //      fn('woot');         // woot 会被传到上面的 data 里
        // });
    ```

- `socket.on(eventName, callback)` (Socket) 为给定事件注册新的处理程序。

    ```js
        socket.on('news', function (data) {
            console.log(data);
        });
    ```

- `socket.join(room[, callback])` (Socket) 与房间相关的，可以把客户端放置在某个房间里。

    ```js

    io.on('connection', function(socket){
        socket.join('room 237', function(){
            console.log(socket.rooms); // [ <socket.id>, 'room 237' ]

            io.to('room 237', 'a new user has joined the room');
            // broadcast to everyone in the room
        });
    });
    ```

- `socket.leave(room[, callback])` (Socket) 踢出分组。

- `socket.to(room)` (Socket) 为随后的事件设置一个修饰符，该事件只会被广播到已加入给定房间的客户端。

    ```js
        // 这种功能的实现，相当于你在多个 QQ 群里面发布消息
        var io = require('socket.io')();

        io.on('connection', function(client){

            // to one room 去到一个房间，然后触发一个事件
            client.to('others').emit('an event', { some: 'data' });

            // to multiple rooms 去到多个房间，然后触发一个事件
            client.to('room1').to('room2').emit('hello');
        });
    ```

- `socket.in(room)` (Socket) 和 socket.to(room). 相同功能。

- `socket.compress(bool)` (Socket) 数据是否压缩，如果值为真，事件数据会被压缩。当你不调用方法时，默认为 true。

- `socket.disconnect(close)` (Socket) 断开该客户端。如果关闭值为 true，则关闭基础连接。否则，它只是退出命名空间。


## 相关一些操作

```js
io.on('connect', onConnect);

function onConnect(socket) {

    // 发送到客户端
    socket.emit('hello', 'can you hear me?', 1, 2, 'abc');

    // 广播给所有的客户端（除了自己）
    socket.broadcast.emit('broadcast', 'hello friends!');

    // 在 game 房间，发送消息（除了自己）
    socket.to('game').emit('nice game', "let's play a game");

    // 在 game1 好 game2 房间，发送消息（除了自己）
    socket.to('game1').to('game2').emit('nice game', "let's play a game (too)");

    // 在 game 房间，发送消息（包括自己）
    io.in('game').emit('big-announcement', 'the game will start soon');

    // 在 myNamespace 命名空间里，发消息（包括自己）
    io.of('myNamespace').emit('bigger-announcement', 'the tournament will start soon');

    // 单独的发个某个 socketid（私聊）
    socket.to(socketid).emit('hey', 'I just met you');

    // 发送消息后，在回调函数里接收确认
    socket.emit('question', 'do you think so?', function (answer) {});

    // 发送时，对数据压缩？
    socket.compress(false).emit('uncompressed', "that's rough");

    // 如果客户端不准备接收消息，则发送一个可能会被丢弃的消息。
    socket.volatile.emit('maybe', 'do you really need it?');

    // 在这个节点上发送给所有客户机（当使用多个节点时）
    io.local.emit('hi', 'my lovely babies');
}
```

## 参考资料
- https://socket.io/docs/