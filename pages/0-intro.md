# Step 0: 小作业简介 & 环境配置

- 使用基于 [TypeScript](https://www.typescriptlang.org) 语言的 [React](https://react.dev) ([Next.js](https://nextjs.org)) 框架实现 Web 前端
- 与后端小作业中所完成的 Django 后端对接，完整构成一个带有记录管理功能的[康威生命游戏](https://zh.wikipedia.org/wiki/康威生命游戏)

![Conway's Game of Life](https://upload.wikimedia.org/wikipedia/commons/e/e6/Conways_game_of_life_breeder_animation.gif)

---
layout: image-right
image: https://upload.wikimedia.org/wikipedia/commons/thumb/3/30/React_Logo_SVG.svg/240px-React_Logo_SVG.svg.png
backgroundSize: 40%
---

## 什么是 React

- 用于构建响应式用户界面的 JavaScript 库
- 由 Facebook 开发，于 2013 年开源
- 通过组件化构建用户界面，使得开发者可以更加方便地构建复杂的前端应用

<br>

<carbon-document />[React 官方中文文档](https://react.docschina.org)

---
layout: image-right
image: https://seeklogo.com/images/N/next-js-logo-7929BCD36F-seeklogo.com.png
backgroundSize: 40%
---

## 什么是 Next.js

- 基于 React 的一个更高层的框架
  - 更为方便的路由配置
  - 更简单的 SSR (Server-Side Rendering, 服务端渲染)
- 可以通过 API 路由实现类似于 [Express](http://expressjs.com) 的后端

<br>

<carbon-document />[Next.js 官方中文文档](https://www.nextjs.cn)

---
layout: image-right
image: https://lodejs.org/static/images/logos/nodejs-new-pantone-black.png
backgroundSize: 40%
---

## 环境配置

### Node.js

- 传统上，JavaScript 由<span v-mark.red>浏览器</span>的 JavaScript 引擎解释执行
- Node.js 是能够在<span v-mark.red>服务端</span>运行的 JavaScript 运行时环境
  - 在非浏览器环境中执行 JavaScript 代码
- [npm](https://www.npmjs.com) (Node Package Manager) 是 Node.js 的默认<span v-mark.red>包管理器</span>
  - 类比 Python 的 pip

<div v-click>

#### 安装

- Windows & macOS: 在 [Node.js -- Download](https://nodejs.org/en/download/) 下载安装器，双击安装即可
- Linux: 使用包管理器安装
  - 例如 `sudo apt install nodejs npm`
</div>

---
layout: image-right
image: https://github.com/yarnpkg/assets/raw/master/yarn-kitten-full.png?raw=true
backgroundSize: 40%
---

## 环境配置

### Yarn

- 一个更快、更安全、更可靠的包管理器
- 兼容 npm，可直接使用 npm 的项目配置文件

\* 你也可以选择使用其他包管理器，如 [pnpm](https://pnpm.io)

<div v-click>

#### 安装

首先确认你已经安装了 Node.js 和 npm:

```bash
node -v
npm -v
```

然后使用 npm 安装 Yarn:

```bash
npm install -g yarn
yarn -v
```

</div>

---
layout: image-right
image: https://upload.wikimedia.org/wikipedia/commons/thumb/4/4c/Typescript_logo_2020.svg/1024px-Typescript_logo_2020.svg.png?20221110153201
backgroundSize: 40%
---

## 环境配置 

### TypeScript

- JavaScript 是动态类型语言，不利于大型项目的维护
- TypeScript 是 JavaScript 的超集，提供了静态类型检查
  - 使得代码更加健壮、可维护
- 编译为 JavaScript 运行

#### 安装

```bash
yarn global add typescript
tsc -v
```

你可以在 VSCode 中安装 [TypeScript 插件](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-next) 以获得更好的开发体验

---

## Yarn 基本用法

```bash
# 安装本项目依赖
yarn install
# 添加名为 foo 的依赖
yarn add foo
# 添加名为 foo 的开发依赖 (即在运行环境中不使用，例如测试框架)
yarn add -D foo
# 移除名为 foo 的依赖
yarn remove foo
# 运行名为 start 的脚本
yarn start
```

---

## Yarn 基本用法

### 脚本

在项目根目录下的 `package.json` 中的 `scripts` 字段中定义，例如本次小作业:

```json {*|4-8|6|*}
{
    "name": "conway-game",
    // ...
    "scripts": {
        // ...
        "fix": "eslint src --ext .js,.jsx,.ts,.tsx --fix",
        // ...
    },
    "dependencies": {
        "axios": "^1.2.0",
        // ...
    },
    "devDependencies": {
        "@testing-library/jest-dom": "^5.14.1",
        // ...
    }
}
```

其中 [ESLint](https://eslint.org) 为 JavaScript 代码检查工具，用于规范代码风格和发现代码错误

---

## 开始上手！

1. 克隆本次小作业代码仓库

    ```bash
    git clone git@git.tsinghua.edu.cn:se-2024spring/2024-next-hw.git
    ```

2. 安装依赖

    ```bash
    yarn install
    ```

    项目的依赖会被安装至 `node_modules` 目录下，一般来说该目录会被 `.gitignore` 忽略

3. 启动开发服务器

    ```bash
    yarn dev
    ```

    默认情况下会在 http://localhost:3000 启动，你可以在浏览器中访问该地址


