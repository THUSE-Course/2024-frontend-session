# TypeScript 语法

<!-- Step 1 控制在 15 分钟左右解决 -->

## 目标

- 掌握基本的 TypeScript 语法

---

## 语法

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

## 语法

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

## 语法

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

## 语法

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

## 语法

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

## 语法

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

## 语法

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

## 语法

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