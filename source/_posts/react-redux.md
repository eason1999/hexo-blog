---
title: redux和react-redux
---

### 关于redux

[redux](https://redux.js.org)是JavaScript应用程序的可预测状态容器，对数据集中管理。这对于`react`高度组件化来说至关重要，不然对于多层嵌套的组件通信就异常的麻烦(这次着重想记录下`react-redux`)

### 关于react-redux

开始我也不是很明白[react-redux](https://react-redux.js.org/docs/introduction/quick-start)又是什么鬼，查了下发现，原来`react-redux`是简化了我们的`state`获取和方法的派发，这里有两个关键API

 - Provider
   我们需要将顶层组件包裹在`Provider`组件之中，**必须将store作为参数传递进去**
   *App.js*
   ```javascript
   import React from 'react'
   import ReactDOM from 'react-dom'
   import { Provider } from 'react-redux'
   import store from './store'
   import './index.css'
   import TodoList from './pages/ToboList/TodoList'

    ReactDOM.render(
      <Provider store={store}>
        <TodoList />
      </Provider> , document.getElementById('root'))
   ```
 - connect
  将组件关联到`store`中，写法如下
  ```javascript
  export default connect(mapStateToProps, mapDispatchToProps)(Component)
  ```
  这里主要有两个参数`mapStateToProps` `mapDispatchToProps`，从字面意思就能看出，一个是将`state`映射到`props`，一个是将`dispatch`映射到`props`

  *TodoList.js*
  ```javascript
   import React, { Component } from 'react'
   import { connect } from 'react-redux'
   import store from './../../store'
   import { delete_list_item, init_list_data } from '../../store/createActions'
   import TodoListUI from '../TodoListUI/TodoListUI'

   class TodoList extends Component {
   constructor(props) {
     super(props)
     this.state = store.getState()
     store.subscribe(() => {
       this.setState(store.getState())
     })
   }
   render() {
     return (
       <TodoListUI
         list={this.props.list}
         deleteListItem={this.props.deleteListItem}
         ></TodoListUI>
     )
   }
   componentDidMount() {
    store.dispatch(init_list_data())
   }
   const mapStateToProps = (state) => {
     return {
       list: state.list
     }
   }
   const mapDispatchToProps = (dispatch) => {
     return {
       deleteListItem(index) {
         dispatch(delete_list_item(index))
       }
     }
   }
   export default connect(mapStateToProps, mapDispatchToProps)(TodoList)
  ```
  