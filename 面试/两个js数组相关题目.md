本文转载自[两个js数组相关题目](https://github.com/jirengu/frontend-interview/issues/40)  

来自xx公司面试题  
1.如何随机打乱一个js数组（使用原生代码）  
2.现在有一个二维数组，数组长度和子数组长度均不可知，问：从每一个子数组里面取一个数成一个数组，要求组合不能重复，问一共有多少个组合，请设计一个函数把所有可能出现的组合以数组的形式输出。  
（由于解题时间花费比较长，代码也有一点小失误，很遗憾没有通过，表示自己的代码很捞，希望看到这两个问题的的同学能想出比较实用且高大上的想法一起讨论）  


如何随机打乱一个js数组
```javascript
/* 
 * Returns a new array after disordered.
 * 
 * @param {array} array - The original array needs to be disordered.
 * @returns {array} newArray - A new array after disordered.
 *
 * @author Caijialinxx
 */
function disorderArray(array) {
  let newArray = []
  let usedIndexs = []    // Put the index of item which had been pushed into newArray. It helps to avoid adding repeated item.
  let validCount = 0     // The count of valid pushing into newArray.
  top:
  while(validCount < array.length) {
    let index = Math.floor(Math.random()*array.length)
    if(usedIndexs.indexOf(index) > -1) {
      continue top
    } else {
      usedIndexs.push(index)
    }
    validCount++
    newArray.push(array[index])
  }
  return newArray
}
```
现在有一个二维数组，数组长度和子数组长度均不可知，问：从每一个子数组里面取一个数成一个数组，要求组合不能重复，问一共有多少个组合，请设计一个函数把所有可能出现的组合以数组的形式输出。

这个方法的适用范围为：二维数组中子数组的每个元素都是数字、重新组合不考虑前后顺序。  
如果子数组中可以有字符串，那么 removeSame() 可能不适用。因为若其中一个子数组为 [1, '1'] ，hash[1] 和 hash['1'] 会被认为相同。  
```javascript
function reCombine(array) {
  if (array.length > 1) {
    let recombinedArray = []
    for(let i = 0; i < array[0].length; i++) {
      for(let j = 0; j < array[1].length; j++) {
        recombinedArray.push([].concat(array[0][i], array[1][j]))
      }
    }
    recombinedArray = removeSame(recombinedArray)
    array.splice(0, 2, recombinedArray)
    reCombine(array)
  }
  return array[0]
}

function removeSame(array) {
  let newArray = [], hash = {}
  for (let i = 0; i < array.length; i++) {
    if (!hash[array[i]]) {
      hash[array[i]] = true
      newArray.push(array[i])
    }
  }
  return newArray
}
```


随机打乱数组:
```javascript
function shuffle(arr) {
  arr.sort(() => {
    return Math.random() - 0.5
  })
}
```
随机打乱数组:
```javascript
function obfs(arr) {
  return arr.slice().reduce(acc => [...acc, ...arr.splice(parseInt(Math.random() * arr.length, 10), 1)], []);
}

console.log(obfs([1, 2, 3, 4, 5]));
```