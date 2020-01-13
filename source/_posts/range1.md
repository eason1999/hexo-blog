---
title: 冒泡排序
---

  冒泡排序是一种简单的排序算法，一次比较两个元素，如果它们的顺序错误就把它们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端；

  ### 步骤

  - 比较相邻的元素。如果第一个比第二个大，就交换它们两个；

  - 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数；

  - 针对所有的元素重复以上的步骤，除了最后一个；

  - 重复步骤1~3，直到排序完成；


  ### 演示

  ![range](/img/img/range1.gif)


  ### 代码

  ```javascript

  function sort(arr) {
    let len = arr.length;
    for (let i = 0; i < len - 1; i++) {
      for (let j = 0; j < len - 1 - i; j++) {
        if (arr[j] > arr[j+1]) {        // 相邻元素两两对比
          let temp = arr[j+1];        // 元素交换
          arr[j+1] = arr[j];
          arr[j] = temp;
        }
      }
    }
    return arr;
  }
 
  ```

  *PS：java*

  ```javascript

  public class Sort implements IArraySort {
​
    @Override
    public int[] sort(int[] sourceArray) throws Exception {
      // 对 arr 进行拷贝，不改变参数内容
      int[] arr = Arrays.copyOf(sourceArray, sourceArray.length);
​
      for (int i = 1; i < arr.length; i++) {
        // 设定一个标记，若为true，则表示此次循环没有进行交换，也就是待排序列已经有序，排序已经完成。
        boolean flag = true;
​
        for (int j = 0; j < arr.length - i; j++) {
          if (arr[j] > arr[j + 1]) {
            int tmp = arr[j];
            arr[j] = arr[j + 1];
            arr[j + 1] = tmp;
​
            flag = false;
          }
        }
​
        if (flag) {
          break;
        }
      }
      return arr;
    }
  }

  ```

  *PS：php*

  ```javascript

  function sort($arr) {
    $len = count($arr);
    for ($i = 0; $i < $len - 1; $i++) {
      for ($j = 0; $j < $len - 1 - $i; $j++) {
        if ($arr[$j] > $arr[$j+1]) {
          $tmp = $arr[$j];
          $arr[$j] = $arr[$j+1];
          $arr[$j+1] = $tmp;
        }
      }
    }
    return $arr;
  }

  ```
 >*参考来源：https://sort.hust.cc/*