在上一篇文章中[JavaScript中AMD和ES6模块的导入导出对比](https://www.qdtalk.com/2019/03/24/require%EF%BC%8Cexports%EF%BC%8Cmodule-exports%E5%92%8Ces6%E4%B8%AD%E7%9A%84import%EF%BC%8Cexport%EF%BC%8Cexport-default/ "JavaScript中AMD和ES6模块的导入导出对比")，偏向于理论层面，还有一些同学在微信群里或是私下里针对一些问题进行了沟通，所以有了这一篇文章，对js的导入导出进行总结和实践
## 当直接给 module.exports时，exports会失效
这个问题其实已经和导入导出没什么关系了，
我们看一个知乎上的问题（详细地址阅读原文可以查看）
我们以此为突破点
js 数组赋值问题 ：值传递还是引用？
```javascript
var a = [1,2,3];
var b = a;
a = [4,5,6];
console.log(b);  //=>[1,2,3]
```
继续看
```javascript
var a = [1,2,3];
var b = a;
a.pop();
console.log(b);  //=>[1,2]
```
为什么会出现这种情况？

> 数组和对象的赋值操作都是引用传递

看下这个（留意注释部分）
```javascript
var a = [1,2,3];// a指向了数组 [1,2,3];
var b = a;//b 指向 a 所指向的数组[1,2,3];
a = [4,5,6];//a 指向了新的数组 [4,5,6],(a的指向发生了变化，改变的是a引用本身，没有改变数组对象,所以b没有变)
console.log(b);  //b没有改变，还是指向数组 [1,2,3];
```
再看下这个留意注释部分）
```javascript
var a = [1,2,3];// a指向了数组 [1,2,3];
var b = a;//b 指向 a 所指向的数组[1,2,3];
a.pop();// a 指向的数组实例发生了 pop 操作
console.log(b);  //=>a和b都是指向同一个数组，a变量，所以b也变量，最后输出=>[1,2]
```
看一张图片，很形象的描述
[![](https://www.qdtalk.com/wp-content/uploads/2019/03/demo.jpg)](https://www.qdtalk.com/wp-content/uploads/2019/03/demo.jpg)
数组如此，对象也是大同小异
看一个群友@ZSing提供的demo
```javascript
var test = {
  "name": "zhangshuo"
}
var demo = test;

demo.name = "want you"
//你认为test是什么？
console.log(test)//=>{ name: 'want you' }
```
下面通过注释解释一下（如出一辙）
```javascript
var test = {  "name": "zhangshuo"}//test指向了一个对象 {  "name": "zhangshuo"}
var demo = test;//demo 指向 test 所指向的对象 {  "name": "zhangshuo"}
demo.name = "want you"//对象的属性发生了改变 {  "name": "want you"}
//你认为test是什么？
console.log(test)//=>{ name: 'want you' }
```
test和demo指向了同一个对象，一个变了，就都变了
同样的，我们对上面的demo做一下改造
```javascript
var test = {
  "name": "zhangshuo"
}
var demo = test;
  test={
    "name": "更改了这个name"
  }
demo.name = "want you"
//你认为test是什么？
console.log(test)//=>{ name: '更改了这个name' }
```
~~还需要对此进行赘述吗？~~
还是通过注释对此进行解释说明
```javascript
var test = {  "name": "zhangshuo"}//test指向了一个对象 {  "name": "zhangshuo"}
var demo = test;//demo 指向 test 所指向的对象 {  "name": "zhangshuo"}
  test={ "name": "更改了这个name" }//test的指向发生了变化，指向了一个新对象{ "name": "更改了这个name" }
demo.name = "want you"//demo的指向没有变，改变了原对象的属性 {  "name": "want you"}
//你认为test是什么？
console.log(test)//=>{ name: '更改了这个name' }
```
我相信，上面的两个栗子你已经看懂了，即将进入正题
先来一个过渡
再看一个栗子，用来模拟exports和 module.exports的关联关系
```javascript
  let md = {exps:{}}//md指向一个对象 {exps:{}}
  let exps = md.exps//exps指向了md.exps所指向的对象 ,这个空对象{}
  md.exps = {a: 1,  b: 2}//md.exps指向了一个新对象 {a: 1,  b: 2}
  exps.c=3//exps,属性赋值 {c: 3}
  console.log(md.exps); //新对象{ a: 1, b: 2 }
```
上面栗子中的md就是module，md.exps就是module.exports，exps就是exports
在每一个模块的头部都有一行这样的命令
```javascript
var exports = module.exports;
```
当直接给module.exports赋值时（module.exports={.....}），module.exports就指向了一个新对象,exports会失效
## 直接给exports赋值会切断exports和 module.exports的关联关系
还是这样的一个前提
```javascript
var exports = module.exports;
```
exports是来自于module，exports指向 module.exports所指向的对象
当直接给exports赋值，即
```javascript
 exports = {a:1}
```
exports指向了一个新对象，不再是 module.exports所指向的对象，所以不要给 exports 直接赋值（ exports =。。。）
## 实践=>导出
### exports
exports的output.js
```javascript
exports.str='string字符串'//导出字符串
exports.bool=true//导出布尔
exports.num=123//导出number
exports.foo=(r)=>{//导出函数
  console.log(`导出函数为：${r}`);
}
exports.arr=[1,2,3]//导出数组
exports.obj={ a:1, b:2}//导出对象
```
input.js
```javascript
  const iptObj= require('./output.js')
  console.log(iptObj.str);//=>string字符串
  console.log(iptObj.bool);//=>true
  console.log(iptObj.num);//=>123
  console.log(iptObj.arr);//=>[ 1, 2, 3 ]
  console.log(iptObj.obj);//=>{ a: 1, b: 2 }
  iptObj.foo('参数')//=>导出函数为：参数
```
### module.exports
module.exports的output.js
```javascript
module.exports={
  str:'string字符串',
  bool:true,
  num:123,
  foo:(r)=>{
    console.log(`导出函数为：${r}`);
  },
  arr:[1,2,3],
  obj:{ a:1, b:2}
}
```
input.js
```javascript
  const iptObj= require('./output.js')
  console.log(iptObj.str);//=>string字符串
  console.log(iptObj.bool);//=>true
  console.log(iptObj.num);//=>123
  console.log(iptObj.arr);//=>[ 1, 2, 3 ]
  console.log(iptObj.obj);//=>{ a: 1, b: 2 }
  iptObj.foo('参数')//=>导出函数为：参数
```
module.exports的output.js同时支持如下写法
```javascript
module.exports.str='string字符串'
module.exports.bool=true
module.exports.num=123
module.exports.foo=(r)=>{
  console.log(`导出函数为：${r}`);
}
module.exports.arr=[1,2,3]
module.exports.obj={ a:1, b:2}
```
input.js不变
### export
export的output.js
```javascript
export const srt = 'string字符串'
export const bool = true
export const num = 123
export const arr = [1, 2, 3]
export const obj = { a: 1, b: 2}
export function foo(r) {
  console.log(`导出函数为：${r}`);
}
```
input.js
```javascript
import {str,arr,obj,bool,num,foo} from './output'
console.log(str)
console.log(arr)
console.log(obj)
console.log(bool)
console.log(num)
foo('参数')
```
export的output.js同时支持如下写法
```javascript
const str = 'string字符串' 
const bool = true
const num = 123
const arr = [1, 2, 3]
const obj = { a: 1, b: 2}
function foo(r) {
  console.log(`导出函数为：${r}`);
}
export {
  str,bool,num,arr,obj,foo
}
```
input.js 导入支持重命名
```javascript
import {str as STR,arr,obj,bool,num,foo as FOO} from './output'
console.log(STR)
console.log(arr)
console.log(obj)
console.log(bool)
console.log(num)
FOO('参数')
```
继续重命名
```javascript
import * as newName from './output'
console.log(newName.str)
console.log(newName.arr)
console.log(newName.obj)
console.log(newName.bool)
console.log(newName.num)
newName.foo('参数')
```
### export default
export default的output.js
```javascript
export default {
  str: 'string字符串',
  bool: true,
  num: 123,
  foo: (r) => {
    console.log(`导出函数为：${r}`);
  },
  arr: [1, 2, 3],
  obj: { a: 1, b: 2 }
}
```
input.js
```javascript
import defaultObj from './output'
console.log(defaultObj.str)
console.log(defaultObj.arr)
console.log(defaultObj.bool)
console.log(defaultObj.num)
console.log(defaultObj.obj)
defaultObj.foo('ef')//=>导出函数为：ef
```
export default的output.js同时支持如下写法
```javascript
const str = 'string字符串'
const bool = true
const num = 123
const arr = [1, 2, 3]
const obj = {a: 1, b: 2}
function foo(r) {
  console.log(`导出函数为：${r}`);
}
export default {
  str,
  bool,
  num,
  arr,
  obj,
  foo
}
```
input.js不变
## 总结
这篇文章是对上一篇文章的总结和实践

-  当直接给 module.exports时，exports会失效
-  直接给exports赋值会切断exports和 module.exports的关联关系
-  export，export default，exports，module.exports具体的使用方法实例

参考链接
[js 数组赋值问题 ：值传递还是引用？](https://www.zhihu.com/question/26042362 "js 数组赋值问题 ：值传递还是引用？")