---
title: 基于vue的tab切换组件
---

项目中引入的UI组件--`tab`组件在项目使用上有点无法满足UI布局及设计，所以自己重新开发了一版`tab`组件

*demo.vue*

```javascript
    <tab 
      :active-color="'#333'"
      :bar-active-color="'#FB625B'"
      v-model='tabIndex'
      @on-item-click="handlerTabItem">
      <tab-item v-for="(item, index) in tabs" :key="index" :label="item.name" :name='item.id'></tab-item>
    </tab>

```

*tab.vue*

```javascript

  <template>
      <div class="tabs">
        <div class="tabs-bar">
          <!--标题页的标题 v-for遍历, :class 动态绑定class-->
          <div :class="[tabCls(item), navList.length > 4 ? 'tab-cls1' : 'tab-cls2']" v-for="(item, index) in navList" @click="handleChange(index, $event)">
            <span :ref="currentValue === (item.name || index) ? 'currentTab' : '' " class="inner-tab" :style="{'color': currentValue === (item.name || index) ? activeColor : '#666'}">{{item.label}}</span>
          </div>
          <span class="border-active" :style="borderStyle"></span>
        </div>
        <div class="tabs-content">
          <slot></slot>
        </div>
    </div>
  </template>

  <script>
    export default {
      data() {
        return {
          navList: [],
          // 保存父组件的value到currentValue变量中
          currentValue: this.value,
          borderStyle: {
            background: this.barActiveColor,
            width: ''
          }
        }
      },
      props: {
        // 接收父组件的value
        value: {
          type: [String, Number]
        },
        activeColor: {
          type: String,
          default: '#333'
        },
        barActiveColor: {
          type: String,
          default: '#FF0000'
        }
      },
      methods: {
        // 使用$children遍历子组件，得到所有的组件
        getTabs() {
          return this.$children.filter((item) => item.$options._componentTag === 'tab-item')
        },
        // 更新tabs
        updateNav() {
          this.navList = []
          let _this = this
          this.getTabs().forEach((pane, index) => {
            _this.navList.push({
              label: pane.label,
              name: pane.name || index
            })
            if (!pane.name) {
              pane.name = index
            }
            if (index === 0) {
              if (!_this.currentValue) {
                _this.currentValue = pane.name || index
              }
            }
          })
          this.updateStatus()
        },
        updateStatus() {
          let tabs = this.getTabs()
          let _this = this
          // 显示当前选中的tab对应组件，隐藏没有选中的
          tabs.forEach((tab) => {
            tab.show = tab.name === _this.currentValue
            return tab
          })
        },
        tabCls(item) {
          return [
            'tabs-tab',
            {
              // 为当前选中的tab加一个tabs-tab-active class
              'tabs-tab-active': item.name === this.currentValue,
            }
          ]
        },
        // 点击tab标题触发
        handleChange(index, evt) {
          let nav = this.navList[index]
          let name = nav.name
          // 改变当前选中的tab，触发watch
          this.currentValue = name
          let widths = evt.srcElement.children.length ? evt.srcElement.children[0].offsetWidth : evt.srcElement.offsetWidth
          let offsetLeft = evt.srcElement.children.length ? evt.srcElement.children[0].offsetLeft : evt.srcElement.offsetLeft
          this.borderStyle = Object.assign(this.borderStyle, {
            width: `${widths}px`,
            // transform: `translateX(${offsetLeft}px)`,
            left: `${offsetLeft}px`,
            transitionDuration: '0.3s'
          })

          // 实现子组件与父组件通信
          this.$emit('on-item-click', name)
        }
      },
      watch: {
        value(val) {
          this.currentValue = val
        },
        currentValue() {
          // tab发生变化时，更新显示状态
          this.updateStatus()
        }
      },
      mounted() {
        this.$nextTick(() => {
          this.borderStyle = Object.assign(this.borderStyle, {
            width: `${this.$refs.currentTab[0].offsetWidth}px`,
            left: `${this.$refs.currentTab[0].offsetLeft}px`,
            transitionDuration: '0.3s'
          })
          console.log(this.borderStyle, 54654)
        })
      },
    }
  </script>

  <style lang="less" scoped>
    .tabs{
      width: 100%;
      height: 45px;
      font-size: 14px;
      color: #657180;
    }
    .tabs-bar {
      position: relative;
      display: flex;
      align-items: center;
      width: 100%;
      height: 100%;
      background: #fff;
      overflow-x: auto;
    }

    .tabs-bar .border-active {
      position: absolute;
      bottom: 0;
      height: 2px;
      // background: red;
    }
    .tabs-tab{
      text-align: center;
      // line-height: 35px;
    }
    .tabs-tab .inner-tab {
      padding: 0 4px;
      display: inline-block;
      height: 100%;
      box-sizing: border-box;
    }
    .tab-cls1 {
      flex: 0 0 22%;
    }
    .tab-cls2 {
      flex: 1;
    }
    .tabs-content{
      padding: 8px 0;
    }
  </style>

```

*tab-item.vue*
```javascript

  <template>
    <div class="pane" v-show="show">        
      <slot></slot>
    </div>
  </template>

  <script>
  export default {
    props: {
      name: {
        type: [String, Number]
      },
      label: {
        type: String,
        default: ''
      }
    },
    data() {
      return {
        show: true
      }
    },
    components: {},
    methods: {
      updateNav() {
        this.$parent.updateNav()
      }
    },
    watch: {
      label() {
        this.updateNav()
      }
    },
    mounted() {
      // 初始化tabs
      this.updateNav()
    },
  }
  </script>

```

### 效果如下

![gif图](/img/img/1_clip.gif)
图制作有误，将就着看吧，哈哈···

[参考](https://www.cnblogs.com/mengfangui/p/8072480.html)