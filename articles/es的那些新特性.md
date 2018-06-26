前段时间换工作，面试了几家公司，有一道题发现基本是必问的，就是说一说平时用到的那些 es6 7 8 9 等的特性。一直没有做总结，现在就整理一下平时自己在工作中用到的比较多的那些新特性。

## const and let

声明变量新增的两个关键词，与 var 不同的一点在于，在 JS 函数中的 var 声明，其作用域是函数体的全部，而 const 和 let 是有块作用域的。

看一个经典的闭包问题，现在用 let 就可以轻易地避免了。

```js
// before
for (var i = 0; i < 3; i++) {
  setTimeout(() => {
    console.log(i); // => 3, 3, 3
  });
}

// now
for (let i = 0; i < 3; i++) {
  setTimeout(() => {
    console.log(i); // 0, 1, 2
  });
}
```

const 是常量的声明，表示的是变量不可被再次赋值，而变量的值是可以改变的

```js
const obj = { a: 1 };

obj = 'a'; // => Uncaught TypeError: Assignment to constant variable.

obj.b = 2;
```

## Template Literals

允许嵌入表达式的字符串字面量，可以使用多行字符串和字符串插值功能。

```js
const name = 'laohan';
const grettings = `hello ${name}`;

console.log(grettings); // => 'hello laohan'

const html = `
  <div>
    利用字面量，可以很方便的实现多行字符串
  </div>
`;
```

## String padding

用另一个字符串填充当前字符串(重复，如果需要的话)，以便产生的字符串达到给定的长度。

### String.prototype.padStart

```js
str.padStart(targetLength [, padString])

'abc'.padStart(10);         // '       abc'
'abc'.padStart(10, "foo");  // 'foofoofabc'
```

### String.prototype.padEnd

```js
str.padEnd(targetLength [, padString])

'abc'.padEnd(10);          // 'abc       '
'abc'.padEnd(10, "foo");   // 'abcfoofoof'
```

padStart 与 padEnd 的不同在于填充的字符串是在当前字符串的左边还是右边。padStart 在左，padEnd 在右。

## Exponentiation infix operator （指数运算符）

```js
// before
Math.pow(2, 3); // => 8

// now
2 ** 3; // => 8
```

## Array and Object destructing

解构赋值

```js
const arr = [1, 2, 3];
// before
var a = arr[0];
var b = arr[1];
var c = arr[2];

// now
const [a, b, c] = arr;
console.log(a, b, c); // => 1, 2, 3

const obj = {
  a: 1,
  b: 2,
  c: 3,
};

// before
var a = obj.a;
var b = obj.b;
var c = obj.c;

// now
const { a, b, c } = obj;
console.log(a, b, c); // => 1, 2, 3
```

## Arrow Functions

更简短的函数并且不绑定 this。
箭头函数没有自己的 this，适用于那些本来需要匿名函数的地方。

```js
// before
function log(name) {
  console.log(name);
}
// now
// 当参数只有一个的时候，（）是可以省略的
const fn = name => {
  console.log(name);
};
```

箭头函数写起来更简短，但也不能到处用。有些不该用的地方使用了箭头函数，会得到不是你想要的结果

```js
const obj = {
  name: 'laohan',
  getName() {
    console.log(this.name);
  },
};

obj.getName(); // => 'laohan'

const obj = {
  name: 'laohan',
  getName: () => {
    console.log(this.name);
  },
};
obj.getName(); // => undefined
```

## Object.assign()

将所有可枚举属性的值从一个或多个源对象复制到目标对象

```js
const obj1 = { a: 1, b: 2 };
const obj2 = { c: { d: 3 } };

const obj3 = Object.assign({}, obj1, obj2);
// 这里也可以利用解构来实现
const obj4 = { ...obj1, ...obj2 };
console.log(obj3); // => {a: 1, b: 2, c: {d: 3}}

// ps： Object.assign是浅复制的
obj2.c.d = 4;
console.log(obj3); // => {a: 1, b: 2, c: {d: 4}}
```

## Object.keys()

返回一个由一个给定对象的自身可枚举属性组成的数组。

```js
const obj = {
  a: 1,
  b: 2,
  c: 3,
};
const keys = Object.keys(obj);
console.log(keys); // => ['a', 'b', 'c']
```

## Object.values()

返回一个给定对象自己的所有可枚举属性值的数组。

```js
const obj = {
  a: 1,
  b: 2,
  c: 3,
};

const values = Object.values(obj);
console.log(values); // => [1, 2, 3]
```

## Object.entries()

返回一个给定对象自身可枚举属性的键值对数组

```js
const obj = {
  a: 1,
  b: 2,
  c: 3,
};
const entries = Object.entries(obj);
console.log(entries); // => [['a', 1], ['b', 2], ['c', 3]]
```

## Array.prototype.includes

用于判断数组中是否包含某一个指定的值，根据情况，如果包含则返回 true，否则返回 false。

```js
const arr = [1, 2, 3, NaN];

// before
if (arr.indexOf(3) !== -1) {
  console.log(true);
}

// now
if (arr.includes(3)) {
  console.log(true);
}

// ps: indexOf 对于无法判断是否包含 NaN
arr.includes(NaN); // => true
arr.indexOf(NaN); // => -1
```

## Promise

Promise 对象用于表示一个异步操作的最终状态（完成或失败），以及其返回的值。
Promise 的出现可以解决‘回调 - 地狱’的问题，通过 then 的链式调用，使得我们的代码看起来更加美观一些。

```js
function asyncFunc() {
  return new Promise((resolve, reject) => {
    ...
    resolve(result)
    ...
    reject(error)
  })
}

asyncFunc()
.then(result => { ··· })
.catch(error => { ··· });
```

这篇文章 [We have a problem with promises](http://fex.baidu.com/blog/2015/07/we-have-a-problem-with-promises/) 可以看一看，理解里面的问题就算懂了。

## for...of

新的循环方式，代替之前的 for...in 和 forEach 方法。
可在可迭代对象（包括 Array，Map，Set，String，arguments 对象等等）进行迭代循环

```js
const arr = ['a', 'b'];
for (const x of arr) {
  console.log(x);
}
// Output
// 'a'
// 'b'
```

break 和 continue 也可以用于 for...of 循环

```js
const arr = ['a', '', 'b'];
for (const x of arr) {
  if (x.length === 0) break;
  console.log(x);
}

// Output:
// a
```

如果需要在循环中同时获取 index 索引的时候，可以这么写

```js
const arr = ['a', 'b'];
for (const [index, element] of arr.entries()) {
  console.log(`${index}. ${element}`);
}

// Output:
// 0. a
// 1. b
```

需要注意的是，for...of 仅可用于可迭代对象

```js
// Array-like, but not iterable!
const arrayLike = { length: 2, 0: 'a', 1: 'b' };

for (const x of arrayLike) {
  console.log(x);
}
// => TypeError: arrayLike is not iterable

for (const x of Array.from(arrayLike)) {
  console.log(x); // OK
}
```

上面的 arrayLike 虽然跟 Array 相似，有 length 属性，也有索引等，为什么没有办法像 Array 那样实现 for...of 的循环呢？
其实，Array、String 能使用 for...of 是由于其原型中有一个[Symbol.iterator]的属性，如果我们给上面的 arrayLike 也添加这个属性，那么也是可以实现 for...of 操作的。

```js
const arrayLike = {
  length: 2,
  0: 'a',
  1: 'b',
  [Symbol.iterator]() {
    let i = 0;
    return {
      next() {
        if (i < arrayLike.length) {
          return { done: false, value: arrayLike[i++] };
        }
        return { done: true };
      },
    };
  },
};
for (const x of arrayLike) {
  console.log(x);
}
```

## Generator Functions

```js
function* genFunc() {
  console.log('First');
  yield;
  console.log('Second');
}

const genObj = genFunc();

genObj.next();
// Output: First
genObj.next();
// output: Second
```

function\* 是一个新的'关键词'，标识 generator 函数。

生成器对象是由一个 generator function 返回的,并且它符合可迭代协议和迭代器协议，主要有两点：

1.  为迭代提供更高层次的抽象
2.  提供新的控制流程来帮助解决“回调 - 地狱”问题。

在之前，我们是通过添加[Symbol.iterator]属性来实现可迭代的。其实还可以通过 generator 来实现

```js
const obj = {
  length: 2,
  0: 'a',
  1: 'b',
  // generator 会返回一个 iterator
  *getIterator() {
    let i = 0;
    while (i < this.length) {
      yield this[i++];
    }
  },
};

for (const x of obj.getIterator()) {
  console.log(x);
}
```

我们之前写异步函数的时候，经常都是利用 callback 的形式去完成。利用 generator，就可以有一种新的方式了。

```js
const fs = require('fs');
const path = require('path');

function* logFiles(dir) {
  for (const fileName of fs.readdirSync(dir)) {
    const filePath = path.resolve(dir, fileName);
    yield filePath;
    const stats = fs.statSync(filePath);
    if (stats.isDirectory()) {
      yield* logFiles(filePath);
    }
  }
}
for (const p of logFiles(process.argv[2])) {
  console.log(p);
}
```

## async / await

async / await 的出现是为了帮我们解决“回调 - 地狱”的问题，让我们可以编写出看似同步的代码来实现异步调用。

async 关键词告诉 js 编译器区别对待这个函数。 当调用一个 async 函数时，会返回一个 Promise 对象，async 函数中可能也会有 await 表达式，这会使 async 函数暂停执行，等待表达式中的 Promise 解析完成后继续执行 async 函数并返回解决结果。

```js
function getUserName() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('laohan');
    }, 1000);
  });
}

function getUserPhone() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(1234567);
    }, 1000);
  });
}

async function getUserInfo() {
  const name = await getUserName();
  const phone = await getUserPhone();

  console.log(name); // => 'laohan'
  console.log(phone); // => 1234567
}
```

 虽然使用 async 可以让我们的函数看起来像同步一样的，但对于一些新手来说，如果不小心的话，会犯一些小错误。 如上面的代码，getUserName 和 getUserPhone 两者是没有关联的， 并不需要等到 getUserName 完成之后才去调用 getUserPhone，这两个是可以并行执行的。

```js
async function getUserInfo() {
  const [name, phone] = await Promise.all([getUserName(), getUserPhone()]);
  console.log(name); // => 'laohan'
  console.log(phone); // => 1234567
}
```

## 参考资料

[JavaScript Symbols, Iterators, Generators, Async/Await, and Async Iterators — All Explained Simply](https://medium.freecodecamp.org/some-of-javascripts-most-useful-features-can-be-tricky-let-me-explain-them-4003d7bbed32)
[Here are examples of everything new in ECMAScript 2016, 2017, and 2018](https://medium.freecodecamp.org/here-are-examples-of-everything-new-in-ecmascript-2016-2017-and-2018-d52fa3b5a70e)
[Coding recipe: extracting loop functionality (via callbacks and generators)](http://2ality.com/2018/04/extracting-loops.html)
