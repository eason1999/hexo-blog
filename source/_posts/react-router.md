---
title: react-router V4
---

### React router V4 vs V3

 - `BrowerRouter` 和 `hashRouter`, 服务于不同应用场景
 - 不再使用`{props.children}`来处理嵌套路由
 - V4 不做路由默认排他，会多个匹配

### BrowserRouter vs HashRouter

`<BrowserRouter>`和`<HashRouter>`是可以在浏览器中使用的。如果你使用的是一个非静态的站点、要处理各种不同的url那么你就需要使用`BrowserRouter`。相反的如果你的server只处理静态的url，那么就使用`HashRouter`

### 关于Route

`Route`有如下属性：

 - `path`属性，用来匹配url
 - `component`属性，绘制对应组件
  - `render` 可替代`component`
   ```javascript
   <Route path="/" render={()=><div>Home</div>} />
   ```
  - `children` 可替代`component`
 - `exact` 当前路由是否排他匹配
  ```javascript
  <Route exact path="/" component={Home} />
  ```
 - `strict` 只匹配以斜杠结尾的路径

 ### 关于Link 和 NavLink

  - `to`属性，router要跳转到的地址，值可以字符串，也可是一个对象
    ```javascript
    <Link to '/'>Home</Link>
    ```
    ```javascript
    <Link to{{
      pathname: '/me',
      search: '?sort=asc',
      hash: '#hash',
      state: { fromHome: true }
      }} />
    ```
  - `replace`属性，点击操作路由历史当前路由会被替换
  - `NavLink`是`Link`子类，在`Link`组件基础上增加绘制组件样式
    ```javascript
    <NavLink to="/me" activeStyle={{SomeStyle}} activeClassName="selected">
      ...
    </NavLink>
    ```
 ### 何为排他和非排他路由

 组件路由使用属性`exact`

 ```javascript
 <Route path="/" exact component={Home} />
 ```

 - 非排他指`"/"`和`"/login"`都满足匹配条件，对应的登录组件和Home组件就会同时出现在界面上
 - 排他指只有第一个匹配路由对应组件被渲染
  - V4 的`switch`组件可实现排他
    ```javascript
    import { Switch, Route } from 'react-router';
    <Switch>
      <Route exact path="/" component={Home} />
      <Route path="/about" component={About} />
      <Route path="me" component={Profile} />
      <Route component={NotFound} />
    </Switch>
    ```

 ### 浏览器历史

  - `history`对象，react中使用如下：

   ```javascript
    history.push("/my-path")
    history.replace("/my-path")
   ```
  - `Redirect`组件重定向
  
   ```javascript
    <Redirect to {{
      pathname: '/register',
      search: '?utm=something',
      state: { referrer: someplage.com }
    }}>
   ```

   ```javascript
    <Redirect to="register" />
   ```

  ### react中子组件取不到this.props.history?

  `react` 中被`react-redux`包裹的子组件取不到`this.props.history`对象，咋办？
  - 首先我们需要`prop-types`
   
   ```javascript
    import PropTypes from 'prop-types'
   ```
  
  - 组件需要设置contextTypes
   
   ```javascript
    Component.contextTypes = {
      router: PropTypes.object.isRequired
    }
   ```
  
  - 之后在组件内可以如下使用

   ```javascript
    console.log(this.context) 
    // {
    //   router:
    //     history: ...
    // }
    this.context.router.history.push(...)
   ```