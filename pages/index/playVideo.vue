<template>
	<view class="content">
		<!-- 方案三：mp4可以播放 -->
		<!-- <video src="@/static/video/bee.mp4"></video> -->
		<!-- 方案一：m3u8不能播放 -->
		<!-- <video src="@/static/video/1618380180266.m3u8"></video> -->
	</view>
</template>

<script>
	var _openw = null;
	export default {
		onLoad() {
			// 在html页面上引用videojs-contrib-hls，再绘制到vue页面上
			// 方案二：android可以播放，ios不能播放
			// this.clicked('static/video_videoplayer.html', 'video_videoplayer')
			
			
			// 在html页面上引用阿里播放器，再绘制到vue页面上
			// 方案四：android可以播放，ios不能播放
			this.clicked('static/video_ali.html', 'video_videoplayer')
		},
		methods: {
			clicked(id, t, d) {
				if (_openw) {
					return;
				} // 防止快速点击
				var ws = {
					scrollIndicator: 'none',
					scalable: false,
					popGesture: 'close',
					backButtonAutoControl: 'close',
					titleNView: {
						autoBackButton: true,
						backgroundColor: '#D74B28',
						titleColor: '#CCCCCC'
					}
				};
				t && (ws.titleNView.titleText = t, d || (d = t.toLowerCase()));
				d && (ws.titleNView.buttons = [{
					fontSrc: '_www/helloh5.ttf',
					text: '\ue301',
					fontSize: '22px',
					onclick: 'javascript:openDoc("/doc/' + d + '.html")'
				}]);
				_openw = plus.webview.create(id, id, ws);
				_openw.addEventListener('loaded', function() { //页面加载完成后才显示
					_openw && _openw.show(as, null, function() {
						_openw = null; //避免快速点击打开多个页面
					});
				}, false);
				_openw.addEventListener('hide', function() {
					_openw = null;
				}, false);
				_openw.addEventListener('close', function() { //页面关闭后可再次打开
					_openw = null;
				}, false);
				var currentWebview = this.$scope.$getAppWebview();
				//此对象相当于html5plus里的plus.webview.currentWebview()。在uni-app里vue页面直接使用plus.webview.currentWebview()无效，非v3编译模式使用this.$mp.page.$getAppWebview()
				currentWebview.append(_openw); //一定要append到当前的页面里！！！才能跟随当前页面一起做动画，一起关闭
			}
		}
	}
</script>
