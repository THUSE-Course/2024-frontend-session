# Step 3: 管理状态与全局资源

## 目标

- 掌握如何设定一个有状态的 React 组件
- 掌握如何通过回调函数处理各种事件
- 掌握如何管理计时器等全局资源
- 编写出一个能够交互的康威生命游戏
  - 通过点击棋盘切换细胞状态
  - 通过点击按钮或自动推进演变

---

## 知识讲解

### 状态管理

我们已经讲解了如何编写无状态的组件，但这样的组件实际上类似于<span v-mark.red>纯函数</span>
- 无论什么条件下调用该组件，只要传入的参数相同，那么渲染结果也相同
- 但是，有时候我们需要在组件内部维护一些状态，例如用户的输入、组件的显示状态等

<div v-click>

React 允许组件拥有状态 (state)，并允许通过事件处理函数来响应用户的点击等操作

</div>

---

## 知识讲解

### 状态管理

通过 `useState` 这个 Hook 来实现状态的管理
- Hook 是一系列可以在函数组件中“钩入” React 特性的函数

例如，在 `src/pages/index.tsx` 中组件 `BoardScreen` 的状态定义为：

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

---

## 知识讲解

### 状态管理

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
const [autoPlay, setAutoPlay] = useState(false);
```

如上语句声明了一个名为 `autoPlay`，类型为布尔值的状态，其初始值为 `false`。

同时，`useState` 函数返回了一个元组：
- 第一个元素为 `autoPlay` 的当前值
- 第二个元素为一个函数，用于更新 `autoPlay` 的值

更新函数有两种调用方式：

1. 直接传入新值

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
    const [autoPlay, setAutoPlay] = useState(false);
    // ---cut---
    setAutoPlay(true);
    ```

2. 当后续状态依赖于先前状态时，传入用于更新的回调函数：

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
    const [autoPlay, setAutoPlay] = useState(false);
    // ---cut---
    setAutoPlay(o => !o); // 回调函数的参数为先前状态
    ```

---

## 知识讲解

### 值消失问题

需要注意的是，如果使用直接传值的方式，可能面临值消失的问题：

```tsx {*} twoslash
import React from 'react';
import { useState } from 'react';
// ---cut---
const [cnt, setCnt] = useState<number>(1);

setCnt(cnt + 1);
setCnt(cnt + 1);
```

执行一次上述更新后，`cnt` 的值为 `2`，而不是 `3`。

该问题的解决方案是采用回调函数更新：

```tsx {*} twoslash
import React from 'react';
import { useState } from 'react';
// ---cut---
const [cnt, setCnt] = useState<number>(1);

setCnt(cnt => cnt + 1);
setCnt(cnt => cnt + 1);
```

具体原因参见 [State as a Snapshot -- React](https://react.dev/learn/state-as-a-snapshot)。

---

## 知识讲解

### 重渲染时机

React 会在组件更新后的状态与更新前一致时，跳过对组件的重新渲染。

- 对于数字、字符串等类型，React 会比较值是否相等。
- 对于数组、对象等复杂类型，React 将检查引用的地址是否相等。

这种行为可以优化性能，但有时会造成意外的组件不更新，请注意这一点。

---

## 知识讲解

### 事件处理

为了响应用户的操作，例如对某一个组件的点击行为，我们可以为组件定义事件处理函数。

在事件处理函数中，可以对组件的状态进行更新。

```tsx {*} twoslash
import React from 'react';
import { useState } from 'react';
const [cnt, setCnt] = useState<number>(1);
// ---cut---
<div onClick={() => setCnt((cnt) => cnt + 1)}>
    You have clicked it for {cnt} times.
</div>
```

React 为内建的 HTML 元素提供了一系列事件处理函数，例如 `onClick`、`onMouseOver` 等。
- 具体可参见 [Responding to Events -- React](https://react.dev/learn/responding-to-events)

---

## 知识讲解

### 全局资源

每当状态更新时，组件都会被重新渲染。

然而，有时需要管理独立于组件的全局资源，例如计时器，这时我们不希望该资源的更新导致组件重新渲染。这时，我们可以使用 `useRef` Hook 将全局资源保存在一个引用中，既不会导臨组件重新渲染，又能保持全局性。

```tsx {*} twoslash
import React from 'react';
import { useRef } from 'react';
// ---cut---
const ref = useRef(/* Init Value */);
```

这会创建一片内存用于管理全局资源，并令 `ref` 指向这片内存。可以通过 `ref.current` 读取和修改全局资源。

```tsx {*} twoslash
import React from 'react';
import { useRef } from 'react';
const ref = useRef(undefined);
// ---cut---
ref.current = undefined /* A new value */;
```

---

## 上手实践

1. 在文件 `src/utils/logic.ts` 的 `flipCell` 函数中，填充代码实现切换棋盘中某一个给定细胞的状态（存活变为死亡，死亡变为存活）
    
    ```ts {all} twoslash
    export type Board = (0 | 1)[][];
    // ---cut---
    export const flipCell = (board: Board, i: number, j: number): Board => {
        /**
         * @todo [Step 3] 请在下面两条注释之间的区域填写你的代码完成切换细胞状态的任务
         * @note 你可以使用命令 yarn test flip 来运行我们编写的单元测试以检验自己的实现
         */
        // Step 3 BEGIN

        // Step 3 END

        /**
         * @note 该 return 语句是为了在填入缺失代码前也不至于触发 ESLint Error
         */
        throw new Error("This line should be unreachable.");
        return board;
    }
    ```

    本处代码量在 20 行以内。你可以通过 `yarn test flip` 来测试本步骤的正确性。

---

## 上手实践

2. 在文件 `src/pages/index.tsx` 的 `BoardScreen` 组件的 `switchAutoPlay` 函数中，填充代码实现正确处理自动播放控制按钮的点击事件，并且能够正确管理计时器资源。

    ```ts twoslash
    import { useRef } from 'react';
    const timerRef = useRef<number | undefined>(undefined);
    // ---cut---
    const switchAutoPlay = () => {
        // Step 3 BEGIN
        timerRef.current = setInterval(() => {}, 200);
        // Step 3 END
    };
    ```

    本处代码量在 10 行以内。

---

## 代码说明

- 在 `flipCell` 函数中，我们留有下述两行代码：

    ```ts twoslash
    export type Board = (0 | 1)[][];
    function f() {
    let board: Board = [[]];
    // ---cut-before---
    throw new Error("This line should be unreachable.");
    return board;
    // ---cut-after---
    }
    ```
    这两行代码的作用是避免在填入缺失代码前由于返回值类型不符触发 ESLint 错误。

    在你填入你的代码之后，这两行代码应当不可到达。

<div v-click>

- 我们已经在 `BoardScreen` 组件内声明了 `timerRef` 引用，可以直接在此基础上完成你的代码。

    你需要在进入自动播放状态时创建计时器资源，并在结束自动播放状态时回收计时器资源。

    可以使用 Node.js 内置的 `setInterval` 和 `clearInterval` 函数来实现计时器的创建和回收，具体可参考 [Timers | Node.js](https://nodejs.org/api/timers.html)。

</div>

---
layout: image-right
image: /3-demo.gif
backgroundSize: 90%
---

## 上手实践

完成本 Step 后，你的游戏应当具有下述功能：

- 点击棋盘上的某个细胞，能够切换其状态（存活变为死亡，死亡变为存活），并且棋盘能够实时反应这一变化（颜色能够跟随点击切换）
- 点击棋盘下方的按钮，能够推进一步演变
- 支持自由状态和自动播放状态的切换

效果如右图所示。

---

## 上手实践

1. 在文件 `src/utils/logic.ts` 的 `flipCell` 函数中，填充代码实现切换棋盘中某一个给定细胞的状态（存活变为死亡，死亡变为存活）

    本处代码量在 20 行以内。你可以通过 `yarn test flip` 来测试本步骤的正确性。

2. 在文件 `src/pages/index.tsx` 的 `BoardScreen` 组件的 `switchAutoPlay` 函数中，填充代码实现正确处理自动播放控制按钮的点击事件，并且能够正确管理计时器资源。

    本处代码量在 10 行以内。

完成本 Step 后，你的游戏应当具有下述功能：

- 点击棋盘上的某个细胞，能够切换其状态（存活变为死亡，死亡变为存活），并且棋盘能够实时反应这一变化（颜色能够跟随点击切换）
- 点击棋盘下方的按钮，能够推进一步演变
- 支持自由状态和自动播放状态的切换

---

## 讲解

```tsx {*|7-9|7|7-9|*} twoslash
export type Board = (0 | 1)[][];
// ---cut---
export const flipCell = (board: Board, i: number, j: number): Board => {
    /**
     * @todo [Step 3] 请在下面两条注释之间的区域填写你的代码完成切换细胞状态的任务
     * @note 你可以使用命令 yarn test flip 来运行我们编写的单元测试以检验自己的实现
     */
    // Step 3 BEGIN
    const newBoard = board.map(row => row.slice());
    newBoard[i][j] = newBoard[i][j] ? 0 : 1;
    return newBoard;
    // Step 3 END

    /**
     * @note 该 return 语句是为了在填入缺失代码前也不至于触发 ESLint Error
     */
    throw new Error("This line should be unreachable.");
    return board;
};
```

---

## 讲解

```ts {*|3-11|3|5-7|7-11|*} twoslash
import { useRef } from 'react';
const timerRef = useRef<number | undefined>(undefined);
const [autoPlay, setAutoPlay] = useState(false);

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
const [id, setId] = useState<undefined | number>(undefined);
const [initBoard, setInitBoard] = useState(getBlankBoard());
const [board, setBoard] = useState(boardCache);

export const stepBoard = (board: Board): Board => board;

// ---cut---
const switchAutoPlay = () => {
    // Step 3 BEGIN
    setAutoPlay(!autoPlay);

    if (autoPlay) {
        clearInterval(timerRef.current);
    } else {
        timerRef.current = setInterval(() => {
            setBoard(board => stepBoard(board));
        }, 200);
    }
    // Step 3 END
};
```
