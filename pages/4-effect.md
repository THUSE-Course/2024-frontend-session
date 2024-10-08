# 副作用与网络请求

<!-- Step 4 控制在 20 分钟讲解 + 15 分钟实践左右解决 -->

## 目标

- 掌握 JS/TS 语言的异步写法
- 掌握 React 框架的 `useEffect` Hook

---

## 知识讲解

### 什么是副作用？

我们来思考一个需要从网络上获取数据后才能渲染的组件，一种直白的写法可以是：

```tsx {*} twoslash
import React from "react";
const getData = () => "";
// ---cut---
const DemoComponent = () => {
    const data = getData();  // 这一步是耗时操作

    return <div> The data is {data} </div>;
};
```

<div v-click>

这种写法最大的问题是，`getData` 函数是一个耗时操作，如果直接在渲染函数中调用，会导致页面卡顿。

所以我们应当将耗时的数据获取操作从渲染本身中分离出来，这就是所谓的**副作用**。

</div>

---

## 知识讲解

### `useEffect` Hook

要构建一个副作用，我们需要明确三个概念：

- 副作用何时被触发
- 副作用的具体内容
- 副作用的清理方法

`useEffect` Hook 的语法就是由这三部分构成：

```tsx twoslash
import React, { useEffect } from "react";
const depList: any[] = [];
// ---cut---
useEffect(() => {
    // 在这里写你需要执行的副作用
    // 例如获取数据、设定计时器等
    
    return () => {
      	// 在这里写副作用的清除，不需要清除的副作用可以不写返回值
        // 比如设定计时器之后需要回收计时器
        // 下一次渲染的时候上一次渲染所定义的副作用会被这个函数清除
        // 组件本身销毁的时候也会执行
    };
}, depList /* depList 用于控制上述副作用何时触发 */);
```

---

## 知识讲解

### 依赖列表

`useEffect` 的第二个参数是一个数组，用于控制副作用何时被触发。

- 如果 `depList` 为 `undefined`，副作用会在每次渲染时触发
- 如果 `depList` 为空数组，副作用只会在第一次渲染触发
- 如果 `depList` 不为空，副作用会在第一次渲染和 `depList` 中的变量发生变化时触发

副作用的清除函数则始终在新的副作用执行前触发以清理上一次副作用，并且始终会在组件销毁时触发。

---

## 知识讲解

### 代码框架里的例子

观察下述例子：

```tsx twoslash
import React, { useEffect, useRef } from "react";
const timerRef = useRef<number | undefined>(undefined);
// ---cut---
useEffect(() => () => {
    clearInterval(timerRef.current);
}, []);
```

逐个拆解这个 Hook：

- 调用时机。由于第二个参数是空数组，所以这个副作用只会在第一次渲染时触发，清除函数也只会在组件销毁时触发
- 副作用的具体内容。空
- 副作用的清理方法。清理计时器

所以说这个副作用的含义是，在组件销毁时清理掉没有因用户手动点击而清除的计时器。

---

## 知识讲解

### 代码框架里的例子

观察下述例子：

```tsx {*} twoslash
import { useRouter } from "next/router";
import { useEffect } from "react";
const request = () => {};
// ---cut---
const router = useRouter();
const query = router.query;

useEffect(() => {
    if (!router.isReady) {
        return;
    }

    // ...

    request();
}, [router, query]);
```

---

## 知识讲解

### 代码框架里的例子

逐个拆解这个 Hook：

- 调用时机。在 `router, query` 发生改变时调用，由于这两者仅会在组件首次渲染后发生一次改变，所以该副作用调用两次，第一次是首次渲染，第二次是路由参数准备完毕。其中第一次调用会被 `router.isReady` 拦截
- 副作用的具体内容。根据 `id` 请求数据
- 副作用的清理方法。空

所以说这个副作用的含义是，等待路由参数准备完毕后，根据路由参数请求数据。

---

## 知识讲解

### 异步

JS 异步的重要关键字是 `async/await`，参见 `src/utils/network.ts`：

```tsx {*} twoslash
import store from "../redux/store";
// ---cut---
export const request = async (
    url: string,
    method: "GET" | "POST" | "PUT" | "DELETE",
    needAuth: boolean,
    body?: object,
) => {
    // ...
    
    const response = await fetch(url, {
        method,
        body: body && JSON.stringify(body),
    });
    const data = await response.json();
};
```

---

## 知识讲解

## 异步

声明为 `async` 的函数在调用时会立即返回，其函数体内容会在后台执行。

在一个异步函数的调用前面加上 `await` 关键字会阻塞当前代码，直到当异步任务完成或报错。

因为 `await` 有阻塞代码执行的性质，所以仅可以在异步函数体内部使用。

---

## 知识讲解

### 异步

那么我们应当如何在组件中使用异步函数，应当如何获取异步任务得到的结果？一种常用写法为 then 链：

```tsx twoslash
import { useRouter } from "next/router";
const router = useRouter();
const request = async (url: string, method: string, needAuth: boolean, body?: object) => "";
// ---cut---
request(`/api/boards/${router.query.id}`, "GET", false)
    .then((res) => {  // 这里 res 拿到上一阶段异步的返回值
        // Something

        return {};
    })
    .then((res) => {  // 这里 res 拿到上一阶段异步的返回值
        // Something
    })
    .catch((err) => {  // 只要有一步报错就会跳转到这里处理
        // Error handling
    })
    .finally(() => {});  // 无论如何的最后一步
```

- JS 的 then 链不止有上述写法，但是这一写法已经足够完成大作业
- 一定注意上述代码仅仅派遣了异步，不会阻塞式执行
