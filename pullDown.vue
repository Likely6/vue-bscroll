<template>
	<div class="pull" ref="pull" v-show="listenPullingDown">
		<p v-show="flag">{{pullDownText}}</p>
		<img :src="loading" alt="loading" v-show="!flag">
	</div>
</template>
<script>
import loading from './loading.gif'
export default {
	props: {
		downText: Object,
		listenPullingDown: Boolean
	},
	data() {
		return {
			loading,
			flag: true,
			pullDownText: ''
		}
	},
	methods: {
		dShowText() {
			this.flag = true
		},
		dHideText() {
			this.flag = false
		},
		dPull() {
			this.pullDownText = this.downText.pull
		},
		dLoosen() {
			this.pullDownText = this.downText.loosen
		},
		dComplete() {
			this.pullDownText = this.downText.tip
			this.dShowText()
			return Promise.resolve()
		}
	},
	created() {
		this.pullDownText = this.downText.pull
	}
}
</script>
<style lang='scss' scoped>
.pull{
	width: 100%;
	height: 48px;
	position: absolute;
	top: -58px;
	left: 0;
	padding-bottom: 10px;
	box-sizing: content-box;
	display: flex;
	align-items: flex-end;
	justify-content:center;
	p{
		height: 48px;
		line-height: 48px;
	}
	img{
		width: 30px;
		height: 30px;
	}
}
</style>