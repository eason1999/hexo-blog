---
title: NodeJS入门--process对象
---

### exit事件

  进程退出时触发

  ```javascript
  process.on('exit', function() {
    // 设置一个延迟执行
    setTimeout(function() {
        console.log('主事件循环已停止，所以不会执行');
    }, 0);
    console.log('退出前执行');
  });
  setTimeout(function() {
      console.log('1');
  }, 500);
  ```

### uncaughtException事件

  捕获那些咱没有 try catch 的异常错误

  ```javascript
  process.on('uncaughtException', function() {
    console.log('捕获到一个异常');
  });
  var a = '123';
  a.a(); //触发异常事件
  console.log('这句话扑街了，不会显示出来');
  ```

### process.stdout

  一个指向标准输出流(stdout)的 可写的流(Writable Stream)

### process.stderr

  一个指向标准错误流(stderr)的 可写的流(Writable Stream)

### process.stdin

  一个指向 标准输入流(stdin) 的可读流(Readable Stream)。标准输入流默认是暂停 (pause) 的，所以必须要调用 process.stdin.resume() 来恢复 (resume) 接收

  ```javascript
  process.stdin.on('end', function() {
    process.stdout.write('end');
  });

  function gets(cb){
    process.stdin.setEncoding('utf8');
    //输入进入流模式（flowing-mode，默认关闭，需用resume开启），注意开启后将无法read到数据
    //见 https://github.com/nodejs/node-v0.x-archive/issues/5813
    process.stdin.resume();
    process.stdin.on('data', function(chunk) {
      console.log('start!');
      //去掉下一行可一直监听输入，即保持标准输入流为开启模式
      process.stdin.pause();
      cb(chunk);
    });
    console.log('试着在键盘敲几个字然后按回车吧');
  }

  gets(function(reuslt){
    console.log("["+reuslt+"]");
    //process.stdin.emit('end'); //触发end事件
  });
  ```

### process.argv

  返回当前命令行指令参数

  ```javascript
  console.log(process.argv); //[ 'node', 'E:\\github\\nodeAPI\\process\\argv.js', 'a', 'b' ]
  process.argv.forEach(function(val, index, array) {
    console.log(index + ': ' + val);
  });
  ```

### process.abort

  触发node的abort事件，退出当前进程

  ```javascript
  process.abort();
  console.log('在输出这句话之前就退出了');
  ```

### process.execPath

  获取当前进程的这个可执行文件的绝对路径

  ```javascript
  console.log(process.execPath); //C:\Program Files\nodejs\node.exe
  ```

### process.cwd

  返回当前进程的工作目录

  ```javascript
  console.log('当前目录：' + process.cwd()); //当前目录：E:\github\nodeAPI\process
  ```

### process.chdir(directory)

  改变进程的当前进程的工作目录（该目录必须已存在），若操作失败则抛出异常

  ```javascript
  var path = require('path');
  console.log('当前目录：' + process.cwd()); //当前目录：E:\github\nodeAPI\process
  try {
    process.chdir(path.resolve('.','tmp'));
    console.log('新目录：' + process.cwd()); //新目录：E:\github\nodeAPI\process\tmp
  }
  catch (err) {
    console.log('chdir: ' + err);
  }
  ```

### process.env

  获取当前系统环境信息的对象，常规可以用来进一步获取环境变量、用户名等系统信息

  ```javascript
  console.log(process.env);
  console.log('username: ' + process.env.USERNAME);
  console.log('PATH: ' + process.env.PATH);
  ```

*目前就先记这么多吧*
>引用自[vajoy](https://www.cnblogs.com/vajoy/p/4783390.html)，感谢作者