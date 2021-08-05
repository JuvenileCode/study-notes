## TypeScript暇记

### WebStorm TS设置

**npm安装TypeScript**

```bash
# npm install 
npm install typescript -g
# select ts version
tsc --version
```

**配置 webstorm [TypeScript ](https://www.chunshu.net/article/192/cid/44.html)**

**设置 webstrom [File Watch](https://segmentfault.com/a/1190000023000222)**

**配置 [tsconfig.json ](https://www.tslang.cn/docs/handbook/tsconfig-json.html)**

```json
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "es5",
    "sourceMap": true,
    "noEmitOnError": true,
    "lib": [
      "es6",
      "dom",
      "dom.iterable",
      "esnext"
    ]
  },
  "exclude": [
    "node_modules"
  ],
  "include": [
    "src"
  ]
}
```



### 基础

[教程](https://ts.xcatliu.com/introduction/index.html)

#### 原始数据类型

原始数据类型包括：布尔值、数值、字符串、`null`、`undefined` 以及 ES6 中的新类型 [`Symbol`](http://es6.ruanyifeng.com/#docs/symbol) 和 ES10 中的新类型 `BigInt`

**布尔**

```typescript
let isDone: boolean = false;
// 使用构造函数 Boolean 创造的对象不是布尔值,而是一个Boolean对象
let createdByNewBoolean: Boolean = Boolean(1);
console.log(createdByNewBoolean.valueOf());
// 直接调用 Boolean 也可以返回一个 boolean 类型
let createdByBoolean: boolean = Boolean(1);
```

**数值**

```typescript
let decLiteral: number = 6;
let decLiteralXS: number = 6.55;
let hexLiteral: number = 0xf00d;
// ES6 中的二进制表示法
let binaryLiteral: number = 0b1010;
// ES6 中的八进制表示法
let octalLiteral: number = 0o744;
let notANumber: number = NaN;
let infinityNumber: number = Infinity;
```

**字符串**

```typescript
let myName: string = 'Tom';
let myAge: number = 25;
// 模板字符串 其中 ` 用来定义 ES6 中的模板字符串，${expr} 用来在模板字符串中嵌入表达式
let sentence: string = `Hello, my name is ${myName}.I'll be ${myAge + 1} years old next month.`;
```

**空值**

```typescript
function alertName(): void {
    alert('My name is Tom');
}
// 声明一个 void 类型的变量没有什么用，因为你只能将它赋值为 undefined 和 null（只在 --strictNullChecks 未指定时）：
let unusable: void = undefined;
```

**Null和Undefined**

在 TypeScript 中，可以使用 `null` 和 `undefined` 来定义这两个原始数据类型：

与 `void` 的区别是，`undefined` 和 `null` 是所有类型的子类型。也就是说 `undefined` 类型的变量，可以赋值给 `number` 类型的变量：

```typescript
let u: undefined = undefined;
let n: null = null;
// 可以这样
let num: number = undefined;
let u: undefined;
let num: number = u;
// 而 void 类型的变量不能赋值给 number 类型的变量：
let u: void;
let num: number = u; // Type 'void' is not assignable to type 'number'.
```

**任意值**

任意值（Any）用来表示允许赋值为任意类型

如果是一个普通类型，在赋值过程中改变类型是不被允许的：

```ts
let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
```

但如果是 `any` 类型，则允许被赋值为任意类型。

```ts
let myFavoriteNumber: any = 'seven';
myFavoriteNumber = 7;
```

未声明类型的变量

**变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型**：

```ts
let something;
something = 'seven';
something = 7;
// ===========等价于===========
let something: any;
something = 'seven';
something = 7;
```



#### 类型推论

如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出一个类型。

以下代码虽然没有指定类型，但是会在编译的时候报错：

```ts
let myFavoriteNumber = 'seven';
myFavoriteNumber = 7;// index.ts(2,1): error TS2322: Type 'number' is not assignable to type 'string'.
// =============等价于============
let myFavoriteNumber: string = 'seven';
myFavoriteNumber = 7;
```

TypeScript 会在没有明确的指定类型的时候推测出一个类型，这就是类型推论。

**如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 `any` 类型而完全不被类型检查**：

```ts
let myFavoriteNumber;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```