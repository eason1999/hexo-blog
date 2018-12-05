---
title: TypeScript入门（1）
---

抽点时间，看看`TypeScript`的文档，学习下关于`ts`语法相关。

### 什么是`TypeScript`?

`TypeScript` 是 `JavaScript` 的类型的超集，它可以编译成纯 `JavaScript`。编译出来的 `JavaScript` 可以运行在任何浏览器上。`TypeScript` 编译工具可以运行在任何服务器和任何系统上。`TypeScript` 是开源的。（官方解释，直接拿来）


### 原始数据类型

`javascript` 分两种类型：**原始数据类型** 和 **对象类型**，原始数据类型包括： **布尔值**、**数值**、**字符串**、**null**、**undefined** 以及 ES6 中的新类型 **Symbol**。

 - 布尔值
  
  ```javascript

  let isDone: boolean = false;

  // 编译通过
  // 后面约定，未强调编译错误的代码片段，默认为编译通过

  ```

  **使用构造函数`Boolean`创造的对象不是布尔值**

  ```javascript

  let createdByNewBoolean: boolean = new Boolean(1);

  // index.ts(1,5): error TS2322: Type 'Boolean' is not assignable to type 'boolean'.
  // 后面约定，注释中标出了编译报错的代码片段，表示编译未通过

  ```

 - 数值

   使用`number` 定义数值类型

   ```javascript

   let decLiteral: number = 6;
   let hexLiteral: number = 0xf00d;
   // ES6 中的二进制表示法
   let binaryLiteral: number = 0b1010;
   // ES6 中的八进制表示法
   let octalLiteral: number = 0o744;
   let notANumber: number = NaN;
   let infinityNumber: number = Infinity;

   编辑结果：

   var decLiteral = 6;
   var hexLiteral = 0xf00d;
   // ES6 中的二进制表示法
   var binaryLiteral = 10;
   // ES6 中的八进制表示法
   var octalLiteral = 484;
   var notANumber = NaN;
   var infinityNumber = Infinity;

   ```

 - 字符串
  
  使用`string`定义类型

  ```javascript

   let myName: string = 'Tom';
   let myAge: number = 25;

   // 模板字符串
   let sentence: string = `Hello, my name is ${myName}.
   I'll be ${myAge + 1} years old next month.`;

   编辑结果：

   var myName = 'Tom';
   var myAge = 25;
   // 模板字符串
   var sentence = "Hello, my name is " + myName + ".\nI'll be " + (myAge + 1) + " years old next month.";

   ```

 - 空值
  
  `javascript`没有空值，`ts`语法可以，使用`void`表示

 - null 和 undefined

  `undefined` 类型的变量只能被赋值为 `undefined`，`null` 类型的变量只能被赋值为 `null`。

  ```javascript

   let u: undefined = undefined;
   let n: null = null;

  ```

  `void` 类型的变量不能赋值给 `number` 类型的变量

  ```javascript

   let u: void;
   let num: number = u;

   // index.ts(2,5): error TS2322: Type 'void' is not assignable to type 'number'.

  ```

 - 任意值

  `Any` 表示任意类型，任意值可以访问 **任何属性** 和 **调用任意方法**

  ```javascript

   let myFavoriteNumber: any = 'seven';
   myFavoriteNumber = 7;

   let anyThing: any = 'hello';
   console.log(anyThing.myName);
   console.log(anyThing.myName.firstName);

   let anyThing: any = 'Tom';
   anyThing.setName('Jerry');
   anyThing.setName('Jerry').sayHello();
   anyThing.myName.setFirstName('Cat');

  ```

  变量未声明任何类型，则会被识别为任意值类型

  ```javascript

   let something;
   something = 'seven';
   something = 7;

   something.setName('Tom');

  ```

  等价于

  ```javascript

   let something: any;
   something = 'seven';
   something = 7;

   something.setName('Tom');

  ```