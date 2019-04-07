### 转换布尔类型 变量前加 !!
```javascript
// 转换布尔类型 变量前加 !!
!! 'string'
// true

```
### 转换数字类型 变量前加 + 
```javascript
// 转换数字类型 变量前加 + 
+ '0.7'
// 0.7
```
### 常见数组去重 
```javascript
// 常见数组去重 
const array = [1, 2, 2, 3]
const unique = Array.from(new Set(array))
// unique [1, 2, 3]

// 简单的map
const nums = [1, 2, 3, 4, 5]
const twoXNums = nums.map(i => i * 2)
// twoXNums [2, 4, 6, 8, 10] nums不变

// 简单的filter
const nums = [1, 2, 3, 4, 5]
const evenNums = nums.filter(i => i % 2 === 0)
// evenNums [2, 4] nums不变

// 简单的reduce
const array1 = [1, 2, 3, 4]
const total = array1.reduce((pre, cur) => pre + cur)
// total 10

// 稍复杂一些的reduce
const scores = [
{name: 'zhang san', score: 60},
{name: 'li si', score: 80},
{name: 'wang wu', score: 90},
]
const totalScore = scores.reduce((total, cur) => 
total + cur.score, //reducer函数
0 // 初始分数为0
)
// totalScore 230

// 简单的find
const users = [
{id: 1, name: 'zhang san'},
{id: 2, name: 'li si'}
]
// 找出id为1的user 
const user = users.find(p => p.id === 1).name
// zhang san

// 简单的indexOf 
// 返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1
const data = ['aaa', 'bbb', 'ccc']
let resIndex = data.indexOf('bbb')
// resIndex 1

// 简单的includes
// 断一个数组是否包含一个指定的值 返回布尔值
const data = ['aaa', 'bbb', 'ccc']
let res = data.includes('bbb')
// res true
```
###  向数组中新增元素
```javascript
// 向数组中新增元素
// 比如常见的下拉加载
const data = ['aaa', 'bbb', 'ccc']
const newData = [...data, 'ddd']
// newData ['aaa', 'bbb', 'ccc', 'ddd]
```
###  为对象新增一组键值对
```javascript
// 为对象新增一组键值对
const user = {name: 'zhang san'}
const updatedUser = {...user, age: 23}
//updatedUser的值为：{name: 'zhang san', age: 23}
```
###  修改数组中满足条件的元素对象
```javascript
// 修改数组中满足条件的元素对象
const users = [
{id: 1, name: 'zhang san'},
{id: 2, name: 'li si'},
{id: 3, name: 'wang wu'}
]
```
###  给 id为1的user 修改属性
```javascript
// 给 id为1的user 修改属性
const updatedUsers = users.map(p => 
p.id !== 1 
? p 
: {...p, name: 'zhang buzheng'}
)
/**
* users 不变
* updatedUsers
* [{id: 1, name: "zhang buzheng"},
* {id: 2, name: "li si"},
* {id: 3, name: "wang wu"}
* ]
*/
```
###  删除目标对象的一组属性
```javascript
// 删除目标对象的一组属性
// eg 后端传了一大段属性 你只需要其中几个
const user = {id: 1, name: 'zhang san', age: '20'}
const newUserData = (({name, id}) => ({name, id}))(user)
// newUserData {name: "zhang san", id: 1}

// 传参对象转字符串
const params = {id: 1, name: 'zhang san', age: '20'}
const query = '?' + Object.keys(params).map(k => 
encodeURIComponent(k) + '=' + encodeURIComponent(params[k])
).join('&')
// encodeURIComponent将对特殊字符进行编码。
// query "?id=1&name=zhang%20san&age=20"

// 获取数组中对象的下标 返回第一个
const users = [
{id: 23, name: 'zhang san'},
{id: 5, name: 'li si'},
{id: 54, name: 'wang wu'}
]
const index = users.findIndex(obj => obj.id === 54)
// index 2
```
### 数组去重
```javascript
var arr = [1,2,3,3,4,5,5];
var set = new Set(arr);
var newArr = Array.from(set);
var arr = [{
    "name": "ZYTX",
    "age": "Y13xG_4wQnOWK1QwJLgg11d0pS4hewePU95UHtpMl3eE81uS74NC-6zu-Rtnw4Ix",
    "gender": "AAAAAA.doc"
}, {
    "name": "ZYTA",
    "age": "Y13xG_4wQnOWK1QwJLgg11d0pS4hewePU95UHtpMl3eE81uS74NC-6zu-Rtnw4Ix",
    "gender": "BBBBBB.doc"
}, {
    "name": "ZDTX",
    "age": "Y13xG_4wQnOWK1QwJLgg11d0pS4hewePU95UHtpMl3eE81uS74NC-6zu-Rtnw4Ix",
    "gender": "CCCCCC.doc"
}, {
    "name": "ZYTX",
    "age": "Y13xG_4wQnOWK1QwJLgg11d0pS4hewePU95UHtpMl3eE81uS74NC-6zu-Rtnw4Ix",
    "gender": "AAAAAA.doc"
}];
var hash = {};
arr = arr.reduce(function(item, next) {
    hash[next.name] ? '' : hash[next.name] = true && item.push(next);
    return item
}, [])
console.log(arr);
```