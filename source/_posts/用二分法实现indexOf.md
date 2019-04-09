---
title: 用二分法实现indexOf
categories: js
date: 2018-07-06 13:41:10
tags: [二分法,算法,面试]
---

## 用二分法实现indexOf

在网上看到一个又去的面试题，重新复习了一下二分法之后，自己写了个例子。

<!-- more -->

### 题目

**用二分查找实现 indexOf 方法，不允许用递归**

> 不能用递归哦，那就用 while 循环呀。

### 解答

#### 二分法

二分法是针对一个排好序的数组 ， asc 或者 desc 都可以 ，假设是asc排序， 每次选出 index 的中位数，用目标值和这个 index 上的值比较，如果目标值更多，那就取 [index, length-1] 这一段的数组 ，重复上面的找中位数再比较的操作，知道扎到符合的index 或者找不到就返回 -1；

#### 代码
```js
function binarySearch(array,target){
  var max = array.length-1,min=0;
  var middle ;
  while(min <= max){   //  1
    middle = Math.floor((max+min)/2);
    if(array[middle] === target){
      return middle;
    }else if(array[middle] > target){
      max = middle-1;  // 2
    }else if(array[middle] < target){
      min = middle+1;  // 3
    }
  }  
  // 若循环完还没有找到 就返回 -1
  return -1;
}

var arr = [1,2,4,5,7,8,9];
binarySearch(arr,6);

```
有几点需要注意  , 就是上面的 1,2,3

1 这里要包含 = 号 ， 假如 到最后  [1,3] 要找的是 3 , 比较发现3>1然后 min = middle + 1 , min变成了3，max原先就是3 ，如果不加 = 就直接退出循环返回 -1 了， 加了= ，就会 用[3]去比较 ，最后得出结果；

2 3 都是为了防止死循环 。假如 到最后只剩 [1,3] 要找 2， 没有 middle +1  的话，  就会一直在[1,3]这里循环。
