<template>
	<view>
		<view class="text-area">
			<textarea placeholder="请输入m3u8地址" v-model="url"></textarea>
		</view>
		<view>共{{downloadTaskList.length}}个任务</view>
		<view>
			已下载：{{rate}}%
		</view>
		<button @tap="download">下载</button>
		<button @tap="resumeDownload">恢复下载</button>
		<button @tap="pauseDownload">暂停</button>
		<button @tap="plus.downloader.clear();">清除任务列表</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				url: 'http://vfx.mtime.cn/Video/2019/03/21/mp4/190321153853126488.mp4',
				downloadTaskList: [],
				downloadTask: null,
				rate: 0
			}
		},
		created() {
			// this.downloadTask = uni.getStorageSync('downloadTask') ? JSON.parse(uni.getStorageSync('downloadTask')) : null
			let that = this
			plus.downloader.enumerate(function(downloads) {
				that.downloadTaskList = downloads
				that.downloadTask = downloads[0]
				console.log("===========：" + downloads.length);
				//设置监听器
				that.downloadTask.addEventListener("statechanged", that.onStateChanged);
				//启动任务
				// that.downloadTask.start();
			});
		},
		methods: {
			download() {
				// plus.nativeUI.showWaiting("下载文件...");
				this.downloadTask = plus.downloader.createDownload(this.url, {
					filename: "_doc/update/"
				}, function(d, status) {
					if (status == 200) {
						plus.nativeUI.alert("下载成功！");
						console.log("下载成功：" + d.filename);
					} else {
						plus.nativeUI.alert("下载文件失败！");
					}
					plus.nativeUI.closeWaiting();
				})
				this.downloadTask.addEventListener("statechanged", this.onStateChanged, false);
				this.downloadTask.start();
				console.log('this.downloadTask', this.downloadTask)
				uni.setStorageSync('downloadTask', JSON.stringify(this.downloadTask))
			},
			onStateChanged(download, status) {
				console.log('onStateChanged')
				if (download && download.totalSize > 0) {
					this.rate = (download.downloadedSize * 100 / download.totalSize).toFixed(0)
				}

			},
			// 暂停下载任务 
			pauseDownload() {
				this.downloadTask && this.downloadTask.pause()
			},
			// 取消下载任务 
			abortDownload() {
				this.downloadTask && this.downloadTask.abort()
			},
			resumeDownload() {
				console.log('this.downloadTask', this.downloadTask)
				this.downloadTask && this.downloadTask.resume()
			}
		},

	}
</script>

<style>

</style>
