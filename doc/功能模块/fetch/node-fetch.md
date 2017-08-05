## Fetch
之前在 React.js 上用过 fetch.js。在 React Native 上用过自带的 Fetch 。
感觉 Fetch 非常好用，它的 API 非常的友好。

而在 Nodejs 上一直都是使用着原生的 HTTP 模块，最后还自己封装了一个。
不得不说原生的 HTTP 模块的自由度非常灵活，但是每次使用的时候，你都要自己去封装一下，这样显得非常麻烦。

然而，也有人集成了 fetch for nodejs。

## 安装

```shell
npm install node-fetch --save
```

## 引入

```js
const fetch = require('node-fetch');
```


## 基本使用
简单的说一下基本的使用，更多的去参考官方文档。

### text

```js
fetch('https://github.com/')
    .then(res => res.text())
    .then(body => console.log(body))
    .catch(err => console.error(err));
```

### json

```js
fetch('https://api.github.com/users/github')
    .then(res => res.json())
    .then(json => console.log(json))
    .catch(err => console.error(err));
```

### post
注意 body 需要用 JSON.stringify 序列化为字符串。

```js
fetch('http://httpbin.org/post', {
        method: 'POST',
        body: JSON.stringify({
            name: 'xiaoming',
        }),
    })
    .then(res => res.json())
    .then(json => console.log(json));
```

### post with json

```js
fetch('http://httpbin.org/post', {
        method: 'POST',
        body: JSON.stringify({
            name: 'xiaoming',
        }),
        headers: { 'Content-Type': 'application/json' },
    })
    .then(res => res.json())
    .then(json => console.log(json));
```

### post with form-data
模拟表单数据，同时还可以文件上传。

先是安装 fromData ：

```
npm install --save form-data
```

```js
// 安装 Form Data 模块 （HTML 5 API）
const FormData = require('form-data');
const form = new FormData();

form.append('name', 'xiaoming');

fetch('http://httpbin.org/post', {
        method: 'POST',
        body: form
    })
    .then(res => res.json())
    .then(json => console.log(json));
```

### 图片上传
使用 fetch 来进行文件上传需要使用到一个 fromData 的库。

```js
// uri 图片地址，可以是本地路径，也可以是网络地址
uploadImage(uri) {
    let formData = new FormData();
    let file = {
        uri: uri,               // 类似的 './image/a.jpg'
        type: 'multipart/form-data',
        name: 'a.jpg'           // 必须填写，不然会出错
    };

    formData.append("files", file);  // key

    return fetch('http://127.0.0.1:3000', {
        method:'POST',
        headers:{
            'Content-Type':'multipart/form-data',
        },
        body: formData,
    })
        .then(res => res.json())
        .then(json => console.log(json))
        .catch(err => console.error(err))
}
```

高兴的是，这段代码在 Reac-Native 上也可以使用，React-Native 内置了 Fetch 与 FromData。

### buffer
buffer() 是 node-fetch 特有的 API，其他的 Fetch 没有这个 API。

```js
const fileType = require('file-type');

fetch('https://assets-cdn.github.com/images/modules/logos_page/Octocat.png')
    .then(res => res.buffer())
    .then(buffer => fileType(buffer))
    .then(type => console.log(type));
```

### Stream
`流`，在是非常有用了，它可以提供性能，并且不需要把整个文件下载到内存里，才保存到磁盘上。

**读入流：**

```js
import { createReadStream } from 'fs';

// 创建一个可读流
const stream = createReadStream('input.txt');

// 发送文件
fetch('http://httpbin.org/post', { method: 'POST', body: stream })
	.then(res => res.json())
	.then(json => console.log(json));
```

**写入流：**

```js
import { createReadStream } from 'fs';

fetch('https://assets-cdn.github.com/images/modules/logos_page/Octocat.png')
    .then(res => {
        // 创建一个可写流
        const dest = fs.createWriteStream('./octocat.png');

        dest.on('end', () => {
            console.log('end');
        });

        // 接上 res.body
        res.body.pipe(dest);
    });
```

## 参考资料
> github: https://github.com/bitinn/node-fetch