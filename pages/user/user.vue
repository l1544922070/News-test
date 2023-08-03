<template>
	<view class="user">
		<view class="top">
			<image src="../../static/image/浏览历史 (1).png"></image>
			<text>浏览记录</text>
		</view>
		
		<view class="content">
			<view class="row" v-for="item in listArr">
				<newsbox :item="item"></newsbox>
			</view>
		</view>
		
		<view class="nohistory" v-if="!listArr.length">
			<image src="../../static/image/暂无足迹.png" mode="widthFix">
			<text>暂无足迹</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				listArr:[]
			}
		},
		onShow(){
			this.getHistoryData()
		},
		methods: {
			//获取浏览记录
			getHistoryData(){
				let hisArr=uni.getStorageSync("historyArr") || []
				this.listArr=hisArr
				//console.log(this.listArr)
			}
		}
	}
</script>

<style lang="scss" scoped>
.user{
	.top{
		padding: 50rpx;
		background: #f8f8f8;
		color: #666;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		image{
			width: 150rpx;
			height: 150rpx;
		}
		text{
			font-size: 38rpx;
			padding-top: 20rpx;
		}
	}
	.content{
		padding: 30rpx;
		.row{
			border-bottom: 1px dashed #efefef;
			padding: 20rpx 0;
		}
	}
	.nohistory{
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		image{
			width: 450rpx;
		}
		text{
			font-size: 26rpx;
			color: #888;
		}
	}
}
</style>
