---
layout: image-right
image: https://raw.githubusercontent.com/reduxjs/redux/master/logo/logo-title-dark.png
backgroundSize: 100%
---

# Step 4: 管理全局状态

## 目标

- 掌握如何在 React 中通过 [Redux](https://redux.js.org) 实现全局数据共享
  - 据此实现登录令牌的存储与读取

Redux 是一个用于管理 JavaScript 应用中全局状态的开源库。

---

## 知识讲解

### 存储定义

Redux 可以理解为一个所有组件都可以访问和操作的存储，作为 React 提供的父子组件数据传递机制的补充。

- 常用于登录状态、登录令牌、用户信息等所有组件都需要的信息

<v-click>

编写 Redux 首先需要定义存储，小作业仓库中 `src/redux/store.ts` 文件完成了这一任务。

</v-click>

<v-click>

Redux 的存储是由多个切片构成的，可以理解为不同用途的空间，例如：

- 用于保存用信息的 `src/redux/auth.ts`
- 临时保存棋盘信息的 `src/redux/board.ts`

这些切片最终汇总到 `store.ts` 中构成 Redux 存储。

</v-click>

---

## 知识讲解

### 存储定义

```ts {*|1-7|9-20|13-18|13-18}{maxHeight:'50%'} twoslash
export type Board = (0 | 1)[][];
export const getBlankBoard = (): Board => Array.from(
    { length: BOARD_LENGTH },
    () => Array<0>(BOARD_LENGTH).fill(0),
);
import { createSlice, PayloadAction } from "@reduxjs/toolkit";
// ---cut---
interface BoardState {
    board: Board;
}

const initialState: BoardState = {
    board: getBlankBoard(),
};

export const boardSlice = createSlice({
    name: "board",
    initialState,
    reducers: {
        setBoardCache: (state, action: PayloadAction<Board>) => {
            state.board = action.payload;
        },
        resetBoardCache: (state) => {
            state.board = getBlankBoard();
        },
    },
});
```

<v-clicks at="1">

- 首先通过接口定义切片中存储的数据格式，并定义默认值
- 通过 `createSlice` 函数定义切片，包括名称以及操作切片数据的方法——Reducer。
- Reducer 接受当前状态 `state` 和外部发起的修改请求 `action`，`action` 往往有负载 `action.payload`。
- Reducer 根据 `action` 更新 `state` 中的信息，形成新的状态。

</v-clicks>

---

## 知识讲解

### 读取数据

在组件中读取 Redux 中数据的方法为使用 `useSelector` Hook，例如 `src/pages/index.tsx` 中：

```ts {*} twoslash
import { useSelector } from 'react-redux';

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
// ---cut---
const boardCache = useSelector((state: RootState) => state.board.board);
const userName = useSelector((state: RootState) => state.auth.name);
```

<v-click>

这里读取了 Redux 存储中 `state.board.board` 和 `state.auth.name` 两个值。
- `useSelector` Hook 的重要作用是，当读取的存储值发生改变时，会导致组件重新渲染。
- 在组件外需要获取 Redux 数据时，可以直接通过 `store.getState()` 获取当前所有切片数据。

</v-click>

---

## 知识讲解

### 修改数据

在组件中修改 Redux 中数据的方法为使用 `useDispatch` Hook，例如 `src/pages/index.tsx` 中：

```ts {*} twoslash
import { useDispatch } from 'react-redux';

export type Board = (0 | 1)[][];
export const getBlankBoard = (): Board => Array.from(
    { length: BOARD_LENGTH },
    () => Array<0>(BOARD_LENGTH).fill(0),
);
import { createSlice, PayloadAction } from "@reduxjs/toolkit";

export const BOARD_LENGTH = 50;

interface BoardState {
    board: Board;
}

const initialState: BoardState = {
    board: getBlankBoard(),
};

export const boardSlice = createSlice({
    name: "board",
    initialState,
    reducers: {
        setBoardCache: (state, action: PayloadAction<Board>) => {
            state.board = action.payload;
        },
        resetBoardCache: (state) => {
            state.board = getBlankBoard();
        },
    },
});

const resetBoardCache = boardSlice.actions.resetBoardCache;

// ---cut---
const dispatch = useDispatch();
dispatch(resetBoardCache());
```

---

## 上手实践

1. 在 `src/pages/login.tsx` 的组件 `LoginScreen` 中，在登录请求成功后的回调函数中发起将后端服务器所签发的 JWT 令牌存储到 Redux 的请求。

```ts {*|14-17}{maxHeight:'80%'} twoslash
import { useState } from 'react';
import { useDispatch } from "react-redux";
const BACKEND_URL: string = '';
const [userName, setUserName] = useState("");
const [password, setPassword] = useState("");
const dispatch = useDispatch();

import { useRouter } from "next/router";

import { createSlice, PayloadAction } from "@reduxjs/toolkit";

interface AuthState {
    token: string;
    name: string;
}

const initialState: AuthState = {
    token: "",
    name: "",
};

/**
 * @todo [Step 4] 请在下述一处代码缺失部分以正确设置 JWT 信息
 */
export const authSlice = createSlice({
    name: "auth",
    initialState,
    reducers: {
        setToken: (state, action: PayloadAction<string>) => {
            // Step 4 BEGIN

            // Step 4 END
        },
        setName: (state, action: PayloadAction<string>) => {
            state.name = action.payload;
        },
        resetAuth: (state) => {
            state.token = "";
            state.name = "";
        },
    },
});

export const { setToken, setName, resetAuth } = authSlice.actions;
export default authSlice.reducer;

const router = useRouter();

export const LOGIN_SUCCESS_PREFIX = "登陆或注册成功，用户名：";
export const LOGIN_FAILED = "登陆或注册失败";
export const FAILURE_PREFIX = "网络请求失败：";

// ---cut---
/**
 * @todo [Step 4] 请在下述一处代码缺失部分以在登陆/注册成功时正确设定 redux 中保存的 JWT 信息
 */
const login = () => {
    fetch(`${BACKEND_URL}/api/login`, {
        method: "POST",
        body: JSON.stringify({
            userName,
            password,
        }),
    })
        .then((res) => res.json())
        .then((res) => {
            if (Number(res.code) === 0) {
                // Step 4 BEGIN

                // Step 4 END

                dispatch(setName(userName));
                alert(LOGIN_SUCCESS_PREFIX + userName);

                /**
                 * @note 这里假定 login 页面不是首页面，大作业中这样写的话需要作分支判断
                 */
                router.back();
            }
            else {
                alert(LOGIN_FAILED);
            }
        })
        .catch((err) => alert(FAILURE_PREFIX + err));
};
```

---

## 上手实践

2. 在 `src/redux/auth.ts` 中，编写代码正确处理存储 JWT 令牌的请求并正确更新 Redux 存储。

```ts {*|8-12} twoslash
import { createSlice, PayloadAction } from "@reduxjs/toolkit";
interface AuthState {
    token: string;
    name: string;
}

const initialState: AuthState = {
    token: "",
    name: "",
};
// ---cut---
/**
 * @todo [Step 4] 请在下述一处代码缺失部分以正确设置 JWT 信息
 */
export const authSlice = createSlice({
    name: "auth",
    initialState,
    reducers: {
        setToken: (state, action: PayloadAction<string>) => {
            // Step 4 BEGIN

            // Step 4 END
        },
        setName: (state, action: PayloadAction<string>) => {
            state.name = action.payload;
        },
        resetAuth: (state) => {
            state.token = "";
            state.name = "";
        },
    },
});
```

---

## 上手实践

3. 在 `src/utils/network.ts` 的函数 `request` 中，在网络请求函数中编写代码以读取 Redux 中 JWT 令牌，并将其携带在请求头中供服务器鉴权。

```ts {*|10-12,16-18} twoslash
export const request = async (
    url: string,
    method: "GET" | "POST" | "PUT" | "DELETE",
    needAuth: boolean,
    body?: object,
) => {
    /**
     * @todo [Step 4] 请在下述两处代码缺失部分以正确根据 `needAuth` 参数读取 JWT 信息并构建请求头
     */
    // Step 4 BEGIN

    // Step 4 END
    const response = await fetch(url, {
        method,
        body: body && JSON.stringify(body),
        // Step 4 BEGIN

        // Step 4 END
    });
}
```

---
layout: image-right
image: /4-demo.gif
backgroundSize: 90%
---

## 上手实践

完成本 Step 后，目前的游戏应当可以正常登录。

登录功能的交互如右图所示。

---

## 上手实践

1. 在 `src/pages/login.tsx` 的组件 `LoginScreen` 中，在登录请求成功后的回调函数中发起将后端服务器所签发的 JWT 令牌存储到 Redux 的请求。
2. 在 `src/redux/auth.ts` 中，编写代码正确处理存储 JWT 令牌的请求并正确更新 Redux 存储。
3. 在 `src/utils/network.ts` 的函数 `request` 中，在网络请求函数中编写代码以读取 Redux 中 JWT 令牌，并将其携带在请求头中供服务器鉴权。

以上步骤 1 和 2 的代码量在 10 行以内，步骤 3 的代码量在 20 行以内。

完成本 Step 后，目前的游戏应当可以正常登录。

---

## 讲解

1. 在 `src/pages/login.tsx` 的组件 `LoginScreen` 中，在登录请求成功后的回调函数中发起将后端服务器所签发的 JWT 令牌存储到 Redux 的请求。

```ts {*|14-17}{maxHeight:'80%'} twoslash
import { useState } from 'react';
import { useDispatch } from "react-redux";
const BACKEND_URL: string = '';
const [userName, setUserName] = useState("");
const [password, setPassword] = useState("");
const dispatch = useDispatch();

import { useRouter } from "next/router";

import { createSlice, PayloadAction } from "@reduxjs/toolkit";

interface AuthState {
    token: string;
    name: string;
}

const initialState: AuthState = {
    token: "",
    name: "",
};

/**
 * @todo [Step 4] 请在下述一处代码缺失部分以正确设置 JWT 信息
 */
export const authSlice = createSlice({
    name: "auth",
    initialState,
    reducers: {
        setToken: (state, action: PayloadAction<string>) => {
            // Step 4 BEGIN

            // Step 4 END
        },
        setName: (state, action: PayloadAction<string>) => {
            state.name = action.payload;
        },
        resetAuth: (state) => {
            state.token = "";
            state.name = "";
        },
    },
});

export const { setToken, setName, resetAuth } = authSlice.actions;
export default authSlice.reducer;

const router = useRouter();

export const LOGIN_SUCCESS_PREFIX = "登陆或注册成功，用户名：";
export const LOGIN_FAILED = "登陆或注册失败";
export const FAILURE_PREFIX = "网络请求失败：";

// ---cut---
/**
 * @todo [Step 4] 请在下述一处代码缺失部分以在登陆/注册成功时正确设定 redux 中保存的 JWT 信息
 */
const login = () => {
    fetch(`${BACKEND_URL}/api/login`, {
        method: "POST",
        body: JSON.stringify({
            userName,
            password,
        }),
    })
        .then((res) => res.json())
        .then((res) => {
            if (Number(res.code) === 0) {
                // Step 4 BEGIN
                dispatch(setToken(res.token));
                // Step 4 END

                dispatch(setName(userName));
                alert(LOGIN_SUCCESS_PREFIX + userName);

                /**
                 * @note 这里假定 login 页面不是首页面，大作业中这样写的话需要作分支判断
                 */
                router.back();
            }
            else {
                alert(LOGIN_FAILED);
            }
        })
        .catch((err) => alert(FAILURE_PREFIX + err));
};
```

---

## 讲解

2. 在 `src/redux/auth.ts` 中，编写代码正确处理存储 JWT 令牌的请求并正确更新 Redux 存储。

```ts {*|8-12} twoslash
import { createSlice, PayloadAction } from "@reduxjs/toolkit";
interface AuthState {
    token: string;
    name: string;
}

const initialState: AuthState = {
    token: "",
    name: "",
};
// ---cut---
/**
 * @todo [Step 4] 请在下述一处代码缺失部分以正确设置 JWT 信息
 */
export const authSlice = createSlice({
    name: "auth",
    initialState,
    reducers: {
        setToken: (state, action: PayloadAction<string>) => {
            // Step 4 BEGIN
            state.token = action.payload;
            // Step 4 END
        },
        setName: (state, action: PayloadAction<string>) => {
            state.name = action.payload;
        },
        resetAuth: (state) => {
            state.token = "";
            state.name = "";
        },
    },
});
```

---

## 讲解

3. 在 `src/utils/network.ts` 的函数 `request` 中，在网络请求函数中编写代码以读取 Redux 中 JWT 令牌，并将其携带在请求头中供服务器鉴权。

```ts {*|10-16,20-22}{maxHeight:'80%'} twoslash
import { createSlice, PayloadAction } from "@reduxjs/toolkit";
import { configureStore } from "@reduxjs/toolkit";

export type Board = (0 | 1)[][];

export const BOARD_LENGTH = 50;

export const getBlankBoard = (): Board => Array.from(
    { length: BOARD_LENGTH },
    () => Array<0>(BOARD_LENGTH).fill(0),
);

interface BoardState {
    board: Board;
}

const initialState: BoardState = {
    board: getBlankBoard(),
};

const boardSlice = createSlice({
    name: "board",
    initialState,
    reducers: {
        setBoardCache: (state, action: PayloadAction<Board>) => {
            state.board = action.payload;
        },
        resetBoardCache: (state) => {
            state.board = getBlankBoard();
        },
    },
});

interface AuthState {
    token: string;
    name: string;
}

const initialAuthState: AuthState = {
    token: "",
    name: "",
};


/**
 * @todo [Step 4] 请在下述一处代码缺失部分以正确设置 JWT 信息
 */
const authSlice = createSlice({
    name: "auth",
    initialState: initialAuthState,
    reducers: {
        setToken: (state, action: PayloadAction<string>) => {
            // Step 4 BEGIN
            state.token = action.payload;
            // Step 4 END
        },
        setName: (state, action: PayloadAction<string>) => {
            state.name = action.payload;
        },
        resetAuth: (state) => {
            state.token = "";
            state.name = "";
        },
    },
});

const store = configureStore({
    reducer: {
        auth: authSlice.reducer,
        board: boardSlice.reducer,
    },
});
// ---cut---
export const request = async (
    url: string,
    method: "GET" | "POST" | "PUT" | "DELETE",
    needAuth: boolean,
    body?: object,
) => {
    /**
     * @todo [Step 4] 请在下述两处代码缺失部分以正确根据 `needAuth` 参数读取 JWT 信息并构建请求头
     */
    // Step 4 BEGIN
    const headers = new Headers();
    if (needAuth) {
        const token = store.getState().auth.token;
        headers.append("Authorization", token);
    }
    // Step 4 END
    const response = await fetch(url, {
        method,
        body: body && JSON.stringify(body),
        // Step 4 BEGIN
        headers,
        // Step 4 END
    });
}
```