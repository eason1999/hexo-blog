---
title: react中的css样式作用域
---

感觉`react`的局部样式相比`vue`要麻烦点，`vue`可以在`style`标签上添加`scoped`，达到使样式作用于当前文件的效果，而`react`没有，但是`react`有其他的实现方式，比如我所知道且使用过的`css-Modules`、`styled-components`。

### css-Modules

自动为每一个类生成一个哈希值，产生惟一标志，因此避免了类的覆盖，如下图：

![css-modules](/img/img/demo1.png)

这里需要配置`wepack`文件，`react`项目需要执行`npm eject`命令暴露出`config`文件，我这里增加配置如下：

```javascript

 {
   test: /\.css$/,
   exclude: [/node_modules/, /static/],
   use: getStyleLoaders({
     importLoaders: 1,
     modules: true,
     localIdentName: '[name]_[local]_[hash:base64:5]'
   }),
 },

```
*其中的/static/是一些css全局的文件*

关键配置就是 **modules: true**

### styled-components

`styled-components`是常用的`css in js`类库，他有很多的语法，[传送门](https://www.styled-components.com/docs)，它会在每个标签上生成唯一的类名，如下：

![styled-components](/img/img/demo2.png)

这里需要在项目中引入`styled-components`，安装完成之后可以在需要的目录下创建如`style.js`文件，并在其中引入`styled-components`，使用如下：

*style.js*
```javascript

 import styled  from 'styled-components';

 export const ModeDiv = styled.a.attrs({
  // 这里写入标签属性及赋值
  href: 'javascript:;'
 })`
  float: right;
  line-height: 56px;
  height: 56px;
  padding: 0 10px 0 15px;
  color: #969696;
  text-decoration: none;
  .icon-Aa {
    font-size: 24px;
  }
`
...

export const NavItem = styled.div`
  float: left;
  line-height: 56px;
  font-size: 16px;
  color: #777;
  margin-right: 40px;
  .icon {
    font-size: 20px;
    vertical-align: top;
    margin-right: 2px;
  }
  &.first {
    color: #EA6F5A;
  }
  &.third {
    position: relative;
  }
  .icon-fangdajing {
    position: absolute;
    top: 2px;
    right: 10px;
    z-index: 10px;
  }
  .focused {
    display: inline-block;
    background: #969696;
    color: #fff;
    width: 32px;
    height: 32px;
    border-radius: 50%;
    text-align: center;
    line-height: 32px;
    top: 14px;
    right: 5px;
    cursor: pointer;
  }
  .slide-enter {
    transition: all .2s ease-out;
  }
  .slide-enter-active {
    width: 260px;
  }
  .slide-exit {
    transition: all .2s ease-out;
  }
  .slide-exit-active {
    width: 160px;
  }
`

```

*index.jsx*
```javascript

 import { ModeDiv, NavItem } from './style';
 import { CSSTransition } from 'react-transition-group';

<ModeDiv>
  <i className='iconfont icon-Aa'></i>
</ModeDiv>

...

<NavItem className="third">
  <CSSTransition
    in={focused}
    timeout={200}
    classNames="slide">
    <Fragment>
      <SearchInput
        className={focused ? 'ipt-focused' : ''}
        onFocus={() => handleFocus(list)}
        onBlur={handleBlur}></SearchInput>
      <i className={focused ? 'iconfont icon-fangdajing focused' : 'iconfont icon-fangdajing'}></i>
    </Fragment>
  </CSSTransition>
  {(() => {
    if (focused || mouseIn) {
      return searchWrap();
    }
    return null
  })()}
</NavItem>

```

### 结语

`styled-components`的使用增加了可维护性，但是体验感觉并不好，需要书写的代码更多，项目里我暂时没有试用`less`、`sass`，准备引入了，配合使用`css-modules`体验还是可以的。
