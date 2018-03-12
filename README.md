### vue-bscroll

[vue-bscroll](https://github.com/Likely6/vue-bscroll)是基于[better-scroll](https://ustbhuangyi.github.io/better-scroll/doc/zh-hans/#better-scroll)的vue的滚动组件，它通过props和$emit操作better-scroll的方法，将better-scroll组件化，提供下拉刷新、上拉加载等操作

>npm安装

	npm i vue-bscroll --save

>props

| 参数 | 说明 | 类型 | 默认值 |
| ---- | ---- | ---- | --- |
attr | [better-scroll](https://ustbhuangyi.github.io/better-scroll/doc/zh-hans/options.html) 里的选项 | Object | 见Obj1 |
| func | 通过配置Object来控制是否触发事件(配置选项见Obj1) | Object | 见Obj2 |
| downText | 配置下拉的文本 | Object | ```{pull:'下拉刷新',loosen:'松开刷新',tip:'刷新完成'}``` |
| upText | 配置上拉的文本 | Object | ```{pull:'正在加载数据',tip:'已无更多数据'}``` |

```javascript
Obj1: {
	click: true,
	probeType: 1,
	pullDownRefresh, {
		threshold: 58,
		stop: 50
	},
	pullUpLoad, {
	  threshold: 50
	}
}
Obj2: {
	listenBeforeScrollStart: false,//滚动前时        派发beforeScrollStart事件		返回参数pos
	listenScroll: false,           //滚动时          派发scroll事件				返回参数pos
	listenScrollEnd: false,        //滚动结束时      派发scrollEnd事件     返回参数pos
	listenScrollToEnd: false,      //滚动到底部时     派发scrollToEnd事件	返回参数pos
	listenTouchEnd: false,         //手指/鼠标离开时  派发touchEnd事件			返回参数pos
	listenPullingDown: false,      //下拉刷新时       派发pullingDown事件	在这里执行刷新操作
	listenPullingUp: false         //上拉加载时       派发pullingUp事件		在这里执行数据请求操作
}
```

>methods

| 名称 | 说明 | 参数 |
| ---- | ---- | ---- |
| refresh | 重新计算 better-scroll | 无 |
| scrollTo | 滚动到指定的位置 | x(横轴左边){Number}<br/>y(纵轴坐标){Number}<br/>time(滚动动画执行的时长){Number}<br/>easing(缓动函数){Object} |
| scrollToElement | 滚动到指定的目标元素 | el(滚动到的目标元素){DOM \| String}<br/>time(滚动动画执行的时长){Number}<br/>offsetX(相对于目标元素的横轴偏移量，如果设置为 true，则滚到目标元素的中心位置){Number \| Boolean}<br/>offsetY(相对于目标元素的纵轴偏移量，如果设置为true，则滚到目标元素的中心位置){Number \| Boolean}<br/>easing (缓动函数){Object} |
| afterRefresh | 在下拉刷新后调用(执行刷新操作后调用，必须) | 无 |
| afterUpload | 在上拉加载后调用(即请求获取数据后调用，必须) | flag(true表示还有数据,false表示没有更多数据了){Boolean} |

>events
>注意 这些事件触发需要在props的func里配置对应的参数,下拉刷新同时需要设置listenScroll:true

| 名称 | 说明 | 返回参数 |
| ---- | ---- | ---- |
| beforeScrollStart | 滚动前触发 | pos(一个包括x,y坐标的Object) |
| scroll | 滚动时 | pos(一个包括x,y坐标的Object) |
| scrollEnd | 滚结束时 | pos(一个包括x,y坐标的Object) |
| scrollToEnd | 滚到底部时 | pos(一个包括x,y坐标的Object) |
| touchEnd | 手指、鼠标离开时 | pos(一个包括x,y坐标的Object) |
| pullingDown | 下拉刷新时 | 无 |
| pullingUp | 上拉加载时 | 无 |

>例子

```html
<template>
	<div class="wrapper">
		<BScroll ref="bscroll" :func="func" @pullingUp="listenPullingUp" @pullingDown="listenPullingDown">
			<li v-for="(item, index) in list" :key="index">{{item}}</li>
		</BScroll>
	</div>
</template>
```

```javascript
import BScroll from 'vue-bscroll'
export default {
	data () {
		return {
			list: [],
			func: {
				// 监听滚动
				listenScroll: true,
				// 监听下拉刷新
				listenPullingDown: true,
				// 监听上拉加载
				listenPullingUp: true
			}
		}
	},
	components: {
		BScroll
	},
	methods: {
		listenPullingDown() {
			this.list = []
			this.loadData()
			setTimeout(() => {
				this.$refs.bscroll.afterRefresh()
			}, 1000)
		},
		listenPullingUp() {
			let _this = this
			setTimeout(() => {
				_this.loadData()
				_this.$refs.bscroll.afterUpload(true)
			}, 1000)
		},
		loadData(callback = () => {}) {
			let length = this.list.length
			let newArr = []
			for (let i = length; i < length + 20; i++) {
				newArr.push(`第${i}条数据`)
			}
			this.list = this.list.concat(newArr)
		}
	},
	mounted() {
		this.loadData()
	}
}
</script>
```

```css
<style lang="scss" scoped>
.wrapper{
	height: 500px;
	width: 100%;
	li{
		height: 48px;
		line-height: 48px;
	}
}
</style>
```
<br/>
