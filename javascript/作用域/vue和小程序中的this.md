之前用了两篇文章介绍了 this 的基本使用方法，  
今天我们看看 this 在 vue 和小程序中的使用情况

## vue 中的 this 使用

### 匿名函数下的 this

方便本地 demo，没有使用 webpack  
引入两个文件，vue 和 axios  
axios 返回一个 promise 对象，我们通过 axios 进行 ajax 请求

```html
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<body>
  <div id="app">
    {{ message }}
  </div>
</body>
```

看下 js 部分

```javascript
var message = "我是全局message!";
var app = new Vue({
  el: "#app",
  data() {
    return {
      message: "我是vue下的message!"
    };
  },
  created() {
    this.getData();
  },
  methods: {
    getData() {
      axios
        .get(
          "https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise1"
        )
        .then(function() {
          console.log(this.message); //=>我是全局message!
        });
    }
  }
});
```

不必关心 axios 请求的接口返回的数据  
那么在 axios 下，输出的是=>我是全局 message!,为什么呢？我们是想输出=>"我是 vue 下的 message!"  
在这里有那么一些人就蒙了，为什么 axios 下会这样呢？  
axios 有话说：  
[![](http://www.qdtalk.com/wp-content/uploads/2019/01/2zaIei4O.jpg)](http://www.qdtalk.com/wp-content/uploads/2019/01/2zaIei4O.jpg)

出现这种情况，不是 axios 的锅，  
不信你往下看

```javascript
//其他代码省略
  getData() {
    setTimeout(function () {
      console.log(this.message);//=>我是全局message!
    }, 1000);
  }
}
```

我们将 getData 方法下的 axios 请求换掉，用一个定时器替代，其他部分保持不变  
输出依然是=>我是全局 message!  
为什么？  
因为  
**匿名函数下 this 指向 window**
至于原因，[这里解释的很清楚 https://www.zhihu.com/question/21958425](https://www.zhihu.com/question/21958425 "这里解释的很清楚https://www.zhihu.com/question/21958425")
你只需要记住一点，默认情况下，匿名函数 this 指向 window  
如何处理匿名函数下 this 指向的问题呢？

### 通过 bind 来处理

结合之前所学，我们可以通过 bind 来进行处理

```javascript
//部分代码省略
created() {
	this.getData()
},
	methods: {
		getData() {
			setTimeout(function () {
				console.log(this.message);//=>我是vue下的message!
			}.bind(this), 1000);
		}
	}
```

通过 bind 可以改变 this 的指向，这是一种解决方式  
还有一种比较常用

### this 赋值暂存

```javascript
created() {
  this.getData()
},
methods: {
  getData() {
    const that = this
    setTimeout(function () {
      console.log(that.message);//=>我是vue下的message!
    }, 1000);
  }
}
```

在匿名函数之前，我们先将 this 赋值给 that，在匿名函数中使用 that 来替代原来的 this，同样可以实现我们所希望的效果  
如果你的项目支持 ES6 标准，那么

### 箭头函数是你最佳选择

```javascript
getData() {
  axios.get('https://easy-mock.com/mock/5b0525349ae34e7a89352191/example/promise1')
    .then(() => {
      console.log(this.message);
    })
}
```

我们在之前的文章中总结过一个结论  
**this 的指向是在函数执行的时候定义的，而不是在函数创建时定义的，this 指向的是最后调用它的对象**
我们接下来本篇文章的另一个知识点

## 箭头函数中的 this

看一个栗子

```javascript
var heroName = "黄蓉";
var heroObj = {
  heroName: "郭靖",
  callName: function() {
    console.log(this.heroName); //=>郭靖
  }
};
heroObj.callName();
```

this 指向最后调用它的对象，所以输出=>郭靖  
再看下箭头函数的栗子

```javascript
var heroName = "黄蓉";
var heroObj = {
  heroName: "郭靖",
  callName: () => {
    console.log(this.heroName); //=>黄蓉
  }
};
heroObj.callName();
```

对这个输出结果感到意外吗？  
不管懵没懵，我们再看一个栗子

```javascript
var heroName = "黄蓉";
function getHeroName() {
  this.heroName = "郭靖";
  const foo = () => {
    console.log(this.heroName); //=>郭靖
  };
  foo();
}
getHeroName();
```

放在一起做一下比较：  
普通函数：this 的指向是在函数**执行**的时候绑定的，而不是在函数**创建**时绑定的  
箭头函数：this 的指向是在函数**创建**的时候绑定的，而不是在函数**执行**时绑定的。

不管在什么情况下,箭头函数的 this 跟外层 function 的 this 一致，外层 function 的 this 指向谁，箭头函数的 this 就指向谁，如果外层不是 function 则指向 window。  
ES6 中定义的时候绑定的 this 是继承的父执行上下文里面的 this

### 小程序中的 this

如果项目中的小程序也支持 ES6 标准，无疑，使用箭头函数是一个不错的选择

```javascript
//省略。。。
  getLocation() {
    wx.chooseLocation({
      success: res => {
        if (res.address && res.name) {
          this.setData({
            shopAddress: `${res.address}(${res.name})`
          })
        } else if (res.address) {
          this.setData({
            shopAddress: `${res.address}`
          })
        }
      }
    })
  }
```

很多场景就不需要缓存中转 this

```javascript
var that = this; //使用箭头函数替代此方案
```

合理的使用 this 会使我们事半功倍

参考链接  
[匿名函数的 this 指向为什么是 window?](https://www.zhihu.com/question/21958425 "匿名函数的this指向为什么是window?")  
[一句话记住 js 箭头函数的 this](https://zhuanlan.zhihu.com/p/38997000 "一句话记住js箭头函数的this")
