创建对象的几种方式，  

- 工厂模式  
- 构造函数模式  
- 原型模式创建对象  

创建对象的过程就是javascript面向对象中封装的过程，今天我们开始讨论面向对象的继承，在开始讨论继承之前，有一个无法绕过的知识点  
### call()&apply()
#### call

>语法：call([thisObj[,arg1[, arg2[,   [,.argN]]]]])
定义：调用一个对象的一个方法，以另一个对象替换当前对象。  
说明：  
call 方法可以用来代替另一个对象调用一个方法。call 方法可将一个函数的对象上下文从初始的上下文改变为由 thisObj 指定的新对象。  
如果没有提供 thisObj 参数，那么 Global 对象被用作 thisObj。  

#### apply

>语法：apply([thisObj[,argArray]])
定义：应用某一对象的一个方法，用另一个对象替换当前对象。  
说明：  
如果 argArray 不是一个有效的数组或者不是 arguments 对象，那么将导致一个 TypeError。  
如果没有提供 argArray 和 thisObj 任何一个参数，那么 Global 对象将被用作 thisObj， 并且无法被传递任何参数。  

call和apply可以用来重新定义函数的执行环境，也就是this的指向； call 和 apply 都是为了改变某个函数运行时的 
context 即上下文而存在的 换句话说，就是为了改变函数体内部 this 的指向。因为 JavaScript 的函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。  

我们可以将call()和apply()看做是某个对象的方法，通过调用方法的形式来间接调用函数。这么说有些抽象  
call和apply的区别并不大，了解了一个，另一个自然就懂了，所以我在这里不重点讨论他们的区别，本篇文章的讨论，以call为主  
一个简单的🌰
```javascript
window.name = 'window-name';
document.name = 'document-name';

const obj = {
	name: 'obj-name'
};

function handleName() {
	console.log(this.name);
}
handleName.call(); //=>window-name(如果没有参数，默认参数是window)
handleName.call(window); //=>window-name
handleName.call(document); //=>document-name
handleName.call(this); //=>window-name
handleName.call(obj); //=>obj-name
```
当call()没有传递参数时，默认参数是window  
this的默认也是window  
所以在上面的例子中以下三个等效  
```javascript
handleName.call(); //=>window-name(如果没有参数，默认参数是window)
handleName.call(window); //=>window-name
handleName.call(this); //=>window-name
```
我们重点看下这个  
```javascript
handleName.call(obj); //=>obj-name
```
obj使handleName的this指向发生了变化，从原来的window变到了obj  
我们可以在函数handleName打印this，已观察this的变化  
```javascript
 function handleName() {
	 console.log(this);
 }
 handleName.call(this); 
 handleName.call(obj); 
```
看下输出
[![](http://www.qdtalk.com/wp-content/uploads/2018/12/2.png)](http://www.qdtalk.com/wp-content/uploads/2018/12/2.png)  
上面的例子中，函数handleName有一个方法call，call不是函数handleName通过原型继承得到的，  
```javascript
function handleName() {
	console.log(this.name);
}
console.log(handleName.prototype);
```
输出
[![](http://www.qdtalk.com/wp-content/uploads/2018/12/1.png)](http://www.qdtalk.com/wp-content/uploads/2018/12/1.png)  
输出里不存在call方法  
这是一个关于call最简单的一个🌰，如果理解了，我们继续向下看  
```javascript
const Hero1 = {
	name: "欧阳锋",
	doSth: function (skill) {
		console.log(`${this.name}学习${skill}`);
	}
};

const Hero2 = {
	name: "洪七公"
};

Hero1.doSth.call(Hero2, "降龙十八掌");
```
调用了Hero1下doSth的call方法，并传入Hero2，改变了doSth下this的指向，最终导致doSth下this.name由“欧阳锋”变成了“洪七公”  
我们稍微扩展一下，上面的代码做一下改动  
```javascript
function Hero1(name) {
	this.name = name
	this.doSth = function (skill) {
		console.log(`${this.name}学习${skill}`);
	}
}

function Hero2(name) {
	this.name = name
}
const oyf = new Hero1('欧阳锋')
const hqg = new Hero2('洪七公')
oyf.doSth('九阴真经')//=>欧阳锋学习九阴真经
oyf.doSth.call(hqg, '降龙十八掌')//=>洪七公学习降龙十八掌
```
梳理一下：  
Hero1和Hero2函数名大写了，没错这两个是自定义的构造函数，  
通过Hero1实例化一个函数ofy  
通过Hero2实例化一个函数hqg  
调用函数ofy的doSth方法，  
我们打印一下ofy函数  
```javascript
//以上代码省略
const oyf = new Hero1('欧阳锋')
const hqg = new Hero2('洪七公')
console.log(oyf);
```
看下输出
[![](http://www.qdtalk.com/wp-content/uploads/2018/12/3.png)](http://www.qdtalk.com/wp-content/uploads/2018/12/3.png)  
所以oyf.doSth('九阴真经')可以打印“欧阳锋学习九阴真经”  
看这个  
```javascript
oyf.doSth.call(hqg, '降龙十八掌')
```
doSth下this的指向变为了hqg，所以this.name是“洪七公”，**doSth中的形参就是“降龙十八掌”**，所以  
最终打印输出=>洪七公学习降龙十八掌  
### 一个call的经典案例
```javascript
function add(a, b) {
	console.log('add',this);
	console.log('a+b:',a+b);
}
function sub(a, b) {
	console.log('sub',this);
	console.log('a-b:',a-b);
}
add.call(sub, 3, 1);//=>4
```
我们先看下输出  
[![](http://www.qdtalk.com/wp-content/uploads/2018/12/4.png)](http://www.qdtalk.com/wp-content/uploads/2018/12/4.png)  
对以上的输出和结果感到意外吗？  
分析一下  
首先是调用的add的call方法，并传入了第一个参数sub，  
执行的还是add方法，但是add方法内this的指向变了，this指向了sub，所以会出现上面的输出  
为什么结果是4？  
调用的是add方法，add方法中的this指向的是sub（又说了一遍），然而add.call,还传入了第二个和第三个参数（3，1）  
他们分别是add方法中的a和b,a+b就是3+1=>4  

call 和 apply 最大的好处方便我们解耦，对象不需要和方法有任何的耦合性，能使我们写出更好的面相对象程序。  
大家如果看一些 js 框架底层的话会看到好多地方都有大量用到。  