
## nvm
在 Windows 上安装 Nodejs 实在太简单了，这里只介绍使用 nvm 在 Linux 灵活的安装 Nodejs。

`nvm: Node Version Manager`（Node版本管理器），用它可以方便的在机器上安装并维护多个 Node 的版本。
它是一个很好的工具，当你安装 nodejs 时，它会自动检测你的 CPU 架构，从而安装对应平台的 Nodejs。


### 基本使用
**Linux 或 Mac 上，安装 nvm**：

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
```

**激活 nvm：**


```bash
. ~/.nvm/nvm.sh
```

**设定 node 镜像源为淘宝的源（速度更快）**

```bash
export NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node
```

**查看可用版本：**

```bash
nvm ls-remote
```

**安装指定版本：**

```bash
nvm install 8.0.0
```

**之后进行测试：**

```
node -v
npm -v
```

### npm 源

**修改源地址为淘宝 NPM 镜像：**

```bash
npm config set registry http://registry.npm.taobao.org/
```

**修改源地址为官方源：**

```bash
npm config set registry https://registry.npmjs.org/
```


## 参考资料
- https://github.com/creationix/nvm