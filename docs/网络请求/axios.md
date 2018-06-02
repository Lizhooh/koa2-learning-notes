

## axios
`axios`是一个 用于浏览器和 node.js 的基于 Promise 的 HTTP 库。

**特征：**

- 基于 XMLHttpRequest（浏览器）
- 基于 http （nodejs）
- 支持 Promise API
- 截取请求和响应
- 转换请求和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防范 XSRF

**安装：**

```bash
npm install --save axios
```

## 基本使用
axios 的 API 使用与 fetch 很像。


### get 请求

```js
axios.get('/user?ID=12345')
.then(function (response) {
    console.log(response);
})
.catch(function (error) {
    console.log(error);
});

axios.get('/user', {
    params: {
        ID: 12345
    }
})
.then(function (response) {
    console.log(response);
})
.catch(function (error) {
    console.log(error);
});
```

### post 请求

```js
axios.post('/user', {
    firstName: 'Fred',
    lastName: 'Flintstone'
})
.then(function (response) {
    console.log(response);
})
.catch(function (error) {
    console.log(error);
});
```

### 并发请求
类似于 Promise.all

```js
axios.all([
    axios.get('/user/12345'),
    axios.get('/user/12345/permissions')
])
.then(axios.spread(function (acct, perms) {
    // Both requests are now complete
}));
```

### 自定义 headers

```js
axios('http://bit.ly/2mTM3nY', {
    method:'get',
    responseType:'stream',
    headers: {
        'X-Requested-With': 'XMLHttpRequest',
    }
})
.then(function(response) {
    response.data.pipe(fs.createWriteStream('ada_lovelace.jpg'))
});
```

### stream
可以使用 stream 来保存图片。

```js
axios({
    method:'get',
    url:'http://bit.ly/2mTM3nY',
    responseType:'stream',   // <-- 需要设置
}).then(function(response) {
    response.data.pipe(fs.createWriteStream('ada_lovelace.jpg'))
});
```

## 参考资料
- https://github.com/mzabriskie/axios
