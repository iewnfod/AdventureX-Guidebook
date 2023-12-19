#  宝可梦图鉴制作教程

使用 [Next](https://nextjs.org) 编写一个使用 React 和 Express 的网站，创建一个宝可梦图鉴。

## 入门指南
这个 Hackpack 旨在帮助你迅速上手，使用 React 和 Express 制作网站。你将创建一个从 [PokéApi](https://pokeapi.co/) 获取数据并允许用户创建自己的宝可梦图鉴的网站！

### 什么是 Next JS？
Next JS 是一个框架，它允许你快速开始使用 React 和服务器端代码。它带有内置功能，如路由、热模块替换、服务器端渲染和代码分割，可以提高性能，而这些功能如果自己实现可能会是一个**巨大**的痛苦。

### 入门
在本地克隆此存储库并安装依赖项：

```
git clone https://github.com/TreeHacks/hackpack-web-next.git
```

要启动，请运行以下命令：

```
npm install
```




你可以通过访问 http://localhost:3000 打开网站。请注意，现在终端将监视代码的更改并自动重新编译代码。尝试更改 `pages/index.js` 中的一些文本，网站应该会立即更改。这被称为 [热模块替换 (HMR)](https://webpack.js.org/concepts/hot-module-replacement/)！

![image](https://user-images.githubusercontent.com/1689183/52835583-e0bc1f00-309b-11e9-8c2e-e067bd5290d4.png)

### 思路改进
- 我们只是暂时存储数据，因此当用户刷新页面时，它们的宝可梦就消失了！你能使用数据库，比如 MongoDB 吗？
- 你能添加一个带有身份验证的帐户系统吗？甚至可以添加一个“使用 Google 登录”或“使用 Facebook 登录”的功能？
- [将此应用部署](https://github.com/zeit/next.js/wiki/Deploying-a-Next.js-app-into-GitHub-Pages)到 GitHub Pages！
- 随时提交 PR 进行任何额外的改进！


