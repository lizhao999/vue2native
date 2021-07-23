<template>
	<view class="content" :class="{change:isChange}">
		<image class="logo" src="/static/logo.png"></image>
		
		<view class="text-area">
			<text class="title">{{title}}</text>
			<text class="title">当前平台为{{platform}}</text>
		</view>
		<button type="primary" @click="changebgColor">改变颜色</button>
		<button type="primary" v-on:click="clickBtn">点击我触发调用原生方法</button>
		<button type="primary" v-on:click="clickBack">点击我还原文本</button>
	</view>
</template>
 
<script>
	export default {
		mounted() {
			window.changeTitle = this.changeTitle
			window.changebgColor = this.changebgColor
		},
		data() {
			return {
				title: 'Hello world!',
				platform: uni.getSystemInfoSync().platform ,
				isChange:false
				
			}
		},
		onLoad() {
			console.debug(uni.getSystemInfoSync().platform)
		},
		methods: {
			clickBtn: function() {
				switch(this.$data.platform) {
					case "android":
					    AppJsFunction.showDialog(this.$data.title);
					    break
				    case "ios":
					    window.webkit.messageHandlers.AppJsFunction.postMessage(this.$data.title);
					    break
				}
			},
			clickBack: function(){
				this.$data.title = "Hello world!";
			},
			changeTitle(value) {
				this.$data.title = value;
				return "调用成功";
			},
			changebgColor(){
				this.$data.isChange = !this.$data.isChange
 			}
		}
	}
</script>
<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		/* justify-content: center; */
		background: #4CD964;
		height: 100vh;
	}
	.change{
		background: #007AFF;
		
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: inline-block;
		justify-content: center;
	

		
		
		
	}
.title {
			display: block;
			font-size: 36rpx;
			color: #8f8f94;
		}
	
</style>
