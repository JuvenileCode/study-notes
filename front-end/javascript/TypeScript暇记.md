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



#### 联合数据类型

联合类型（Union Types）表示取值可以为多种类型中的一种

```typescript
let maxVar: string | boolean | number;
maxVar = '啦啦啦啦啦';
console.log(typeof maxVar);
maxVar = true;
console.log(typeof maxVar);
```

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们**只能访问此联合类型的所有类型里共有的属性或方法**：

```ts
function getLength(something: string | number): number {
    return something.length;
    //return something.toString();
}
// index.ts(2,22): error TS2339: Property 'length' does not exist on type 'string | number'.
//   Property 'length' does not exist on type 'number'.
```

联合类型的变量在被赋值的时候，会根据类型推论的规则推断出一个类型：

```ts
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
console.log(myFavoriteNumber.length); // 5
myFavoriteNumber = 7;
console.log(myFavoriteNumber.length); // 编译时报错
```



#### 对象的类型接口

```typescript
interface Person {
    readonly id: number; //只读约束 * 只读的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候
    name: string;
    age: number;
    address?: string;  //可选属性的含义是该属性可以不存在

    [propName: string]: any; //任意的属性 * 定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集
}
let tom: Person[] = [{id: 1, name: "Tom", age: 25, isTrue: true}, {id: 2, name: "JasTin", age: 19, address: "湖南长沙"}]
console.log(tom);
```



#### 数组的类型

```typescript
// 初始定义
let intArray: number[] = [1, 2, 3, 1, 4, 5];
intArray.push(9);
intArray.pop();
// 带泛型定义
let fibonacci: Array<string> = ['a', 'b', 'c', 'd', 'e'];
```



#### 函数的类型

在 JavaScript 中，有两种常见的定义函数的方式——函数声明（Function Declaration）和函数表达式（Function Expression）

```typescript
// 函数声明要进行类型约束 - 可选参数必须接在必需参数后面
function sub(x: number, y: number, z?: number): number {
    if (z) {
        return x - y + z;
    }
    return x - y;
}

// 函数表达式
/*let mySum = function (x: number, y: number): number {
    return x + y;
};*/
// 参数默认值
let mySum: (x: number, y: number) => number = function (x: number, y: number, z: number = 9): number {
    return x + y + z;
};

// 剩余参数
function push(array: any[], ...items: any[]) :any[]{
    items.forEach(function (item) {
        array.push(item);
    });
    return array;
}

// 函数重载
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}

function reverseRepeat(x: number): number;
function reverseRepeat(x: string): string;
function reverseRepeat(x: number | string): number | string | void {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```



#### 类型断言

类型断言（Type Assertion）可以用来手动指定一个值的类型

**用途：**

**将一个联合类型断言为其中一个类型**

```typescript
function isFish(animal: string | Date): number | Date | string {
    if (typeof (animal as Date) === 'object') {
        console.log(typeof animal);
        return (animal as Date).getDate();
    }
    return animal;
}
```

**将一个父类断言为具体的子类**

```typescript
interface ApiError extends Error {
    code: number;
}
interface HttpError extends Error {
    statusCode: number;
}

function isApiError(error: Error) {
    if (typeof (error as ApiError).code === 'number') {
        return true;
    }
    return false;
}
```

**将任何一个类型断言为`any`**

需要注意的是，将一个变量断言为 `any` 可以说是解决 TypeScript 中类型问题的最后一个手段

**它极有可能掩盖了真正的类型错误，所以如果不是非常确定，就不要使用 `as any`**

```ts
window.foo = 1;
// index.ts:1:8 - error TS2339: 
(window as any).foo = 1;
```

**将 `any` 断言为一个具体的类型**

```ts
function getCacheData(key: string): any {
    return (window as any).cache[key];
}
interface Cat {
    name: string;
    run(): void;
}
const tom = getCacheData('tom') as Cat;
tom.run();
```