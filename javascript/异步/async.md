说起 javascript 异步，我想你一定会想起 async，甚至要排在 promise 之前，  
要说 async，就不得不提 Generator 生成器（好多知识都是一环扣一环，很是无奈），async 就是 Generator 的语法糖   

## Generator

关于 Generator，阮一峰对此介绍的很详细，如果想深入研究可以翻阅查看，我们不在这里做详细的研究，简单了解  

### 一个不正经的函数

Generator 写法很像一个函数，但是区别于普通函数，一个栗子  

```javascript
function doSth1() {
  return "这是普通函数";
}
const it1 = doSth1();

console.log(it1); //=>这是普通函数

//一个生成器doSth
function* doSth() {
  yield "郭靖抓鸡";
  yield "郭靖杀鸡";
  yield "黄蓉拔毛";
  yield "黄蓉炖鸡";
  yield "洪七公吃鸡";
  return "洪七公说：这只鸡真好吃";
}
// 通过一个遍历器控制这个生成器的执行
const it = doSth();

console.log(it);
```

我们看下 it 的输出  
[![](http://www.qdtalk.com/wp-content/uploads/2019/01/ddq.png)](http://www.qdtalk.com/wp-content/uploads/2019/01/ddq.png)
it 是一个迭代器，我们没有像打印普通函数那样打印出我们想要的结果    
普通的函数一旦执行，就不会停下来，直到执行完毕（不考虑 debugger，alert，throw err，死循环等情况）  
Generator 生成器，可以做到，它可以让一个函数的执行暂停  
Generator 生成器不会自行启动，需要使用遍历器的 next(),来控制 Generator 生成器的执行

```javascript
console.log(it.next()); //=>{value: "郭靖抓鸡", done: false}
console.log(it.next()); //=>{value: "郭靖杀鸡", done: false}
console.log(it.next()); //=>{value: "黄蓉拔毛", done: false}
console.log(it.next()); //=>{value: "黄蓉炖鸡", done: false}
console.log(it.next()); //=>{value: "洪七公吃鸡", done: false}
console.log(it.next()); //=>{value: "洪七公说：这只鸡真好吃", done: true}
```

### 关于函数的 return

我们打断一下，先声明一个知识点，在函数中，return 不是必须存在的，但是如果我们没有为函数执行 return，函数会隐式存在一个 return undefined，一个栗子

```javascript
//不存在return，则隐式return undefined
function doSth1() {
  let name = "这是普通函数";
}
const it1 = doSth1();

console.log(it1); //=>undefined
//存return
function doSth2() {
  return "这是普通函数";
}
const it2 = doSth2();

console.log(it2); //=>这是普通函数
```

普通函数是这样，Generator 也是如此

### 我们继续

第一次调用 next，是对 Generator 生成器进行启动操作，Generator 生成器启动，执行，当遇到内部的 yield 时就会暂停  
生成器中的 yield 有点像普通函数中的 return，但是在普通函数中当遇到 return，就意味着函数执行完毕，以此作为输出，  
而 yield 不会完全停止函数的执行。  
~~可以这么理解，Generator 从一开始就是暂停的，通 it.next()打破其暂停状态，  
当遇到 yield 时，会执行 yeild 后面的表达式，并返回执行之后的值，然后再次进入暂停状态，~~  
这么理解似乎更合理，  
调用第一个 it.next()，向 Generator 提出申请，我要的下一个值是什么？第一个 yield，对其进行回复，就是 yield '郭靖抓鸡';然后 Generato 暂停了，等待下一次询问  
然后调用第二个 it.next()，向 Generator 提出申请，我要的下一个值是什么？第二个 yield，对其进行回复，就是 yield '郭靖杀鸡';然后又停了  
...
当调用第六个 it.next()时，Generator 已经没有 yield 了，怎么回答呢？它用 return 进行回答，终极回答  
如果没有 return 呢，这就是我们前面说的隐式的 return undefined  
仔细看代码，你会发现，next 和 yield 有一个对应关系，如果要使 Generator 执行完毕（done：true），next 的数量要比 yield 的数量多一个  
为什么会存在这种情况呢？  
因为第一个 it.next()是用来启动 Generator 生成器，启动后，遇到了第一个 yield，停下来了，  
第二个 it.next()是用来启动第一个 yield，以此类推，这样就导致他们“错位”了  
直到 Generator 的 return（显式或隐式）最后执行完毕  
我们再看一个栗子

```javascript
function* doSth(params) {
  return params + (yield "黄蓉");
}
const it = doSth("郭靖");
console.log(it.next()); //=>{value: "黄蓉", done: false}
console.log(it.next("吃饭")); //=>{value: "郭靖吃饭", done: true}
```

上的生成器可以这么写

```javascript
function* doSth(params) {
  let a = params + (yield "黄蓉");
  return a;
}
```

next() 支持传递参数，但是不会在第一个 next()中传递参数，因为第一个 next()是用来启动 Generator 的  
第一个 next()执行，启动了 doSth(),遇到第一个 yield 停下来，yield 把“黄蓉”返给第一个 next()  
第二个 next()执行，启动了第一个 yield，并把“吃饭”传递给正在等待的 yield，然后继续执行，遇到了 return，执行完毕

## 一个需求

需求如下：

> 现在有接口 1，接口 2，接口 3
> 按顺序输出接口 1，接口 2，接口 3 的值

我们还是使用 Jquery 处理 ajax 请求

```html
<script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
```

还是 easy-mock 上面的那三个接口，为了拉长接口三的执行时间，我这接口了加入了长度为 10-20 的数组  
保证它比接口一和接口二返回的时间长  
因为后面要对这三个接口的请求进行封装，先把代码列出来，方便查询参考

```javascript
function loadData1() {
  $.ajax({
    url: "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise1",
    success: ({ data, success }) => {
      if (success) {
        console.log(data);
      }
    }
  });
}

function loadData2() {
  $.ajax({
    url: "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise2",
    success: ({ data }) => {
      console.log(data);
    }
  });
}

function loadData3() {
  $.ajax({
    url: "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/mock",
    success: ({ data, success }) => {
      if (success) {
        console.log(data);
      }
    }
  });
}
```

### 回调函数

常规做法  
即，loadData1 执行成功后，执行 loadData2，loadData2 执行成功后执行 loadData3

```javascript
function getData() {
  $.ajax({
    url: "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise1",
    success: ({ data, success }) => {
      if (success) {
        console.log(data);
        $.ajax({
          url:
            "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise2",
          success: ({ data }) => {
            console.log(data);
            if (data.name) {
              $.ajax({
                url:
                  "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/mock",
                success: ({ data, success }) => {
                  if (success) {
                    console.log(data);
                  }
                }
              });
            }
          }
        });
      }
    }
  });
}
getData();
```

输出符合预期  
[![](http://www.qdtalk.com/wp-content/uploads/2019/01/async1.png)](http://www.qdtalk.com/wp-content/uploads/2019/01/async1.png)
回调地狱，说的就是你 😂

### Promise

来一个 Promise 实现的，顺便对前面的 Promise 进行复习

```javascript
/*
 *对ajax的请求做了一个简单的封装
 *@ajaxUrl,接口地址，公共部分已经抽取
 *@successCb，ajax请求成功后的回调函数
 */
function ajaxFun(ajaxUrl, successCb) {
  $.ajax({
    url: `https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/${ajaxUrl}`,
    success: res => successCb(res)
  });
}
//promise
new Promise((resolve, reject) => {
  ajaxFun("promise1", ({ data, success }) => {
    if (success) {
      console.log("接口一", data);
      resolve(data);
    }
  });
})
  .then(() => {
    ajaxFun("promise2", ({ data }) => {
      if (data.name) {
        console.log("接口二", data);
      }
    });
  })
  .then(() => {
    ajaxFun("mock", ({ data, success }) => {
      if (success) {
        console.log("接口三", data);
      }
    });
  });
```

### Generator 实现

```javascript
function loadData1() {
  ajaxFun("promise1", ({ data, success }) => {
    if (success) {
      console.log("接口一", data);
      it.next(); //“接口一成功返回后，执行下一步”
    }
  });
}

function loadData2() {
  ajaxFun("promise2", ({ data }) => {
    if (data.name) {
      console.log("接口二", data);
      it.next(); //“接口二成功返回后，执行下一步”
    }
  });
}

function loadData3() {
  ajaxFun("mock", ({ data, success }) => {
    if (success) {
      console.log("接口三", data);
    }
  });
}
function* getData() {
  yield loadData1();
  yield loadData2();
  yield loadData3();
}
const it = getData();
it.next(); //“启动生成器”
```

先弄一个遍历器出来，然后通过 it.next(),启动遍历器  
执行第一个接口请求，当第一个接口请求成功了，继续 next(),以此类推  
我想很少有人会通过 Generator 实现多个异步同步执行，但是 async 可以
