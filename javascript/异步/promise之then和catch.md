前面介绍了 Promise.all()、Promise.race()、Promise.finally()、resolve()、reject()

我们展开 promise 的原型链，看看有哪些实例方法  

```javascript
const p1 = new Promise((resolve, reject) => {});
console.log(p1.__proto__);
```

[![](http://www.qdtalk.com/wp-content/uploads/2019/01/promise1.png)](http://www.qdtalk.com/wp-content/uploads/2019/01/promise1.png)
p1 是 Promise 的一个实例，我们展开 p1 的原型链，可以看到  
p1 具有 then 方法，catch 方法，还有 finally 方法（这个方法我们之前讨论过）  
也就是说，这些方法是定义在原型对象 Promise.prototype 上的。  

## then 方法

它的作用是为 Promise 实例添加状态改变时的回调函数。then 方法可以有两个参数（第二个可选），  

- 第一个是 resolve 状态的回调函数  
- 第二个是 reject 状态的回调函数（可选）  

then 方法返回的是一个新的 Promise 实例（不是原来那个 Promise 实例）我们前面说过 Promise 接收一个函数作为参数，这个函数有两个参数，第二个参数可选，分别是 resolve 和 reject，这两个参数分别和 then 方法的两个参数对应。  
then 方法其实并不简单，我们看几个栗子  
观察如下代码，想一想最后输出什么？  

### 一个经典栗子

```javascript
Promise.reject("出错啦")
  .then(res => console.log(`res1:${res}`), err => console.log(`err1:${err}`))
  .then(res => console.log(`res2:${res}`), err => console.log(`err2:${err}`));
```

不卖关子，直接告诉你答案：

```javascript
//=>err1:出错啦
//=>res2:undefined
```

可能和你想的不一样，或者看着输出反推，也没找出个所以然，  
我们一起分析一下原因  
Promise.reject()方法也会返回一个新的 Promise 实例，该实例的状态为 rejected。  
以下两种写法等价  

```javascript
const p1 = Promise.reject("出错啦");
//和下面的方法等效
const p2 = new Promise((resolve, reject) => {
  reject("出错啦");
});
```

resolve 和 reject 当传入一个字符串作为参数时，则会返回一个新的 Promise 实例  
如果是 resolve，新的 Promise 对象的状态为 resolve  
如果是 reject，新的 Promise 对象的状态为 reject  
上面的 p1 和 p2 都会返回一个新的 Promise 实例，并且状态为 reject

```javascript
const p1 = Promise.reject("出错啦");
p1.then(
  res => {
    console.log(`res1:${res}`); //“这里不执行”
  },
  err => {
    console.log(`err1:${err}`); //=>err1:出错啦
  }
);
//最后输出err1:出错啦
```

因为 p1 返回的 Promise 实例状态为 reject，所以执行了 then 下面第二个参数的方法  
到这里可能会有一个疑问，then 方法的第二个参数可选，在这里如果不用第二个参数会怎么样呢？  

```javascript
const p1 = Promise.reject("出错啦");
p1.then(res => {
  console.log(`res1:${res}`);
});
// .catch(err=>{
//   console.log(`err:${err}`)
// })
//=>Uncaught (in promise) 出错啦
```

会报一个未捕获的错，当省略第二个参数时，catch 的存在就很有意义 ，关于 catch 我们稍后讨论   
上面对第一个 then 的输出做了讨论，我们继续看第二 then   
在解释第二个 then 之前，我们先看另一个栗子  

### 关于返回值

```javascript
const p3 = new Promise((resolve, reject) => {
  reject("出错啦");
})
  //第一个then
  .then(
    res => {
      console.log(`resolve-then1：${res}`); //“不执行这里”
      return 1;
    },
    err => {
      console.log(`reject-then1：${err}`); //=>reject-then1：出错啦
      return 1;
    }
  )
  //第二个then
  .then(
    res => {
      console.log(`resolve-then2：${res}`); //=>resolve-then2：1
      throw new Error("抛出一个错");
    },
    err => {
      console.log(`reject-then2：${err}`); //“不执行这里”
      throw new Error("抛出一个错");
    }
  )
  //第三个then
  .then(
    res => {
      console.log(`resolve-then3：${res}`); //“不执行这里”
    },
    err => {
      console.log(`reject-then3：${err}`); //=>reject-then3：Error: 抛出一个错
    }
  )
  //第四个then
  .then(
    res => {
      console.log(`resolve-then4：${res}`); //=>resolve-then4：undefined
    },
    err => {
      console.log(`reject-then4：${err}`); //“不执行这里”
    }
  )
  //第五个then
  .then(
    res => {
      console.log(`resolve-then5：${res}`); //=>resolve-then5：undefined
    },
    err => {
      console.log(`reject-then5：${err}`); //“不执行这里”
    }
  );
```

输出,具体位置，参见上面的代码

```javascript
//=>reject-then1：出错啦
//=>resolve-then2：1
//=>reject-then3：Error: 抛出一个错
//=>resolve-then4：undefined
//=>resolve-then5：undefined
```

上面罗列了我能找到的所有返回值的情况   
[在 MDN 上关于返回值的介绍](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/then#%E8%BF%94%E5%9B%9E%E5%80%BC "在MDN上关于返回值的介绍")，解释的有点绕
[![](http://www.qdtalk.com/wp-content/uploads/2019/01/return.png)](http://www.qdtalk.com/wp-content/uploads/2019/01/return.png)
我们分析一下 p3  
p3 直接执行的 reject('出错啦')，返回的是一个 promise 对象，状态为 reject  
所以第一个 then 执行的第二个参数方法,输出=>reject-then1：出错啦，然后 return 1  
我们看第二个 then 的输出，可以发现，第一个 then 的 return 值会作为下第二个 then 的回调函数的参数值，第二个 then 又执行了 throw Error  
第二个 then 的 throw Error，使得第三个 then 下的 Promise 对象状态为 reject，所以第三个 then 输出=>reject-then3：Error: 抛出一个错   
第三个 then 没有返回值，也没有执行 throw Error，结果导致第四个 then 输出=>resolve-then4：undefined  
第四个 then 和第三个 then 一样，没有返回值，所以第五个 then 输出的结果和第四个一样=>resolve-then5：undefined  

我们做一下总结

1. 如果前一个 Promise 对象是 resolve 状态，则后一个 Promise 对象执行第一个参数方法（resolve）
2. 如果前一个 Promise 对象是 reject 状态，则后一个 Promise 对象执行第二个参数方法（reject）
3. 如果前一个 Promise 对象抛出异常（throw error），则后一个 Promise 对象执行第二个参数方法（reject）
4. 如果前一个 Promise 对象返回具体的值，则此数值将作为后一个 Promise 对象的输入，执行第一个参数方法（resolve）
5. 如果前一个 Promise 对象没有返回状态（resolve 或者 reject），也没有抛错（throw error），也没有返回具体数值，我们则认为它返回 了一个 undefined，则 undefined 将作为后一个 Promise 对象的输入，执行第一个参数方法（resolve）

关于 p3 我们介绍到这里，回头看一下前面的栗子

```javascript
Promise.reject("出错啦")
  .then(res => console.log(`res1:${res}`), err => console.log(`err1:${err}`))
  .then(res => console.log(`res2:${res}`), err => console.log(`err2:${err}`));
```

现在看来似乎简单了  
因为前面是 reject 状态，所以第一个 then 执行第二个参数方法 “err => console.log(`err1:${err}`)”  
因为第一个 then 方法，没有返回状态，没有抛错，没有返会具体值，所以返回的是 undefined，第二个 then 执行  
“res => console.log(`res2:${res}`”

## catch

花了大量篇幅介绍 then 方法，其实 then 方法懂了，catch 自然也就明白了，因为，catch 就是 then 方法的语法糖  
catch 方法是.then(null, rejection)或.then(undefined, rejection)的别名  
也就是说，catch 也是 then，它用于捕获错误，它的参数也就是 then 的第二个参数。  
我们将上面的栗子做一下改动

```javascript
const p4 = new Promise((resolve, reject) => {
  reject("出错啦");
})
  .catch(err => {
    console.log(`catch1：${err}`); //=>catch1：出错啦
    return 1;
  })
  .then(res => {
    console.log(`then1：${res}`); //=>then1：1
    throw new Error("抛出一个错");
  })
  .catch(err => {
    console.log(`catch2：${err}`); //=>catch2：Error: 抛出一个错
  })
  .then(res => {
    console.log(`then2：${res}`); //=>then2：undefined
  });
```

如果上面的关于 then 的介绍看懂了，这个自然就会明白

关于 promise 的 then 和 catch 就介绍这么多

END

参考链接  
[ES6 Promise——then 与 catch 的返回值实践](https://blog.csdn.net/a695993410/article/details/80954552 "ES6 Promise——then与catch的返回值实践")  
[ES6 关于 Promise 的用法](https://segmentfault.com/a/1190000011652907 "ES6关于Promise的用法")  
[promise 的.then 返回的一个新 promise，他的状态和值，问题好大](https://segmentfault.com/q/1010000012805108 "promise的.then返回的一个新promise，他的状态和值，问题好大")  
[Promise.prototype.then()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/then#%E8%BF%94%E5%9B%9E%E5%80%BC "Promise.prototype.then()")
