---
title: 微信公众号授权第三方平台
---

整理下公众号授权，以备不时之需。授权登录并获取用户基本信息。简略步骤如下：
  1、用户同意授权 **获取code**
  2、通过 **code**换取到 **access_token**
  3、适当时候刷新 **access_token**
  4、获取用户信息

### 第一步：用户同意授权，获取code

  检查用户是否授权登录（如token有效性），打开授权跳转链接：
  *参考链接(官方示例)，具体链接参数信息参考官网*
  ```javascript

    scope为snsapi_base
    https://open.weixin.qq.com/connect/oauth2/authorize?appid=wx520c15f417810387&redirect_uri=https%3A%2F%2Fchong.qq.com%2Fphp%2Findex.php%3Fd%3D%26c%3DwxAdapter%26m%3DmobileDeal%26showwxpaytitle%3D1%26vb2ctag%3D4_2030_5_1194_60&response_type=code&scope=snsapi_base&state=123#wechat_redirect

    scope为snsapi_userinfo
    https://open.weixin.qq.com/connect/oauth2/authorize?appid=wxf0e81c3bee622d60&redirect_uri=http%3A%2F%2Fnba.bluewebgame.com%2Foauth_response.php&response_type=code&scope=snsapi_userinfo&state=STATE#wechat_redirect

  ```
  **注意：微信会对授权链接做正则强匹配校验，如果链接的参数顺序不对，授权页面将无法正常访问，跳转回调redirect_uri，应当使用https链接来确保授权code的安全性。**

  #### 用户同意授权
    页面跳转至：`redirect_uri/?code=CODE&state=STATE`（*code说明：code作为换取access_token的票据，每次用户授权带上的code将不一样，code只能使用一次，5分钟未被使用自动过期*）

### 第二步：通过code换取网页授权assess_token

  如果网页授权的作用域为snsapi_base，则本步骤中获取到网页授权access_token的同时，也获取到了openid，snsapi_base式的网页授权流程即到此为止。
  **注意：由于公众号的secret和获取到的access_token安全级别都非常高，必须只保存在服务器，不允许传给客户端。后续刷新access_token、通过access_token获取用户信息等步骤，也必须从服务器发起。**

  ```javascript

    获取code后，请求以下链接获取access_token：
    https://api.weixin.qq.com/sns/oauth2/access_token?appid=APPID&secret=SECRET&code=CODE&grant_type=authorization_code

  ```

### 第三步：如果需要进行刷新access_token

  由于access_token拥有较短的有效期，当access_token超时后，可以使用refresh_token进行刷新，refresh_token有效期为30天，当refresh_token失效之后，需要用户重新授权。

  ```javascript

    获取第二步的refresh_token后，请求以下链接获取access_token：
    https://api.weixin.qq.com/sns/oauth2/refresh_token?appid=APPID&grant_type=refresh_token&refresh_token=REFRESH_TOKEN

  ```

### 第四步：拉取用户信息(需scope为snsapi_userinfo)

  如果网页授权作用域为snsapi_userinfo，则此时开发者可以通过access_token和openid拉取用户信息了。

  ```javascript

    http：GET（请使用https协议） https://api.weixin.qq.com/sns/userinfo?access_token=ACCESS_TOKEN&openid=OPENID&lang=zh_CN

  ```

### 详细步骤如下：

　　1．用户关注微信公众账号。

　　2．微信公众账号提供用户请求授权页面URL。

　　3．用户点击授权页面URL，将向服务器发起请求

　　4．服务器询问用户是否同意授权给微信公众账号(scope为snsapi_base时无此步骤)

　　5．用户同意(scope为snsapi_base时无此步骤)

　　6．服务器将CODE通过回调传给微信公众账号

　　7．微信公众账号获得CODE

　　8．微信公众账号通过CODE向服务器请求Access_Token

　　9．服务器返回Access_Token和OpenID给微信公众账号

　　10．微信公众账号通过Access_Token向服务器请求用户信息(scope为snsapi_base时无此步骤)

　　11．服务器将用户信息回送给微信公众账号(scope为snsapi_base时无此步骤)

### 项目运用

  - 在统一的接口请求返回做拦截，在授权成功的回调地址后拼接token信息

  ```javascript

    // 获取浏览器信息参数
    function BrowserInfo() {
      const u = navigator.userAgent.toLowerCase()
      return {
        isMobile: !!u.match(/applewebkit.*mobile.*/), // 是否为移动终端
        isIos: !!u.match(/\(i[^;]+;( u;)? cpu.+mac os x/), // ios终端
        isAndroid: u.indexOf('android') > -1 || u.indexOf('adr') > -1, // android终端
        isWx: u.indexOf('micromessenger') > -1, // 是否微信
        isApp: u.indexOf('***app') > -1, // 是否 app
        isSimpleBrowser: u.indexOf('micromessenger') === -1 && u.indexOf('***app') === -1, // 普通浏览器环境
      }
    }
    
    axios.interceptors.response.use(response => {
      if (response.data.code === -1) {
        // 这里可以区分不同的端，如微信环境、普通浏览器、嵌入app等，做不同的操作
        if (BrowserInfo.isWx) {
          window.location.href = `${baseURL}/api/userWx/loginAuth?redirectUri=${encodeURIComponent(WxAuthHashUrl.to(currentRoute.fullPath))}&platformType=${platformType}`
          return Promise.reject(err)
        }
      }
    }

  ```

  - 在`vue`的`router`钩子函数处理`token`

    ```javascript

    router.beforeEach(to, from, next) {
      if (to.query.token) {
        session.setAttribute('token', to.query.token)
        let href = to.fullPath
        href = href.replace('token=', 'baseParam=818')
        next(href)
      } else {
        next()
      }
    }

    ```

  - 在统一的接口请求做token传参处理

    ```javascript

      axios.interceptors.request.use((config) => {
        let token = localStorage.getItem('sessionToken')
        config.headers.common['TOKEN'] = token
        config.cache = false
        thisHttp.url = config.url
        // 公共参数
        const commonParams = {
          _t: new Date().getTime(),
          ...
        }
        config.params = config.params || {}
        Object.assign(config.params, commonParams)
        return config
      }, (err) =>
        Promise.reject(err)
      )
    }

    ``` 
