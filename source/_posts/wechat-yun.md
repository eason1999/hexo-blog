---
title: 小程序-云开发
---

空余时间做了个邀请函，主要使用了小程序的云开发能力，使用了部分云开发api，期间有一些点做下笔记，好记性不如烂笔头啊~~~

### 三大基础能力支持

 #### 云函数

  - 在云端运行的代码，微信私有协议天然鉴权，开发者只需编写自身业务逻辑代码

  - 云函数中可以处理一些异步操作，在异步操作完成后再返回一个Promise方法

  - 处理定时触发任务

  - 云函数中使用wx-server-sdk需在对应云函数目录下安装依赖(执行npm install)
 
 #### 数据库

  主要进行相应的增删改查，官方文档有详细说明及对应操作api，微信开发工具上可以进入云开发平台，操作一目了然
 
 #### 存储

  在小程序前端直接上传/下载云端文件，在云开发控制台可视化管理

### 注意事项

 - 1、创建云函数只能在开发者工具中进行,不能在普通代码编辑器中创建

 - 2、云开发需要在main.js文件进行初始化，设置响应的云开发环境(我使用了mpvue)

 ``` javascript

  import Vue from 'vue'
  import App from './App'

  Vue.config.productionTip = false
  App.mpType = 'app'

  wx.cloud.init({
    env: 'mini-car-2cc4a5',
    traceUser: true
  })

  const app = new Vue(App)
  app.$mount()

 ```

 - 3、云开发、云函数需要在app.json、project.config.json文件设置相应属性(低于基础库使用云开发能力)

  *project.config.json*
  ```javascript

    "cloudfunctionRoot": "/static/functions/",

  ```
  *app.json*
  ```javascript

    "cloud": true

  ```

 - 4、地图能力需要在小程序后台“设置-开发者工具-腾讯位置服务”申请开通


### 项目重点

*检测用户版本更新情况app.vue-onLaunch*

```javascript

  if (wx.canIUse('getUpdateManager')) {
    const updateManager = wx.getUpdateManager()
    updateManager.onUpdateReady(function () {
      wx.showModal({
        title: '更新提示',
        content: '新版本已经准备好，是否重启应用？',
        success (res) {
          if (res.confirm) {
            // 新的版本已经下载好，调用 applyUpdate 应用新版本并重启
            updateManager.applyUpdate()
          }
        }
      })
      updateManager.onUpdateFailed(function () {
        // 新版本下载失败
        wx.showModal({
          title: '提示',
          content: '新版本下载失败，请检查网络',
          showCancel: false
        })
      })
    })
  } else {
    wx.showModal({
      title: '提示',
      confirmColor: '#5BB53C',
      content: '当前微信版本过低，无法使用该功能，请升级到最新微信版本后重试。'
    })
  }

```

*打开地址*

```javascript

  wx.openLocation({
    latitude: 28.34334,
    longitude: 117.787878,
    scale: 28,
    name: addr // 打开后显示的地址名称
  })

```

*数据请求*

```javascript

  getMusicUrl () {
    const db = wx.cloud.database()
    const music = db.collection('music')
    music.get().then(res => {
      let musicUrl = res.data[0].url
      audioCtx.src = musicUrl
      audioCtx.loop = true
      audioCtx.obeyMuteSwitch = false
      audioCtx.autoplay = true
      this.getImage()
    })
  }

```

*云函数处理*
```javascript

  // 云函数入口文件
  const cloud = require('wx-server-sdk')

  cloud.init()
  const db = cloud.database()
  const MAX_LIMIT = 100
  // 云函数入口函数
  exports.main = async (event, context) => {
    const countResult = await db.collection('message').count()
    const total = countResult.total
    // 计算需分几次取
    const batchTimes = Math.ceil(total / 100)
    // 承载所有读操作的 promise 的数组
    const tasks = []
    for (let i = 0; i < batchTimes; i++) {
      const promise = db.collection('message').skip(i * MAX_LIMIT).limit(MAX_LIMIT).get()
      tasks.push(promise)
    }
    // 等待所有
    if (tasks.length) {
      return (await Promise.all(tasks)).reduce((acc, cur) => ({
        data: acc.data.concat(cur.data),
        errMsg: acc.errMsg
      }))
    }
    return (await Promise.resolve({
      data: [],
      errMsg: '请求成功'
    }))
  }

```

*调用云函数方法*
```javascript

  getList () {
    wx.showLoading()
    wx.cloud.callFunction({
      name: 'messageList'
    }).then(res => {
      this.messageList = [...res.result.data].reverse()
      if (!this.messageList.length) {
        this.isEmpty = true
      }
      wx.hideLoading()
    })
  }

```

