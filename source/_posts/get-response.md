---
title: 随便几点
---

1、**whistle**、**fiddler**、**charles**

2、 [文字艺术](https://fsymbols.com/text-art/blocky/)

3、小程序input方法节流

  ```javascript
    // wxml 代码：
    <input value="{{keyWord}}" type="text" bindinput="inputValue"/>
    // 这种写法 bindconfirm 绑定的函数在点击软键盘右下角回车键时，触发 searchIt 函数
    // confirm-type="search" 写法，使右下角回车键显示为 ‘搜索’ 
    
    // js 代码：
    // 实时监听输入框的 关键词，放到 data 中
      inputValue(e) {
        var keyWord = e.detail.value        // keyWord 为输入的值
        // 假设现在需要检测到用户输入的值，用户 300 毫秒内没有继续输入就将该值打印出来
        this.throttle(this.queryData, null, 300, keyWord)
      },
    // 节流
      throttle(fn, context, delay, text) {
        clearTimeout(fn.timeoutId)
        fn.timeoutId = setTimeout(() => {
          fn.call(context, text)
        }, delay)
      },
    // 想要执行的函数
    queryData(e){
        console.log(e)        // 此处打印出来的就是 用户输入的值
    }
  ```