<template>
	<div class="scroll" ref="scroll">
		<ul class="scroll-wrapper">
			<PullDown ref="dPull" :downText="downText" :listenPullingDown="funcs.listenPullingDown"></PullDown>
			<slot></slot>
			<PullUp ref="uPull" :upText="upText" :listenPullingUp="funcs.listenPullingUp"></PullUp>
		</ul>
	</div>
</template>
<script>
import BScroll from 'better-scroll'
import PullDown from './pullDown.vue'
import PullUp from './pullUp.vue'
export default {
	props: {
		attr: Object,
		func: Object,
		downText: {
			type: Object,
			default: () => {
				return {
					pull: '下拉刷新',
					loosen: '松开刷新',
					tip: '刷新完成'
				}
			}
		},
		upText: {
			type: Object,
			default: () => {
				return {
					pull: '正在加载数据',
					tip: '已无更多数据'
				}
			}
		}
	},
	data() {
		return {
			defaultAttr: {
				click: true,
				probeType: 1
			},
			defaultFunc: {
				listenBeforeScrollStart: false,
				listenScroll: false,
				listenScrollEnd: false,
				listenScrollToEnd: false,
				listenTouchEnd: false,
				listenPullingDown: false,
				listenPullingUp: false
			},
			completeFlag: false
		}
	},
	components: {
		PullDown,
		PullUp
	},
	computed: {
		attrs() {
			return Object.assign({}, this.defaultAttr, this.attr)
		},
		funcs() {
			return Object.assign({}, this.defaultFunc, this.func)
		}
	},
	methods: {
		calc() {
			let res = this.funcs
			if (res.listenPullingDown) {
				this.$set(this.defaultAttr, 'pullDownRefresh', {
					threshold: 58,
  					stop: 50
				})
			}
			if (res.listenPullingUp) {
				this.$set(this.defaultAttr, 'pullUpLoad', {
				  threshold: 50
				})
			}
		},
		init() {
			let _this = this
			this.BScroll = new BScroll(this.$refs.scroll, this.attrs)
			// 滚动开始前
			if (this.funcs.listenBeforeScrollStart) {
				this.BScroll.on('beforeScrollStart', (pos) => {
					this.$emit('beforeScrollStart', pos)
				})
			}
			// 滚动时
			if (this.funcs.listenScroll) {
				this.BScroll.on('scroll', (pos) => {
					this.$emit('scroll', pos)
					if (this.funcs.listenPullingDown) {
						if (pos.y > this.attrs.pullDownRefresh.threshold && !this.completeFlag) {
							this.$refs.dPull.dLoosen()
						}
					}
					if (this.funcs.listenPullingUp) {
						if (pos.y <= this.attrs.pullUpLoad.threshold && !this.completeFlag) {
							this.$refs.dPull.dPull()
						}
					}
				})
			}
			// 滚动结束
			if (this.funcs.listenScrollEnd) {
				this.BScroll.on('scrollEnd', (pos) => {
					this.$emit('scrollEnd', pos)
				})
			}
			// 滚动到底部
			if (this.funcs.listenScrollToEnd) {
				this.BScroll.on('scrollEnd', (pos) => {
					if (pos.y <= this.BScroll.maxScrollY + 50) {
						this.$emit('scrollToEnd', pos)
					}
				})
			}
			// 鼠标、手指离开
			if (this.funcs.listenTouchEnd) {
				this.BScroll.on('touchEnd', (pos) => {
					this.$emit('touchEnd', pos)
				})
			}
			// 监听下拉刷新
			if (this.funcs.listenPullingDown) {
				this.BScroll.on('pullingDown', () => {
					// 触发刷新
					_this.$refs.dPull.dHideText()
					_this.completeFlag = true
					// 派遣函数到父组件去刷新数据
					this.$emit('pullingDown')
				})
			}
			// 监听上拉刷新
			if (this.funcs.listenPullingUp) {
				// 监听上拉加载
				this.BScroll.on('pullingUp', () => {
					_this.$refs.uPull.uShow()
					// 派遣函数到父组件去更新数据
					this.$emit('pullingUp')
				})
			}
		},
		refresh() {
			this.BScroll && this.BScroll.refresh()
		},
		scrollTo() {
			this.BScroll && this.BScroll.scrollTo.apply(this.BScroll, arguments)
		},
		scrollToElement() {
			this.BScroll && this.BScroll.scrollToElement.apply(this.BScroll, arguments)
		},
		_finishPullDown() {
			this.BScroll && this.BScroll.finishPullDown()
		},
		_finishPullUp() {
			this.BScroll && this.BScroll.finishPullUp()
		},
		afterRefresh() {
			let _this = this
			this.$refs.dPull.dComplete().then(() => {
				setTimeout(() => {
					_this.finishPullDown()
					setTimeout(() => {
						_this.completeFlag = false
						_this.refresh()
					}, 500)
				}, 1000)
			})
		},
		afterUpload(flag = true) {
			// 有无结果
			if (flag) {
				this.$refs.uPull.uHide()
			} else {
				this.$refs.uPull.uNoResult()
			}
			this.finishPullUp()
		}
	},
	mounted() {
		this.calc()
		this.$nextTick(() => {
			this.init()
		})
	}
}
</script>
<style lang='scss' scoped>
.scroll{
	width: 100%;
	height: 100%;
	overflow: hidden;
}
</style>