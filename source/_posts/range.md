---
title: 归并排序
---

  归并排序（Merge sort）是建立在归并操作上的一种有效的排序算法，该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。作为一种典型的分而治之思想的算法应用，归并排序的实现有两种方法：

  - 自上而下的递归（所有递归的方法都可以用迭代重写，所以就有了第 2 种方法）；
  - 自下而上的迭代；

  ### 步骤

  - 申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列；

  - 设定两个指针，最初位置分别为两个已经排序序列的起始位置；

  - 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置；

  - 重复步骤 3 直到某一指针达到序列尾；

  - 将另一序列剩下的所有元素直接复制到合并序列尾

 >*来源：https://github.com/hustcc/JS-Sorting-Algorithm*

  ### 演示

  ![range](/img/img/range.gif)


  ### 代码

  ```javascript

  function mergeSort(arr) {  // 采用自上而下的递归方法
    var len = arr.length;
    if(len < 2) {
      return arr;
    }
    var middle = Math.floor(len / 2),
      left = arr.slice(0, middle),
      right = arr.slice(middle);
    return merge(mergeSort(left), mergeSort(right));
  }

  function merge(left, right) {
    var result = [];
    while (left.length && right.length) {
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
 
  ```

  *感谢作者--菠了个菜，来源其个人公众号「五分钟学算法」。「五分钟学算法」是致力于通过各种动画的形式来描绘出各种数据结构，并图解LeetCode原题的学习平台，一起学习，一起进步！*