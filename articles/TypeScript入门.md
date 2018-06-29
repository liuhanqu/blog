## 什么是 TypeScript

> TypeScript 是一种由微软开发的自由和开源的编程语言。它是 JavaScript 的一个严格超集，并添加了可选的静态类型和基于类的面向对象编程。
> 以上解释来源于 [维基百科 - TypeScript](https://zh.wikipedia.org/wiki/TypeScript)

## 使用 TypeScript 的好处

JavaScript 是一门动态类型的语言

```js
let x = 5;
x = 'abc';
```

如上，变量 x 起先是一个数值，后来是一个字符串，类型是不确定的，它的好处在于灵活， 可以写出简洁的代码。但是对于大型的项目来说，这会是一个隐患。因为你并不能明确地知道在接下来其他地方使用 x 时，它会是一个数字还是一个字符串，可能会发生未知的错误。而使用 TypeScript 时，是可以避免此情况的发生的。
使用 TypeScript 主要有以下几点好处

1.  配合 VS Code，强大的代码提示
2.  静态类型检查
3.  方便代码重构

## 静态类型

TypeScript 是 JavaScript 的超集，可以理解为在 JavaScript 的基础之上添加了一些东西（静态类型检查），语法并没有什么变化。

```js
function greeting(name) {
  console.log(`Hello ${name}`);
}

greeting('laohan');
```

上面的是 js 的代码，换做 ts 的写法就如下

```ts
function greeting(name: string): void {
  console.log(`Hello ${name}`);
}

greeting('laohan');
```

如上，声明了函数的参数 name 为 string 类型，返回值的为 void（没有返回值)，但编译后的代码跟上面的 js 代码是没有差异的。你可以在 [TypeScript Playground](http://www.typescriptlang.org/play/index.html) 进行试验。

假如我们将上面的函数调用 greeting('laohan')，改成了 greeting(12)，编辑器就会提示错误（出现红线，鼠标移上去，会有提示），如下图所示
![](/images/typescript/0.jpg)

鼠标不移到红线处，编辑器下方也是可以看到具体的错误
![](/images/typescript/1.jpg)
需要注意的是，这个错误是在代码编译时发生的，但是 TypeScript 仍然会将代码编译成 JavaScript，且编译后的代码也没有什么变化。

TypeScript 的类型检查是在编译时进行的，编译后的 JavaScript 代码并不会增加任何类型检查相关的代码，因此不需要担心由此带来的性能问题。也就是说，如果我们的 TypeScript 项目编译成了 JavaScript 再被其他的 JavaScript 程序调用，如果传递了不合法的数据类型，程序可能会抛出异常。

## Basic Type

TypeScript 有几种基础类型

- boolean 布尔值类型

```ts
let isDone: boolean = false;
```

- number 数字类型

```ts
let decimal: number = 6;
```

- string 字符串类型

```ts
let color: string = 'blue';
color = 'red';
```

- Array 数组类型

```ts
let list: number[] = [1, 2, 3];
// or
let list: Array<number> = [1, 2, 3];
```

- Tuple 元组类型

```ts
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ['hello', 10]; // OK
// Initialize it incorrectly
x = [10, 'hello']; // Error
```

- Enum 枚举类型

```ts
enum Color {
  Red,
  Green,
  Blue,
}
let c: Color = Color.Green;
```

- Any 任意类型

```ts
let notSure: any = 4;
notSure = 'maybe a string instead';
notSure = false; // okay, definitely a boolean
```

- Void 空类型

```ts
let unusable: void = undefined;
```

- Null and Undefined

```ts
// Not much else we can assign to these variables!
let u: undefined = undefined;
let n: null = null;
```

- Never

```ts
// Function returning never must have unreachable end point
function infiniteLoop(): never {
  while (true) {}
}
```

- Object

```ts
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(42); // Error
```

详细信息可以参考这里: [TypeScript Handbook - Basic Types
](https://www.typescriptlang.org/docs/handbook/basic-types.html)

## interface

interface，即接口，主要有以下几种常见的形式：

- normal

```ts
// 定义一个对象，具有 color 和 width 属性
interface SquareConfig {
  color: string;
  width: number;
}
```

- Optional Properties 可选属性

```ts
// 定义一个对象，可能具有 color 和 width 属性
// ? , 表示可选
interface SquareConfig {
  color?: string;
  width?: number;
}
```

- Readonly properties 只读属性

```ts
// 定义一个对象，具有 x 和 y 属性, 且不可被修改
interface Point {
  readonly x: number;
  readonly y: number;
}
```

- Function Types 函数类型

```ts
// 定义一个函数，有两个参数，且都为 string 类型， 并返回一个 boolean 类型的值
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

- Indexable Types

有时候我们是无法列出一个对象的所有属性值的，这就需要 Indexable Types 出场了

```ts
// 定义一个对象，key 的值为 string 类型， 值为 number 类型
interface NumberObject {
  [prop: string]: number;
}
```

- Class Types

```ts
interface ClockConstructor {
  new (hour: number, minute: number);
}

class Clock implements ClockConstructor {
  currentTime: Date;
  constructor(h: number, m: number) {}
}
```

详细信息可以参考这里: [TypeScript Handbook - Interfaces
](https://www.typescriptlang.org/docs/handbook/interfaces.html)

 上面的 interface 接口的定义，大多数都是可以用 type 的方式去声明，如

```ts
type Point = {
  x: number;
  y: number;
};
```

两者还是会有一些区别，可以看看这篇文章 [Interface vs Type alias in TypeScript 2.7](https://medium.com/@martin_hotell/interface-vs-type-alias-in-typescript-2-7-2a8f1777af4c)

## Function

函数常见的几种定义方式

```ts
function add(x: number, y: number): number {
  return x + y;
}
```

```ts
let myAdd: (baseValue: number, increment: number) => number = function(x, y) {
  return x + y;
};
```

```ts
interface AddFn {
  (baseValue: number, increment: number): number;
}
let myAdd: AddFn = function(x, y) {
  return x + y;
};
```

## Classes

TypeScript 的类定义跟 JavaScript 的定义方法类型一样，不同的点在于 TypeScript 多了 public, private, protected, readonly 等修饰符。

```ts
class Person {
  protected name: string;
  constructor(name: string) {
    this.name = name;
  }
}
class Employee extends Person {
  private department: string;

  constructor(name: string, department: string) {
    super(name);
    this.department = department;
  }

  public getElevatorPitch() {
    return `Hello, my name is ${this.name} and I work in ${this.department}.`;
  }
}
```

当一个类的成员用 private 修饰时，表示这个成员只能在该类中被访问，如上，Employee 中的 department 只能在 Employee 中的中被使用，而它的实例或者继承 Employee 的  类，都是没有办法访问。当用 protected 修饰时，则这个成员允许在继承类中被访问，如上，Person 的 name 是可以在 Employee 中被访问到的。而 public 修饰时，则没有任何限制。

如果一个类的成员没有修饰符，则默认为 public。

需要注意的是，这些修饰符仅在 TypeScript 程序中才有效，编译后的代码是没有这些修饰符的，如果直接使用 JavaScript 是可以在类外部访问到被声明为 private 或者 protocted 的类成员，也可以修改被声明为 readonly 的成员变量。

详细信息可以参考这里：[TypeScript Handbook - Classes](https://www.typescriptlang.org/docs/handbook/classes.html)

## Generic 泛型

泛型的存在使得组件不仅能够支持当前的数据类型，同时也能支持未来的数据类型，这在创建大型系统时为你提供了十分灵活的功能。

```ts
function identity<T>(arg: T): T {
  return arg;
}
```

```ts
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}
```

详细信息可以参考这里：[TypeScript Handbook - Generics](https://www.typescriptlang.org/docs/handbook/generics.html)

## 快速开始

首先，全局安装 typescript

```bash
npm install -g typescript
```

## 参考资料

[TypeScript 入门指南](http://morning.work/page/others/typescript-start-guide-for-nodejs-developer.html)
[TypeScript Document](https://www.typescriptlang.org/docs/home.html)
[TypeScript 中文](https://legacy.gitbook.com/book/zhongsp/typescript-handbook/details)
