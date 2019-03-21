---
title: 'vue组件之挂载'
---

构造组件四步骤

 - 生成组件
 - vue.extend构建组件
 - 挂载 Vue.prototype
 - 在js中使用

###1、写好组件

*Message.vue*
```javascript

	<template>
		<p class="Message">{{value}}</p>
	</template>

	<script>
	export default {
		data() {
			return {
				value: "我是一个弹框"
			};
		}
	};
	</script>

	<style>
	.Message {
		position: fixed;
		bottom: 30px;
		width: 200px;
		background-color: rgba(0, 0, 0, 0.5);
		color: #fff;
		border-radius: 10px;
		left: 50%;
		transform: translateX(-50%);
		line-height: 30px;
		text-align: center;
		font-size: 15px;
		animation: messageFade 3s 1;
	}
	/* 加个简单动画 */
	@keyframes messageFade {
		0% {
			opacity: 0;
			-webkit-transform: translate3d(-50%, 80%, 0);
			transform: translate3d(-50%, 80%, 0);
		}
		16% {
			opacity: 1;
			-webkit-transform: translate3d(-50%, 0, 0);
			transform: translate3d(-50%, 0, 0);
		}
		84% {
			opacity: 1;
			-webkit-transform: translate3d(-50%, 0, 0);
			transform: translate3d(-50%, 0, 0);
		}
		100% {
			opacity: 0;
			-webkit-transform: translate3d(-50%, 80%, 0);
			transform: translate3d(-50%, 80%, 0);
		}
	}
	</style>

```

###2. vue.extend构建组件

*Message.js*
```javascript

	import Vue from 'vue';
	import Message from './Message.vue';
	// 构造组件
	const MessageConstructor = Vue.extend(Message);
	// 设置删除组件
	const removeDom = (target) => {
    target.parentNode.removeChild(target);
	};
	// 构造组件添加关闭方法
	MessageConstructor.prototype.close = function() {
    this.visible = false;
    removeDom(this.$el);
	};

	const MessageDiv = (options) => {
    // 实例化组件
    const instance = new MessageConstructor({
        el: document.createElement('div'),
        // 组件参数，运用到组件内的data
        data: options,
    });
    // 在body添加组件
    document.body.appendChild(instance.$el);
    Vue.nextTick(() => {
        instance.timer = setTimeout(() => {
            // 定时关闭组件
            instance.close();
        }, 3000);
    });
    return instance;
	}
	export default MessageDiv

```

###3. 挂载 `Vue.prototype`

*main.js*
```javascript

	import Message from '@/components/Message.js'
	Vue.prototype.$message = Message;

```

###4. 使用

```javascript

	<template>
  <div id="app">
    <Button color="blue" @click.native="msg">我是全局按钮</Button>
  </div>
	</template>

	<script>
	import Button from "@/components/Button.vue"
	export default {
		name: "app",
		components: {
			Button
		},
		methods: {
			msg() {
				// 4. 使用构造组件
				this.$message({value:'我是构造组件'});
			}
		}
	}

```