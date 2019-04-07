我们说处理 javascript 异步最常用的方式就是通过回调函数，对于回调函数我们昨天对此做了介绍  
简单快速，  
我们一般使用嵌套回调或者链式回调，会产生以下问题

1.  当采用嵌套回调时，会导致层级太多，不利于维护
2.  所以我们又采用了链式回调，对嵌套回调进行拆分，拆分后的函数间耦合度很高，
3.  如果需要传递参数，函数之间的关联性会更高，而且要对参数进行校验以提高代码的健壮性
4.  如果将我们自己的回调函数传递给第三方插件或者库，就要考虑一些不可控因素

    -     	调用回调过早
    -     	调用回调过晚（或不被调用）
    -     	调用回调次数过多或者过少

promise 的存在就是为了解决以上问题  
虽然我们日常写回调函数不会有这么严格的要求，但是如果不这样去写回调函数，就会存在隐患，当在团队协作的时候，显得编码规范显得尤为重要

本文不重点介绍如何使用 promise，重点介绍的是 promise 解决了哪些异步回调出现的问题。

### 什么是 promise

我们来看一个场景，有助于我们了解 promise

> 设想一下这个场景，我去 KFC，交给收银员 10 元，下单买一个汉堡，下单付款。到这里，我已经发出了一个请求（买汉堡），启动了一次交易。  
> 但是做汉堡需要时间，我不能马上得到这个汉堡，收银员给我一个收据来代替汉堡。到这里，收据就是一个承诺(promise),保证我最后能得到汉堡。  
> 所以我需要好好的保留的这个收据，对我来说，收据就是汉堡，虽然这张收据不能吃，我需要等待汉堡做好，等待收银员叫号通知我  
> 等待的过程中，我可以做些别的事情  
> 收银员终于叫到了我的号，我用收据换来了汉堡  
> 当然还有一种情况，当我去柜台取汉堡的时候，收银员告诉我汉堡卖光了，做汉堡的师傅受伤了等等原因，导致了我无法得到这个汉堡  
> 虽然我有收据（承诺），但是可能得到汉堡（成功），可能得不到汉堡（失败）  
> 我由等待汉堡变成了等到或者等不到，这个过程不可逆，

上面很形象的介绍了 promise，上面的等待汉堡和得到汉堡，汉堡卖光了，得不到汉堡，分别对应 promise 的三种状态  
三种状态：pending（进行中）、fulfilled（已成功）和 rejected（已失败）(一旦状态改变，就不会再变)

### 回调函数调用过早

调用过早就是将异步函数作为同步处理了，  
我们之前说过，javascript 以单线程同步的方式执行主线程，遇到异步会将异步函数放入到任务队列中，  
当主线程执行完毕，会循环执行任务队列中的函数，也就是事件循环，直到任务队列为空。

#### 事件循环和任务队列

事件循环就像是一个游乐场，玩过一个游戏后，你需要重新排到队尾才能再玩一次  
任务队列就是，在你玩过一个游戏后，可以插队接着玩

我们看一个栗子

```javascript
const promise = new Promise((resolve, reject) => {
  resolve("成功啦");
});
promise.then(res => {
  console.log(res);
  console.log("我是异步执行的");
});
console.log("我在主线程");
```

看下输出，重点看输出顺序

```javascript
//我在主线程
//成功啦
//我是异步执行的
```

直接手动是 promise 的状态切为成功状态，console.log("我是异步执行的");这段代码也是异步执行的  
提供给 then()的回调永远都是异步执行的，所以 promise 中不会出现回调函数过早执行的情况

### 回调函数调用过晚或不被调用

#### 回调函数调用过晚

回调函数调用过晚的处理原理和调用过早很类似，  
在 promise 的 then()中存放着异步函数，所有的异步都存在于 js 的任务队列中，当 js 的主线程执行完毕后，会依次执行任务队列中的内容，不会出现执行过晚的情 况

#### 回调函数不被调用

我们用栗子说话

```javascript
const promise = new Promise((resolve, reject) => resolve("成功啦"));
promise.then(s => console.log(s));
console.log("我在主线程");
```

成功状态的输出

```javascript
//我在主线程
//成功啦
```

成功状态下回调被调用  
继续看一下失败的回调

```javascript
const promise = new Promise((resolve, reject) => reject("失败啦"));
promise.then(null, s => console.log(s));
console.log("我在主线程");
```

失败状态的输出

```javascript
//我在主线程
//失败啦
```

失败状态下回调被调用  
所以说，不管是失败还是成功，回调函数都会被调用

### 回调函数调用次数过多或者过少

#### 调用次数过多

我们之前说了 promise 有三种状态
pending（进行中）、fulfilled（已成功）和 rejected（已失败）状态一旦状态改变，就不会再变  
一个栗子

```javascript
const promise = new Promise((resolve, reject) => {
  reject("失败啦");
  resolve("成功啦");
});
promise
  .then(
    res => {
      console.log(`我是异步执行的成功:${res}`);
    },
    err => {
      console.log(`我是异步执行的失败:${err}`);
    }
  )
  .catch(err => {
    console.log(err);
  });
console.log("我在主线程");
```

输出

```javascript
//我在主线程
//我是异步执行的失败:失败啦
```

当状态变为失败时，就不会再变为成功，成功的函数也不会执行，反之亦然

#### 调用次数过少

回调函数正常是调用一次，过少=>0 次=>回调函数不被调用，上面刚刚讨论过

### promise 局限

#### 错误被吃掉

首先我们要理解，什么是错误被吃掉，是指错误信息不被打印吗？  
并不是，举个例子：

```javascript
throw new Error("error");
console.log("不会执行这里");
```

在这种情况下，因为 throw error 的缘故，代码被阻断执行，并不会打印 “不会执行这里”，  
再举个栗子：

```javascript
const promise = new Promise(null);
console.log("不会执行这里");
```

以上代码依然会被阻断执行，这是因为如果通过无效的方式使用 Promise，并且出现了一个错误阻碍了正常 Promise 的构造，结果会得到一个立刻抛出的异常，而不是一个被拒绝的 Promise。  
然而再举个栗子：

```javascript
let promise = new Promise(() => {
  throw new Error("error");
});
console.log("不会执行这里");
```

这次会正常的打印 “不会执行这里”，说明 Promise 内部的错误不会影响到 Promise 外部的代码，而这种情况我们就通常称为 “吃掉错误”。  
其实这并不是 Promise 独有的局限性，try..catch 也是这样，同样会捕获一个异常并简单的吃掉错误。  
而正是因为错误被吃掉，Promise 链中的错误很容易被忽略掉，这也是为什么会一般推荐在 Promise 链的最后添加一个 catch 函数，因为对于一个没有错误处理函数的 Promise 链，任何错误都会在链中被传播下去，直到你注册了错误处理函数。

#### 单一值

Promise 只能有一个完成值或一个拒绝原因，然而在真实使用的时候，往往需要传递多个值，一般做法都是构造一个对象或数组，然后再传递，then 中获得这个值后，又会进行取值赋值的操作，每次封装和解封都无疑让代码变得笨重。  
说真的，并没有什么好的方法，建议是使用 ES6 的解构赋值：

```javascript
Promise.all([Promise.resolve(1), Promise.resolve(2)]).then(([x, y]) => {
  console.log(x, y);
});
```

#### 无法取消

Promise 一旦新建它就会立即执行，无法中途取消。

#### 无法得知 pending 状态

当处于 pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

参考链接  
[JavaScript Promise 迷你书](http://liubin.org/promises-book/ "JavaScript Promise 迷你书")  
[Promise 对象](http://es6.ruanyifeng.com/#docs/promise "Promise 对象")  
[ES6 系列之我们来聊聊 Promise](https://juejin.im/post/5bc5e114e51d450e632277aa#heading-12 "ES6 系列之我们来聊聊 Promise")
