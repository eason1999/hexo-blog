---
title: react项目环境变量添加
---

### 默认环境变量

 可以使用的环境变量有`NODE_ENV`(这个环境变量已经定义好了)，这是一个内置的环境变量，你可以通过process.env.NODE_ENV访问到这个变量，react不允许自己设置这个变量的值，默认已经设置好了，有development,test,production这几种情况

### 自定义环境变量

 如果需要自行定义环境变量，需要以`REACT_APP_`开头设置，否则设置无效会被忽略，这是为了避免和系统本机的公钥冲突，如果修改了环境变量，必须重启你的项目

### 变量获取

 环境变量会被定义在`process.env`上面，比如你有一个环境变量叫做`REACT_APP_SECRET_CODE`，在js中使用时必须通过`process.env.REACT_APP_SECRET_CODE`访问

### 变量定义方式

 定义变量的方式有两种：
 
  - 命令行工具中定义
   
    在`package.json`文件`scripts`对象下添加运行环境变量设置

  - 新建.env文件
        
    在项目根目录中创建`.env`文件，在里面定义变量 `REACT_APP_SECRET_CODE = abcdef`


**因为在项目开发过程中，设置自定义环境变量的时候花了我一点时间，就是忽略了自定义的变量需要以`REACT_APP_`开头，所以始终设置失败，记录下~~~**