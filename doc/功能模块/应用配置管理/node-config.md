## 前言
在编写 Nodejs 应用时，几乎都需要写一些配置文件，以应付万变的情况，其中最大的好处就是可以根据配置项快速的修改一些配置。比如一些数据库的配置项，development、production、test 不同开发环境的配置项。

在以往，可能自己去写一个配置文件：`config.js`，在不同的层级的文件里去引用，可是这样可能会遇到引用层级很深的路径，还不方便维护。

好，为了解决这些问题，引入 `node-config` 模块的使用。
<!-- more -->

## node-config
[node-config](https://github.com/lorenwest/node-config) 就是一个为您的应用程序部署组织分层配置。
它允许您定义一组默认参数，并将其扩展到不同的部署环境（development, qa, staging, production 等），并且还支持多 Nodejs 部署。


<b>安装：</b>

```bash
npm install --save config
```

### 基本使用
node-config 支持多种环境的配置文件，它会自动根据当前的 process.env.NODE_DEV 进行环境判断从而去加载对应的配置文件。

默认情况下，node-config 会去读取应用跟目录下的 `config` 文件夹，再从 config 文件夹里读取对应的配置文件。


```js
app
├── config                      // Nodejs 应用配置文件夹
│   ├── default.json            // 默认配置文件
│   ├── development.json        // 开发环境配置文件
│   ├── production.json         // 生成环境配置文件
│   ├── test.json               // 测试环境配置文件
│   └── mydev.json              // 自定义环境配置文件
```


```js
// 创建完成后在 default.json 文件里编写：
{
    "name": "app"
}

// 在 development.json 文件里编写：
{
    "name": "app dev"
}

// 在 production.json 文件里编写：
{
    "name": "app pro"
}
```

好了，编写配置文件完成后，就可以在应用中引用了。

```js
// in app.js
const config = require('config');
console.log(config);  // Config { name: 'app dev' }
```

如果你使用 `node app.js` 启动，就会看到输出 app dev，因为默认启动是 development 模式。

为了统一各平台的环境设置，将使用 `cross-env` 来设置 NODE_ENV。

```js
"scripts": {
    "start": "cross-env NODE_ENV=production node index",
    "dev": "cross-env NODE_ENV=development node index",
    "jest": "cross-env NODE_ENV=test jest"
},
```

为什么这样做？因为 `NODE_DEV` 在不同平台上的设置是不同的，为了统一设置，把这些坑爹的兼容性交给 cross-env 就对了。


### 加载顺序
NODE_ENV=production 时 node-config 会自动加载 default.json、production.json 配置文件。

也就是说 node-config 先会加载 default.json 文件，接着去加载 production.json 文件。

如果 production.json 文件里的属性名称与 default.json 里重名了会怎样处理？`node-config` 的做法是后者覆盖前者。

最后的配置对象是：

```js
const config = {
    ...default,
    ...production
};
```

其次是，配置的覆盖是进行深度覆盖的，也就是说在 default 进行了如下配置：

```json
{
    "name": "app",
    "version": "0.1.0",
    "mongodb": {
        "host": "localhost",
        "port": 27017,
        "db": "test"
    },
}
```

在 development.json 进行如下配置：

```json
{
    "mongodb": {
        "db": "dev"
    }
}
```

最后输出的配置项是：

```json
{
    "name": "app",
    "version": "0.1.0",
    "mongodb": {
        "host": "localhost",
        "port": 27017,
        "db": "dev"
    },
}
```

### JavaScript 配置文件
有时候，一些配置项是需要动态的计算的，比如配置一个 appRoot 路径，那么就需要使用 `__dirname` 来配置。`node-config` 对 `.js` 配置文件同样有效。

像动态计算的配置项，最好放在 default.js 里：

```js
const path = require('path');

module.exports = {
    root: path.join(__dirname, '../'),
}
```

其他环境的配置文件仍然是 `.json` 这样还是会生效的，无论如何它们都会被转化为 Object 来统一处理。

那么 config 文件夹里包含了：

```js
app
├── config                      // Nodejs 应用配置文件夹
│   ├── default.js              // 默认配置文件
│   ├── development.json        // 开发环境配置文件
│   ├── production.json         // 生成环境配置文件
│   ├── test.json               // 测试环境配置文件
│   └── mydev.json              // 自定义环境配置文件
```

此外还提供延迟功能，让你根据配置对象的上下文进行计算。

```js
// In default.js
// using defer functions is optional. See example and docs below.
const defer = require('config/defer').deferConfig;

module.exports = {
    firstName: undefined,
    lastname: undefined;
    fullName: defer(function () {
        return this.firstName + ' ' + this.lastName;
    }
}

// Then in config/production.js:
module.exports = {
   firstName: "Jane",
   lastName: "Doe",
}
```

最后在 production 环境下，得出 fullName 的值为 `Jane Doe`。


### 多个节点实例
如果你的后端服务需要多个 Nodejs 节点，那么也意味着需要多个配置文件。

为了支持在机器上运行的每个应用程序实例的不同配置，`NODE_APP_INSTANCE` 环境变量将被检查，并用于加载实例特定的文件。

如果 `NODE_APP_INSTANCE = 3` 这时候会先加载 default.json 文件，接着加载 default-3.json 文件。
如果 `NODE_APP_INSTANCE = node1` 这时候会先加载 default.json 文件，接着加载 default-node1.json 文件。

也就是说，加载的顺序最先的还是 default，接着加载对应的实例配置项：`default-{NODE_APP_INSTANCE}.json`。

如果 `NODE_APP_INSTANCE = node1` 并且 `NODE_ENV = test` 这时候会先加载 default.json 文件，接着加载 default-node1.json，再去加载 test.json 文件，最后还会加载 test-node1.json。

这里有一个前提是：如果这些文件存在。不存在也没关系，它不会报错，只会默默的忽视。

## API
node-config 只有 3 个 api：`get`、`has`、`util`。

### get 方法
无论在那个文件 `node-config` 返回的是一个配置对象。node-config 提供的 get 方法让你获取某个属性值。

```js
const config = require('config');
config.get('name'); // app dev
```

如果 get 一个不存在的属性值，那么就会抛出错误。

如果不想抛出错误，也可以直接使用对象的方式：

```js
const config = require('config');
config.name; // app dev
```

值得幸运的是 node-config 对属性值进行 setter 无效化，所以 `config.name = 'new app'` 是不会修改 name 的值的，在其他模块里输出时还是 `app dev`。

### has 方法
如果您想查看是否存在配置值，请使用 has() 方法。

```js
if (config.has('dbConfig')) {
    // ...
}
```

这可以作为 `get()` 绕过 try / catch 块的替代方法。

`has()` 不会抛出异常，并且当且仅当配置文件为所提供的键定义了一个值时才返回 true。请注意 null 也是一个定义的值。
`has()` 如果传递的参数是 null 或者 undefined 只是简单地返回，则不会抛出异常或 false，也就是只要有值，has 就会返回 true。

### util 对象
node-config 提供了 util 工具对象，它有以下 API：

- `util.cloneDeep` 对象深拷贝。
- `util.diffDeep` 返回包含两个对象之间不同的所有元素的对象。
- `util.equalsDeep` 两个对象进行深对比。
- `util.extendDeep` 用一个或多个对象扩展另一个对象（深度）。
- `util.getConfigSources` 返回 config 文件夹的文件描述对象。
- `util.getEnv` 获取配置环境变量的当前值。
- `util.loadFileConfigs` 使用与主配置目录相同的约定来读取给定的目录。
- `util.makeHidden` 使对象属性隐藏，因此枚举对象的元素时不会出现。
- `util.makeImmutable` 使一个 js 对象属性不可变。
- `util.toObject` 返回当前配置文件的 Object 深副本。

上面的 api 都是以函数的形式使用：


```js
const bfObject = config.util.cloneDeep(fromObject);
```

详细说明参考：[Using Config Utilities](https://github.com/lorenwest/node-config/wiki/Using-Config-Utilities)

## 在 Webpack 中使用
`node-config` 不仅仅可以用于后端 Nodejs 应用，同时也可以用于前端构建开发环境。

因为 Webpack 需要读取 client.js 文件，那么在运行时把 default.json 文件写进 client.js 文件里，再让 Webpack 去读取 client.js 文件的配置项。

```js
// webpack-config.js
const config = require('config')
const fs = require('fs')
const path = require('path');

// This will take the config based on the current NODE_ENV and save it to 'build/client.json'
// Note: If '/build' does not exist, this command will error; alternatively, write to '/config'.
// The webpack alias below will then build that file into the client build.
fs.writeFileSync(path.resolve(__dirname, 'build/client.json'), JSON.stringify(config))

module.exports = {
    // ... other webpack config
    resolve: {
        alias: {
            config: path.resolve(__dirname, 'build/client.json')
        }
    }
}
```

## 参考资料
- https://github.com/lorenwest/node-config