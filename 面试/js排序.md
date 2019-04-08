本文转载自[前端面试十大经典排序算法（动画演示）](https://blog.csdn.net/xiaoxiaojie12321/article/details/81380834)  
原文作者： 陌上xiao小  
（具体实现思路，参见原文链接）

## 0、算法概述
0.1 算法分类  

十种常见排序算法可以分为两大类：
> **非线性时间比较类排序**：通过比较来决定元素间的相对次序，由于其时间复杂度不能突破O(nlogn)，因此称为非线性时间比较类排序。  
>**线性时间非比较类排序**：不通过比较来决定元素间的相对次序，它可以突破基于比较排序的时间下界，以线性时间运行，因此称为线性时间非比较类排序。 

0.2 算法复杂度
<img src="../img/ms/sortTable.png" >
0.3 相关概念
>**稳定**：如果a原本在b前面，而a=b，排序之后a仍然在b的前面。  
>**不稳定**：如果a原本在b的前面，而a=b，排序之后 a 可能会出现在 b 的后面。  
>**时间复杂度**：对排序数据的总的操作次数。反映当n变化时，操作次数呈现什么规律。  
>**空间复杂度**：是指算法在计算机内执行时所需存储空间的度量，它也是数据规模n的函数。   


##  1、冒泡排序（Bubble Sort）
<img src="../img/ms/1.gif" >  
```javascript
    const ARR = [3, 5, 8, 0, 12, 87, 45]

    function bubbleSort(arr) {
      var len = arr.length;
      for (var i = 0; i < len - 1; i++) {
        for (var j = 0; j < len - 1 - i; j++) {
          if (arr[j] > arr[j + 1]) { // 相邻元素两两对比
            var temp = arr[j + 1]; // 元素交换
            arr[j + 1] = arr[j];
            arr[j] = temp;
          }
        }
      }
      return arr;
    }
    console.log(bubbleSort(ARR))//=>[0, 3, 5, 8, 12, 45, 87]
```
##  2、选择排序（Selection Sort）
<img src="../img/ms/2.gif" >  
```javascript
    const ARR = [3, 5, 8, 0, 12, 87, 45]

    function selectionSort(arr) {
      var len = arr.length;
      var minIndex, temp;
      for (var i = 0; i < len - 1; i++) {
        minIndex = i;
        for (var j = i + 1; j < len; j++) {
          if (arr[j] < arr[minIndex]) { // 寻找最小的数
            minIndex = j; // 将最小数的索引保存
          }
        }
        temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
      }
      return arr;
    }
    console.log(selectionSort(ARR))//=>[0, 3, 5, 8, 12, 45, 87]
```
##  3、插入排序（Insertion Sort）
<img src="../img/ms/3.gif" >  
```javascript
    const ARR = [3, 5, 8, 0, 12, 87, 45]

    function insertionSort(arr) {
      var len = arr.length;
      var preIndex, current;
      for (var i = 1; i < len; i++) {
        preIndex = i - 1;
        current = arr[i];
        while (preIndex >= 0 && arr[preIndex] > current) {
          arr[preIndex + 1] = arr[preIndex];
          preIndex--;
        }
        arr[preIndex + 1] = current;
      }
      return arr;
    }
    
```
##  4、希尔排序（Shell Sort）
<img src="../img/ms/4.gif" >  
```javascript
    const ARR = [3, 5, 8, 0, 12, 87, 45]

    function shellSort(arr) {
      var len = arr.length,
        temp,
        gap = 1;
      while (gap < len / 3) { // 动态定义间隔序列
        gap = gap * 3 + 1;
      }
      for (gap; gap > 0; gap = Math.floor(gap / 3)) {
        for (var i = gap; i < len; i++) {
          temp = arr[i];
          for (var j = i - gap; j > 0 && arr[j] > temp; j -= gap) {
            arr[j + gap] = arr[j];
          }
          arr[j + gap] = temp;
        }
      }
      return arr;

    }
    console.log(shellSort(ARR))//=>[0, 3, 5, 8, 12, 45, 87]
```
##  5、归并排序（Merge Sort）
<img src="../img/ms/5.gif" >  
```javascript
    const ARR = [3, 5, 8, 0, 12, 87, 45]

    function mergeSort(arr) { //采用自上而下的递归方法
      var len = arr.length;
      if (len < 2) {
        return arr;
      }
      var middle = Math.floor(len / 2),
        left = arr.slice(0, middle),
        right = arr.slice(middle);
      return merge(mergeSort(left), mergeSort(right));
    }

    function merge(left, right) {
      var result = [];

      while (left.length > 0 && right.length > 0) {
        if (left[0] <= right[0]) {
          result.push(left.shift());
        } else {
          result.push(right.shift());
        }
      }

      while (left.length)
        result.push(left.shift());

      while (right.length)
        result.push(right.shift());
      return result;
    }
    
    console.log(mergeSort(ARR));//=>[0, 3, 5, 8, 12, 45, 87]
```
##  6、快速排序（Quick Sort）
<img src="../img/ms/6.gif" >  
```javascript
    const ARR = [3, 5, 8, 0, 12, 87, 45]

    function quickSort(array, left, right) {
      if (left < right) {
        var x = array[right],
          i = left - 1,
          temp;
        for (var j = left; j <= right; j++) {
          if (array[j] <= x) {
            i++;
            temp = array[i];
            array[i] = array[j];
            array[j] = temp;
          }
        }
        quickSort(array, left, i - 1);
        quickSort(array, i + 1, right);
      }
      return array;
    }

    console.log(quickSort(ARR));//=>[0, 3, 5, 8, 12, 45, 87]
```
##  7、堆排序（Heap Sort）
<img src="../img/ms/7.gif" >  
```javascript
    const ARR = [3, 5, 8, 0, 12, 87, 45]

    function heapSort(array) {
      //建堆
      var heapSize = array.length,
        temp;
      for (var i = Math.floor(heapSize / 2) - 1; i >= 0; i--) {
        heapify(array, i, heapSize);
      }
      //堆排序
      for (var j = heapSize - 1; j >= 1; j--) {
        temp = array[0];
        array[0] = array[j];
        array[j] = temp;
        heapify(array, 0, --heapSize);
      }
      return array;
    }

    function heapify(arr, x, len) {
      var l = 2 * x + 1,
        r = 2 * x + 2,
        largest = x,
        temp;
      if (l < len && arr[l] > arr[largest]) {
        largest = l;
      }
      if (r < len && arr[r] > arr[largest]) {
        largest = r;
      }
      if (largest != x) {
        temp = arr[x];
        arr[x] = arr[largest];
        arr[largest] = temp;
        heapify(arr, largest, len);
      }
    }

    console.log(heapSort(ARR));//=>[0, 3, 5, 8, 12, 45, 87]
```
##  8、计数排序（Counting Sort）
<img src="../img/ms/8.gif" >  
```javascript
    const ARR = [3, 5, 8, 0, 12, 87, 45]

    function countingSort(array) {
      var len = array.length,
        B = [],
        C = [],
        min = max = array[0];
      for (var i = 0; i < len; i++) {
        min = min <= array[i] ? min : array[i];
        max = max >= array[i] ? max : array[i];
        C[array[i]] = C[array[i]] ? C[array[i]] + 1 : 1;
      }

      // 计算排序后的元素下标
      for (var j = min; j < max; j++) {
        C[j + 1] = (C[j + 1] || 0) + (C[j] || 0);
      }
      for (var k = len - 1; k >= 0; k--) {
        B[C[array[k]] - 1] = array[k];
        C[array[k]]--;
      }
      return B;
    }
    console.log(countingSort(ARR));//=>[0, 3, 5, 8, 12, 45, 87]
```
##  9、桶排序（Bucket Sort）
<img src="../img/ms/9.png" >  
```javascript
    const ARR = [3, 5, 8, 0, 12, 87, 45]
// @param array 数组
// @param num 桶的数量
    function bucketSort(array, num) {
      if (array.length <= 1) {
        return array;
      }
      var len = array.length,
        buckets = [],
        result = [],
        min = max = array[0],
        space, n = 0;

      var index = Math.floor(len / num);
      while (index < 2) {

        num--;
        index = Math.floor(len / num);
      }

      for (var i = 1; i < len; i++) {
        min = min <= array[i] ? min : array[i];
        max = max >= array[i] ? max : array[i];
      }
      space = (max - min + 1) / num; //步长
      for (var j = 0; j < len; j++) {
        var index = Math.floor((array[j] - min) / space);
        if (buckets[index]) { // 非空桶，插入排序
          var k = buckets[index].length - 1;
          while (k >= 0 && buckets[index][k] > array[j]) {
            buckets[index][k + 1] = buckets[index][k];
            k--;
          }
          buckets[index][k + 1] = array[j];
        } else { //空桶，初始化
          buckets[index] = [];
          buckets[index].push(array[j]);
        }
      }
      while (n < num) {
        result = result.concat(buckets[n]);
        n++;
      }
      return result;
    }

    console.log(bucketSort(ARR,6));//=>[0, 3, 5, 8, 12, 45, 87]
```
##  10、基数排序（Radix Sort）
<img src="../img/ms/10.gif" >  
```javascript
    const ARR = [3, 5, 8, 0, 12, 87, 45]

    /**
     * 基数排序适用于：
     * (1)数据范围较小，建议在小于1000
     * (2)每个数值都要大于等于0
     * @author damonare
     * @param arr 待排序数组
     * @param maxDigit 最大位数
     */
    //LSD Radix Sort

    function radixSort(arr, maxDigit) {
      var mod = 10;
      var dev = 1;
      var counter = [];
      for (var i = 0; i < maxDigit; i++, dev *= 10, mod *= 10) {
        for (var j = 0; j < arr.length; j++) {
          var bucket = parseInt((arr[j] % mod) / dev);
          if (counter[bucket] == null) {
            counter[bucket] = [];
          }
          counter[bucket].push(arr[j]);
        }
        var pos = 0;
        for (var j = 0; j < counter.length; j++) {
          var value = null;
          if (counter[j] != null) {
            while ((value = counter[j].shift()) != null) {
              arr[pos++] = value;
            }
          }
        }
      }
      return arr;
    }

    console.log(radixSort(ARR, 6));//=>[0, 3, 5, 8, 12, 45, 87]
```