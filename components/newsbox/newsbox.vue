<template>
	<view class="newsbox" @click="goDetail(item)" >
		<view class="pic">
			<image :src="item.picurl" mode="aspectFill"></image>
		</view>
		<view class="text">
			<view class="title">{{item.title}}</view>
			<view class="info" v-if="!item.looktime">
				<text>{{item.author}}</text>
				<text>{{item.hits}}浏览</text>
			</view>
			<view class="info" v-else>
				<text>浏览时间:{{item.looktime}}</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name:"newsbox",
		props:{
			item:{
				type:Object,
				default(){
					return{
						title:"组件默认标题",
						author:"作者名",
						hits:889,
						picurl:"/static/logo.png"
					}
				}
			}
		},
		data() {
			return {
				
			};
		},
		methods:{
			//跳转详情页
			goDetail(item){
				uni.navigateTo({
					url:`/pages/detail/detail?cid=${item.classid}&id=${item.id}`
				})
			}
		}
	}
</script>

<style lang="scss">
.newsbox{
	display: flex;
	.pic{
		width: 230rpx;
		height: 160rpx;
		image{
			width: 100%;
			height: 100%;
		}
	}
	
	.text{
		flex: 1;
		padding-left: 20rpx;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		.title{
			font-size: 36rpx;
			color: #333;
			//多行省略
			overflow: hidden;
			text-overflow: ellipsis;
			display: -webkit-box;
			-webkit-line-clamp: 2;
			-webkit-box-orient: vertical;

		}
		.info{
			font-size: 26rpx;
			color: #999;
			text{
				padding-right: 30rpx;
			}
		}
	}
}
</style>