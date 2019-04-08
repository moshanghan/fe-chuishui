本文转载自[js数组去重的10种方法](https://blog.csdn.net/weixin_42412046/article/details/81459294)    
原文作者： sun6sian  
(原文错误较多，不建议查看，以下代码本人都逐一跑了一遍，可以正常运行)  

### 方法 1: 思路：定义一个新数组，并存放原数组的第一个元素，然后将元素组一一和新数组的元素对比，若不同则存放在新数组中。
```javascript
    function unique(arr) {
      let newArr = [arr[0]];
      for (let i = 1; i < arr.length; i++) {
        let repeat = false;
        for (let j = 0; j < newArr.length; j++) {
          if (arr[i] === newArr[j]) {
            repeat = true;
            break;
          } else {

          }
        }
        if (!repeat) {
          newArr.push(arr[i]);
        }
      }
      return newArr;
    }

    console.log(unique([1, 1, 2, 3, 5, 3, 1, 5, 6, 7, 4]));
    // 结果是[1, 2, 3, 5, 6, 7, 4]
```
### 方法 2: 思路：先将原数组排序，在与相邻的进行比较，如果不同则存入新数组。
```javascript
    function unique2(arr) {
      var formArr = arr.sort()
      var newArr = [formArr[0]]
      for (let i = 1; i < formArr.length; i++) {
        if (formArr[i] !== formArr[i - 1]) {
          newArr.push(formArr[i])
        }
      }
      return newArr
    }
    console.log(unique2([1, 1, 2, 3, 5, 3, 1, 5, 6, 7, 4]));
    // 结果是[1, 2, 3,  4,5, 6, 7]
```
### 方法 3: 利用对象属性存在的特性，如果没有该属性则存入新数组。
```javascript
    function unique3(arr) {
      var obj = {}
      var newArr = []
      for (let i = 0; i < arr.length; i++) {
        if (!obj[arr[i]]) {
          obj[arr[i]] = 1
          newArr.push(arr[i])
        }
      }
      return newArr
    }
    console.log(unique3([1, 1, 2, 3, 5, 3, 1, 5, 6, 7, 4]));
    // 结果是[1, 2, 3, 5, 6, 7, 4]
```
### 方法 4: 利用数组的indexOf下标属性来查询。
```javascript
    function unique4(arr) {
      var newArr = []
      for (var i = 0; i < arr.length; i++) {
        if (newArr.indexOf(arr[i]) === -1) {
          newArr.push(arr[i])
        }
      }
      return newArr
    }
    console.log(unique4([1, 1, 2, 3, 5, 3, 1, 5, 6, 7, 4]));
    // 结果是[1, 2, 3, 5, 6, 7, 4]
```
### 方法 5: 利用数组原型对象上的includes方法。
```javascript
    function unique5(arr) {
      var newArr = []
      for (var i = 0; i < arr.length; i++) {
        if (!newArr.includes(arr[i])) {
          newArr.push(arr[i])
        }
      }
      return newArr
    }
    console.log(unique5([1, 1, 2, 3, 5, 3, 1, 5, 6, 7, 4]));
    // 结果是[1, 2, 3, 5, 6, 7, 4]
```
### 方法 6: 利用数组原型对象上的 filter 和 includes方法。
```javascript
    function unique6(arr) {
      var newArr = []
      newArr = arr.filter(function (item) {
        return newArr.includes(item) ? '' : newArr.push(item)
      })
      return newArr
    }
    console.log(unique6([1, 1, 2, 3, 5, 3, 1, 5, 6, 7, 4]));
    // 结果是[1, 2, 3, 5, 6, 7, 4]
```
### 方法 7: 利用数组原型对象上的 forEach 和 includes方法。
```javascript
    function unique7(arr) {
      var newArr = []
      arr.forEach(item => {
        return newArr.includes(item) ? '' : newArr.push(item)
      });
      return newArr
    }
    console.log(unique7([1, 1, 2, 3, 5, 3, 1, 5, 6, 7, 4]));
    // 结果是[1, 2, 3, 5, 6, 7, 4]
```
### 方法 8: 利用数组原型对象上的 splice 方法。
```javascript
    function unique8(arr) {
      var i, j, len = arr.length;
      for (i = 0; i < len; i++) {
        for (j = i + 1; j < len; j++) {
          if (arr[i] == arr[j]) {
            arr.splice(j, 1);
            len--;
            j--;
          }
        }
      }
      return arr;
    }
    console.log(unique8([1, 1, 2, 3, 5, 3, 1, 5, 6, 7, 4]));
```
### 方法 9: 利用数组原型对象上的 lastIndexOf 方法。
```javascript
    function unique9(arr) {
      var res = [];
      for (var i = 0; i < arr.length; i++) {
        res.lastIndexOf(arr[i]) !== -1 ? '' : res.push(arr[i]);
      }
      return res;
    }
    console.log(unique9([1, 1, 2, 3, 5, 3, 1, 5, 6, 7, 4]));
    // 结果是[1, 2, 3, 5, 6, 7, 4]
```
### 方法 10: 利用 ES6的set 方法。
```javascript
    function unique10(arr) {
      //Set数据结构，它类似于数组，其成员的值都是唯一的
      return Array.from(new Set(arr)); // 利用Array.from将Set结构转换成数组
    }
    console.log(unique10([1, 1, 2, 3, 5, 3, 1, 5, 6, 7, 4]));
    // 结果是[1, 2, 3, 5, 6, 7, 4]
```
