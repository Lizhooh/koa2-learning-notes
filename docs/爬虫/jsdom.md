## JSDom
`jsdom` 是许多 Web 标准的纯 JavaScript 实现，特别是 WHATWG DOM 和 HTML 标准，用于 Node.js。一般来说，该项目的目标是模拟足够多的 Web 浏览器子集，以便用于测试和挖掘真实世界的 Web 应用程序。

说白，一般我们会使用它来在 Nodejs 上解析 DOM。

## 基本使用
jsdom 和 cheerio 有类似功能，但是 cheerio 更倾向于 DOM 的解析，而 jsdom 除了 DOM 解析外还有一些其他的功能。

```js
const jsdom = require("jsdom");
const { JSDOM } = jsdom;
// 一个 DOM 就这么来了
const dom = new JSDOM(`<!DOCTYPE html><p>Hello world</p>`);
console.log(dom.window.document.querySelector("p").textContent);
```

这样就构造了一个 DOM 处理，后面的处理基本都是操作 DOM 节点。

也可以把 JQuery 加载进去：

```js
jsdom.env("http://www.baidu.com", ["http://code.jquery.com/jquery.js"],
    (err, window) => {
        console.log(window.$('title').html());
        console.log(window.$('.sizeBox').html());
    }
)
```

## 构造函数
JSDOM 的构造函数有一系列的配置项。

```js
const dom = new JSDOM(html, {
    url: "https://example.org/",
    referrer: "https://example.com/",
    contentType: "text/html",
    userAgent: "Mellblomenator/9000",
    includeNodeLocations: true
});
```

<b>说明：</b>
- `url`：默认为"about:blank"，这是一个 url 地址，可以通过 window.location，document.URL 获取。
- `referrer`：影响 document.referrer 的值。
- `contentType`：影响 document.contentType 以及如何解析文档，默认为"text/html"。
- `userAgent`：影响 navigator.userAgent 的值，浏览器的标识。
- `includeNodeLocations`：保留由 HTML 解析器产生的位置信息，它默认为 false 提供最佳性能。

## 执行 script
jsdom 最强大的功能是它可以在 jsdom 中执行脚本，jsdom 提供了一个沙盒环境。但是要注意的是，如果 script 足以强大，可以击穿沙盒访问 Nodejs 环境。

```js
const dom = new JSDOM(`
    <body>
        <script>document.body.appendChild(document.createElement("hr"));</script>
    </body>
`);

dom.window.document.body.children.length === 1;
```

在默认情况下，所有的 script 并不会运行，如果要运行则需要使用 dangerously 选项。

```js
const dom = new JSDOM(`
    <body>
        <script>document.body.appendChild(document.createElement("hr"));</script>
    </body>
`, { runScripts: "dangerously" });

dom.window.document.body.children.length === 2;
```

如果从外部执行脚本，而不是让 script 元素执行，那么使用  outside-only 选项。

```js
const window = (new JSDOM(``, { runScripts: "outside-only" })).window;

window.eval(`document.body.innerHTML = "<p>Hello, world!</p>";`);
window.document.body.children.length === 1;
```


## 假装成一个视觉浏览器
jsdom 没有渲染可视内容的能力，在默认情况下会像无头浏览器一样工作。

当 pretendToBeVisual 选项设置为 true，jsdom 会假装它正在渲染并显示内容。

```js
const window = (new JSDOM(``, { pretendToBeVisual: true })).window;

window.requestAnimationFrame(timestamp => {
    console.log(timestamp > 0);
});
```

## 加载子资源
默认情况下，jsdom 不会加载任何子资源，如脚本，样式表，图像或 iframe。如果希望加载，设置 usable 选项。

```js
const dom = new JSDOM(html, { resources: "usable" });
```

## 虚拟控制台
在 jsdom 里有一个 virtual console 类似 console 的使用。

```js
const virtualConsole = new jsdom.VirtualConsole();

virtualConsole.on("error", () => { ... });
virtualConsole.on("warn", () => { ... });
virtualConsole.on("info", () => { ... });
virtualConsole.on("dir", () => { ... });

const dom = new JSDOM(``, { virtualConsole });
```

当，在 html 里使用 console 时，virtualConsole 提供对应的输出，获取相关信息。

virtualConsole 提供了一个输出流重定向，可以把 virtualConsole 重定向到 Nodejs 的 console 上。

```js
virtualConsole.sendTo(console);
```

## cookie jar
像网页浏览器一样，jsdom 具有 cookie jar 的概念。
默认情况下，JSDOM 构造函数将返回一个带有空 cookie 的实例，cookie 最好自己去设置。

```js
const cookieJar = new jsdom.CookieJar(store, options);
const dom = new JSDOM(html, { cookieJar });
```


## 参考资料
- https://github.com/jsdom/jsdom
