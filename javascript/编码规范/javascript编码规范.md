## 命名规范

* 标准变量采用驼峰式命名
* ‘ID’在变量名中全大写
* 常量全大写，用下划线连接构造函数，大写第一个字母
* jquery对象必须以’$’开头命名


```javascript
let thisIsMyName;
let goodID;
let reportURL;
let AndroidVersion;
let iOSVersion;
let MAX_COUNT = 10;
function Person(name) {
this.name = name;
}
// not good
let body = $('body');
// good
let $body = $('body');

```
## 局部变量命名规范

* s：表示字符串。例如：sName，sHtml;
* n：表示数字。例如：nPage，nTotal;
* b：表示逻辑。例如：bChecked，bHasLogin;
* a：表示数组。例如：aList，aGroup;
* r：表示正则表达式。例如：rDomain，rEmail;
* f：表示函数。例如：fGetHtml，fInit;
* o：表示以上未涉及到的其他对象，例如：oButton，oDate;

## 函数命名
> 小驼峰命名法，可使用常见动词约定：

* can 判断是否可执行某个动作，函数返回一个布尔值。true：可执行；false：不可执行
* has 判断是否含有某个值， 函数返回一个布尔值。true：含有此值；false：不含有此值
* is 判断是否为某个值，函数返回一个布尔值。true：为某个值；false：不为某个值
* get 获取某个之，函数返回一个非布尔值
* set 设置某个值，无返回值、返回是否设置成功或者返回链式对象load 加载某些数据,无返回值或者返回是否加载完成的结果
```javascript
// 是否可阅读
function canRead() {
 return true;
}
// 获取名称
function getName() {
 return this.name;
}
```
## 引用 References
### 对所有的引用使用 const ；不要使用 var。
eslint: prefer-const, no-const-assign
> 这可以确保你无法对引用重新分配，重新分配可能会导致 bug 和难以理解的代码。

```javascript
// bad
var a = 1;
var b = 2;
// good
const a = 1;
const b = 2;
```
### 如果你一定需要可变动的引用，使用 let 代替 var 。
eslint: no-var jscs: disallowVar

```javascript
// bad
var count = 1;
if (true) {
count += 1;
}
// good, 使用 let.
let count = 1;
if (true) {
count += 1;
}
```
## 对象Objects
### 使用字面量语法创建对象。
eslint: no-new-object

```javascript
// bad
const item = new Object();
// good
const item = {};
```
### 当创建带有动态属性名称的对象时使用计算的属性名称。
它们允许你在一个地方定义一个对象的所有属性。

```javascript
function getKey(k) {
 return `a key named k`;
}
// bad
const obj = {
id: 5,
name: 'San Francisco',
};
obj[getKey('enabled')] = true;
// good
const obj = {
    id: 5,
    name: 'San Francisco',
    [getKey('enabled')]: true,
};
```
### 使用对象方法速记语法。 
eslint: object-shorthand jscs: requireEnhancedObjectLiterals

```javascript
// bad
const atom = {
value: 1,
addValue: function (value) {
    return atom.value + value;
  },
};
// good
const atom = {
value: 1,
addValue(value) {
    return atom.value + value;
  },
};
```
### 使用对象属性速记语法。
eslint: object-shorthand jscs: requireEnhancedObjectLiterals
```javascript
const lukeSkywalker = 'Luke Skywalker';
// bad
const obj = {
  lukeSkywalker: lukeSkywalker,
};
// good
const obj = {
  lukeSkywalker,
};
```
### 将速记属性分组写在对象声明的开始处
> 更容易看出哪些属性在使用速记语法

```javascript
const anakinSkywalker = 'Anakin Skywalker';
const lukeSkywalker = 'Luke Skywalker';
// bad
const obj = {
episodeOne: 1,
twoJediWalkIntoACantina: 2,
lukeSkywalker,
episodeThree: 3,
mayTheFourth: 4,
anakinSkywalker,
};
// good
const obj = {
lukeSkywalker,
anakinSkywalker,
episodeOne: 1,
twoJediWalkIntoACantina: 2,
episodeThree: 3,
mayTheFourth: 4,
};
```
### 只用引号引无效标识符的属性。 
eslint: quote-props jscs: disallowQuotedKeysInObjects
> 一般来说，我们认为比较容易阅读。它改进了语法高亮显示，并且更容易被许多JS引擎优化。

```javascript
// bad
const bad = {
'foo': 3,
'bar': 4,
'data-blah': 5,
};
// good
const good = {
foo: 3,
bar: 4,
'data-blah': 5,
};
```
<!-- ### 用对象展开操作符浅复制对象，优先于Object.assign 。使用对象剩余操作符来获得一个省略某些属性的新对象。

```javascript
// very bad
const original = { a: 1, b: 2 };
const copy = Object.assign(original, { c: 3 }); //  `original` 是可变的 ಠ_ಠ
delete copy.a; // so does this
// bad
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }
// good
const original = { a: 1, b: 2 };
const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }
const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
``` -->
## 数组 Arrays
### 使用字面量创建数组。 
eslint: no-array-constructor
```javascript
// bad
const items = new Array();
// good
const items = [];
```
### 使用数组展开操作符 … 复制数组。

```javascript
// bad
const len = items.length;
const itemsCopy = [];
let i;
for (i = 0; i < len; i += 1) {
itemsCopy[i] = items[i];
}
// good
const itemsCopy = [...items];

```
### 使用展开操作符 … 代替 Array.from，来将一个类数组(array-like) 对象转换成数组。

```javascript
const foo = document.querySelectorAll('.foo');
// good
const nodes = Array.from(foo);
// best
const nodes = [...foo];
```
### 实用 Array.from 代替展开操作符 … 来映射迭代，因为它避免了创建媒介数组。

```javascript
// bad
const baz = [...foo].map(bar);
// good
const baz = Array.from(foo, bar);

```
## 解构 Destructuring
### 当访问和使用对象的多个属性时，请使用对象解构。
eslint: prefer-destructuring jscs: requireObjectDestructuring
```javascript
// bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;
  return `firstName lastName`;
}
// good
function getFullName(user) {
  const { firstName, lastName } = user;
  return `firstName lastName`;
}
// best
function getFullName({ firstName, lastName }) {
  return `firstName lastName`;
}
```
### 使用数组解构。 
eslint: prefer-destructuring jscs: requireArrayDestructuring
```javascript
const arr = [1, 2, 3, 4];
// bad
const first = arr[0];
const second = arr[1];
// good
const [first, second] = arr;

```
### 使用对象解构来实现多个返回值，而不是数组解构。jscs: disallowArrayDestructuringReturn
> 您可以随着时间的推移添加新的属性或更改排序，而不会改变调用时的位置。

```javascript
// bad
function processInput(input) {
  return [left, right, top, bottom];
}
// 调用者需要考虑返回数据的顺序
const [left, __, top] = processInput(input);
// good
function processInput(input) {
  return { left, right, top, bottom };
}
// 调用者只选择他们需要的数据
const { left, top } = processInput(input);
```
## 字符串 Strings
### 字符串使用单引号 ‘’。 
eslint: quotes jscs: validateQuoteMarks

```javascript
// bad
const name = "Capt. Janeway";
// bad - 模板字面量应该包含插值或换行符
const name = `Capt. Janeway`;
// good
const name = 'Capt. Janeway';
```
### 以编程方式构建字符串时，请使用模板字符串而不是字符串连接。
eslint: prefer-template template-curly-spacing jscs: requireTemplateStrings
```javascript
// bad
function sayHi(name) {
	return 'How are you, ' + name + '?';
}
// bad
function sayHi(name) {
	return ['How are you, ', name, '?'].join();
}
// bad
function sayHi(name) {
	return `How are you, ${ name }?`;
}
// good
function sayHi(name) {
 	return `How are you, ${name}?`;
}
```
### 永远不要在字符串上使用 eval() ，它会打开太多的漏洞。 
eslint: no-eval
## 函数 Functions
### 使用命名函数表达式而不是函数声明。 
eslint: func-style jscs: disallowFunctionDeclarations
> 函数声明很容易被提升（Hoisting）,这对可读性和可维护性来说都是不利的;
```javascript
/ bad
function foo() {
  // ...
}
// bad
const foo = function () {
  // ...
};
// good 
// 用明显区别于变量引用调用的词汇命名
const short = function longUniqueMoreDescriptiveLexicalFoo() {
  // ...
};
```
### 用圆括号包裹立即调用函数表达式 (IIFE)。 
eslint: wrap-iife jscs: requireParenthesesAroundIIFE
> 一个立即调用函数表达式是一个单独的单元 – 将函数表达式包裹在括号中，后面再跟一个调用括号，这看上去很紧凑。

```javascript
// 立即调用函数表达式 (IIFE)
(function () {
console.log('Welcome to the Internet. Please follow me.');
}());
```
### 不要使用 arguments。可以选择 rest 语法 … 替代。
> 使用 … 能明确你要传入的参数。另外 rest（剩余）参数是一个真正的数组，而 arguments 是一个类数组（Array-like）。

```javascript
// bad
function concatenateAll() {
  const args = Array.prototype.slice.call(arguments);
  return args.join('');
}
// good
function concatenateAll(...args) {
  return args.join('');
}
```
### 使用默认参数语法，而不要使用一个变化的函数参数
```javascript
// really bad
function handleThings(opts) {
  // 更加糟糕: 如果参数 opts 是 falsy(假值) 的话，它将被设置为一个对象，
  // 这可能是你想要的，但它可以引起一些小的错误。
  opts = opts || {};
  // ...
}
// still bad
function handleThings(opts) {
  if (opts === void 0) {
  opts = {};
}
// ...
}
// good
function handleThings(opts = {}) {
  // ...
}
```
### 始终将默认参数放在最后。
```javascript
// bad
function handleThings(opts = {}, name) {
// ...
}
// good
function handleThings(name, opts = {}) {
// ...
}
```
### 隔开函数签名，括号两边用空格隔开。

```javascript
// bad
const f = function(){};
const g = function (){};
const h = function() {};
// good
const x = function () {};
const y = function a() {};

```
### 不要改变参数。 
eslint: no-param-reassign
> 操作作为参数传入的对象，可能会在调用原始对象时造成不必要的变量副作用。（对象是引用类型）

```javascript
// bad
function f1(obj) {
obj.key = 1;
}
// good
function f2(obj) {
  const key = Object.prototype.hasOwnProperty.call(obj, 'key') ? obj.key : 1;
}
```
## 箭头函数 Arrow Functions
### 当您必须使用匿名函数（如在传递一个内联回调时），请使用箭头函数表示法。 
eslint: prefer-arrow-callback, arrow-spacing jscs: requireArrowFunctions
> 它创建了一个在 this 上下文中执行的函数的版本，这通常是你想要的，而且这样的写法更为简洁。

```javascript
// bad
[1, 2, 3].map(function (x) {
  const y = x + 1;
  return x * y;
});
// bad
[1, 2, 3].map( _ => {
  
  return 0;
});
// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});
// good
[1, 2, 3].map(() => {
  
  return 0;
});
```
### 如果函数体由一个返回无副作用(side effect)的expression(表达式)的单行语句组成，那么可以省略大括号并使用隐式返回。否则，保留大括号并使用 return 语句。

```javascript
// bad
[1, 2, 3].map(number => {
const nextNumber = number + 1;
return `A string containing the nextNumber.`;
});
// good
[1, 2, 3].map(number => `A string containing the number.`);
```
### 如果表达式跨多行，将其包裹在括号中，可以提高可读性。

```javascript
// bad
['get', 'post', 'put'].map(httpMethod => Object.prototype.hasOwnProperty.call(
  httpMagicObjectWithAVeryLongName,
  httpMethod,
  )
);
// good
['get', 'post', 'put'].map(httpMethod => (
  Object.prototype.hasOwnProperty.call(
  httpMagicObjectWithAVeryLongName,
  httpMethod,
  )
));
```
### 如果你的函数只有一个参数并且不使用大括号，则可以省略参数括号。否则，为了清晰和一致性，总是给参数加上括号。
```javascript
// bad
[1, 2, 3].map((x) => x * x);
// good
[1, 2, 3].map(x => x * x);
// good
[1, 2, 3].map(number => (
`A long string with the number. It’s so long that we don’t want it to take up space on the .map line!`
));
// 总是添加（）
// bad
[1, 2, 3].map(x => {
  const y = x + 1;
  return x * y;
});
// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});
```
### 避免使用比较运算符(< =, >=)时，混淆箭头函数语法(=>)。

```javascript
// bad
const itemHeight = item => item.height > 256 ? item.largeSize : item.smallSize;
// bad
const itemHeight = (item) => item.height > 256 ? item.largeSize : item.smallSize;
// good
const itemHeight = item => (item.height > 256 ? item.largeSize : item.smallSize);
// good
const itemHeight = (item) => {
  const { height, largeSize, smallSize } = item;
  return height > 256 ? largeSize : smallSize;
};
```
##类 Classes & 构造函数 Constructors
### 总是使用 *class。避免直接操作 prototype 。

```javascript
// bad
function Queue(contents = []) {
  this.queue = [...contents];
}
Queue.prototype.pop = function () {
  const value = this.queue[0];
  this.queue.splice(0, 1);
  return value;
};
// good
class Queue {
  constructor(contents = []) {
    this.queue = [...contents];
  }
  pop() {
    const value = this.queue[0];
    this.queue.splice(0, 1);
    return value;
  }
}
```
### 使用 extends 继承。
> 因为 extends 是一个内置的原型继承方法并且不会破坏 instanceof。

```javascript
// bad
const inherits = require('inherits');
  function PeekableQueue(contents) {
  Queue.apply(this, contents);
}

inherits(PeekableQueue, Queue);
  PeekableQueue.prototype.peek = function () {
  return this.queue[0];
};

// good
class PeekableQueue extends Queue {
  peek() {
    return this.queue[0];
  }
}
```
### 如果没有指定，类有一个默认的构造函数。一个空的构造函数或者只是委托给父类则不是必须的。 
eslint: no-useless-constructor

```javascript
// bad
class Jedi {
  constructor() {}
  getName() {
    return this.name;
  }
}
// bad
class Rey extends Jedi {
  constructor(...args) {
    super(...args);
  }
}
// good
class Rey extends Jedi {
  constructor(...args) {
    super(...args);
    this.name = 'Rey';
  }
}
```
### 避免重复类成员。 
eslint: no-dupe-class-members

```javascript
// bad
class Foo {
  bar() { return 1; }
  bar() { return 2; }
}
// good
class Foo {
  bar() { return 1; }
}
// good
class Foo {
  bar() { return 2; }
}
```
## 模块 Modules
### 总是使用模块 (import/export) 而不是其他非标准模块系统。
```javascript
// bad
const AirbnbStyleGuide = require('./AirbnbStyleGuide');
module.exports = AirbnbStyleGuide.es6;
// ok
import AirbnbStyleGuide from './AirbnbStyleGuide';
export default AirbnbStyleGuide.es6;
// best
import { es6 } from './AirbnbStyleGuide';
export default es6;
```
### 不要使用通配符 import(导入)。
> 这样能确保你只有一个默认 export(导出)。
```javascript
// bad
import * as AirbnbStyleGuide from './AirbnbStyleGuide';
// good
import AirbnbStyleGuide from './AirbnbStyleGuide';
```
###  不要从 import(导入) 中直接 export(导出)。
> 虽然一行代码简洁明了，但有一个明确的 import(导入) 方法和一个明确的 export(导出) 方法，使事情能保持一致。

```javascript
// bad
// filename es6.js
export { es6 as default } from './AirbnbStyleGuide';
// good
// filename es6.js
import { es6 } from './AirbnbStyleGuide';
export default es6;
```
###  一个地方只在一个路径中 import(导入) 。

```javascript
// bad
import foo from 'foo';
// … 其他一些 imports … //
import { named1, named2 } from 'foo';
// good
import foo, { named1, named2 } from 'foo';
// good
import foo, {
  named1,
  named2,
} from 'foo';
```
###  不要 export(导出) 可变绑定。
eslint: import/no-mutable-exports
> 一般应该避免可变性，特别是在导出可变绑定时。虽然一些特殊情况下，可能需要这种技术，但是一般而言，只应该导出常量引用。
```javascript
// bad
let foo = 3;
export { foo };
// good
const foo = 3;
export { foo };

```
###  在只有单个导出的模块中，默认 export(导出) 优于命名 export(导出)。
eslint: import/prefer-default-export
> 为了鼓励更多的文件只有一个 export(导出)，这有利于模块的可读性和可维护性。

```javascript
// bad
export function foo() {}
// good
export default function foo() {}

```
###  将所有 import 导入放在非导入语句的上面。
eslint: import/first
> 由于 import 被提升，保持他们在顶部，防止意外的行为。

```javascript
// bad
import foo from 'foo';
foo.init();
import bar from 'bar';
// good
import foo from 'foo';
import bar from 'bar';
foo.init();

```
###  多行导入应该像多行数组和对象字面量一样进行缩进。

```javascript
// bad
import {longNameA, longNameB, longNameC, longNameD, longNameE} from 'path';
// good
import {
longNameA,
longNameB,
longNameC,
longNameD,
longNameE,
} from 'path';
```
## 属性 Properties
###  使用 点语法(.) 来访问对象的属性。 
eslint: dot-notation jscs: requireDotNotation

```javascript
const luke = {
jedi: true,
age: 28,
};
// bad
const isJedi = luke['jedi'];
// good
const isJedi = luke.jedi;
```
###  当通过变量访问属性时使用中括号 []。

```javascript
const luke = {
jedi: true,
age: 28,
};
function getProp(prop) {
return luke[prop];
}
const isJedi = getProp('jedi');

```
###  求幂时使用求幂运算符 ** 。
eslint: no-restricted-properties.

```javascript
// bad
const binary = Math.pow(2, 10);
// good
const binary = 2 ** 10;
```
## 变量 Variables
###  总是使用 const 或 let 来声明变量。 不这样做会导致产生全局变量。 我们希望避免污染全局命名空间。
 eslint: no-undef prefer-const
```javascript
// bad
superPower = new SuperPower();
// good
const superPower = new SuperPower();

```
### 将所有的 const 和 let 分组 。
> 当你需要把已分配的变量分配给一个变量时非常有用
```javascript
// bad
let i, len, dragonball,
items = getItems(),
goSportsTeam = true;
// bad
let i;
const items = getItems();
let dragonball;
const goSportsTeam = true;
let len;
// good
const goSportsTeam = true;
const items = getItems();
let dragonball;
let i;
let length;
```
### 变量不要链式赋值。
eslint: no-multi-assign
> 链接变量赋值会创建隐式全局变量。

```javascript
// bad
(function example() {
// JavaScript 将其解析为
// let a = ( b = ( c = 1 ) );
// let关键字只适用于变量a;
// 变量b和c变成了全局变量。
let a = b = c = 1;
}());
console.log(a); // 抛出 ReferenceError（引用错误）
console.log(b); // 1
console.log(c); // 1
// good
(function example() {
let a = 1;
let b = a;
let c = a;
}());
console.log(a); // 抛出 ReferenceError（引用错误）
console.log(b); // 抛出 ReferenceError（引用错误）
console.log(c); // 抛出 ReferenceError（引用错误）
// 同样适用于 `const`
```
###  避免使用一元递增和递减运算符(++, –-)。
> 根据 eslint 文档，一元递增和递减语句会受到自动插入分号的影响，并可能导致应用程序中的值递增或递减，从而导致无提示错误。使用像 num += 1 而不是 num++ 或 num ++ 这样的语句来改变你的值也更具有表现力。不允许一元递增和递减语句也会阻止您无意中预先递增/递减值，这也会导致程序中的意外行为。

```javascript
// bad
const array = [1, 2, 3];
let num = 1;
num++;
--num;
let sum = 0;
let truthyCount = 0;
for (let i = 0; i < array.length; i++) { 
  let value = array[i]; 
  sum += value;
   if (value) {
      truthyCount++; 
    } 
 }
// good 
const array = [1, 2, 3]; 
let num = 1; num += 1; num -= 1; 
const sum = array.reduce((a, b) => a + b, 0);
const truthyCount = array.filter(Boolean).length;
```
## 比较运算符 Comparison Operators 和 等号 Equality
###  使用 === 和 !== 优先于 == 和 !=。 
eslint: eqeqeq
### 对于布尔值使用简写，但对于字符串和数字使用显式比较。

```javascript
// bad
if (isValid === true) {
// ...
}
// good
if (isValid) {
// ...
}
// bad
if (name) {
// ...
}
// good
if (name !== '') {
// ...
}
// bad
if (collection.length) {
// ...
}
// good
if (collection.length > 0) {
// ...
}
```
### 在 case 和 default 子句中，使用大括号来创建包含词法声明的语句块(例如 let, const, function, 和 class).
 eslint: no-case-declarations

```javascript
// bad
switch (foo) {
  case 1:
    let x = 1;
  break;
  case 2:
    const y = 2;
  break;
  case 3:
    function f() {
      // ...
    }
  break;
default:
  class C {}
}
// good
switch (foo) {
  case 1: {
    let x = 1;
    break;
  }
  case 2: {
    const y = 2;
    break;
  }
  case 3: {
    function f() {
      // ...
    }
    break;
  }
  case 4:
    bar();
    break;
  default: {
    class C {}
  }
}
```
###  三元表达式不应该嵌套，通常写成单行表达式。 
eslint: no-nested-ternary

```javascript
// bad
const foo = maybe1 > maybe2
? "bar"
: value1 > value2 ? "baz" : null;
// 拆分成2个分离的三元表达式
const maybeNull = value1 > value2 ? 'baz' : null;
// better
const foo = maybe1 > maybe2
? 'bar'
: maybeNull;
// best
const foo = maybe1 > maybe2 ? 'bar' : maybeNull;

```
### 避免不必要的三元表达式语句。 
eslint: no-unneeded-ternary
```javascript
/ bad
const foo = a ? a : b;
const bar = c ? true : false;
const baz = c ? false : true;
// good
const foo = a || b;
const bar = !!c;
const baz = !c;
```
###  当运算符混合在一个语句中时，请将其放在括号内。混合算术运算符时，不要将 * 和 % 与 + ， -，，/ 混合在一起。
eslint: no-mixed-operators
> 这可以提高可读性，并清晰展现开发者的意图。
```javascript
/ bad
const foo = a && b < 0 || c > 0 || d + 1 === 0;
// bad
const bar = a ** b - 5 % d;
// bad
if (a || b && c) {
return d;
}
// good
const foo = (a && b < 0) || c > 0 || (d + 1 === 0);
// good
const bar = (a ** b) - (5 % d);
// good
if ((a || b) && c) {
return d;
}
// good
const bar = a + b / c * d;
```
### 代码块 Blocks
###  使用大括号包裹所有的多行代码块。 
eslint: nonblock-statement-body-position
```javascript
// bad
if (test)
  return false;
// good
if (test) return false;
// good
if (test) {
  return false;
}
// bad
function foo() { return false; }
// good
function bar() {
  return false;
}
```
###  如果通过 if 和 else 使用多行代码块，把 else 放在 if 代码块闭合括号的同一行。
eslint: brace-style

```javascript
// bad
if (test) {
  thing1();
  thing2();
}
else {
  thing3();
}
// good
if (test) {
  thing1();
  thing2();
} else {
  thing3();
}

```
###  如果一个 if 块总是执行一个 return 语句，后面的 else 块是不必要的。在 else if 块中的 return，可以分成多个 if 块来 return 。
eslint: no-else-return

```javascript
// bad
function foo() {
  if (x) {
    return x;
  } else {
    return y;
  }
}
// bad
function cats() {
  if (x) {
    return x;
  } else if (y) {
    return y;
  }
}
// bad
function dogs() {
  if (x) {
    return x;
  } else {
  if (y) {
    return y;
  }
}
}
// good
function foo() {
  if (x) {
    return x;
  }
return y;
}
// good
function cats() {
  if (x) {
    return x;
  }
  if (y) {
    return y;
  }
}
//good
function dogs(x) {
  if (x) {
    if (z) {
      return y;
  }
} else {
  return z;
  }
}
```
## 控制语句 Control Statements
###  如果您的控制语句(if, while 的)太长或超过最大行长度，那么每个（分组）条件可以放单独一行。逻辑运算符应该放在每行起始处。

```javascript
// bad
if ((foo === 123 || bar === 'abc') && doesItLookGoodWhenItBecomesThatLong() && isThisReallyHappening()) {
 thing1();
}
// bad
if (foo === 123 &&
  bar === 'abc') {
  thing1();
}
// bad
if (foo === 123
  && bar === 'abc') {
  thing1();
}
// bad
if (
  foo === 123 &&
  bar === 'abc'
) {
  thing1();
}
// good
if (
  foo === 123
  && bar === 'abc'
) {
  thing1();
}
// good
if (
  (foo === 123 || bar === "abc")
  && doesItLookGoodWhenItBecomesThatLong()
  && isThisReallyHappening()
) {
  thing1();
}
// good
if (foo === 123 && bar === 'abc') {
  thing1();
}
```
## 注释 Comments
### 多行注释使用 /* … /。
```javascript
/**
* @param {Grid} grid 需要合并的Grid
* @param {Array} cols 需要合并列的Index(序号)数组；从0开始计数，序号也包含。
* @param {Boolean} isAllSome 是否2个tr的cols必须完成一样才能进行合并。true：完成一样；false(默认)：不完全一样
* @return void
* @author 单志永 2018/11/8
*/
function mergeCells(grid, cols, isAllSome) {
    // Do Something
}
```
### 单行注释使用 // 。将单行注释放在需注释的语句上方。在注释之前放置一个空行，除非它位于代码块的第一行。

```javascript
// bad
const active = true;  // is current tab
// good
// is current tab
const active = true;
// bad
function getType() {
  console.log('fetching type...');
  // set the default type to 'no type'
  const type = this.type || 'no type';
  return type;
}
// good
function getType() {
  console.log('fetching type...');
  // set the default type to 'no type'
  const type = this.type || 'no type';
  return type;
}
// also good
function getType() {
  // set the default type to 'no type'
  const type = this.type || 'no type';
  return type;
}
```
###  所有注释符和注释内容用一个空格隔开，让它更容易阅读。 
eslint: spaced-comment

```javascript
// bad
//is current tab
const active = true;
// good
// is current tab
const active = true;
// bad
/**
*make() returns a new element
*based on the passed-in tag name
*/
function make(tag) {
// ...
return element;
}
// good
/**
* make() returns a new element
* based on the passed-in tag name
*/
function make(tag) {
// ...
return element;
}
```
###  给注释增加 FIXME 或 TODO 的前缀，可以帮助其他开发者快速了解这个是否是一个需要重新复查的问题，或是你正在为需要解决的问题提出解决方案。这将有别于常规注释，因为它们是可操作的。使用 FIXME – need to figure this out 或者 TODO – need to implement。
###  使用 // FIXME: 来标识需要修正的问题。注：如果代码中有该标识，说明标识处代码需要修正，甚至代码是错误的，不能工作，需要修复，如何修正会在说明中简略说明。
```javascript
lass Calculator extends Abacus {
  constructor() {
    super();
    // FIXME: shouldn’t use a global here
    total = 0;
  }
}
```
### 使用 // TODO: 来标识需要实现的问题。注：如果代码中有该标识，说明在标识处有功能代码待编写，待实现的功能在说明中会简略说明。

```javascript
class Calculator extends Abacus {
  constructor() {
    super();
    // TODO: total should be configurable by an options param
    this.total = 0;
  }
}
```
## 空格 Whitespace
### 使用 2 个空格作为缩进

```javascript
// bad
function foo() {
∙∙∙∙let name;
}
// bad
function bar() {
∙let name;
}
// good
function baz() {
∙∙let name;
}
```
###  在大括号前放置 1 个空格。
eslint: space-before-blocks jscs: requireSpaceBeforeBlockStatements

```javascript
// bad
function test(){
  console.log('test');
}
// good
function test() {
  console.log('test');
}
// bad
dog.set('attr',{
  age: '1 year',
  breed: 'Bernese Mountain Dog',
});
// good
dog.set('attr', {
  age: '1 year',
  breed: 'Bernese Mountain Dog',
});
```
### 在控制语句（if、while 等）的小括号前放一个空格。在函数调用及声明中，不在函数的参数列表前加空格。 
eslint: keyword-spacing jscs: requireSpaceAfterKeywords

```javascript
// bad
if(isJedi) {
  fight ();
}
// good
if (isJedi) {
  fight();
}
// bad
function fight () {
  console.log ('Swooosh!');
}
// good
function fight() {
  console.log('Swooosh!');
}
```
### 使用空格把运算符隔开。 
eslint: space-infix-ops jscs: requireSpaceBeforeBinaryOperators, requireSpaceAfterBinaryOperators

```javascript
// bad
const x=y+5;
// good
const x = y + 5;

```
### 在文件末尾插入一个空行。 
eslint: eol-last
```javascript
// bad
import { es6 } from './AirbnbStyleGuide';
// ...
export default es6;

// bad
import { es6 } from './AirbnbStyleGuide';
// ...
export default es6;

// good
import { es6 } from './AirbnbStyleGuide';
// ...
export default es6;
```
### 长方法链式调用时使用缩进（2个以上的方法链式调用）。使用一个点 . 开头，强调该行是一个方法调用，不是一个新的声明。
eslint: newline-per-chained-call no-whitespace-before-property

```javascript
// bad
$('#items').find('.selected').highlight().end().find('.open').updateCount();

// bad
$('#items').
find('.selected').
highlight().
end().
find('.open').
updateCount();

// good
$('#items')
.find('.selected')
.highlight()
.end()
.find('.open')
.updateCount();

// bad
const leds = stage.selectAll('.led').data(data).enter().append('svg:svg').classed('led', true)
.attr('width', (radius + margin) * 2).append('svg:g')
.attr('transform', `translate(${radius + margin},${radius + margin})`)
.call(tron.led);
// good
const leds = stage.selectAll('.led')
.data(data)
.enter().append('svg:svg')
.classed('led', true)
.attr('width', (radius + margin) * 2)
.append('svg:g')
.attr('transform', `translate(${radius + margin},${radius + margin})`)
.call(tron.led);

// good
const leds = stage.selectAll('.led').data(data);
```
### 不要在圆括号内加空格。

```javascript
// bad
function bar( foo ) {
  return foo;
}
// good
function bar(foo) {
  return foo;
}
// bad
if ( foo ) {
  console.log(foo);
}
// good
if (foo) {
  console.log(foo);
}
```
### 不要在中括号内添加空格。
eslint: array-bracket-spacing jscs: disallowSpacesInsideArrayBrackets

```javascript
// bad
const foo = [ 1, 2, 3 ];
console.log(foo[ 0 ]);
// good
const foo = [1, 2, 3];
console.log(foo[0]);
```
### 在大括号内添加空格
```javascript
// bad
const foo = {clark: 'kent'};
// good
const foo = { clark: 'kent' };
```
## 类型转换 Type Casting & Coercion
###  在声明语句的开始处就执行强制类型转换.
###  字符串: 
eslint: no-new-wrappers

```javascript
// => this.reviewScore = 9;
// bad
const totalScore = new String(this.reviewScore); // typeof totalScore 是 "object" 而不是 "string"
// bad
const totalScore = this.reviewScore + ''; // 调用 this.reviewScore.valueOf()
// bad
const totalScore = this.reviewScore.toString(); // 不能保证返回一个字符串
// good
const totalScore = String(this.reviewScore);
```
### 使数字: 使用 Number 进行转换，而 parseInt 则始终以基数解析字串。 
eslint: radix no-new-wrappers

```javascript
const inputValue = '4';
// bad
const val = new Number(inputValue);
// bad
const val = +inputValue;
// bad
const val = inputValue >> 0;
// bad
const val = parseInt(inputValue);
// good
const val = Number(inputValue);
// good
const val = parseInt(inputValue, 10);

```
### 布尔值: 
eslint: no-new-wrappers

```javascript
const age = 0;
// bad
const hasAge = new Boolean(age);
// good
const hasAge = Boolean(age);
// best
const hasAge = !!age;
```
## 命名规则 Naming Conventions
### 避免使用单字母名称。使你的命名具有描述性。 
eslint: id-length

```javascript
// bad
function q() {
// ...
}
// good
function query() {
// ...
}
```
### 当命名对象，函数和实例时使用驼峰式命名。 
eslint: camelcase jscs: requireCamelCaseOrUpperCaseIdentifiers

```javascript
// bad
const OBJEcttsssss = {};
const this_is_my_object = {};
function c() {}
// good
const thisIsMyObject = {};
function thisIsMyFunction() {}

```
### 当命名构造函数或类的时候使用 PascalCase 式命名，（注：即单词首字母大写）。
eslint: new-cap

```javascript
// bad
function user(options) {
  this.name = options.name;
}
const bad = new user({
  name: 'nope',
});
// good
class User {
  constructor(options) {
    this.name = options.name;
  }
}
const good = new User({
  name: 'yup',
});
```
### 当 导出(export) 一个默认函数时使用驼峰式命名。你的文件名应该和你的函数的名字一致。

```javascript
function makeStyleGuide() {
// ...
}
export default makeStyleGuide;

```
### 当导出一个 构造函数 / 类 / 单例 / 函数库 / 纯对象时使用 PascalCase 式命名，（愚人码头注：即单词首字母大写）。
```javascript
const AirbnbStyleGuide = {
  es6: {
    },
};
export default AirbnbStyleGuide;

```
## 存取器 Accessors
###  属性的存取器函数不是必须的。
### 別使用 JavaScript 的 getters/setters，因为它们会导致意想不到的副作用，而且很难测试，维护和理解。相反，如果要使用存取器函数，使用 getVal() 及 setVal(‘hello’)。

```javascript
// bad
class Dragon {
  get age() {
    // ...
  }
  set age(value) {
    // ...
  }
}
// good
class Dragon {
  getAge() {
    // ...
  }
  setAge(value) {
    // ...
  }
}
```
###  如果属性/方法是一个 boolean, 使用 isVal() 或 hasVal() 方法。

```javascript
// bad
if (!dragon.age()) {
  return false;
}
// good
if (!dragon.hasAge()) {
  return false;
}
```

)