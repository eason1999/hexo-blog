---
记一次react项目优化
---

1. 安装依赖插件--`react-loadable`，同时定义`loading`组件。

```javascript

  npm install react-loadable --save-dev
  
  // loading组件
  import * as React from 'react'
  import { Spin } from 'antd'
    
  function PageLoading() {
    return (
      <div>
        <Spin />
      </div>
    )
  }
  export default PageLoading

```
  
2. 更改项目页面功能组件引入方式，所有路由都改为动态加载。

```javascript

  import * as Loadable from 'react-loadable'
  import Loading from '@/components/PageLoading'
  
  const LoadableComponent = loader => Loadable({
    loader,
    loading: Loading,
  })
  const BigDataHome = LoadableComponent(() => import('@/page/BigDataHome'))
    ...
```

3. 配置文件新建`webpack.dll.conf.js`文件，只要包含的库没有增减、升级，就不需要重新打包线上的dll代码不需要随着版本发布频繁更新，这样也大大提高了每次编译的速度。

```javascript

  const path = require('path')
  const webpack = require('webpack')
  const UglifyJsPlugin = require('uglifyjs-webpack-plugin')

  module.exports = {
    entry: {
      vendor: ['react', 'react-dom'],
      comment: ['antd']
    },
    output: {
      filename:  '[name].dll.[hash:8].js',
      path: path.join(__dirname, './../', 'dll'),
      library: 'dll_[name]_[hash:8]'
    },
    plugins: [
      new webpack.DllPlugin({
        path: path.join(__dirname, './../', 'dll/[name].manifest.json'),
        name: 'dll_[name]_[hash:8]'
      })
    ],
    optimization: {
      minimizer: [
        new UglifyJsPlugin()
      ]
    }
  }
```

4. 执行命令`webpack --config config/webpack.dll.conf.js`根目录下生成dll文件夹，文件为基于`react`、`react-dom`、`antd`打包好的压缩文件。
![dll](/img/img/dll.png)

5. `webpack.config.js`文件`plugins`配置项增加dll配置及文件引入。


```javascript

  const AddAssetHtmlPlugin = require('add-asset-html-webpack-plugin')
  
  plugins: [
    new webpack.DllReferencePlugin({
      manifest:   require('../dll/vendor.manifest.js  on')
    }),
    new webpack.DllReferencePlugin({
      manifest:   require('../dll/comment.manifest.json')
   }),
    new AddAssetHtmlPlugin({
     filepath: path.resolve(__dirname, './../dll/**/*.js'),
     includeSourcemap: false
    }),
    ...
  ]

```

当然我们还可以引入`happyPack`，将打包任务分解给多个子进程去并发的执行，子进程处理完后再把结果发送给主进程。


```javascript

  new HappyPack({
    id: 'babel',
    loaders: [
      {
        loader: 'babel-loader',
        query: '?cacheDirectory',
        options: {
          presets: ['@babel/env']
        }
      }],
    threadPool: happyThreadPool,
    verbose: true
  })

```

```javascript
 {
   test: /\.js$/,
   include: [srcPath, iqiyiPath],
   exclude: /(node_modules|bower_components)/,
   loader: 'happypack/loader?id=babel'
 }

```
项目的打包时间由原来的50多秒，下降到平均不到30秒，提高了20秒左右，二次打包平均时间1秒多。

### before:
![before](/img/img/before.png)
![before-save](/img/img/before-save.png)

### after:
![after](/img/img/after.png)
![after-save](/img/img/after-save.png)
