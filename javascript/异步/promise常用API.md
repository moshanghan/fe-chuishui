网络上关于 PromiseAPI 使用的文章多如牛毛，为了保持 javascript 异步系列文章的完整性，现在对 promise 的 API 进行简单全面的介绍

### 准备工作

我在 easy-mock 添加了三个接口，备用  
[![](http://www.qdtalk.com/wp-content/uploads/2018/12/easy-mock.png)](http://www.qdtalk.com/wp-content/uploads/2018/12/easy-mock.png)
依然使用 axios 进行 ajax 请求

### Promise.all()

Promise.all()有点像“并行”  
我们看一个栗子

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>promise</title>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
  </head>

  <body>
    <script>
      {
        const p1 = axios
          .get(
            "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise1"
          )
          .then(({ data }) => {
            console.log("p1成功啦");
            return data.data;
          });

        const p2 = axios
          .get(
            "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise2"
          )
          .then(({ data }) => {
            console.log("p2成功啦");
            return data.data;
          });

        const p3 = axios
          .get(
            "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/mock"
          )
          .then(({ data }) => {
            console.log("p3成功啦");
            return data.data;
          });

        const p = Promise.all([p3, p1, p2])
          .then(arr => {
            console.log(arr);
            console.log("Promise.all成功啦");
          })
          .catch(err => {
            console.log(err, "Promise.all错啦");
          });
      }
    </script>
  </body>
</html>
```

我们知道 axios 返回的是一个 promise 对象,我们可以看下

```javascript
console.log(p1);
```

[![](http://www.qdtalk.com/wp-content/uploads/2018/12/p1.png)](http://www.qdtalk.com/wp-content/uploads/2018/12/p1.png)
Promise.all 就是用于将多个 Promise 实例，包装成一个新的 Promise 实例  
Promise.all,接收一个数组作为参数，数组的每一项都返回 Promise 实例  
我们重点看这段代码

```javascript
const p = Promise.all([p3, p1, p2])
  .then(arr => {
    console.log(arr);
    console.log("Promise.all成功啦");
  })
  .catch(err => {
    console.log(err, "Promise.all错啦");
  });
```

p1,p2,p3 都是返回 promise 实例，Promise.all 不关心他们的执行顺序,**如果他们都返回成功的状态，Promise.all 则返回成功的状态**，输出一个数组，是这三个 p1,p2,p3 的返回值，数组的顺序和他们的执行顺序无关，和他们作为参数排列的顺序有关  
我们看下输出
[![](http://www.qdtalk.com/wp-content/uploads/2018/12/output.png)](http://www.qdtalk.com/wp-content/uploads/2018/12/output.png)
为了是拉长接口三的返回时间我对接口三的数据进行了修改，返回值是长度 1000-2000 之间的随机数组，所以 p3 的执行要晚于 p1 和 p2，  
但我们输出的 arr，p3 依然在前面，这给我们带来一个便利，返回值数组的顺序和方法的执行顺序无关，可以进行人为进行控制  
我们将 p1 做一下改动，使 p1 报错

```javascript
const p1 = axios
  .get("https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise1")
  .then(({ data }) => {
    console.log("p1成功啦");
    return xxxx.data; //将data.data修改为xxxx.data
  });
```

[![](http://www.qdtalk.com/wp-content/uploads/2018/12/err1.png)](http://www.qdtalk.com/wp-content/uploads/2018/12/err1.png)
**如果有一个返回失败（reject），Promise.all 则返回失败（reject）的状态，**此时第一个被 reject 的实例的返回值，会传递给 P 的回调函数。  
三个 promise 实例参数之间是“与”的关系，全部成功，Promise.all 就返回成功，有一个失败，Promise.all 就返回失败  
换个角度说，一个 promise 的执行结果依赖于另外几个 promise 的执行结果，  
例如：
几个 ajax 全部执行完了，才能渲染页面，  
几个 ajax 全部执行完了，才能做一些数据的计算操作，  
不关心执行顺序，只关心集体的执行结果

### Promise.race()

Promise 中的竞态，用法和 Promise.all 类似，对应参数的要求和 Promise.all 相同，传入一个数组作为参数，参数要返回一个 Promise 实例  
race 就是竞争的意思，数组内的 Promise 实例，谁执行的快，就返回谁的执行结果，不管是成功还是失败

```javascript
const p = Promise.race([p3, p1, p2])
  .then(res => {
    console.log(res);
    console.log("Promise.all成功啦");
  })
  .catch(err => {
    console.log(err, "Promise.all错啦");
  });
```

[![](http://www.qdtalk.com/wp-content/uploads/2018/12/p2.png)](http://www.qdtalk.com/wp-content/uploads/2018/12/p2.png)
通过输出我们发现  
p1 是第一个完成的，所以 p 的返回结果就是 p1 的执行结果  
而且就算完成，但是 进程不会立即停止，还会继续执行下去。

#### 关于 race 的使用场景

搜了一下，很多文章都说是用来解决网络超时的提示，类似于下面这样

```javascript
const p3 = axios
  .get("https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/mock")
  .then(({ data }) => {
    console.log("p3成功啦");
    return data.data;
  });
const p4 = new Promise(function(resolve, reject) {
  setTimeout(() => reject(new Error("网络连接超时")), 50);
});
const p = Promise.race([p3, p4])
  .then(res => console.log(res))
  .catch(err => console.log(err));
```

p3 的 ajax 和 50ms 的定时器比较，看谁执行的快，如果超过了 50ms，p3 的 ajax 还没返回，就告知用户网络连接超时  
这里有个问题，就算提示超时了，p3 还在继续执行，它并没有停下来，直到有状态返回  
个人观点：race 可以用来为 ajax 请求的时长划定范围，如果 ajax 请求时长超过 xxxms 会执行某个方法，或者 ajax 请求时长不超过 xxms 会执行某个方法，总之，race 的应用空间不是很大

### Promise.finally()

finally 方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。该方法是 ES2018 引入标准的。

```javascript
const p = Promise.race([p3, p4])
  .then(res => console.log(res))
  .catch(err => console.log(err))
  .finally(() => {
    console.log("finally的执行与状态无关");
  });
```

当 promise 得到状态（不论成功或失败）后就会执行 finally，

可以这么说，resolve 和 reject 撑起了 Promise 的半边天，我们平时用的最多的就是这两个 API

### 准备工作

1. 上一次我们用 axios 来实现 ajax 请求，因为 axios 也是返回的 promise，可能有人会产生误导，这次我们用 Jquery 来实现 ajax 请求
2. mock 配置接口，用来模拟后台接口

### resove()和 reject()

我们知道 Promise 是一个构造函数，用来实例化一个 Promise 实例，  
这个 Promise 构造函数，用一个函数来作为参数  
这个作为参数的函数又有两个参数  
第一个参数是 resolve  
第二个参数是 reject  
当然这两个参数都不是必填项

Promise 有三个状态：等待，成功，失败  
异步是一个耗时的过程，当在执行异步的时候，就是 Promise 的等待过程  
当异步函数执行完，
如果异步执行成功，我们就调用 resolve，将等待切换到成功  
如果异步执行失败，我们就调用 reject，将等待切换到失败  
（类似于，有个判断，如果成功就执行 resolve，如果失败就执行 reject，需要我们手动切换）  
过程不可逆，失败就是失败，不会再变，反之亦然  
我们先看下 mock 的数据

```javascript
{
  "success":true,
  "data": {
    "name": "这是接口一的返回值"
  }
}
```

再来看下我们的代码栗子：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>promise</title>
    <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
  </head>

  <body>
    <script>
      window.onload = () => {
        new Promise((resolve, reject) => {
          $.ajax({
            url:
              "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise1",
            success: ({ data, success }) => {
              if (success) {
                resolve(data);
              } else {
                reject(data);
              }
            }
          });
        }).then(res => {
          console.log(res);
        });
      };
    </script>
  </body>
</html>
```

Jquery 请求后台接口，如果返回的值是成功的，是我们想要的，就把 data 作为参数传递给 resolve，然后就会执行 then 方法，最后输出

```javascript
//=>{name: "这是接口一的返回值"}
```

有几点要声明  
resolve, reject 是形参，不是必须这么写  
如下写法也可以

```javascript
//省略。。
  new Promise((aaa, bbb) => {
//省略。。
 if (success) {
	 aaa(data)
 } else {
	 bbb(data)
 }
//省略。。
```

关于 resolve 和 reject 的参数介绍可以参考[《ECMAScript 6 入门》](http://es6.ruanyifeng.com/?search=prosmise&x=0&y=0#docs/promise "《ECMAScript 6 入门》")，不再此赘述

### 使用场景

异步都可以使用 Promise 进行包装  
ajax 请求，图片加载  
定时器  
UI 交互（点击事件等）

#### ajax 请求

上面介绍了 Promise 的 ajax 请求的实现

#### 图片加载

```javascript
new Promise((resolve, reject) => {
  const url =
    "http://www.qdtalk.com/wp-content/uploads/2018/11/bryan-goff-528709-unsplash-1.jpg";
  const image = new Image();
  image.onload = () => {
    resolve(image);
  };
  image.onerror = () => {
    reject(new Error("图片加载失败"));
  };
  //对image添加一些属性
  image.src = url;
  image.alt = "这是陌上寒个人博客的banner";
}).then(res => {
  console.log("图片加载成功，即将返回一个图片dom");
  console.log(res); //=>  <img src="http://www.qdtalk.com/wp-content/uploads/2018/11/bryan-goff-528709-unsplash-1.jpg" alt="这是陌上寒个人博客的banner">
});
```

这个栗子有点“hello world”，有以下场景可能会用到

1. 当在处理图片懒加载的时候，
2. 图片过大，可能加载失败
3. 页面图片过多（页面就是由图片堆起来的）通过图片的加载，来实现加载的进度条。

#### 定时器

我想定时器就没有必要通过 Promie 包装了，因为它本身就有一个回调函数

```javascript
setTimeout(
  (a, b) => {
    console.log(a); //=>第一个参数
    console.log(b); //=>第二个参数
  },
  1000,
  "第一个参数",
  "第二个参数"
);
```

所以说用 Promise 包装定时器意义不大

#### UI 交互

我们举一个常用的栗子=>模态框  
[![](http://www.qdtalk.com/wp-content/uploads/2018/12/模态框.png)](http://www.qdtalk.com/wp-content/uploads/2018/12/模态框.png)
当我触发某一个动作的时候，会弹出这个模态框，当你点击了确定或者取消都会进行与之相关的操作，我们在这里应用了 Promise 进行包装  
代码如下,重点看 js 代码

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>promise</title>
    <link rel="stylesheet" href="https://qdtalk.com/weixin/style1229.css" />
  </head>

  <body>
    <button id="myBtn">出现弹窗</button>

    <div class="modal-wap">
      <header>标题</header>
      <article>
        这是内容
      </article>
      <footer>
        <button id="confirm">确定</button>
        <button id="cancel">取消</button>
      </footer>
    </div>
    <script>
      function dom$(id) {
        return document.getElementById(id);
      }

      function modelFun() {
        return new Promise((resolve, reject) => {
          dom$("confirm").onclick = () => {
            resolve();
          };
          dom$("cancel").onclick = () => {
            reject();
          };
        });
      }
      dom$("myBtn").onclick = () => {
        document.querySelector(".modal-wap").style.display = "block";
        modelFun()
          .then(
            () => {
              console.log("你点击了确定");
            },
            () => {
              console.log("你点击了取消");
            }
          )
          .finally(() => {
            document.querySelector(".modal-wap").style.display = "none";
          });
      };
    </script>
  </body>
</html>
```

相信你的眼光，这是一个很漂亮的模态框！😂  
[![](http://www.qdtalk.com/wp-content/uploads/2018/12/my模态框.png)](http://www.qdtalk.com/wp-content/uploads/2018/12/my模态框.png)
点击“出现弹窗”按钮，出现模态框，模态框有两个按钮，取消和确定  
我们对这个模态框进行了包装，如果点击了确定就执行 resolve，如果点击了取消就执行 reject,然后就会执行 then 方法  
then 方法，不是我们今天要介绍的内容，简单说：它也有两个参数，一个是 resolve，另一个是 reject  
点了不同的按钮，执行了不同的事件  
最后执行 finally，不管是点击了取消还是确定，只要状态切换，都会关闭模态框  
easy  
以上就是关于 resolve 和 reject 的常用内容介绍

参考链接  
[Promise 对象](http://es6.ruanyifeng.com/#docs/promise#Promise-%E7%9A%84%E5%90%AB%E4%B9%89 "Promise 对象")
[Promise.prototype.finally](https://www.jianshu.com/p/44182971d555 "Promise.prototype.finally")
