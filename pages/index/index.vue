<template>
	<view class="content">
		<view class="text-area">
			<textarea placeholder="请输入m3u8地址" v-model="url"></textarea>
		</view>
		<button @tap="down">下载</button>
		<view class="load" v-show="showload">
			<view class="back">
				<view class="width" :style="'width: '+ percentage +'%;'">
				</view>
			</view>
			<view>
				{{complete}}/{{count}}
			</view>
		</view> 
		<view> videourl: {{videourl}}</view>
		<view> defaultUrl: {{defaultUrl}}</view>
		<video :src="defaultUrl"></video>
		<video :src="videourl" ref="videoWrapHls"> </video>
	</view>
</template>

<script>
	import downm3u8 from '@/utils/downm3u8.js'
	var that
	export default {
		data() {
			return {
				videourl: '',
				percentage: 0,
				complete: 0,
				count: 0,
				title: 'Hello',
				// defaultUrl: 'https://www.zhuticlub.com:65/20190511/1CAQr5Vz/index.m3u8',
				defaultUrl: 'http://hls.videocc.net/ccb547c108/c/ccb547c108b24806a418164ce3429c7a_1.m3u8',
				url: '',
				showload: false,
				id: 0,
				videoContext: null,
				key: ''
			}
		},
		onLoad() {
			that = this
			that.url = that.defaultUrl
			this.id = new Date().getTime()

			plus.downloader.enumerate(function(e) {
				// console.log(e)
			}, -1)

		},
		methods: {
			down() {
				console.log("down", this.showload)
				if (this.showload === true) {
					return
				}
				let that = this
				var url = this.url //m3u8地址
				var option = {
					url: url, //m3u8地址
					dirpath: 'video', //保存到目录
					id: this.id, //下载标识，用于断线续传：开始任务时存在即续传，不存在新建
					tryTepeat: 3, //下载失败重试次数
					timeout: 30, //超时时间
					retryInterval: 30, //重试等待时间
					meanwhile: 20, //并发下载数
					initcallback(res) {
						//m3u8解析的回调，解析成功返回片段列表并开始下载，解析失败message
						console.log("initcallback", res)
						that.showload = true
						that.complete = 0
						that.count = res.obj.tslist.length
						that.percentage = 0
					},
					tsloadcallback(res) {
						//片段下载回调，成功返回片段信息，失败返回respose，
						console.log("tsloadcallback", res)
						that.complete++
						that.percentage = Math.floor((that.complete / that.count) * 100)
					},
					finishcallback(res) {
						//文件保存为 app缓存 / 保存目录 / 下载标识.m3u8
						console.log("finishcallback", res)
						that.videourl = res.obj.path
						that.videoContext = plus.video.createVideoPlayer('video', {
							top: '20px',
							left: '0px',
							width: '200px',
							height: '225px',
							position: 'static',
							src: that.videourl,
							direction: -90,
							controls: true
						});
						let pages = getCurrentPages();
						let page = pages[pages.length - 1];
						that.mWebview = page.$getAppWebview();
						that.mWebview.append(that.videoContext);
						that.videoContext.play();
						that.showload = false
					}
				}
				downm3u8(option);
			}
		}
	}
</script>

<style>
	.width {
		background-color: red;
		height: 3px;
	}

	.back {
		flex: 1;
		background-color: #eee;
		height: 3px;
		margin-right: 20rpx;
	}

	.load {
		display: flex;
		align-items: center;
		margin: 20rpx 0;
	}

	/* * {
		box-sizing: border-box;
	} */

	.content {
		padding: 20rpx 30rpx;
	}

	textarea {
		border: 1px solid #EEE;
		padding: 20rpx 30rpx;
		width: 100%;
		height: 80px;
	}
</style>
