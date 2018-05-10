
## 前言
压力测试几乎是后端测试必不可少的一部分，有时候为了清楚，服务器能承受多少请求量，可以对服务器进行大量的并发测试。
Apache 有一个 ab 模块，可以对应用进行并发的测试，巧好可以用来测试 Nodejs 的并发处理能力测试，压力测试。

## Apache ab
`Apache ab`测试工具是模拟**多线程**并发请求，就是有 n 多请求同时向服务器发送，同时也使得 ab 成为某些网络攻击的工具。在测试的时候， Apache ab 会创建尽可能多的线程进行测试，如果测试服务与 Apache ab 在同一台机器上，这样的测试结果会有些差异。

Apache ab 在 Apache 的 bin 目录下，可以看到有 `ab.exe` 文件，为了方便使用，可以把这个目录路径添加到系统的 PATH 下。
如果你没有安装 Apache，请先安装 Apache，一般有 xampp，phpStudy 这些软件包，可以在对应目录下寻找。


**补充：**除了使用 Apache ab 外，还可以使用 http_load，webbench 等压力测试工具。

一般来说只需要使用测试命令：

```bash
ab -n100000 -c100 http://127.0.0.1:5000/
```

**其中：**
- -n 指定请求数量
- -c 指定并发数量

## 压力测试

先是编写一个简单的 HTTP 服务，返回一段 json 数据，这段 json 数据有一定的大小。

```js
const Koa = require('koa');
const Router = require('koa-router');

const app = new Koa();
const router = new Router();

let n = 0;
const list = [];
for (let i = 0; i < 30; i++) {
    list.push(Math.random * 1000 | 0);
}

router.get('/', ctx => {
    ctx.body = {
        stutas: ctx.status,
        data: list,
        id: 1 + n++,
    }
});

app
    .use(router.routes())
    .use(router.allowedMethods());

app.listen(5000, () => {
    console.log('server run in 5000');
});
```


**执行命令：**

```bash
ab -n100000 -c100 http://127.0.0.1:5000/
```

**测试结果：**

```bash
Server Software:
Server Hostname:        127.0.0.1               # 主机名
Server Port:            5000                    # 端口

Document Path:          /                       # 路径
Document Length:        75 bytes

Concurrency Level:      100                     # 并发数
Time taken for tests:   25.627 seconds          # 测试使用的时间
Complete requests:      100000                  # 完成的请求数
Failed requests:        0                       # 失败的请求数，即表示超负荷数
Total transferred:      21700000 bytes          # 总共传输字节数，包含 http 的头信息等
HTML transferred:       7500000 bytes           # html 字节数，实际的页面传递字节数
Requests per second:    3902.06 [#/sec] (mean)  # 每秒多少请求，即服务器的吞吐量
Time per request:       25.627 [ms] (mean)      # 用户平均请求等待时间
Time per request:       0.256 [ms]              # 服务器平均处理时间，即服务器吞吐量的倒数
Transfer rate:          826.90 [Kbytes/sec] received # 每秒获取的数据长度

Connection Times (ms)                           # 连接时间
              min  mean[+/-sd] median   max
Connect:        0    0   0.3      0       2
Processing:     9   25   3.0     25      44
Waiting:        8   25   3.0     24      43
Total:          9   26   3.0     25      44

Percentage of the requests served within a certain time (ms)
  50%     25                    # 50% 的请求在 25ms 内返回
  66%     26                    # 60% 的请求在 26ms 内返回
  75%     27                    # 70% 的请求在 27ms 内返回
  80%     27                    # 80% 的请求在 27ms 内返回
  90%     30                    # 90% 的请求在 30ms 内返回
  95%     32                    # 95% 的请求在 32ms 内返回
  98%     34                    # 98% 的请求在 34ms 内返回
  99%     35                    # 99% 的请求在 35ms 内返回
 100%     44 (longest request)  # 100% 的请求在 44ms 内返回
```


可以看出，Nodejs 的并发处理能力非常优秀，在`10w`请求量，并发`100`，也能在 50ms 内完成。
当然这没有只是一个简单的 HTTP 服务，也没有包括 IO，数据库方面的操作，加上电脑的 CPU 并不是服务器的级别，总之数据仅限参考。

## 均衡负载测试
下面进行 `10w` 请求，`1000` 并发量的测试。

```bash
Server Software:
Server Hostname:        127.0.0.1
Server Port:            5000

Document Path:          /
Document Length:        75 bytes

Concurrency Level:      1000
Time taken for tests:   52.963 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      21700000 bytes
HTML transferred:       7500000 bytes
Requests per second:    1888.11 [#/sec] (mean)
Time per request:       529.630 [ms] (mean)
Time per request:       0.530 [ms] (mean, across all concurrent requests)
Transfer rate:          400.12 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0  12.0      0     509
Processing:    48  525 259.7    740     792
Waiting:       35  412 269.3    225     782
Total:         48  526 259.7    740     792

Percentage of the requests served within a certain time (ms)
  50%    740
  66%    750
  75%    755
  80%    757
  90%    764
  95%    770
  98%    776
  99%    781
 100%    792 (longest request)
```

可以看出，当请求量 `10w`，并发量达到`1000`以上的时候，Nodejs 已经开始忙不过来了，不过可以开启**均衡负载**模式，进一步提供并发处理能力。

## 使用 PM2 Cluster
使用 PM2 开启均衡负载，也就是集群模式（cluster）

```bash
pm start yali.js -i max
```

下面是测试结果：

```bash
Server Software:
Server Hostname:        127.0.0.1
Server Port:            5000

Document Path:          /
Document Length:        74 bytes

Concurrency Level:      1000
Time taken for tests:   27.475 seconds
Complete requests:      100000
Failed requests:        0
Total transferred:      21600000 bytes
HTML transferred:       7400000 bytes
Requests per second:    3639.73 [#/sec] (mean)
Time per request:       274.746 [ms] (mean)
Time per request:       0.275 [ms] (mean, across all concurrent requests)
Transfer rate:          767.76 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.3      0       4
Processing:    62  273  18.2    272     362
Waiting:        9  137  73.8    136     347
Total:         62  273  18.2    272     362

Percentage of the requests served within a certain time (ms)
  50%    272
  66%    275
  75%    277
  80%    279
  90%    284
  95%    293
  98%    329
  99%    348
 100%    362 (longest request)
```

比较：`1888.11 [#/sec] `，`3639.73 [#/sec]`。
可以看出在均衡负载的模式下，**并发处理能力基本提升了 2 倍多**。

## 压力测试指标
测试结果出来了，有时候需要根据测试结果来确定是否需要优化，添加服务数量等。

先来说说，有哪些测试指标：
- TPS：即每秒钟完成的请求数量。也即是上面的 `3639.73 [#/sec]`
- 并发数：时间段，系统同时处理的请求数量
- 响应时间：所有请求处理完毕的使用时间
- 吞吐量：单位时间系统传送的总量。
- PV：页面浏览量，页面每刷新一次算一个 PV 流量。

通过这里指标，我们可以计算出需要的服务器数量。假设每天`80%`的访问量集中在`20%`的时间段里，这个时间段就叫**峰值时间**。因此只要确保在峰值时间里服务器也能扛起并发访问压力就可以了。

设定：某网站，每天有`300W PV`，使用单台服务器提供服务。那么这台服务器需要多少的 TPS？

> TPS = ( 300w × 0.8 ) / ( 24h × 60 × 60 × 0.2 ) = 139

如果，每台服务器的 TPS 为 M，每天有 P 个PV，那么需要 N（N 应向上取整）服务器才能支撑整个后端服务。

> N = ((P × 0.8) / (24 × 60 × 60 × 0.2)) / M


## 总结
- 压力测试机器，不应与服务端在同一个机器上，最好进行外网测试，而不是内网测试
- 压力测试工作应该放到产品上线之前，而不是上线以后
- 一般只需要对 get 进行测试，如果 post 流量比价大，也可以对 post 进行测试。
- 测试时并发应当由小逐渐加大，比如并发 100 时观察一下网站负载是多少、打开页面是否流畅
- 更详细的进行某个页面测试，如电商网站可以着重测试搜索页、推广页面等流量大的页面


