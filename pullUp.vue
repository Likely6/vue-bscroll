<template>
	<div class="pull" ref="pull" v-show="listenPullingUp">
		<p v-show="flagA">{{pullUpText}}</p>
		<img :src="loading" alt="loading" v-show="flagB">
	</div>
</template>
<script>
import loading from './loading.gif'
export default {
	props: {
		upText: Object,
		listenPullingUp: Boolean
	},
	data() {
		return {
			loading,
			flagA: false,
			flagB: false,
			pullUpText: ''
		}
	},
	methods: {
		uShow() {
			this.pullUpText = this.upText.pull
			this.flagA = true
			this.flagB = true
		},
		uHide() {
			this.flagA = false
			this.flagB = false
		},
		uNoResult() {
			this.pullUpText = this.upText.tip
			this.flagB = false
		}
	},
	created() {
		this.pullUpText = this.upText.pull
	}
}
</script>
<style lang='scss' scoped>
.pull{
	width: 100%;
	height: 48px;
	text-align: center;
	display: flex;
	align-items: center;
	justify-content:center;
	p{
		margin-right: 10px;
	}
	img{
		width: 30px;
		height: 30px;
	}
}
</style>