本文转载自[forEach、for in 、 for of三者的区别](https://blog.csdn.net/one_girl/article/details/80192899)  
原文作者：  ygman  

### for循环
其实除了这三种方法以外还有一种最原始的遍历，自Javascript诞生起就一直用的 就是for循环，它用来遍历数组
```javascript
var arr = [1,2,3,4]
for(var i = 0 ; i< arr.length ; i++){
    console.log(arr[i])
}
```
### forEach
从ES5开始 Javascript内置了forEach方法 用来遍历数组
```javascript
let arr = ['a', 'b', 'c', 'd']
arr.forEach(function (val, idx, arr) {
    console.log(val + ', index = ' + idx) // val是当前元素，index当前元素索引，arr数组
    console.log(arr)
})
```
输出结果：
```javascript
a, index = 0
(4) ["a", "b", "c", "d"]
b, index = 1
(4) ["a", "b", "c", "d"]
c, index = 2
(4) ["a", "b", "c", "d"]
d, index = 3
(4) ["a", "b", "c", "d"]
```
**写法简单了很多，但是也存在一个局限 就是你不能中断循环(使用break语句或使用return语句）。**
### for…in
for-in循环实际是为循环”enumerable“对象而设计的
```javascript
let obj = {a: '1', b: '2', c: '3', d: '4'}
for (let o in obj) {
    console.log(o)    //遍历的实际上是对象的属性名称 a,b,c,d
    console.log(obj[o])  //这个才是属性对应的值1，2，3，4
}
```
for - in 也可用来循环数组，但一般并不推荐

### for…of
它是ES6中新增加的语法 
#### 循环一个数组
```javascript
let arr = ['China', 'America', 'Korea']
for (let o of arr) {
    console.log(o) //China, America, Korea
}
```
但是它**并不能循环一个普通对象**
```javascript
let obj = {a: '1', b: '2', c: '3', d: '4'}
for (let o of obj) {
    console.log(o)   //Uncaught TypeError: obj[Symbol.iterator] is not a function
}
```
如果我们按对象所拥有的属性值进行循环，可使用内置的Object.values()方法
```javascript
let obj = {a: '1', b: '2', c: '3', d: '4'}
for (let o of Object.values(obj)) {
    console.log(o) // 1,2,3,4
}
```
循环一个字符串
```javascript
let str = 'love'
for (let o of str) {
    console.log(o) // l,o,v,e
}
```
循环一个Map
```javascript
let iterable = new Map([["a", 1], ["b", 2], ["c", 3]]);

for (let [key, value] of iterable) {
  console.log(value);
}
// 1
// 2
// 3

for (let entry of iterable) {
  console.log(entry);
}
// [a, 1]
// [b, 2]
// [c, 3]
```
循环一个Set
```javascript
let iterable = new Set([1, 1, 2, 2, 3, 3]);

for (let value of iterable) {
  console.log(value);
}
// 1
// 2
// 3
```
循环一个类型化数组
```javascript
let iterable = new Uint8Array([0x00, 0xff]);

for (let value of iterable) {
  console.log(value);
}
// 0
// 255
```