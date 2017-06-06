
## Ejs
“E” 的含义是 “有效” 的意思。EJS 是一个简单的模板语言，可以让你使用原生 JavaScript 生成 HTML 标记。
没有关于如何组织内容的语法规则，也没有循环和控制流的重载，只是使用原生的 JavaScript。


**安装**

```js
npm install ejs
```

## 特性

- `<% %>` 用于控制流
- `<%= %>` 用于转义的输出
- `<%- %>` 用于非转义的输出
- `-%>` 结束标签用于换行移除模式
- 带有 `<%_ _%>` 的控制流使用空白字符移除模式
- 自定义分隔符 (例如，使用 `<? ?>` 代替 `<% %>`)
- 客户端支持
- 中介 JavaScript 的静态缓存
- 模板的静态缓存
- 与 Express 视图系统兼容

## 标签

- `<% 'Scriptlet'` 标签, 用于控制流，没有输出
- `<%=` 向模板输出值（带有转义）
- `<%-` 向模板输出没有转义的值
- `<%#` 注释标签，不执行，也没有输出
- `<%%` 输出字面的 `<%`
- `%>` 普通的结束标签
- `-%>` Trim-mode ('newline slurp') 标签, 移除随后的换行符

## Koa2 use Ejs

before:

```bash
npm install --save koa-views
npm install --save ejs
```

after:

```js
// 加载模板引擎
app.use(views(path.join(__dirname, './view'), {
    extension: 'ejs'
}))
```

## 基本使用

```html
<% if (user) { %>
  <h2><%= user.name %></h2>
<% } %>
```

**主要的 API：**

```js
<% code %>
  // 无缓冲的条件语句元素

<%= code %>
  // 转义HTML，该code并且会打印出来

<%- code %>
  // 非转义的buffering，该code并且会打印出来

<% include file %>
  // 内嵌别的文件

<% layout(file) -%>
  // 指定布局文件

<% script(file) -%>
  // 包含js脚本文件

<% stylesheet(file) -%>
  // 包含css文件

<% block(code, code) -%>
  // 指定块内容
```

**使用 js 函数：**

```html
<ul>
    <% users.forEach(function(user){ %>
        <%- include('user/show', {user: user}) %>
    <%});%>
</ul>
```

**页面布局：**
```js
//comic.ejs:
<%- include('header'); %>

<h1>hot comic now: </h1>

<% if(comic) { %>
    <h2><%= comic.name %></h2>
<% } %>

<%- include('footer')  %>
```

## 例子


```js
app.get("/", async crx => {
    res.render('home.ejs', {
        name: 'xiaoming'
    });
});

// 加载模板引擎
app.use(views(path.join(__dirname, './views'), {
    extension: 'ejs'
}))
```

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <p>Hi <%= name %></p>
</body>
</html>
```

渲染出的结果：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <p>Hi xiaoming</p>
</body>
</html>
```
