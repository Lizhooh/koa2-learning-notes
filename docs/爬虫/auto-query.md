
## auto query
`auto-query` 笔者（我）编写的一个基于 Cheerio 的 DOM 数据解析库。

下面以一个例子，来说一下场景来看看，我为什么编写一个 `auto-query`。

首先，如果爬一个有多层数据的 html，例如爬取 github 搜索结果信息，那么需要这样编写。

```js
const fetch = require('node-fetch');
const cheerio = require('cheerio');
// 假设数据格式
// {
//     title: '',
//     results:[{
//         name: '',
//         summary: '',
//         star: '',
//         url: '',
//     }]
// }
+ async function() {
    const url = 'https://github.com/search?q=auto';
    const html = await fetch(url).then(res => res.text());
    const $ = cheerio.load(html + '');
    const data = {};

    data.title = $('head > title');
    data.results = $('ul.repo-list > div').toArray().map((item, index) => {
        const $item = $(item);
        const name = $item.find('h3').text().trim();
        const summary = $item.find('h3 + p').text().trim();
        const star = $item.find('.text-right').text().trim();
        const url = $item.find('h3 > a').attr('href');
        return { name, summary, star, url };
    })

    console.log(data);
} ();
```

当数组很多时候，显得会非常麻烦，为了接着这个问题，使用 auto-query 只需要定义数据模型以及选择器，数据格式即可。

```js
const autoQuery = require('auto-query');

+ async function () {
    const url = 'https://github.com/search?q=auto';
    const html = await fetch(url).then(res => res.text());
    const schema = {
        title: '$ head > title',
        results: {
            select: 'ul.repo-list > div',
            data: [{
                name: {
                    select: 'h3',
                    data: '#text',
                    trim: true,
                },
                summary: {
                    select: 'h3 + p',
                    data: '#text',
                    trim: true,
                },
                star: {
                    select: '.text-right',
                    data: '#text',
                },
                url: {
                    select: 'h3 > a',
                    data: '@href',
                },
            }],
        },
    };
    const data = autoQuery(html, schema);

    console.log(data);
} ();
```

更多的使用方法，请参考[文档](https://www.npmjs.com/package/auto-query)，不在这里详细说明了。