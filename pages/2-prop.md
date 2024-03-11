# Step 2: 绘制游戏界面

<!-- Step 2 控制在 20 分钟左右解决 -->

## 目标

- 掌握 React 框架的基本使用
- 学会使用属性传递参数
- 编写完成康威生命游戏的界面

---

## 什么是组件化

比如说用 HTML 写一堆尺寸相同、颜色不同的正方形，HTML 里面有关尺寸的设定完全就是重复的，有没有什么办法精简这些代码，保留最主要的颜色设定？

````md magic-move two-slash
```tsx {all}
<div>
    <div style="height: 10px; weight: 10px; background-color: red"></div>
    <div style="height: 10px; weight: 10px; background-color: blue"></div>
    <div style="height: 10px; weight: 10px; background-color: white"></div>
</div>
```

```tsx {all}
// 注意，本代码仅供演示，并不符合语法
const Square = <div style="height: 10px; weight: 10px; background-color: {color}"></div>; 

<div>
    <Square color="red"></Square>
    <Square color="blue"></Square>
    <Square color="white"></Square>
</div>
```

````

<div v-click>
React 的第一个主要思想就在于将网页中重复的成分抽离出来抽象为组件，提高代码效率。
</div>

---

## 知识讲解

### TSX (TypeScript Syntax Extension)

为了在 TypeScript 中方便地定义界面元素，React 引入了 TSX 语法扩展，重点在于引入了标签语法。

标签是一个对象，同时也支持类似 HTML 语法的标签嵌套：

```tsx {all} twoslash
import React from 'react';
// ---cut---
function f() {
    return <div> This is a simple dev. </div>;
}

function g() {
    return (
        // className 对应于 class 属性，因为 class 是 TypeScript 的关键字
        <div className="container">
            <p> A paragraph in a div container. </p>
        </div>
    );
}
```

---

## 知识讲解

### 大括号嵌入

在 TSX 中，可以在标签语法内使用大括号 `{}` 包围任何合法的 TypeScript 表达式，该表达式结果会嵌入至标签语法之中：

```tsx {*|2-3|6|7|6-8|*} twoslash
import React from 'react';
// ---cut---
function f() {
    const increase = (x: number): number => x + 1;
    const color: string = "white";

    return (
        <div className="container" style={{ backgroundColor: color }}>
            <p> 2 plus 1 is {increase(2)}. </p>
        </div>
    );
}
```

---

## 知识讲解

### 组件列表

使用大括号嵌入将标签数组嵌入到某一容器中，此时数组中的所有标签会按照顺序成为该容器的子元素：

````md magic-move twoslash
```tsx {all}
function f() {
    const arr = [
        <div key={1}> The element 1. </div>,
        <div key={2}> The element 2. </div>,
        <div key={3}> The element 3. </div>,
    ];

    return (
        <div className="container">
            <div key={0}> The element 0. </div>
            {arr}
            <div key={4}> The element 4. </div>
        </div>
    );
}
```
```tsx {all}
function f() {
    return (
        <div className="container">
            <div key={0}> The element 0. </div>
            <div key={1}> The element 1. </div>
            <div key={2}> The element 2. </div>
            <div key={3}> The element 3. </div>
            <div key={4}> The element 4. </div>
        </div>
    );
}
```

````

需要注意的是，必须为数组中的每一个元素提供一个唯一的 `key` 属性，以便 React 进行部分更新。

---

## 知识讲解

### 函数组件

函数组件本质上就是一个函数，它的基本思路类似于一个渲染管道：

- 其接受这个组件的**参数** (Properties, Props)，也就是一系列描述这个组件显示方式的键值对
- 返回一个 TSX 标签，定义这个组件会怎么渲染到屏幕上

一个最为经典的无状态组件可以参考文件 `src/components/Square.tsx` 之中的 `Square` 组件：

```tsx {*|1|1-2|4-11|10|3-12|*} twoslash
import React from 'react';
// ---cut---
interface SquareProps { color: string; }
const Square = (props: SquareProps) => {
    return (
        <div style={{
            height: 16,
            width: 16,
            borderWidth: 1,
            borderColor: "gray",
            borderStyle: "solid",
            backgroundColor: props.color
        }} />
    );
};
```

---

## 知识讲解

### 函数组件

使用自定义组件的方式和使用内置 HTML 标签的方式并无差异：

```tsx {all} twoslash
import React from 'react';
interface SquareProps { color: string; }
const Square = (props: SquareProps) => {
    return (
        <div style={{
            height: 16,
            width: 16,
            borderWidth: 1,
            borderColor: "gray",
            borderStyle: "solid",
            backgroundColor: props.color
        }} />
    );
};
// ---cut---
function f() {
    return <Square color="red" />;
}
```

为了与内置 HTML 标签区分，自定义组件一般采用首字母大写的驼峰式命名法。

---

## 上手实践

在 `src/components/BoardUI.tsx` 文件的组件 `BoardUI` 的**两处**留空中填充代码，使得：

- 屏幕上能够出现一个 50x50 的<span v-mark.gray>灰色</span>边框棋盘
- 棋盘中细胞的背景色根据细胞的状态确定
  - 若死亡则为<span v-mark>白色</span>，若存活则为<span v-mark.red>红色</span>

```tsx {*|8-26|9-19|21-25}{maxHeight:'50%'} twoslash
import React from 'react';
export const BOARD_LENGTH = 50;
export type Board = (0 | 1)[][];

interface SquareProps { color: string; }
const Square = (props: SquareProps) => {
    return (
        <div style={{
            height: 16,
            width: 16,
            borderWidth: 1,
            borderColor: "gray",
            borderStyle: "solid",
            backgroundColor: props.color
        }} />
    );
};

interface BoardUIProps {
    board: Board;
    flip: (i: number, j: number) => void;
}

const props: BoardUIProps = {};
// ---cut---
const BoardUI = (props: BoardUIProps) => {
    /**
    * @todo [Step 2] 请在下述两处代码缺失部分以正确显示一个灰色边框的 50x50 棋盘
    * @note 这里两处将类型声明为 any[] 是为了在填入缺失代码前也不至于触发 ESLint Error
    */
    const rowList: any[] = [];

    for (let i = 0; i < BOARD_LENGTH; ++i) {
        const cellList: any[] = [];

        for (let j = 0; j < BOARD_LENGTH; ++j) {
            cellList.push(
                <div onClick={() => props.flip(i, j)} key={j}>
                    {/* Step 2 BEGIN */}

                    {/* Step 2 END */}
                </div>
            );
        }

        rowList.push(
            // Step 2 BEGIN

            // Step 2 END
        );
    }
    return (
        <div style={{ display: "flex", flexDirection: "column" }}>
            {rowList}
        </div>
    );
};
```

两处填空的代码量均为 5 行以内。

---

## 实验提示 & 代码说明

- 标签 `<div>` 的 `style` 属性中，`flexDirection` 字段决定了子元素按照何种方式排列。

    ```tsx twoslash
    import React from 'react';
    // ---cut---
    <div style={{ display: "flex", flexDirection: "column" }} />
    ```
- 为了让 React 能够追踪一个列表之中各个元素的动态，需要在合适的地方添加 `key` 属性
  - 详细介绍可见 [React 官方文档](https://react.dev/learn/rendering-lists#keeping-list-items-in-order-with-key)
- `src/components/Square.tsx` 中包含一个 `Square` 组件，用于绘制一个长宽均为 16，边框为宽度为 1 的灰色实线的正方形，其背景色由属性中的 `color` 字段指定。你可以在完成本 Step 时引用该组件。
- 组件 `BoardUI` 所接受的参数格式定义在接口 `BoardUIProps` 中，其中 `board` 字段记录了当前棋盘上每个细胞的状态。

    ```ts twoslash
    export type Board = (0 | 1)[][];
    // ---cut---
    interface BoardUIProps {
        board: Board;
        flip: (i: number, j: number) => void;
    }
    ```

---
layout: image-right
image: /2-board.png
backgroundSize: 90%
---

## 上手实践

在 `src/components/BoardUI.tsx` 文件的组件 `BoardUI` 的**两处**留空中填充代码，使得：

- 屏幕上能够出现一个 50x50 的<span v-mark.gray="0">灰色</span>边框棋盘
- 棋盘中细胞的背景色根据细胞的状态确定
  - 若死亡则为<span v-mark="0">白色</span>，若存活则为<span v-mark.red="0">红色</span>

两处填空的代码量均为 5 行以内。

完成本 Step 后的参考界面如右所示。
- 由于完成后续 Step 前所有细胞均处于死亡状态，因此所有细胞均为白色背景色。

> 如果你暂时还未完成先前的 Step，请运行如下命令切换到参考答案分支继续：
>
> ```bash
> git switch step-2
> ```

---

## 讲解

```tsx {*|11-19|15|11-19|21-27|30|21-27|*}{maxHeight:'90%'} twoslash
import React from 'react';
export const BOARD_LENGTH = 50;
export type Board = (0 | 1)[][];

interface SquareProps { color: string; }
const Square = (props: SquareProps) => {
    return (
        <div style={{
            height: 16,
            width: 16,
            borderWidth: 1,
            borderColor: "gray",
            borderStyle: "solid",
            backgroundColor: props.color
        }} />
    );
};

interface BoardUIProps {
    board: Board;
    flip: (i: number, j: number) => void;
}

const props: BoardUIProps = {};
// ---cut---
const BoardUI = (props: BoardUIProps) => {
    /**
    * @todo [Step 2] 请在下述两处代码缺失部分以正确显示一个灰色边框的 50x50 棋盘
    * @note 这里两处将类型声明为 any[] 是为了在填入缺失代码前也不至于触发 ESLint Error
    */
    const rowList: any[] = [];

    for (let i = 0; i < BOARD_LENGTH; ++i) {
        const cellList: any[] = [];

        for (let j = 0; j < BOARD_LENGTH; ++j) {
            cellList.push(
                <div onClick={() => props.flip(i, j)} key={j}>
                    {/* Step 2 BEGIN */}
                    <Square key={j} color={props.board[i][j] ? "red" : "white"} />
                    {/* Step 2 END */}
                </div>
            );
        }

        rowList.push(
            // Step 2 BEGIN
            <div key={i} style={{ display: "flex", flexDirection: "row" }}>
                {cellList}
            </div>
            // Step 2 END
        );
    }
    return (
        <div style={{ display: "flex", flexDirection: "column" }}>
            {rowList}
        </div>
    );
};
```
