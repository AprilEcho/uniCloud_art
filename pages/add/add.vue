<template>
	<view class="add">
		<form @submit="onSubmit">
			<view class="item">
				<input v-model="formValue.title" type="text" name="title" placeholder="请输入标题" />
			</view>
			<view class="item">
				<input v-model="formValue.author" type="text" name="author" placeholder="请输入作者" />
			</view>
			<view class="item">
				<textarea v-model="formValue.content" name="content" placeholder="请输入内容"></textarea>
			</view>
			<view class="item">
				<uni-file-picker v-model="imageValue" fileMediatype="image" mode="grid" @success="uploadSuccess" />
			</view>
			<view class="item">
				<button form-type="reset">重置</button>
				<button form-type="submit" type="primary" :disabled="isDisabled">提交</button>
			</view>
		</form>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				imageValue: [],
				formValue: {
					title: "",
					author: "",
					content: ""
				},
				picurls: []
			};
		},
		computed: {
			isDisabled() {
				return this.formValue.title === "" || this.formValue.author === "" || this.formValue.content === ""
			}
		},
		methods: {
			onSubmit(e) {
				let detail = e.detail.value
				uniCloud.callFunction({
					name: "art_add_row",
					data: {
						detail,
						picurls: this.picurls
					}
				}).then(res => {
					console.log(res)
					uni.showToast({
						title: "发布成功"
					})
					setTimeout(() => {
						uni.reLaunch({
							url: "/pages/index/index"
						})
					}, 1000)
				})
			},
			uploadSuccess(e) {
				this.picurls = e.tempFilePaths
			}
		}
	}
</script>

<style lang="scss" scoped>
	.add {
		padding: 30rpx;

		.item {
			padding-bottom: 20rpx;

			input {
				border: 1rpx solid #eee;
				height: 80rpx;
				padding: 0 20rpx;
			}

			textarea {
				border: 1rpx solid #eee;
				box-sizing: border-box;
				width: 100%;
				height: 200rpx;
				padding: 20rpx;
			}

			button {
				margin-bottom: 20rpx;
			}
		}
	}
</style>