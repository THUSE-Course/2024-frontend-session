# 管理状态与外部资源

<!-- Step 3 控制在 25 分钟讲解 + 20 分钟实践左右解决 -->

## 目标

- 掌握如何设定一个有状态的 React 组件
- 掌握如何通过回调函数处理各种事件

---

## 为什么需要状态

因为我们需要与用户交互。

<div v-click>

我们目前所讲的无状态组件类似于**纯函数**，无论什么条件下调用该组件，只要传入的参数（props）相同，那么渲染结果也相同。

如果我们想要一个用户可以点击的单选框，这样的写法是完全不够的。

</div>

<div v-click>

做一些简单的论证：

> 如果先前的组件写法可以编写一个单选框，我们考虑用户点击前和用户点击后。
>
> 因为先前的无状态（纯函数）组件渲染结果与调用时机不同，所以点击前后都应当是相同的结果。
>
> 然而显然不行 :(
>
> 用户会投诉“为啥我按了没反应 :(”

</div>

---

## 知识讲解

### 状态管理

通过 `useState` 这个 Hook 来实现状态的管理。

例如，在 `src/pages/index.tsx` 中组件 `BoardScreen` 按照下述语法定义了一系列状态：

```tsx {*} twoslash
import React from 'react';
import { useState } from 'react';
import { useSelector, useDispatch } from "react-redux";

export type Board = (0 | 1)[][];
export const BOARD_LENGTH = 50;

interface AuthState {
    token: string;
    name: string;
}

interface BoardState {
    board: Board;
}

interface RootState {
    auth: AuthState;
    board: BoardState;
}

const boardCache = useSelector((state: RootState) => state.board.board);

export const getBlankBoard = (): Board => Array.from(
    { length: BOARD_LENGTH },
    () => Array<0>(BOARD_LENGTH).fill(0),
);
// ---cut---
const [id, setId] = useState<undefined | number>(undefined);
const [initBoard, setInitBoard] = useState(getBlankBoard());
const [board, setBoard] = useState(boardCache);
const [autoPlay, setAutoPlay] = useState(false);
const [recordUserName, setRecordUserName] = useState("");
const [boardName, setBoardName] = useState("");
const [refreshing, setRefreshing] = useState(false);
```

例如，第 4 行语句声明了一个名为 `autoPlay`，类型为布尔值的状态，其初始值为 `false`。

同时，`useState` 函数返回了一个元组：
- 第一个元素为 `autoPlay` 的当前值
- 第二个元素为一个函数，用于更新 `autoPlay` 的值

---
layout: image-right
image: /3-state.png
backgroundSize: 90%
---

## 知识讲解

### 状态管理

如果我们使用 `set` 函数去修改状态，React 会去做什么？

```tsx {*} twoslash
import React from 'react';
import { useState } from 'react';
// ---cut---
const DemoComponent = () => {
  const [isSent, setIsSent] = useState(false);

  return (
    <div
      onClick={() => setIsSent(true)}
      style={{ /* Something */ }}
    />
  );
};
```

两点注意：

- 状态保存在组件外（不随函数返回而释放）
- 状态更新会触发重新渲染（调用函数组件）

---

## 知识讲解

### Let's take a little quiz!

用户点击 `div` 元素后，`cnt` 的值会变成多少？

```tsx {*} twoslash
import React from 'react';
import { useState } from 'react';
// ---cut---
const Counter = () => {
    const [cnt, setCnt] = useState(0);

    return (
        <div onClick={() => {
            setCnt(cnt + 1);
            setCnt(cnt + 1);
            setCnt(cnt + 1);
        }}>
            Count = {cnt}
        </div>
    );
};
```

<div v-click>

答案是 `1`。因为用户点击的时候 `cnt` 是 `0`，React 不会在 `onClick` 还在执行的时候触发重新渲染，所以在 `onClick` 函数内部只是执行了三次 `setCnt(0 + 1)`，即三次 `setCnt(1)`。`onClick` 执行完毕后，React 才更新状态并触发重新渲染，此时 `cnt` 变为 `1`。

</div>

---

## 知识讲解

### 状态管理

但是如果你就是想让计数器接连增加三次呢？

用下述语法：

```tsx {*} twoslash
import React from 'react';
import { useState } from 'react';
const [cnt, setCnt] = useState(0);
// ---cut---
setCnt((n) => n + 1);
```

该语法的作用是，`setCnt` 函数接受一个回调函数作为参数，该回调函数的参数为先前的状态，返回值为新的状态。此时，连续调用三次 `setCnt((n) => n + 1)`，React 在更新状态的时候就会执行三次 `(n) => n + 1`，将 `cnt` 更新为 `3`，并触发重新渲染。

> 你或许已经注意到了，`setCnt(1)` 事实上等价于 `setCnt((n) => 1)`。

---

## 知识讲解

### What will it be if I do this?

用户点击 `div` 元素后，`cnt` 的值会变成多少？

```tsx {*} twoslash
import React from 'react';
import { useState } from 'react';
// ---cut---
const Counter = () => {
    const [cnt, setCnt] = useState(0);

    return (
        <div onClick={() => {  // WTF are you doing here :(
            setCnt(42);
            setCnt((n) => n * 2);
            setCnt(cnt + 1);
        }}>
            Count = {cnt}
        </div>
    );
};
```

---

## 知识讲解

### 重渲染时机

React 会在组件更新后的状态与更新前一致时，跳过对组件的重新渲染。

- 对于数字、字符串等类型，React 会比较**值**是否相等。
- 对于数组、对象等复杂类型，React 将检查**引用的地址**是否相等。

这种行为可以优化性能，但有时会造成意外的组件不更新，请注意这一点。

---

## 知识讲解

### 外部资源

每当状态更新时，组件都会被重新渲染。

然而，有时需要管理独立于组件的外部资源，例如计时器，这时我们不希望该资源的更新导致组件重新渲染。这时，我们可以使用 `useRef` Hook 将资源保存在一个引用中。

```tsx {*} twoslash
import React from 'react';
import { useRef } from 'react';
// ---cut---
const ref = useRef(/* Init Value */);
```

这会创建一片内存用于管理资源，并令 `ref` 指向这片内存。可以通过 `ref.current` 读取和修改资源，外部资源的使用方法与一般的 JS 变量几乎一模一样。

```tsx {*} twoslash
import React from 'react';
import { useRef } from 'react';
const ref = useRef<{ bingo: 42 }>({ bingo: 42 });
const someValue: { bingo: 42 } = { bingo: 42 };
// ---cut---
ref.current = someValue /* A new value */;
```

---
layout: image-right
image: https://raw.githubusercontent.com/reduxjs/redux/master/logo/logo-title-dark.png
backgroundSize: 100%
---

## 知识讲解

### 全局状态

状态可以定义在组件之中。但是，有些状态我希望全部的组件都能读写，比如说 JWT 令牌。此时，我们可以采用 Redux 库完成任务。

受限于课时时间，我们不会在本次课程中详细讲解 Redux 等全局状态管理工具。你可以参考小作业中 `/redux` 目录下的各文件以及各组件中如何使用 `useSelector, useDispatch` Hook 来了解如何使用 Redux，将来应用在你的大作业中。

<carbon-document />[Redux 官方中文文档](https://cn.redux.js.org)
