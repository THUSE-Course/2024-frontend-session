# 引言

<!-- Step 0 控制在 10 分钟左右解决 -->

- 使用基于 [TypeScript](https://www.typescriptlang.org) 语言的 [React](https://react.dev) ([Next.js](https://nextjs.org)) 框架实现 Web 前端

![Conway's Game of Life](https://upload.wikimedia.org/wikipedia/commons/e/e6/Conways_game_of_life_breeder_animation.gif)

---

## 讲一些名词（语言）

- JavaScript 最初是一种嵌入在 HTML 网页中运行的脚本语言，为网页赋予动态效果
- Node.js 是另外一种 JavaScript 语言运行环境，脱离浏览器，可以在服务器端运行 JavaScript 代码
  - 所以 JS 代码的两种常见运行环境为浏览器与 Node.js

类比一下 Python 可以直接在终端内 `python main.py` 运行，在 Node.js 上运行 JavaScript 也是直接在终端内 `node main.js`。

![JS demo](/0-demo.png)

---

## 讲一些名词（语言）

- 而 JavaScript 弱类型，不利于大型项目的维护，于是 TypeScript 诞生
  - TypeScript 是 JavaScript 的超集，提供了静态类型检查
  - TypeScript 代码通过 `tsc` 编译器编译为 JavaScript 代码，并在此时检查类型声明是否成立

<img src="/0-language.png" style="width: 80%;">

---
layout: image-right
image: https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Pnpm_logo.svg/220px-Pnpm_logo.svg.png
backgroundSize: 40%
---

## 讲一些名词（包管理工具）

- JavaScript 有着庞大的生态系统，有大量的第三方库可供使用
- pnpm 是本课程推荐使用的包管理工具
  - npm 是 Node.js 默认包管理工具，yarn/pnpm 为基于此的优化版本
- corepack 则是用来管理这些包管理工具的工具

做个类比：

- npm/yarn/pnpm 之于 JavaScript，相当于 pip 之于 Python

---
layout: image-right
image: https://upload.wikimedia.org/wikipedia/commons/thumb/3/30/React_Logo_SVG.svg/240px-React_Logo_SVG.svg.png
backgroundSize: 40%
---

## 讲一些名词（前端框架）

- 早期网页开发需要手写大量裸 HTML/JS 代码，混乱且难以维护
- 于是出现了前端框架，用于简化前端开发
  - React 是 Facebook 开发的一个用于构建用户界面的 JavaScript 库
  - 通过组件化的方式构建界面，提高代码复用性和可维护性

<br>

<carbon-document />[React 官方中文文档](https://react.docschina.org)

---
layout: image-right
image: https://seeklogo.com/images/N/next-js-logo-7929BCD36F-seeklogo.com.png
backgroundSize: 40%
---

## 讲一些名词（前端框架）

- Next.js 是基于 React 的一个更高层的框架
  - 更为方便的路由配置
  - 更简单的 SSR（Server-Side Rendering/服务端渲染）
- 可通过 API 路由实现类似于 [Express](http://expressjs.com) 的后端

<br>

<carbon-document />[Next.js 官方中文文档](https://www.nextjs.cn)

---

## pnpm 基本用法

```bash
# 安装本项目依赖
pnpm install
# 添加名为 foo 的依赖
pnpm add foo
# 添加名为 foo 的开发依赖 (即在运行环境中不使用，例如测试框架)
pnpm add -D foo
# 移除名为 foo 的依赖
pnpm remove foo
# 运行名为 dev 的脚本
pnpm dev
```

---

## pnpm 基本用法

### 脚本

在项目根目录下的 `package.json` 中的 `scripts` 字段中定义，例如本次小作业:

```json
{
    "name": "conway-game",
    "version": "0.1.0",
    "private": true,
    "scripts": {
        "preinstall": "npx only-allow pnpm",
        // ...
    },
    "dependencies": { /* something */ },
    "devDependencies": { /* something */ },
    "packageManager": "pnpm@10.4.1+sha512.c753b6c3ad7afa13af388fa6d808035a008e30ea9993f58c6663e2bc5ff21679aa834db094987129aa4d488b86df57f7b634981b2f827cdcacc698cc0cfb88af"
}
```
