---
title: react多个代理时的设置方法
---

项目中可能会需要根据环境变量不同切换访问不同域名下api的需要，这时候就需要设置代理，不然会出现跨域问题，我在项目开发中遇到的是根据查询资料说多个代理地址时也是在`package.json`文件内以对象的形式设置`proxy`

*这样设置有问题，应该是我当前项目版本比较高*
```javascript

    "proxy": {
        "/api1": {
            "target": "http://localhost:8001",
            "changeOrigin": true
        },
        "/api2": {
            "target": "http://localhost:8002",
            "changeOrigin": true
        }
    }

```

后面自行查看了`create-react-app`官方文档，得到了答案：

### 解决办法

 - 安装`http-react-middleware`依赖包

 ```javascript

    npm install http-proxy-middleware --save

 ```

 - `src`目录下新建`setupProxy.js`，添加内容如下

 ```javascript

    const proxy = require('http-proxy-middleware');

    module.exports = function(app) {
        app.use('/api1', { target: 'http://localhost:8001' });
        app.use('/api2', { target: 'http://localhost:8002' });
    }

 ```

`setupProxy.js`文件不需要在任何地方引入，`webpack`会自动打包编译这个文件，现在可以顺利的切换使用啦！官方原文点[这里](https://facebook.github.io/create-react-app/docs/proxying-api-requests-in-development)，找到 **Back-End Integration**目录，第一个就是啦~~~



