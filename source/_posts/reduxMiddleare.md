---
title: Redux中间件之redux-thunk和redux-saga
---
### 中间件
 桥接`aciton`与`store`的中间连接者

### redux-thunk
 正常`action`返回的是一个对象，`redux-thunk`中间件允许我们返回一个函数，而其中我们可以将我们的异步请求逻辑放在里面，异步操作在`action`完成

*store*
 ```javascript
  import { createStore, applyMiddleware } from 'redux'
  import reducers from './reducers'
  import thunk from 'redux-thunk'

  const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose // 这个是为了方便调试redux，官网有的哦

  const enhancer = composeEnhancers(
    applyMiddleware(thunk)
  )

  const store = createStore(
    reducers,
    enhancer
  )
  export default store
 ```
*createActions.js*
 ```javascript
  import { INIT_LIST_DATA } from '../actionTypes.js'
  export const get_list_async = () => {
    return (
      (dispatch) => {
        axios.get('list.json').then(res => {
          const data = res.data
          const action = {
            type: INIT_LIST_DATA,
            data
          }
          dispatch(action)
        })
      }
    )
  }
 ```
*list.jsx*
 ```javascript
  componentDidMount() {
    store.dispatch(get_list_async())
  }
 ```

### redux-saga
 同样可以帮助我们处理异步请求，但是异步逻辑并不是在`action`完成，这里需要我们在`store`中新增一个`sagas.js`文件，集中处理我们的异步请求

*store*
 ```javascript
  import { createStore, applyMiddleware, compose } from 'redux'
  import reducers from './reducers'
  import createSagaMiddleware from 'redux-saga'
  import mySaga from './sagas'

  const sagaMiddleware = createSagaMiddleware()
  const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose

  const enhancer = composeEnhancers(
    applyMiddleware(sagaMiddleware)
  )

  const store = createStore(
    reducers,
    enhancer
  )
  sagaMiddleware.run(mySaga)
  export default store
 ```

 *sagas.js*
 ```javascript
  import { takeEvery, put } from 'redux-saga/effects'
  import axios from 'axios'
  import { INIT_LIST_DATA, GET_LIST_FAILED, REQUEST_DATA } from './actionTypes'

  function* get_list_async() {
    try {
      const res = yield axios.get('list.json')
      yield put({
        type: INIT_LIST_DATA,
        data: res.data
      })
    } catch (e) {
      yield put({
        type: GET_LIST_FAILED,
        message: e.message
      })
    }
  }

  function* init_data() {
    yield takeEvery(REQUEST_DATA, get_list_async)
  }
  export default init_data
 ```
 *createActions.js*
 ```javascript
  export const init_list_data = () => {
    return {
      type: REQUEST_DATA
    }
  }
 ```
 *list.jsx*
 ```javascript
  componentDidMount() {
    store.dispatch(init_list_data())
  }
 ```

### redux-thunk or redux-saga

- 相似处
 - 都是处理异步请求逻辑的中间件

- 不同处
 - `thunk`操作简便点，在`action`中返回一个函数，其中可处理异步逻辑
 - `saga`则是`store`目录下新建`**.js`文件统一管理异步处理函数（这里需要提醒，这个js返回的是一个`generator`函数，这是一个ES6的语法）
 - `saga`相对更复杂

### redux-saga这么麻烦，为啥还要用？
 
 个人理解，[redux-saga](https://github.com/superRaytin/redux-saga-in-chinese)更适合团队协作，因为它将所有的异步处理逻辑都放在单独的js文件管理，这样更宜于维护，简单项目[redux-thunk](https://github.com/reduxjs/redux-thunk)足够使用啦。。。
 