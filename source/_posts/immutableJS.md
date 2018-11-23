---
title: immutable.js 在redux项目中实践
---

### immutable.js主要有三大特性：

 - Persistent data structure （持久化数据结构）
 - structural sharing （结构共享）
 - support lazy operation （惰性操作）


### 常用api

```javascript
  //Map()  原生object转Map对象 (只会转换第一层，注意和fromJS区别)
  immutable.Map({name:'danny', age:18})

  //List()  原生array转List对象 (只会转换第一层，注意和fromJS区别)
  immutable.List([1,2,3,4,5])

  //fromJS()   原生js转immutable对象  (深度转换，会将内部嵌套的对象和数组全部转成immutable)
  immutable.fromJS([1,2,3,4,5])    //将原生array  --> List
  immutable.fromJS({name:'danny', age:18})   //将原生object  --> Map

  //toJS()  immutable对象转原生js  (深度转换，会将内部嵌套的Map和List全部转换成原生js)
  immutableData.toJS();

  //查看List或者map大小  
  immutableData.size  或者 immutableData.count()

  // is()   判断两个immutable对象是否相等
  immutable.is(imA, imB);

  //merge()  对象合并
  var imA = immutable.fromJS({a:1,b:2});
  var imA = immutable.fromJS({c:3});
  var imC = imA.merge(imB);
  console.log(imC.toJS())  //{a:1,b:2,c:3}

  //增删改查（所有操作都会返回新的值，不会修改原来值）
  var immutableData = immutable.fromJS({
    a:1,
    b:2，
    c:{
      d:3
    }
  });
  var data1 = immutableData.get('a') //  data1 = 1  
  var data2 = immutableData.getIn(['c', 'd']) // data2 = 3   getIn用于深层结构访问
  var data3 = immutableData.set('a' , 2);   // data3中的 a = 2
  var data4 = immutableData.setIn(['c', 'd'], 4);   //data4中的 d = 4
  var data5 = immutableData.update('a',function(x){return x+4})   //data5中的 a = 5
  var data6 = immutableData.updateIn(['c', 'd'],function(x){return x+4})   //data6中的 d = 7
  var data7 = immutableData.delete('a')   //data7中的 a 不存在
  var data8 = immutableData.deleteIn(['c', 'd'])   //data8中的 d 不存在
```

### immutable.js的优缺点
 
 - 优点
   - 降低mutable带来的复杂度
   - 节省内存
   - 历史追溯性（时间旅行）：时间旅行指的是，每时每刻的值都被保留了，想回退到哪一步只要简单的将数据取出就行，想一下如果现在页面有个撤销的操作，撤销前的数据被保留了，只需要取出就行，这个特性在redux或者flux中特别有用
   - 拥抱函数式编程：immutable本来就是函数式编程的概念，纯函数式编程的特点就是，只要输入一致，输出必然一致，相比于面向对象，这样开发组件和调试更方便
 - 缺点
   - 需要重新学习api
   - 资源包大小增加（源码5000行左右）
   - 容易与原生对象混淆：由于api与原生不同，混用的话容易出错

### immutable.js使用过程中的一些注意点

 1. fromJS和toJS会深度转换数据，随之带来的开销较大，尽可能避免使用，单层数据转换使用Map()和List()

 2. js是弱类型，但Map类型的key必须是string！

 3. 所有针对immutable变量的增删改必须左边有赋值，因为所有操作都不会改变原来的值，只是生成一个新的变量
 ```javascript
  //javascript
  var arr = [1,2,3,4];
  arr.push(5);
  console.log(arr) //[1,2,3,4,5]
  
  //immutable
  var arr = immutable.fromJS([1,2,3,4])
  //错误用法
  arr.push(5);
  console.log(arr) //[1,2,3,4]
  //正确用法
  arr = arr.push(5);
  console.log(arr) //[1,2,3,4,5]
 ```

 4. 引入immutablejs后，不应该再出现对象数组拷贝的代码
 ```javascript
  //es6对象复制
  var state = Object.assign({}, state, {
      key: value
  });
  
  //array复制
  var newArr = [].concat([1,2,3])
 ```

 5. 获取深层深套对象的值时不需要做每一层级的判空
 ```javascript
  //javascript
  var obj = {a:1}
  var res = obj.a.b.c   //error
  
  //immutable
  var immutableData=immutable.fromJS({a:1})
  var res = immutableData.getIn(['a', 'b', 'c'])  //undefined
 ```

 6. immutable对象直接可以转JSON.stringify(),不需要显式手动调用toJS()转原生
 7. 判断对象是否是空可以直接用size
 8. 调试过程中要看一个immutable变量中真实的值，可以chrome中加断点，在console中使用.toJS()方法来查看