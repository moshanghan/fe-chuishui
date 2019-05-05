本文转载自[前端面试总结-基础javascript篇](https://github.com/forthealllight/blog/issues/15)  
原文作者：JonyYu


# 前端面试总结——基础javascript篇
js是前端开发人员必须熟练掌握的技能，这里概括js的一些必须了解的理论知识：

> * 梳理js基础知识
> * 查漏补缺

### 1. get请求传参长度的误区
_**误区：我们经常说get请求参数的大小存在限制，而post请求的参数大小是无限制的。**_

实际上HTTP 协议从未规定 GET/POST 的请求长度限制是多少。对get请求参数的限制是来源与浏览器或web服务器，浏览器或web服务器限制了url的长度。为了明确这个概念，我们必须再次强调下面几点:

* HTTP 协议 未规定 GET 和POST的长度限制
* GET的最大长度显示是因为 浏览器和 web服务器限制了 URI的长度
* 不同的浏览器和WEB服务器，限制的最大长度不一样
* 要支持IE，则最大长度为2083byte，若只支持Chrome，则最大长度 8182byte

### 2. 补充get和post请求在缓存方面的区别
post/get的请求区别，具体不再赘述。

补充补充一个get和post在缓存方面的区别：

* get请求类似于查找的过程，用户获取数据，可以不用每次都与数据库连接，所以可以使用缓存。
* post不同，post做的一般是修改和删除的工作，所以必须与数据库交互，所以不能使用缓存。因此get请求适合于请求缓存。

### 3. 闭包
一句话可以概括：闭包就是能够读取其他函数内部变量的函数，或者子函数在外调用，子函数所在的父函数的作用域不会被释放。

### 4. 类的创建和继承
（1）类的创建（es5）：new一个function，在这个function的prototype里面增加属性和方法。

下面来创建一个Animal类：

```
// 定义一个动物类
function Animal (name) {
  // 属性
  this.name = name || 'Animal';
  // 实例方法
  this.sleep = function(){
    console.log(this.name + '正在睡觉！');
  }
}
// 原型方法
Animal.prototype.eat = function(food) {
  console.log(this.name + '正在吃：' + food);
};
```

这样就生成了一个Animal类，实力化生成对象后，有方法和属性。

（2）类的继承——原型链继承

```
--原型链继承
function Cat(){ }
Cat.prototype = new Animal();
Cat.prototype.name = 'cat';
//　Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.eat('fish'));
console.log(cat.sleep());
console.log(cat instanceof Animal); //true 
console.log(cat instanceof Cat); //true
```

* 介绍：在这里我们可以看到new了一个空对象,这个空对象指向Animal并且Cat.prototype指向了这个空对象，这种就是基于原型链的继承。
* 特点：基于原型链，既是父类的实例，也是子类的实例
* 缺点：无法实现多继承

（3）构造继承：使用父类的构造函数来增强子类实例，等于是复制父类的实例属性给子类（没用到原型）

```
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // false
console.log(cat instanceof Cat); // true
```

* 特点：可以实现多继承
* 缺点：只能继承父类实例的属性和方法，不能继承原型上的属性和方法。

（4）实例继承和拷贝继承

实例继承：为父类实例添加新特性，作为子类实例返回

拷贝继承：拷贝父类元素上的属性和方法

上述两个实用性不强，不一一举例。

（5）组合继承：相当于构造继承和原型链继承的组合体。通过调用父类构造，继承父类的属性并保留传参的优点，然后通过将父类实例作为子类原型，实现函数复用

```
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
Cat.prototype = new Animal();
Cat.prototype.constructor = Cat;
// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // true
```

* 特点：可以继承实例属性/方法，也可以继承原型属性/方法
* 缺点：调用了两次父类构造函数，生成了两份实例

（6）寄生组合继承：通过寄生方式，砍掉父类的实例属性，这样，在调用两次父类的构造的时候，就不会初始化两次实例方法/属性

```
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
(function(){
  // 创建一个没有实例方法的类
  var Super = function(){};
  Super.prototype = Animal.prototype;
  //将实例作为子类的原型
  Cat.prototype = new Super();
})();
// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); //true
```

* 较为推荐

### 5. 如何解决异步回调地狱
promise、generator、async/await

### 6. 说说前端中的事件流
HTML中与javascript交互是通过事件驱动来实现的，例如鼠标点击事件onclick、页面的滚动事件onscroll等等，可以向文档或者文档中的元素添加事件侦听器来预订事件。想要知道这些事件是在什么时候进行调用的，就需要了解一下“事件流”的概念。

什么是事件流：事件流描述的是从页面中接收事件的顺序,DOM2级事件流包括下面几个阶段。

* 事件捕获阶段
* 处于目标阶段
* 事件冒泡阶段

**addEventListener**：**addEventListener** 是DOM2 级事件新增的指定事件处理程序的操作，这个方法接收3个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值。最后这个布尔值参数如果是true，表示在捕获阶段调用事件处理程序；如果是false，表示在冒泡阶段调用事件处理程序。

**IE只支持事件冒泡**。

### 7. 如何让事件先冒泡后捕获
在DOM标准事件模型中，是先捕获后冒泡。但是如果要实现先冒泡后捕获的效果，对于同一个事件，监听捕获和冒泡，分别对应相应的处理函数，监听到捕获事件，先暂缓执行，直到冒泡事件被捕获后再执行捕获之间。

### 8. 事件委托
* 简介：事件委托指的是，不在事件的发生地（直接dom）上设置监听函数，而是在其父元素上设置监听函数，通过事件冒泡，父元素可以监听到子元素上事件的触发，通过判断事件发生元素DOM的类型，来做出不同的响应。
* 举例：最经典的就是ul和li标签的事件监听，比如我们在添加事件时候，采用事件委托机制，不会在li标签上直接添加，而是在ul父元素上添加。
* 好处：比较合适动态元素的绑定，新添加的子元素也会有监听函数，也可以有事件触发机制。

### 9. 图片的懒加载和预加载
* 预加载：提前加载图片，当用户需要查看时可直接从本地缓存中渲染。
* 懒加载：懒加载的主要目的是作为服务器前端的优化，减少请求数或延迟请求数。

两种技术的本质：两者的行为是相反的，一个是提前加载，一个是迟缓甚至不加载。
懒加载对服务器前端有一定的缓解压力作用，预加载则会增加服务器前端压力。

### 10. mouseover和mouseenter的区别
* mouseover：当鼠标移入元素或其子元素都会触发事件，所以有一个重复触发，冒泡的过程。对应的移除事件是mouseout
* mouseenter：当鼠标移除元素本身（不包含元素的子元素）会触发事件，也就是不会冒泡，对应的移除事件是mouseleave

### 11. js的new操作符做了哪些事情
new 操作符新建了一个空对象，这个对象原型指向构造函数的prototype，执行构造函数后返回这个对象。

### 12.改变函数内部this指针的指向函数（bind，apply，call的区别）
* 通过apply和call改变函数的this指向，他们两个函数的第一个参数都是一样的表示要改变指向的那个对象，第二个参数，apply是数组，而call则是arg1,arg2...这种形式。
* 通过bind改变this作用域会返回一个新的函数，这个函数不会马上执行。

### 13. js的各种位置，比如clientHeight,scrollHeight,offsetHeight ,以及scrollTop, offsetTop,clientTop的区别？
* clientHeight：表示的是可视区域的高度，不包含border和滚动条
* offsetHeight：表示可视区域的高度，包含了border和滚动条
* scrollHeight：表示了所有区域的高度，包含了因为滚动被隐藏的部分。
* clientTop：表示边框border的厚度，在未指定的情况下一般为0
* scrollTop：滚动后被隐藏的高度，获取对象相对于由offsetParent属性指定的父坐标(css定位的元素或body元素)距离顶端的高度。

### 14. js拖拽功能的实现
* 首先是三个事件，分别是mousedown，mousemove，mouseup
  当鼠标点击按下的时候，需要一个tag标识此时已经按下，可以执行mousemove里面的具体方法。
* clientX，clientY标识的是鼠标的坐标，分别标识横坐标和纵坐标，并且我们用offsetX和offsetY来表示元素的元素的初始坐标，移动的举例应该是：
  **鼠标移动时候的坐标-鼠标按下去时候的坐标。**
  也就是说定位信息为：
  鼠标移动时候的坐标-鼠标按下去时候的坐标+元素初始情况下的offetLeft.
* 还有一点也是原理性的东西，也就是拖拽的同时是绝对定位，我们改变的是绝对定位条件下的left
  以及top等等值。

补充：也可以通过html5的拖放（Drag 和 drop）来实现

### 15. 异步加载js的方法
* defer：只支持IE如果您的脚本不会改变文档的内容，可将 defer 属性加入到<script>标签中，以便加快处理文档的速度。因为浏览器知道它将能够安全地读取文档的剩余部分而不用执行脚本，它将推迟对脚本的解释，直到文档已经显示给用户为止。
* async，HTML5属性仅适用于外部脚本，并且如果在IE中，同时存在defer和async，那么defer的优先级比较高，脚本将在页面完成时执行。
* 创建script标签，插入到DOM中

### 16. Ajax 解决浏览器缓存问题
* 在ajax发送请求前加上 anyAjaxObj.setRequestHeader("If-Modified-Since","0")。
* 在ajax发送请求前加上 anyAjaxObj.setRequestHeader("Cache-Control","no-cache")。
* 在URL后面加上一个随机数： "fresh=" + Math.random()。
* 在URL后面加上时间搓："nowtime=" + new Date().getTime()。
* 如果是使用jQuery，直接这样就可以了 $.ajaxSetup({cache:false})。这样页面的所有ajax都会执行这条语句就是不需要保存缓存记录。

### 17. js的节流和防抖
http://www.cnblogs.com/coco1s/p/5499469.html

### 18. JS中的垃圾回收机制
**必要性**：**_由于字符串、对象和数组没有固定大小，所有当他们的大小已知时，才能对他们进行动态的存储分配。JavaScript程序每次创建字符串、数组或对象时，解释器都必须分配内存来存储那个实体。只要像这样动态地分配了内存，最终都要释放这些内存以便他们能够被再用，否则，JavaScript的解释器将会消耗完系统中所有可用的内存，造成系统崩溃。_**

这段话解释了为什么需要系统需要垃圾回收，JS不像C/C++，他有自己的一套垃圾回收机制（Garbage Collection）。JavaScript的解释器可以检测到何时程序不再使用一个对象了，当他确定了一个对象是无用的时候，他就知道不再需要这个对象，可以把它所占用的内存释放掉了。例如：

```
var a="hello world";
var b="world";
var a=b;
//这时，会释放掉"hello world"，释放内存以便再引用
```

垃圾回收的方法：标记清除、计数引用。

* **标记清除**

这是最常见的垃圾回收方式，当变量进入环境时，就标记这个变量为”进入环境“,从逻辑上讲，永远不能释放进入环境的变量所占的内存，永远不能释放进入环境变量所占用的内存，只要执行流程进入相应的环境，就可能用到他们。当离开环境时，就标记为离开环境。

垃圾回收器在运行的时候会给存储在内存中的变量都加上标记（所有都加），然后去掉环境变量中的变量，以及被环境变量中的变量所引用的变量（条件性去除标记），删除所有被标记的变量，删除的变量无法在环境变量中被访问所以会被删除，最后垃圾回收器，完成了内存的清除工作，并回收他们所占用的内存。

* **引用计数法**

另一种不太常见的方法就是引用计数法，引用计数法的意思就是每个值没引用的次数，当声明了一个变量，并用一个引用类型的值赋值给改变量，则这个值的引用次数为1,；相反的，如果包含了对这个值引用的变量又取得了另外一个值，则原先的引用值引用次数就减1，当这个值的引用次数为0的时候，说明没有办法再访问这个值了，因此就把所占的内存给回收进来，这样垃圾收集器再次运行的时候，就会释放引用次数为0的这些值。

用引用计数法会存在内存泄露，下面来看原因：

```
function problem() {
    var objA = new Object();
    var objB = new Object();
    objA.someOtherObject = objB;
    objB.anotherObject = objA;
}
```

在这个例子里面，objA和objB通过各自的属性相互引用，这样的话，两个对象的引用次数都为2，在采用引用计数的策略中，由于函数执行之后，这两个对象都离开了作用域，函数执行完成之后，因为计数不为0，这样的相互引用如果大量存在就会导致内存泄露。

特别是在DOM对象中，也容易存在这种问题：

```
var element=document.getElementById（’‘）；
var myObj=new Object();
myObj.element=element;
element.someObject=myObj;
```

这样就不会有垃圾回收的过程。

### 19.eval是做什么的
它的功能是将对应的字符串解析成js并执行，应该避免使用js，因为非常消耗性能（2次，一次解析成js，一次执行）

### 20.如何理解前端模块化
前端模块化就是复杂的文件编程一个一个独立的模块，比如js文件等等，分成独立的模块有利于重用（复用性）和维护（版本迭代），这样会引来模块之间相互依赖的问题，所以有了commonJS规范，AMD，CMD规范等等，以及用于js打包（编译等处理）的工具webpack

### 21.Commonjs、 AMD和CMD
一个模块是能实现特定功能的文件，有了模块就可以方便的使用别人的代码，想要什么功能就能加载什么模块。

* Commonjs：开始于服务器端的模块化，同步定义的模块化，每个模块都是一个单独的作用域，模块输出，modules.exports，模块加载require()引入模块。
* AMD：中文名异步模块定义的意思。

requireJS实现了AMD规范，主要用于解决下述两个问题。

```
1.多个文件有依赖关系，被依赖的文件需要早于依赖它的文件加载到浏览器

2.加载的时候浏览器会停止页面渲染，加载文件越多，页面失去响应的时间越长。
```

语法：requireJS定义了一个函数define，它是全局变量，用来定义模块。

requireJS的例子：

```
//定义模块
define(['dependency'], function(){
        var name = 'Byron';
        function printName(){
            console.log(name);
        }
        return {
            printName: printName
        };
   });
//加载模块
require(['myModule'], function (my){
 my.printName();
}
```

requirejs定义了一个函数define,它是全局变量，用来定义模块：

```
define(id?dependencies?,factory)
```

在页面上使用模块加载函数：

```
require([dependencies],factory)；
```

总结AMD规范：require（）函数在加载依赖函数的时候是异步加载的，这样浏览器不会失去响应，它指定的回调函数，只有前面的模块加载成功，才会去执行。
因为网页在加载js的时候会停止渲染，因此我们可以通过异步的方式去加载js,而如果需要依赖某些，也是异步去依赖，依赖后再执行某些方法。

### 22.对象深度克隆的简单实现
```
function deepClone(obj){
  var newObj= obj instanceof Array ? []:{};
  for(var item in obj){
    var temple= typeof obj[item] == 'object' ? deepClone(obj[item]):obj[item];
    newObj[item] = temple;
  }
  return newObj;
}
```

ES5的常用的对象克隆的一种方式。注意数组是对象，但是跟对象又有一定区别，所以我们一开始判断了一些类型，决定newObj是对象还是数组~

### 23.实现一个once函数，传入函数参数只执行一次
```
function ones(func){
    var tag=true;
    return function(){
      if(tag==true){
        func.apply(null,arguments);
        tag=false;
      }
      return undefined
    }
}
```

### 24.将原生的ajax封装成promise
```
var  myNewAjax=function(url){
  return new Promise(function(resolve,reject){
      var xhr = new XMLHttpRequest();
      xhr.open('get',url);
      xhr.send(data);
      xhr.onreadystatechange=function(){
           if(xhr.status==200&&readyState==4){
                var json=JSON.parse(xhr.responseText);
                resolve(json)
           }else if(xhr.readyState==4&&xhr.status!=200){
                reject('error');
           }
      }
  })
}
```

### 25.js监听对象属性的改变
我们假设这里有一个user对象,

#### (1)在ES5中可以通过Object.defineProperty来实现已有属性的监听
```
Object.defineProperty(user,'name',{
    set：function(key,value){
       
    }
})
```

缺点：如果id不在user对象中，则不能监听id的变化

#### (2)在ES6中可以通过Proxy来实现
```
var  user = new Proxy({}，{
 set：function(target,key,value,receiver){

  }
})
```

这样即使有属性在user中不存在，通过user.id来定义也同样可以这样监听这个属性的变化哦~

### 26.如何实现一个私有变量，用getName方法可以访问，不能直接访
#### (1)通过defineProperty来实现
```
obj={
  name:yuxiaoliang,
  getName:function(){
    return this.name
  }
}
object.defineProperty(obj,"name",{
   //不可枚举不可配置
});
```

#### (2)通过函数的创建形式
```
function product(){
    var name='yuxiaoliang';
    this.getName=function(){
      return name;
    }
}
var obj=new product();
```

### 27. ==和===、以及Object.is的区别
#### (1) ==
主要存在：强制转换成number,null==undefined

```
" "==0  //true
"0"==0  //true
" " !="0" //true
123=="123" //true
null==undefined //true
```

#### (2)Object.js
主要的区别就是+0！=-0 而NaN==NaN
(相对比===和==的改进)

### 28.setTimeout、setInterval和requestAnimationFrame之间的区别
这里有一篇文章讲的是requestAnimationFrame：http://www.cnblogs.com/xiaohuochai/p/5777186.html
与setTimeout和setInterval不同，requestAnimationFrame不需要设置时间间隔，
大多数电脑显示器的刷新频率是60Hz，大概相当于每秒钟重绘60次。大多数浏览器都会对重绘操作加以限制，不超过显示器的重绘频率，因为即使超过那个频率用户体验也不会有提升。因此，最平滑动画的最佳循环间隔是1000ms/60，约等于16.6ms。

RAF采用的是系统时间间隔，不会因为前面的任务，不会影响RAF，但是如果前面的任务多的话，
会响应setTimeout和setInterval真正运行时的时间间隔。

特点：
（1）requestAnimationFrame会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率。

（2）在隐藏或不可见的元素中，requestAnimationFrame将不会进行重绘或回流，这当然就意味着更少的CPU、GPU和内存使用量

（3）requestAnimationFrame是由浏览器专门为动画提供的API，在运行时浏览器会自动优化方法的调用，并且如果页面不是激活状态下的话，动画会自动暂停，有效节省了CPU开销。
