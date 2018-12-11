---
title: vue项目数据埋点
---

### click埋点统计

在已有的项目基础上，做数据埋点统计，前提是不能干扰现有的结构和数据逻辑，将影响降到最低，通过自定义指令可以在节点增加click数据统计。在`main.js`入口文件配置全局的自定义指令.

```javascript

  Vue.directive('stat', {
    bind(el, binding) {
      el.addEventListener('click', () => {
        const data = binding.value
        // 接口调用，传递数据到后端
        axios.post(api, data)
      }, false)
    }
  })

```

### 路由拦截统计页面pv

页面pv曝光统计，是在路由层面进行处理，因为我们项目需要统计停留时长，我们的思路是设置一个初始时间节点同时记录是否第一次进入，每个页面的id则挂在路由meta对象下.

*公共方法封装GetPvData*
```javascript

  let pvData = sessionStorage.getItem('PV_DATA')
  if (pvData) {
    pvData = JSON.parse(pvData)
    if (pvData.isFirstEnter) {
      pvData.isFirstEnter = false
    }
    let time = new Date().valueOf()
    pvData.t = time - pvData.time // 计算停留时长
    pvData.time = time // 将当前时间设置为初始时间
  } else {
    pvData = {
      isFirstEnter: true,
      time: new Date().valueOf(), // 落地时间
      t: 0, // 停留时长
    }
  }
  sessionStorage.setItem('PV_DATA', JSON.stringify(pvData))
  return pvData

```

*路由钩子*
```javascript
  router.afterEach((to, from) => {
    const pvData = GetPvDdata()
    const params = {
      pvId: to.meta.pvId || '',
      staySeconds: pvData.t,
      pvName: to.meta.pvName || '',
      ...
    }

    // 接口调用，传递数据到后端，当前页面如果不需要统计pv，则路由层不设置pvId值即可
    pvId && $store.dispatch('doBuryPointer', params)
  }

```

以上是我在项目中的一些使用，记录下。参考：[https://www.jianshu.com/p/7bc63935a570](https://www.jianshu.com/p/7bc63935a570)
