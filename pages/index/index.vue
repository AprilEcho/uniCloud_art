<template>
	<view class="home">
		<view class="content">
			<view class="item" v-for="item in listArr" :key="item._id" @click="goDetail(item._id)">
				<view class="text">
					<view class="title">{{item.title}}</view>
					<view class="info">
						<text>{{item.author}}</text>
						<text><uni-dateformat :threshold="[60000, 3600000]" format="MM-dd"
								:date="item.posttime"></uni-dateformat></text>
					</view>
				</view>
				<view class="pic">
					<image :src="item.picurls?item.picurls[0]:'../../static/images/nopic.jpg'" mode="aspectFill"></image>
				</view>
			</view>
		</view>
		<view class="goAdd" @click="goAdd">
			<uni-icons type="plusempty" size="20" color="#fff"></uni-icons>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				listArr: []
			}
		},
		onLoad() {
			this.getData()
		},
		onReachBottom() {
			this.getData()
		},
		onPullDownRefresh() {
			this.listArr = []
			this.getData()
		},
		methods: {
			getData() {
				uniCloud.callFunction({
					name: "art_get_all",
					data: {
						skip: this.listArr.length
					}
				}).then(res => {
					let oldList = this.listArr;
					let nsList = [...oldList, ...res.result.data]
					this.listArr = nsList
					uni.stopPullDownRefresh()
				})
			},
			goAdd() {
				uni.navigateTo({
					url: "/pages/add/add"
				})
			},
			goDetail(id) {
				uni.navigateTo({
					url: "/pages/detail/detail?id=" + id
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.home {
		.content {

			padding: 30rpx;

			.item {
				display: flex;
				justify-content: space-between;
				padding: 20rpx 0;
				border-bottom: 1rpx solid #eee;

				.text {
					flex: 1;
					display: flex;
					flex-direction: column;
					justify-content: space-between;

					.title {
						text-align: justify;
						padding-right: 20rpx;
						font-size: 44rpx;
						color: #333;
						text-overflow: -o-ellipsis-lastline;
						overflow: hidden;
						text-overflow: ellipsis;
						display: -webkit-box;
						-webkit-line-clamp: 2;
						line-clamp: 2;
						-webkit-box-orient: vertical;
					}

					.info {
						display: flex;
						justify-content: space-between;
						font-size: 28rpx;
						color: #888;

						text {
							padding-right: 20rpx;
						}
					}
				}

				.pic {
					width: 260rpx;
					height: 180rpx;

					image {
						width: 100%;
						height: 100%;
					}
				}
			}
		}

		.goAdd {
			width: 80rpx;
			height: 80rpx;
			background: #2B9939;
			color: #fff;
			display: flex;
			justify-content: center;
			align-items: center;
			border-radius: 50%;
			font-size: 50rpx;
			position: fixed;
			right: 60rpx;
			bottom: 100rpx;
			box-shadow: 0 0 20rpx rgba(43, 153, 57, 0.7);

		}

	}
</style>