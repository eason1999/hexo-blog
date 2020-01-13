---
title: ES5/ES6相关
---

### ES5/ES6的一些区别：

 - `class`会声明提升，但不会初始赋值

  ```javascript
    const bar = new Bar(); // it's ok
    function Bar() {
      this.bar = 42;
    }

    const foo = new Foo(); // ReferenceError: Foo is not defined
    class Foo {
      constructor() {
        this.foo = 42;
      }
    }
  ```

 - `class`声明内部会启用严格模式

  ```javascript
    // 引用一个未声明的变量
    function Bar() {
      baz = 42; // it's ok
    }
    const bar = new Bar();

    class Foo {
      constructor() {
        fol = 42; // ReferenceError: fol is not defined
      }
    }
    const foo = new Foo()
  ```

 - `class`的所有方法（静态、实例方法）都不可枚举

  ```javascript

    // 引用一个未声明的变量
    function Bar() {
      this.bar = 42;
    }
    Bar.answer = function() {
      return 42;
    };
    Bar.prototype.print = function() {
      console.log(this.bar);
    };
    const barKeys = Object.keys(Bar); // ['answer']
    const barProtoKeys = Object.keys(Bar.prototype); // ['print']

    class Foo {
      constructor() {
        this.foo = 42;
      }
      static answer() {
        return 42;
      }
      print() {
        console.log(this.foo);
      }
    }
    const fooKeys = Object.keys(Foo); // []
    const fooProtoKeys = Object.keys(Foo.prototype); // []

  ```

 - `class`方法必须使用`new`调用

  ```javascript

    function Bar() {
      this.bar = 42;
    }
    const bar = Bar(); // it's ok

    class Foo {
      constructor() {
        this.foo = 42;
      }
    }
    const foo = Foo(); // TypeError: Class constructor Foo cannot be invoked without 'new'

  ```

 - `class`内部无法重写类名

  ```javascript

    function Bar() {
      Bar = 'Baz'; // it's ok
      this.bar = 42;
    }
    const bar = new Bar();
    // Bar: 'Baz'
    // bar: Bar {bar: 42}  

    class Foo {
      constructor() {
        this.foo = 42;
        Foo = 'Fol'; // TypeError: Assignment to constant variable
      }
    }
    const foo = new Foo();
    Foo = 'Fol'; // it's ok

  ```
 
 - `const`、`let`声明变量无法通过`window`获取

  ```javascript

    let a = 10;
    const b = 20;
    // 相当于：
    (function() {
      var  a = 10;
      var b = 20;
    })()

  ```

 - 代码改造，使之分别打印10、20

  ```javascript

    // 题
    var b = 10
    (function b(){
        b = 20
        console.log(b)
    })()

    // 打印10
    var b = 10;
    (function b(b) {
    window.b = 20;
    console.log(b)
    })(b)

    // var b = 10;
    // (function b(b) {
    // b.b = 20;
    // console.log(b)
    // })(b)

    // 打印20
    var b = 10;
    (function b(b) {
    b = 20;
    console.log(b)
    })(b)

    // var b = 10;
    // (function b() {
    // var b = 20;
    // console.log(b)
    // })()

  ```

 - `Array`数组扩展

  ```javascript
    
    // flat、sort、set

    [8, 9, [1, 2, 44, 56], [1, 10, 21], [3, 4, 55, [4, 61, [1, 61, 4]]]

    'aaasdofjaopfjopaiiisjssfopiasdfffff'.match(/(.)\1+/g)

  ```
