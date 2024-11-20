<template>
	<view>
		<web-view :src="targetUrl" @message="handleMessage"></web-view>
	</view>
</template>

<script>
    const TTSSpeech = uni.requireNativePlugin('MT-TTS-Speech');
	import Vue from 'vue'
	export default {
		data() {
			return {
				targetUrl: 'http://192.168.53.112:81/ui-triage/login',
				queue:[],
			}
		},
		methods: {
			handleMessage(res) {
				const {text,pitch,speed} = res.detail.data[0]
				this.queue.push({
					text:text,
					pitch:pitch,
				    speed:speed
				})
		        this.speak();
			},
			speak(){
				let status=TTSSpeech.isSpeeking();
				if(status){				//正在发声等待1秒再次触发
					setTimeout(()=>{
						this.speak();
					},1000)
					return;
				}
				let voice=this.queue.pop();
				if(voice){
					TTSSpeech.setPitch(voice.pitch || 100);//设置语调  0-100, 默认 100
					TTSSpeech.setSpeed(voice.speed || 50); // 设置语速 0-100, 默认 50
					TTSSpeech.speak({
					   text: voice.text,
					});
				}
			}
		},
		onLoad() {
			TTSSpeech.init((status) => {
			  if (status === 0) {
				console.log('TTS引擎初始化成功');
			  } else {
				console.error('TTS引擎初始化失败，状态码:', status);
			  }
			}, 'com.iflytek.speechcloud'); 
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
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
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
