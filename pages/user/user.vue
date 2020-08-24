<template>
	<view class="container">

		<view class="user-section">
			<image class="bg" src="/static/user-bg.png"></image>
			<view class="user-info-box">
				<view class="portrait-box">
					<image class="portrait" :src="userInfo.avatarUrl || '/static/missing-face.png'"></image>
				</view>
				<view class="info-box">

					<button v-if="!userInfo.nickName"  open-type="getUserInfo" @getuserinfo="wxGetUserInfo" >一键登录</button>
					<button v-else>{{userInfo.nickName}}</button>

				</view>
				<!-- <button open-type="getUserInfo" @getuserinfo='login'>一键登录</button> -->
				<view class="vip" v-if='userInfo.nickName'>
					普通会员
				</view>
			</view>
		</view>
		<view class="cover-container">
			<image class="arc" src="/static/arc.png"></image>
			<view class="history-section icon">
				<list-cell icon="icon-iconfontweixin" iconColor="#e07472" title="我的账户"></list-cell>
				<view class="tj-sction">
					<view class="tj-item">
						<text class="num">128.8</text>
						<text>A余额</text>
					</view>
					<view class="tj-item">
						<text class="num">0</text>
						<text>B余额</text>
					</view>
					<view class="tj-item">
						<text class="num">20</text>
						<text>C余额</text>
					</view>
				</view>
			</view>
			<!-- 订单 -->
			<view class="order-section">
				<view class="order-item" @click="navTo('/pages/order/order?state=0')" hover-class="common-hover" :hover-stay-time="50">
					<text class="yticon icon-shouye"></text>
					<text>全部订单</text>
				</view>
				<view class="order-item" @click="navTo('/pages/order/order?state=1')" hover-class="common-hover" :hover-stay-time="50">
					<text class="yticon icon-daifukuan"></text>
					<text>待付款</text>
				</view>
				<view class="order-item" @click="navTo('/pages/order/order?state=2')" hover-class="common-hover" :hover-stay-time="50">
					<text class="yticon icon-yishouhuo"></text>
					<text>待收货</text>
				</view>
				<view class="order-item" @click="navTo('/pages/order/order?state=4')" hover-class="common-hover" :hover-stay-time="50">
					<text class="yticon icon-shouhoutuikuan"></text>
					<text>退款/售后</text>
				</view>
			</view>
			<!-- 列表 -->
			<view class="history-section icon">
				<list-cell icon="icon-pinglun-copy" iconColor="#5fcda2" title="绑定手机" tips="请绑定手机号"></list-cell>
				<list-cell icon="icon-dizhi" iconColor="#5fcda2" title="地址管理" @eventClick="navTo('/pages/address/address')"></list-cell>
				<list-cell icon="icon-share" iconColor="#9789f7" title="分享" tips="邀请好友赢10万大礼"></list-cell>
				<list-cell icon="icon-shoucang_xuanzhongzhuangtai" iconColor="#54b4ef" title="我的收藏"></list-cell>
				<list-cell icon="icon-shezhi1" iconColor="#e07472" title="设置" border="" @eventClick="navTo('/pages/set/set')"></list-cell>
			</view>
		</view>


	</view>
</template>
<script>
	import listCell from '@/components/mix-list-cell';
	import {
		mapState,
		mapMutations

	} from 'vuex';
	let startY = 0,
		moveY = 0,
		pageAtTop = true;
	export default {
		components: {
			listCell
		},
		data() {
			return {
				coverTransform: 'translateY(0px)',
				coverTransition: '0s',
				moving: false,
				// userInfo:{
					
				// }
			}
		},
		
		onLoad() {
			wx.getSetting({
				success: function(res) {
					if (res.authSetting['scope.userInfo']) {
						wx.getUserInfo({
							success: function(res) {
								console.log(res.userInfo)
								//用户已经授权过
							}
						})
					}
				},
			})
		},
		// #ifndef MP
		onNavigationBarButtonTap(e) {
			const index = e.index;
			if (index === 0) {
				this.navTo('/pages/set/set');
			} else if (index === 1) {
				// #ifdef APP-PLUS
				const pages = getCurrentPages();
				const page = pages[pages.length - 1];
				const currentWebview = page.$getAppWebview();
				currentWebview.hideTitleNViewButtonRedDot({
					index
				});
				// #endif
				uni.navigateTo({
					url: '/pages/notice/notice'
				})
			}
		},
		// #endif
		computed: {
			...mapState(['hasLogin', 'userInfo'])
		},
		methods: {
			...mapMutations(['login']),
			/**
			 * 统一跳转接口,拦截未登录路由
			 * navigator标签现在默认没有转场动画，所以用view
			 */
			navTo(url) {
				if (!this.hasLogin) {
					url = '/pages/public/login';
				}
				uni.navigateTo({
					url
				})
			},
// <<<<<<< HEAD
			wxGetUserInfo(res){
				console.log(res)
				if (!res.detail.iv) {
					uni.showToast({
						title: "您取消了授权,登录失败",
						icon: "none"
					});
					return false;
				}
// =======
// 			toLogin() {
// 				let that =this
// >>>>>>> 7e3fbfdf53c4d98b3f4c731f28c89d118d8e5375
				uni.login({
					provider: 'weixin',
					success: function(loginRes) {
						let code = loginRes.code
// <<<<<<< HEAD
						console.log(loginRes)
						console.log(code)
						// uni.getUserInfo({
						// 	success: info => {
						// 		this.userInfo = info.userInfo
						// 		this.login(info.userInfo)
						// 		console.log(info);
						// 	},
						// 	fail: function(res) {
						// 		console.log(res)
						// 	}
						// })
					},
					fail: function(res) {
						console.log(res)
// =======
						uni.getUserInfo({
							success: info => {
								let xinxi = {
									"encryptedData": info.encryptedData,
									"signature": info.signature,
									"iv": info.iv,
								}
								if(xinxi !=''){
									that.login(xinxi);
								}
							}
						})
// >>>>>>> 7e3fbfdf53c4d98b3f4c731f28c89d118d8e5375
					}
				});
				this.login(res.detail.userInfo)
				console.log('-------用户授权，并获取用户基本信息和加密数据------')
				console.log(res.detail.userInfo);
			},
			// login(res) {
			// console.log(res)
				
			// },
		}
	}
</script>
<style lang='scss'>
	%flex-center {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}

	%section {
		display: flex;
		justify-content: space-around;
		align-content: center;
		background: #fff;
		border-radius: 10upx;
	}

	.user-section {
		height: 480upx;
		padding: 100upx 30upx 0;
		position: relative;

		.bg {
			position: absolute;
			left: 0;
			top: 0;
			width: 100%;
			height: 100%;
		}
	}

	.user-info-box {
		height: 180upx;
		display: flex;
		align-items: center;
		position: relative;
		z-index: 1;

		.portrait {
			width: 130upx;
			height: 130upx;
			border: 5upx solid #fff;
			border-radius: 50%;
		}

		button {
			background: transparent;
			border: none;
			color: #fff;
		}

		button::after {
			border: none !important;
		}

		.username {
			font-size: $font-lg + 6upx;
			color: #fff;
			margin-left: 20upx;
		}

		.info-box {}

		.vip {
			width: 132upx;
			height: 40upx;
			text-align: center;
			line-height: 40upx;
			font-size: 22upx;
			color: #36343c;
			border-radius: 20px;
			background: linear-gradient(left, #f9e6af, #ffd465);
			z-index: 1;
		}
	}

	.cover-container {
		background: $page-color-base;
		margin-top: -150upx;
		padding: 0 30upx;
		position: relative;
		background: #f5f5f5;
		padding-bottom: 20upx;

		.arc {
			position: absolute;
			left: 0;
			top: -34upx;
			width: 100%;
			height: 36upx;
		}
	}

	.tj-sction {
		@extend %section;

		.tj-item {
			@extend %flex-center;
			flex-direction: column;
			height: 140upx;
			font-size: $font-sm;
			color: #75787d;
		}

		.num {
			font-size: $font-lg;
			color: $font-color-dark;
			margin-bottom: 8upx;
		}
	}

	.order-section {
		@extend %section;
		padding: 28upx 0;
		margin-top: 20upx;

		.order-item {
			@extend %flex-center;
			width: 120upx;
			height: 120upx;
			border-radius: 10upx;
			font-size: $font-sm;
			color: $font-color-dark;
		}

		.yticon {
			font-size: 48upx;
			margin-bottom: 18upx;
			color: $uni-color-primary;
		}

		.icon-shouhoutuikuan {
			font-size: 44upx;
		}
	}

	.history-section {
		padding: 30upx 0 0;
		margin-top: 20upx;
		background: #fff;
		border-radius: 10upx;

		.sec-header {
			display: flex;
			align-items: center;
			font-size: $font-base;
			color: $font-color-dark;
			line-height: 40upx;
			margin-left: 30upx;

			.yticon {
				font-size: 44upx;
				color: #5eba8f;
				margin-right: 16upx;
				line-height: 40upx;
			}
		}

		.h-list {
			white-space: nowrap;
			padding: 30upx 30upx 0;

			image {
				display: inline-block;
				width: 160upx;
				height: 160upx;
				margin-right: 20upx;
				border-radius: 10upx;
			}
		}
	}
</style>
