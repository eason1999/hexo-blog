---
title: 小程序登录授权
---

小程序授权登录整体流程如下：

![授权流程图](/img/img/flow.jpg)


### 登录授权

  页面进入需判断是否授权时，在页面`onShow`生命周期：

  ```javascript

    onShow() {
      login() {
        wx.showLoading()
        wx.login({
          success(res) {
            if (res.code) {
              getUserInfo(res.code, callback)
            } else {
              ...
            }
          },
          fail() {
            ...
          }
        })
      }
    }

    getUserInfo(code, callback) {
      wx.getUserInfo({
        success(res) {
          // 全局存储
          store.commit('userInfo', res.userInfo)
          doLogin(code, res.iv, res.encryptedData, callback)
        },
        fail() {
          wx.hideLoading()
          store.commit('storeUpdateToken', '')
          showLoginModal()
        }
      })
    }

    doLogin(code, iv, encryptedData, callback) {
      let params = {
        code,
        iv,
        encryptedData
      }
      request(apiUrl.postLogin, params, 'post').then((res) => {
        if (res.code == 1) {
          wx.hideLoading()
          store.commit('storeUpdateToken', res.data.token)
          callback && callback()
        } else {
          showToast()
        }
      }).catch((err) => {
        showToast()
      })

      showToast(content = '登录失败，请稍后再试') {
        wx.showToast({
          title: content,
          icon: 'none'
        })
      }

  ```

  整个流程是 `wx.login => wx.getUserInfo =>` 开发者服务器登录`doLogin`.
  由于微信小程序授权的接口`wx.getUserInfo`和`wx.authorize`中`scope `为 `scope.userInfo` ，新版中调用这两个API是不会主动触发弹出授权窗口的。需要使用 `<button open-type="getUserInfo" @getuserinfo="getUserInfo"></button> `方法。

  *感谢作者[江北晨](https://blog.csdn.net/qq_33388137/article/details/80828732)分享*