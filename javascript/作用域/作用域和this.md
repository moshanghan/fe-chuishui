## this

我们几乎每天都在和 this 打交道，先不去管他的概念，看一个小栗子，预热一下

### this 初探

⚠️ 约定：  
我们之前的一篇文章提到过，使用 let 和 const 声明的变量没有挂载到全局变量下，所以在全局下声明的变量我们使用 var

```javascript
var heroName = "黄蓉";
function hero() {
  const heroName = "黄药师";
  console.log(this); //window
  console.log(this.heroName); //=>黄蓉
}

hero();
```

我们执行 hero(),通过打印我们发现

this 指向的是 window 全局，所以 this.heroName，应当在全局下查找 heroName，所以输出=>黄蓉  
当全局下没有 heroName 呢？this 指向的是全局，全局没有就是没有，那就是 undefined

```javascript
function hero() {
  const heroName = "黄药师";
  console.log(this); //window
  console.log(this.heroName); //=>undefined
}

hero();
```

我们继续看一个栗子

```javascript
var heroName = "黄蓉";
function hero() {
  const heroName = "黄药师";
  console.log(this); //oHero
  console.log(this.heroName); //=>欧阳锋
}
const oHero = {
  heroName: "欧阳锋",
  hero
};
oHero.hero();
```

提示：在声明 oHero 中的 hero 中，使用了 ES6 标准，当 key 和 value 同名是，可以像上面这样简写  
上面的栗子中，调用 hero()的环境变了 ，根据输出我们发现，this 的指向变了  
this 的指向是 oHero 对象，所以 this.heroName=>欧阳锋,同样的当 oHero 下没有 heroName，毫无悬念，就会输出=>undefined  
如果前面两个小栗子搞懂了，我们就继续  
一个栗子

```javascript
var heroName = "黄蓉";

function hero() {
  console.log(this.heroName);
}
const oHero1 = {
  heroName: "郭靖",
  hero
};

hero(); //=>黄蓉
oHero1.hero(); //=>郭靖
```

现在我们尝试着给 this 下一个定义：

this 指的是函数运行时所在的环境，如果一个函数内部有 this，this 就会指向一个对象，指向哪个对象呢？取决于这个函数的执行环境。  
补充（如果在全局下调用这个函数，则 this 指向全局，如果在某个对象下调用 this，则 this 指向这个对象）  
能看到这里，说明你很聪明，哇，this 这么 easy 吗？

### this 并没有那么简单

再看一个栗子

```javascript
var heroName = "黄蓉";
function hero1() {
  const heroName = "黄药师";
  this.hero2();
}
function hero2() {
  console.log(this.heroName);
}
hero1();
```

先不关心输出
我们在 hero2 中的 console.log 前加一个 debugger，分别看下两个 this 的指向  
[![](http://www.qdtalk.com/wp-content/uploads/2019/01/this1.png)](http://www.qdtalk.com/wp-content/uploads/2019/01/this1.png)

[![](http://www.qdtalk.com/wp-content/uploads/2019/01/this2.png)](http://www.qdtalk.com/wp-content/uploads/2019/01/this2.png)

可以发现。两个 this 均指向 window  
在脑中跑一遍这段代码，希望你还清醒  
我们一起捋一下：  
全局下执行 hero1()  
在 hero1 中，this 指向的是全局=>window, 全局下存在 hero2()，  
在 hero2 中，this 指向哪里？取决于是哪里调用的 hero2()，是在 hero1()中调用的 hero2(),  
所以 hero2 中的 this 指向 hero1？输出=>黄药师?  
但是我们通过 debugger 得出，hero2 中的 this 指向的是 window，所以输出必然是=>黄蓉  
为什么呢？  
我们再看一个栗子

```javascript
function hero() {
  console.log(this.heroName);
}
var HERO1 = {
  heroName: "黄蓉",
  hero
};
var HERO2 = {
  heroName: "郭靖",
  HERO1
};
var HERO3 = {
  heroName: "郭靖",
  HERO2
};
HERO3.HERO2.HERO1.hero(); //=>黄蓉
```

我相信不会有人写出这样的代码，除了我！  
这依然是个调用链  
我们在输出前打一个断点  
[![](http://www.qdtalk.com/wp-content/uploads/2019/01/this3.png)](http://www.qdtalk.com/wp-content/uploads/2019/01/this3.png)
this 指向了谁？不是最初的调用方 HERO3，而是上一次，或者所最后一次=>HERO1

### this 似乎也不难

出现上面这种情况，是由函数调用方式决定的

1. 作为一个函数进行的调用
2. 作为一个对象的方法进行的调用  
   ~~3. 作为构造器进行的调用~~  
   ~~4. 通过 apply()、call()函数进行的调用~~

后面两个暂不做讨论  
作为一个函数调用 this 指向 window，作为一个对象的方法调用，this 指向当前调用的对象  
回顾之前的栗子

```javascript
var heroName = "黄蓉";

function hero() {
  console.log(this.heroName);
}
const oHero1 = {
  heroName: "郭靖",
  hero
};

hero(); //=>黄蓉
oHero1.hero(); //=>郭靖
```

hero(),是作为一个函数进行调用的，所以 this 指向 window  
oHero1.hero()， 是作为一个函数的方法调用的，所以 this 指向 oHero1 对象  
继续看一个栗子

```javascript
function hero1() {
  this.hero2();
}
function hero2() {
  console.log(this.heroName); //Uncaught TypeError: this.hero2 is not a function
}
var HERO1 = {
  heroName: "黄蓉",
  hero1
};
var HERO2 = {
  heroName: "郭靖",
  HERO1
};
HERO2.HERO1.hero1();
```

报错了  
HERO1 对象下调用的 hero1(),所以 hero1()中 this 指向的是 HERO1，但是 HERO1 中不存在方法 hero2()，所以报错  
稍作修改

```javascript
function hero1() {
  HERO3.hero2();
}
function hero2() {
  console.log(this.heroName); //=>郭靖
}
var HERO1 = {
  heroName: "黄蓉",
  hero1
};
var HERO2 = {
  heroName: "郭靖",
  HERO1
};
var HERO3 = {
  heroName: "郭靖",
  hero2
};
HERO2.HERO1.hero1();
```

我们似乎总结出来一点小窍门，我们从最后的输出反推执行顺序  
是谁调用的 hero2(),是 HERO3，所以 hero2,中的 this 指向 HERO3，所以输出是=>郭靖

函数的调用方式分四种情况：

1. 作为一个函数进行的调用
2. 作为一个对象的方法进行的调用
3. 作为构造器进行的调用
4. 通过 apply()、call()函数进行的调用

上次说了前两种情况，其实（1）是（2）的一种特殊情况，  
即当作为一个函数调用的时候，就是作为 window 对象的一个方法进行调用  
一个小栗子进行简单回顾

```javascript
var heroName = "郭靖";

function hero1() {
  console.log(this.heroName);
}
var HERO1 = {
  heroName: "黄蓉",
  hero1: hero1
};
HERO1.hero1(); //=>黄蓉
hero1(); //=>郭靖
```

都是执行 hero1()方法，同样都是输出 this.heroName，却得到了不同的结果  
**this 的指向是在函数执行的时候定义的，而不是在函数创建时定义的**  
基于上面的栗子，我们再看一个栗子

```javascript
var heroObj = {
  heroName: "郭靖",
  heroFoo: {
    heroName: "黄蓉",
    hero: function() {
      console.log(this.heroName);
    }
  }
};

heroObj.heroFoo.hero(); //=>黄蓉

var h = heroObj.heroFoo.hero;
h(); //=>undefined
```

为了直观表达我们做一下改动

```javascript
//部分代码省略
window.heroObj.heroFoo.hero(); //=>黄蓉

var h = heroObj.heroFoo.hero;
window.h(); //=>undefined
```

输出的结果是相同的，我要表达的内容也很就很直观明显了，  
这些对象和方法，最终都会挂载到 window 对象下，我们看这一句

```javascript
window.heroObj.heroFoo.hero(); //=>黄蓉
```

hero()中的 this 是指向调用它的对象，那是哪个对象调用的 hero()呢？  
这是个问题  
这是个问题？  
这不是个问题

**this 指向的是最后调用它的对象**  
上面的栗子

```javascript
window.heroObj.heroFoo.hero(); //=>黄蓉
```

最后调用 hero()的是 heroFoo，所以 hero()中 this 指向了 heroFoo 对象

```javascript
var h = heroObj.heroFoo.hero;
window.h(); //=>undefined
```

最后调用 h()的是 window 对象，所以 hero()中 this 指向了 window 对象

综上
**this 的指向是在函数执行的时候定义的，而不是在函数创建时定义的，this 指向的是最后调用它的对象**

## this 继续聊

下面讨论剩下的两种情况

### 函数作为构造器进行调用

之前在介绍面向对象的时候，[谈一谈 javascript 面向对象](https://www.qdtalk.com/2018/12/11/%E8%B0%88%E4%B8%80%E8%B0%88javascript%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1/ "谈一谈javascript面向对象")，讨论过，使用构造函数来创建对象

```javascript
function Hero(name, nickname, skill) {
  this.name = name;
  this.nickname = nickname;
  this.doSth = function() {
    return skill;
  };
}
const hero = new Hero("黄药师", "东邪", "碧海潮生曲");
console.log(hero);
```

我们还说到，使用自定义构造器构造一个对象需要四步

1. 创建一个新对象

```javascript
const hero = new Hero("黄药师", "东邪", "碧海潮生曲");
```

2. 将构造函数的作用域赋给新对象,设置原型链（因此 this 就指向了这个新对象 hero）

```javascript
hero.__proto__ = Hero.prototype;
```

3. 执行构造函数 Hero 中的代码（为这个新对象 hero 添加属性和方法）
4. 返回新对象 hero

说 this 指向 hero，Hero 内所有针对 this 的操作，都会发生在 hero 上面

```javascript
console.log(hero.name); //=>黄药师
console.log(hero.nickname); //=>东邪
console.log(hero.doSth()); //=>碧海潮生曲
```

构造函数是不需要 return 的，  
return 在普通函数中也不是必须存在的，我们知道，在普通函数中，如果没有手动 return ，会默认 return undefined  
但是如果在构造函数中使用了 return，会存在一些坑  
我们一起来填坑  
我们把上面的栗子进行简化

```javascript
function Hero(name) {
  this.heroName = name;
  return 123;
}
const hero = new Hero("郭靖");
console.log(hero.heroName); //=>郭靖
```

```javascript
function Hero(name) {
  this.heroName = name;
  return null;
}
const hero = new Hero("郭靖");
console.log(hero.heroName); //=>郭靖
```

```javascript
function Hero(name) {
  this.heroName = name;
  return undefined;
}
const hero = new Hero("郭靖");
console.log(hero.heroName); //=>郭靖
```

不再进行一一列举，上面 return 的都是基本数据类型  
继续看  
数组类型

```javascript
function Hero(name) {
  this.heroName = name;
  return [];
}
const hero = new Hero("郭靖");
console.log(hero.heroName); //=>undefined
```

object 类型

```javascript
function Hero(name) {
  this.heroName = name;
  return {
    heroName: "黄蓉"
  };
}
const hero = new Hero("郭靖");
console.log(hero.heroName); //=>黄蓉
```

funciton 类型

```javascript
function Hero(name) {
  this.heroName = name;
  return function() {};
}
const hero = new Hero("郭靖");
console.log(hero.heroName); //=>undefined
```

funciton 升级

```javascript
function Hero(name) {
  this.heroName = name;
  return (function() {
    return { heroName: "黄蓉" };
  })();
}
const hero = new Hero("郭靖");
console.log(hero.heroName); //=>黄蓉
```

总结一下  
构造函数会改版 this 的指向，指向通过 new 实例化出来的对象  
构造函数中不需要 return，当存在 return 时，一下几点需要注意

1. 当 return 的是基本数据类型时，返回不变
2. 当 return 的没有返回时，默认返回 undefined，返回不变
3. 当 return 的是引用类型时，返回的是这引用类型

### 函数通过 apply()、call()进行调用

之前在讲面向对象的时候，对 call 进行过讨论，[javascript 面向对象之一篇文章搞定 call()方法](https://www.qdtalk.com/2018/12/13/javascript-call/ "javascript 面向对象之一篇文章搞定call()方法")
它们两个也是用来改变 this 的指向的，  
为什么要改变 this 的指向呢？  
看一个栗子

```javascript
const hero1 = {
  name: "欧阳锋",
  doSth: function(skill) {
    console.log(`${this.name}学习${skill}`);
  }
};

const hero2 = {
  name: "洪七公"
};
hero1.doSth("九阴真经"); //=>欧阳锋学习九阴真经
```

执行了 hero1 对象下面的 doSth 方法，并传入参数“九阴真经”，最后输出=>欧阳锋学习九阴真经  
结合上面的介绍讲解，我们知道，doSth 中的 this 是指向的 hero1，这个没问题吧

好了，现在 hero2 下面有一个“洪七公”，“洪七公”也想调用 hero1 下面的 doSth 方法，怎么办呢？

```javascript
const hero1 = {
  name: "欧阳锋",
  doSth: function(skill) {
    console.log(`${this.name}学习${skill}`);
  }
};

const hero2 = {
  name: "洪七公"
};
hero1.doSth.call(hero2, "降龙十八掌"); //=>洪七公学习降龙十八掌
```

doSth 是由 hero1 调用的，默认情况下 doSth 中的 this 指向的是 hero1，但是使用了 call，所以，this 的指向变了，指向了 call 方法中的第一个参数 hero2，
apply 呢？apply 和 call 很是类似

```javascript
const hero1 = {
  name: "欧阳锋",
  doSth: function(skill, favourite) {
    console.log(`${this.name}学习${skill}`);
    console.log(`${this.name}喜欢${favourite}`);
  }
};

const hero2 = {
  name: "洪七公"
};
hero1.doSth.apply(hero2, ["降龙十八掌", "吃鸡"]);
hero1.doSth.call(hero2, "降龙十八掌", "吃鸡");
```

这两种写法等效,只是传入的参数格式略有不同

```javascript
hero1.doSth.apply(hero2, ["降龙十八掌", "吃鸡"]);
hero1.doSth.call(hero2, "降龙十八掌", "吃鸡");
```

### 补充 bind

bind 也可以改变 this 的指向，在用法上和 call 和 apply 略有不同，bind 的使用更加灵活

```javascript
const hero1 = {
  name: "欧阳锋",
  doSth: function(skill, favourite) {
    console.log(`${this.name}学习${skill}`);
    console.log(`${this.name}喜欢${favourite}`);
  }
};

const hero2 = {
  name: "洪七公"
};
const foo = hero1.doSth.bind(hero2, "降龙十八掌", "吃鸡");
foo();
```

之前说的 call 和 apply 都是立即执行，而 bind 不会立即执行，需要手动执行，所以 bind 的使用更加灵活  
不止于此  
上面的 foo 方法在调用的时候可以额外的传入参数

```javascript
const hero1 = {
  name: "欧阳锋",
  doSth: function(skill, favourite, q, n, b) {
    console.log(`${this.name}学习${skill}`); //=>洪七公学习降龙十八掌
    console.log(`${this.name}喜欢${favourite}`); //=>洪七公喜欢吃鸡
    console.log(q, n, b); //=>1 2 3
  }
};

const hero2 = {
  name: "洪七公"
};
const foo = hero1.doSth.bind(hero2, "降龙十八掌", "吃鸡");
foo(1, 2, 3);
```

今天的主要内容就是在函数的调用中，this 的指向会被改变，我们要留意里面的坑，

当然我们也可以通过一些手段改变 this 的指向。
