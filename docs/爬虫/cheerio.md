
## cheerio
在 Nodejs 上写爬虫，绝对不能错过 cheerio。
`cheerio` 是一个轻量级的 dom/xml 解析库，有着与 Jquery dom 类似的 API。

**安装：**

```bash
npm install --save cheerio
```

## 基本使用
使用起来似乎非常简单，因为 API 类似 Jquery dom 的操作，如果你对 Jquery 熟悉，那么基本可以直接学会了。

```js
const cheerio = require('cheerio');
const $ = cheerio.load('<h2 class="title">Hello world</h2>');

$('h2.title').text('Hello there!');
$('h2').addClass('welcome');

$.html();
// => <h2 class="title welcome">Hello there!</h2>
$.text();
// => Hello there
```

### Selectors
支持 css3 的所有选择器，包括 jquery 的选择器。

> $(selector, [context], [root])

```js
$('.apple', '#fruits').text()
//=> Apple

$('ul .pear').attr('class')
//=> pear

$('li[class=orange]').html()
//=> Orange
```

### Attributes


> element.attr( name, value )

```js
$('ul').attr('id')
//=> fruits

$('.apple').attr('id', 'favorite').html()
//=> <li class="apple" id="favorite">Apple</li>
```

### 更多的
api 基本与 jquery 的 dom 操作一致，这里就不一一介绍了。
要说一点的是：如果选择器选择的是一个数组，那么可以使用 element.map / Array.map，它们有一定的差别。

```js
// element.map
$('ul li').map((index, item) => {
    const $item = $(item);
    console.log($item.html());
});

// Array.map
$('ul li').toArray().map((item, index) => {
    const $item = $(item);
    console.log($item.html());
});
```

## 示例

```js
const cio = require('cheerio');
const fetch = require('node-fetch');

fetch('https://github.com')
    .then(res => res.text())
    .then(html => {
        const $ = cio.load(html);
        console.log($('title').text());
    });
```

## 参考资料
- https://github.com/cheeriojs/cheerio