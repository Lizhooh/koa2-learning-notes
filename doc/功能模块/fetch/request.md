

## request
如果你使用过 Python 的 [http://cn.python-requests.org/zh_CN/latest/](Requests)，那么对 Nodejs 的 request.js 肯定不会陌生。

`request.js` 是一个 HTTP 库，提供的简单的 API，简化 HTTP 的相关操作，可以在 Web/React-Native/Nodejs  等支持 javascript 与 xhr 网络接口的环境上使用。

特性：
- 流
- 承诺与异步/等待
- 表单数据
- HTTP 身份验证
- 自定义 HTTP 头
- OAuth 签名
- 代理
- Unix 域套接字
- TLS / SSL协议
- 支持 HAR 1.2


### 基本使用
如果有使用 HTTP 库的经验，那么这是非常好理解的，因为大多都差不多。

```js
const request = require('request');
request('http://www.google.com', function (error, response, body) {
    console.log('error:', error);
    console.log('statusCode:', response && response.statusCode);
    console.log('body:', body);
});
```

指明使用 get/post：

```js
request
    .get('http://google.com/img.png')
    .on('response', function(response) {
        console.log(response.statusCode) // 200
        console.log(response.headers['content-type']) // 'image/png'
    });

request.post('http://b.com')
    .get(')

```

### 使用流
当需要使用 request 来下载图片时，使用流来下载非常简单。

```js
request('http://google.com/doodle.png')
    .pipe(fs.createWriteStream('./doodle.png'));
```

还可以把通过流来上传文件：

```js
fs.createReadStream('file.json')
    .pipe(request.put('http://mysite.com/obj.json'));
```

还可以从一个网络流向另一个网络：

```js
request('http://www.baidu.com/img.png')
    .pipe(request.put('http://itbilu.com/img.png'));
```

### 表单
request 支持 application/x-www-form-urlencoded 和 multipart/form-data 编码的上传。对于 multipart/related 参考 multipartAPI。

**application/x-www-form-urlencoded (URL-Encoded Forms)：URL-encoded 码格式的数据提交**

```js
request.post('http://service.com/upload', { form: { key: 'value' } })
// or
request.post('http://service.com/upload').form({ key: 'value' })
// or
request.post({ url: 'http://service.com/upload', form: { key: 'value' } },
    function (err, httpResponse, body) { /* ... */ });
```

**multipart/form-data (Multipart Form Uploads)：当上传的是文件是，需要使用 multipart/form-data 编码**

```js
// 当使用 multipart/form-data 编码格式提交数据时，request 会调用 form-data 库对数据进行处理
const formData = {
    my_field: 'my_value',
    my_buffer: new Buffer([1, 2, 3]),
    my_file: fs.createReadStream(__dirname + '/unicycle.jpg'),
    attachments: [
        fs.createReadStream(__dirname + '/attachment1.jpg'),
        fs.createReadStream(__dirname + '/attachment2.jpg')
    ],
    // 更多设置，请查看 `form-data` 选项: https://github.com/felixge/node-form-data
    custom_file: {
        value: fs.createReadStream('/dev/urandom'),
        options: {
            filename: 'topsecret.jpg',
            contentType: 'image/jpeg'
        }
    }
};
request.post({ url: 'http://service.com/upload', formData: formData },
    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('upload failed:', err);
        }
        console.log('Upload successful!  Server responded with:', body);
    }
);
```

对于大多数问题，可以使用简化方式：

```js
const r = request.post('http://service.com/upload',
    function optionalCallback(err, httpResponse, body) { /*...*/ });;

const form = r.form();
form.append('my_field', 'my_value');
form.append('my_buffer', new Buffer([1, 2, 3]));
form.append('custom_file', fs.createReadStream(__dirname + '/unicycle.jpg'), {filename: 'unicycle.jpg'});
```

### 自定义 HTTP Headers
一些 web 请求中需要设置 HTTP 请求的头信息，你可以将 User-Agent 之类的设置放在 options 对象中：

```js
request({
    url: 'https://itbilu.com',
    headers: {
        'User-Agent': 'request'
    }
}, (err, req, body) => {
    // ...
});
```

## 参考资料
> https://github.com/request/request
> https://github.com/form-data/form-data