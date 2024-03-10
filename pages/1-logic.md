# Step 1: TypeScript 初探与游戏逻辑

<!-- Step 1 控制在 15 分钟左右解决 -->

## 目标

- 掌握基本的 TypeScript 语法
- 使用 TypeScript 语言编写完成康威生命游戏的核心逻辑

---

## 语法 recap

以下部分内容是 JS/TS 小作业中 Cheat Sheet 的真子集。

### 变量声明

```ts twoslash
// 声明一个字符串类型的常量
const caption: string = "This is a string";
// 声明一个数字类型的变量
let val: number = 1;
// 给变量 val 赋予新值
val = 2;
// 常量不能重新赋值
caption = "This is another string";
// 不能给变量赋予不符合其类型声明的值
val = "A string"
```

<div v-click>

当然，TypeScript 拥有强大的类型推断功能，可以省略部分类型标记：

```ts twoslash
let youKnowIt = true;
youKnowIt = false;
```

</div>

---

## 语法 recap

### 运算

一般的运算符与 C++ 语法近似：

```ts twoslash
1 > 2; // false
1 <= 4; // true
1 + 3 > 2; // true
```

由于 JavaScript 的特性，请使用 `===` 与 `!==` 代替 `==` 与 `!=`，因为后者会进行类型转换：

```ts 
1 == true; // true
1 === true; // false
```

---

## 语法 recap

### 控制流

控制流的写法与 C++ 语法近似：

```ts twoslash
const foo: number = 3;
if (foo !== 2) {
    console.log("foo is not 2");
}

for (let i = 0; i < 10; i++) {
    console.log(`Now, i is ${i}`);
}
```

---

## 语法 recap

### 数组

```ts twoslash
// 在类型后面加上 [] 表示这个类型的值构成的数组
const arr = [1, 2, 3, 4]; 
// 二维数组
const mat = [[1, 2], [3, 4]]; 
// 元组：长度和每个元素的类型固定
const tuple: [number, string] = [1, "TS"];
```

### 对象

TypeScript 中，对象是键值对的集合，类似于 Python 中的字典:

```ts twoslash
const barfoo = {
    foo: 1,
    bar: "Test string",
    foobar: [1, 2, 3],
};
```

---

## 语法 recap

### 接口

接口是一种抽象结构，用于定义对象应具有的属性：

```ts twoslash
interface Bar {
    foo: number;
    bar: string;
    foobar: number[];
}

const barfoo = {
    foo: 1,
    bar: "Test string",
    foobar: [1, 2, 3],
} as Bar;

const bad = {
    foo: "String",
} as Bar;
```

---

## 语法 recap

### `undefined`

在访问数组或对象的属性时，需要注意可能出现的 `undefined`：

```ts twoslash
const arr: number[] = [1, 2, 3, 4];
arr[0]; // 1
arr[100]; // undefined

const obj: any = {
    foo: 1,
    bar: "bar",
};
obj.foo // 1
obj.barbar // undefined
```

尝试调用 `undefined` 或 `null` 的方法或属性会导致运行时错误，你可以在控制台看到报错。同时，TypeScript 会尝试检测这种错误。

```ts twoslash
const x = 1 > 2 ? 3 : undefined;
x.toString();
```

---

## 语法 recap

### 函数声明

```ts twoslash
// 经典方法
function sum(x: number, y: number): number {
    return x + y;
}

// 箭头函数，可作为 lambda 表达式使用
const sum2 = (x: number, y: number): number => x + y;

// 调用
sum(1, 2); // 3
sum2(2, 3); // 5
```

---

## 语法 recap

### 数组的常用方法

由于 TypeScript 的动态特性，回调模式被广泛地使用，指将函数作为参数传递给另一个函数：

```ts twoslash
const arr = [1, 2, 3, 4, 5];

// 遍历数组，第一个参数是数组元素的值，第二个参数是数组下标
arr.forEach((val, ind) => {
    console.log(`The value at index ${ind} is ${val}.`);
});
// 映射数组，返回一个新数组
arr.map(val => val * val); // [1, 4, 9, 16, 25]
// 筛选数组，返回一个新数组
arr.filter(val => val % 2 === 0); // [2, 4]

// 在尾部添加元素
arr.push(6);
arr; // [1, 2, 3, 4, 5, 6]
```

---

## 学习资源

以上仅仅是对 TypeScript 语法最基本的介绍，如果你希望更加深入地学习 TypeScript 语言：

- [TypeScript 官方文档](https://www.typescriptlang.org/docs/)
- [TypeScript 语言基础 - 计算机系科协技能引导文档](https://docs.net9.org/languages/typescript/)

---

## 游戏规则

这一小节的实践中，我们将会完成康威生命游戏的核心逻辑，因此首先介绍康威生命游戏的规则。

- 本作业中，我们使用一个 50x50 的棋盘，每个格子代表一个**细胞**。细胞具有**存活**和**死亡**两种状态。
- 每个细胞在 $t + 1$ 时刻的状态取决于它自身在 $t$ 时刻的状态，以及其所有**邻居**在 $t$ 时刻的状态。
- 每一个细胞都有 8 个邻居，这里由于棋盘大小有限，故采用循环相邻的定义方式。

以下是一个 8x8 棋盘的简化示例，对于每一个红色的细胞，8 个黄色细胞称为其邻居：

![Conway's Life Game Example](/1-neighbor.png)

---

## 游戏规则

基于上述定义，每个细胞在下一个时刻的状态按照下述规则确定：

- 如果该细胞在这个时刻存活
    - **（孤独死亡）** 如果其邻居中存活的个数不多于 1 个，则该细胞在下一时刻死亡
    - **（人口超载）** 如果其邻居中存活的个数不少于 4 个，则该细胞在下一时刻死亡
    - **（环境适中）** 如果其邻居中存活的个数为 2 个或者 3 个，则该细胞在下一时刻继续存活
- 如果该细胞在这个时刻死亡
    - **（细胞繁殖）** 如果其邻居中存活的个数为 3 个，则该细胞在下一时刻存活
    - 其余情况下，该细胞保持死亡状态

所有细胞从这个时刻按照上述规则变换为下一时刻的状态称为一次**演变 (step)**。

- 可以在 [康威生命游戏示例网站](https://playgameoflife.com/) 试玩以加深对规则的理解
  - 注意该网站所实现的棋盘是无限的，与本作业要求的并不一致，并且部分交互也与我们要求不同

---

## 上手实践

在 `src/utils/logic.ts` 文件的函数 `stepBoard` 之中填充代码，完成康威生命游戏的核心逻辑

```ts twoslash
export type Board = (0 | 1)[][];
// ---cut---
export const stepBoard = (board: Board): Board => {
    const newBoard: Board = [];

    /**
     * @todo [Step 1] 请在下面两条注释之间的区域填写你的代码完成该游戏的核心逻辑
     * @note 你可以使用命令 yarn test step 来运行我们编写的单元测试与我们提供的参考实现对拍
     */
    // Step 1 BEGIN

    // Step 1 END

    return newBoard;
};
```

代码量约为 20 - 40 行。

完成后，你可以执行如下命令来测试你的代码：

```bash
yarn test step
```

---

## 代码说明

在 `src/utils/types.ts` 文件中，我们规定了表示棋盘的类型如下：

```ts twoslash
export type Board = (0 | 1)[][];
```

`export` 用于导出一个变量、函数、类型等，使之可以被其他文件引用。

我们在此使用一个二维数组表示棋盘，每个元素代表一个细胞，取值为 `0` 或 `1`，分别代表细胞死亡与存活。

<div v-click>

你需要填充的 `stepBoard` 函数接受一个 `Board` 类型的参数 `board`，表示当前时刻的棋盘状态。该函数的返回值为 `Board` 类型，表示下一时刻的棋盘状态。

</div>

<div v-click>

完成本步骤时，可以使用 `src/constants/constants.ts` 中定义的常量，如描述棋盘尺寸的常量：

```ts twoslash
export const BOARD_LENGTH = 50;
```

</div>

---

## 讲解

```ts {*|9-49|9-10|12-13|15-23|25-29|31-39|31-47|12-48|9-49|52|*}{maxHeight:'90%'} twoslash
export type Board = (0 | 1)[][];
export const BOARD_LENGTH = 50;
// ---cut---
export const stepBoard = (board: Board): Board => {
    const newBoard: Board = [];

    /**
     * @todo [Step 1] 请在下面两条注释之间的区域填写你的代码完成该游戏的核心逻辑
     * @note 你可以使用命令 yarn test step 来运行我们编写的单元测试与我们提供的参考实现对拍
     */
    // Step 1 BEGIN
    for (let i = 0; i < BOARD_LENGTH; ++i) {
        newBoard.push([]);

        for (let j = 0; j < BOARD_LENGTH; ++j) {
            let aliveCounter = 0;

            const left = (j - 1 + BOARD_LENGTH) % BOARD_LENGTH;
            const right = (j + 1) % BOARD_LENGTH;
            const up = (i - 1 + BOARD_LENGTH) % BOARD_LENGTH;
            const down = (i + 1) % BOARD_LENGTH;
            const checkList = [
                [up, left], [up, j], [up, right],
                [i, left], [i, right],
                [down, left], [down, j], [down, right],
            ];

            checkList.forEach((ord) => {
                if (board[ord[0]][ord[1]] === 1) {
                    ++aliveCounter;
                }
            });

            const nowState = board[i][j];
            if (nowState === 0) {
                if (aliveCounter === 3) {
                    newBoard[i].push(1);
                }
                else {
                    newBoard[i].push(0);
                }
            }
            else {
                if (aliveCounter === 2 || aliveCounter === 3) {
                    newBoard[i].push(1);
                }
                else {
                    newBoard[i].push(0);
                }
            }
        }
    }
    // Step 1 END

    return newBoard;
};
```
