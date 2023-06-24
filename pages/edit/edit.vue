<template>
	<view class="edit">
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
	let id;
	export default {
		data() {
			return {
				picurls: [],
				imageValue: [],
				formValue: {
					title: "",
					author: "",
					content: ""
				}
			};
		},
		onLoad(e) {
			id = e.id
			this.getDetail()
		},
		computed: {
			isDisabled() {
				return this.formValue.title === "" || this.formValue.author === "" || this.formValue.content === ""
			}
		},
		methods: {
			uploadSuccess(e) {
				this.picurls = e.tempFilePaths
			},
			getDetail() {
				uniCloud.callFunction({
					name: "art_get_row",
					data: {
						id
					}
				}).then(res => {
					this.formValue = res.result.data[0]
					if (!this.formValue.picurls) return
					let urls = this.formValue.picurls.map(item => {
						return {
							url: item
						}
					})
					this.imageValue = urls
				}).catch(() => {
					uni.showToast({
						icon: "error",
						title: "文章不存在"
					})
					setTimeout(() => {
						uni.reLaunch({
							url: "/pages/index/index"
						})
					}, 1000)
				})
			},
			onSubmit(e) {
				let _picurls = this.imageValue.map(item => {
					return item.url;
				})
				uniCloud.callFunction({
					name: "art_update_row",
					data: {
						detail: this.formValue,
						picurls: _picurls
					}
				}).then(res => {
					console.log(res)
					uni.showToast({
						title: "修改成功！"
					})
					setTimeout(() => {
						uni.navigateBack()
					}, 800)
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.edit {
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