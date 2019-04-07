之前说过 async 就是 Generator 的语法糖  

## Generator 简单回顾

看一段伪代码（纯展示逻辑，跑不起来的）

```javascript
function* getData() {
  yield loadData1(); //“这个执行成功后执行it.next()，启动下一个yield，没有yield就执行return，没有return 就是return undefined”
  yield loadData2(); //“这个执行成功后执行it.next()，启动下一个yield，没有yield就执行return，没有return 就是return undefined”
  yield loadData3(); //“这个执行成功后执行it.next()，启动下一个yield，没有yield就执行return，没有return 就是return undefined”
}
const it = getData();
it.next(); //“启动生成器”
```

执行的逻辑看上面的代码注释，真正处理异步的时候，我相信很有有人用 Generator 来处理，至少我不会，太麻烦了  
这时候 async 来了，它的出现大大简化了 Generator 的使用  

## async 简单介绍

上面的伪代码，用 async 来实现  

```javascript
async function getData() {
  await loadData1();
  await loadData2();
  await loadData3();
}
getData();
```

async 就像是 Generator 中的“\*”，await 就相当于 Generator 中的 yield，同样的 await 只能在使用了 async 的函数内使用  

### 关于 async 的返回

一个小栗子

```javascript
async function doSth() {
  const GJ = "郭靖在发呆";
}
console.log(doSth()); //=>Promise {<resolved>: undefined}
```

async 自动把函数转换为 Promise，返回的是一个 Promise 对象    
关于 async 的介绍的文章，多如牛毛，我么不再赘述，进入主题  

## 异步实战

还是那个需求

> 现在有接口 1，接口 2，接口 3
> 按顺序输出接口 1，接口 2，接口 3 的值

我们上一次分别用了回调函数嵌套，原生的 Promise，以及 Generator 来实现的这个功能   
ajax 是使用 jquery

在写法上还是有些“hello world”，因为内容太理论，我们下面采用 axios+async 来实现   
上面的需求可以这么理解，先调用接口一，接口二的执行依赖于接口一的返回，接口三的执行依赖于接口二的返回  
所以，接口一，接口二，接口三，需要同步的按顺序执行，因为现在 ajax 请求基本都是通过异步来处理的  
众所周知，axios 返回的一个 Promise 对象，下面的栗子依赖于这一特性  
为了简化 demo 的流程，方便大家实战操作，我没有搭建 webpack 或者 babel 环境，  
就是新建了一个 html 页面，在浏览器控制台查看效果  
因为兼容问题，建议使用 Chrome 浏览器进行操作  
首先

```html
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

js 部分,easy-mock 上面有三个接口，接口一和接口二的返回值很简单，接口三返回值是长度在 10-20 之间的数组，  
以保证接口三的返回时间要长一些，以体现异步效果

```javascript
// 一个函数p11，返回一个axios，Promise对象
function p11() {
  return axios(
    "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise1"
  ).then(({ data }) => {
    if (data.data.name) {
      return data.data.name;
    }
  });
}
// 一个函数p22，返回一个axios，Promise对象
function p22() {
  return axios(
    "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise2"
  ).then(({ data }) => {
    if (data.data.name) {
      return data.data.name;
    }
  });
}
// 一个函数p33，返回一个axios，Promise对象
function p33() {
  return axios(
    "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/mock"
  ).then(({ data }) => {
    if (data.success) {
      return data.data.projects;
    }
  });
}
```

三个请求已经封装完毕，函数没有 cnsole.log  
作为比较，也作为复习，我们看下 Promie 的实现

```javascript
p11()
  .then(res1 => {
    console.log(res1);
    return p22();
  })
  .then(res2 => {
    console.log(res2);
    return p33();
  })
  .then(res3 => {
    console.log(res3);
    return res3;
  });
```

输出
[![](http://www.qdtalk.com/wp-content/uploads/2019/01/async.png)](http://www.qdtalk.com/wp-content/uploads/2019/01/async.png)
对此没什么可以赘述的

### async 处理异步

再我们看下 async

```javascript
async function asyncFUn() {
  const RES1 = await p11();
  console.log(RES1);
  const RES2 = await p22();
  console.log(RES2);
  const RES3 = await p33();
  console.log(RES3);
  return RES3;
}
asyncFUn();
```

await 的存在 使异步变成了同步  
在处理异步的时候，await 后面要根的是一个返回 Promise 的函数  
就像上面的 p11,p22,p33,函数都是返回一个 axios（axios 返回一个 Promise 对象），如果不是就用 Promise 包装一下  
否则会出现这样的结果

```html
<script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
```

```javascript
function loadData1() {
  $.ajax({
    url: "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise1",
    success: ({ data, success }) => {
      if (success) {
        console.log(`接口一返回：${data.name}`);
        return data.name;
      }
    }
  });
}

function loadData2() {
  $.ajax({
    url: "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise2",
    success: ({ data }) => {
      console.log(`接口二返回：${data.name}`);
      return data.name;
    }
  });
}

function loadData3() {
  $.ajax({
    url: "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/mock",
    success: ({ data, success }) => {
      if (success) {
        console.log("接口三返回", data.projects);
        return data.projects;
      }
    }
  });
}
async function asyncFUn1() {
  const RES1 = await loadData1();
  console.log(RES1); //=>undefined
  const RES2 = await loadData2();
  console.log(RES2); //=>undefined
  const RES3 = await loadData3();
  console.log(RES3); //=>undefined
  return RES3;
}
asyncFUn1();
```

输出
[![](http://www.qdtalk.com/wp-content/uploads/2019/01/async2.png)](http://www.qdtalk.com/wp-content/uploads/2019/01/async2.png)
这时候就需要用 Promise 进行包装。类似于这样

```javascript
function loadData1() {
  return new Promise((resolve, reject) => {
    $.ajax({
      url:
        "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise1",
      success: ({ data, success }) => {
        if (success) {
          console.log(`接口一返回：${data.name}`);
          resolve(data.name);
        }
      }
    });
  });
}
//后面省略
```

如果不是异步，那就没有必要通过 await 来处理了，当然 await 后面也支持非异步，可以自行实践

### async 处理报错

看到了，async 中没有 then catch，怎么处理报错信息呢？  
天无绝人之路，使用 try catch

```javascript
async function asyncFUn() {
  const RES1 = await p11();
  console.log(RES1);
  const RES2 = await p22();
  console.log(RES2);
  const RES3 = await p33();
  console.log(RES3);
  return RES3;
}
try {
  asyncFUn();
} catch (error) {
  console.log(error);
}
```

好，关于 async await 就介绍到这里
