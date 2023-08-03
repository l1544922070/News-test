<template>
	<view class="detail">
		<view class="title">
			{{detail.title}}
		</view>
		<view class="info">
			<view class="author">编辑:{{detail.author}}</view>
			<view class="time">发布时间：{{detail.posttime}}</view>
		</view>
		<view class="content">
			<rich-text :nodes="detail.content"></rich-text>
		</view>
		<view class="description">
			声明：本站内容均采集于腾讯新闻，若有侵犯请及时联系管理者（513894357@qq.com）。
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				options:null,
				detail:{}
			}
		},
		onLoad(e){
			//console.log(e)
			this.options=e;
			this.getDetail();
		},
		methods: {
			//时间戳转换时间格式
			getTime(t){
				let date = new Date(t * 1000);  // 参数需要毫秒数，所以这里将秒数乘于 1000
				let Y = date.getFullYear() + '-';
				let M = (date.getMonth()+1 < 10 ? '0'+(date.getMonth()+1) : date.getMonth()+1) + '-';
				let D = date.getDate() + ' ';
				let h = date.getHours() + ':';
				let m = date.getMinutes() + ':';
				let s = date.getSeconds();
				return Y+M+D+h+m+s;

			},
			getDetail(){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/detail.php",
					data:this.options,
					success: (res) => {
						//正则表达式将富文本中的图片添加样式使得宽度能完全展示
						res.data.content=res.data.content.replace(/<img/gi,'<img style="max-width:100%"')
						res.data.posttime=this.getTime(res.data.posttime)
						this.detail=res.data
						//触发储存历史记录
						this.saveHistory()
						//点击详情页切换顶部标题
						uni.setNavigationBarTitle({
							title:this.detail.title
						})
					}
				})
			},
			//储存历史记录
			saveHistory(){
				let historyArr=uni.getStorageSync("historyArr") || []
				let item={
					id:this.detail.id,
					classid:this.detail.classid,
					title:this.detail.title,
					picurl:this.detail.picurl,
					looktime:this.getTime(Date.now() / 1000)
				}
				//剔除已存在的历史记录重新录入
				let index=historyArr.findIndex(i=>{
					return i.id==this.detail.id
				})//返回值为已有的记录的index，没有的返回值为-1
				//console.log(index)
				if(index>=0){
					historyArr.splice(index,1)
				}//删除已有
				
				historyArr.unshift(item)
				historyArr=historyArr.slice(0,10)   //只显示最近十条历史记录
				uni.setStorageSync("historyArr",historyArr)
				//console.log(historyArr)
			}
		}
	}
</script>

<style lang="scss" scoped>
.detail{
	padding: 30rpx;
	.title{
		font-size: 46rpx;
		color: #333;
	}
	.info{
		background: #f6f6f6;
		padding: 20rpx;
		font-size: 25rpx;
		color: #666;
		display: flex;
		justify-content: space-between;
		margin: 40rpx 0;
	}
	.content{
		padding-bottom: 50rpx;
		//富文本图片宽度完全显示，小程序不生效
		/*img{
			max-width: 100%;
		}*/
	}
	.description{
		background: #fef0f0;
		font-size: 26rpx;
		padding: 20rpx;
		color: #f89898;
		line-height: 1.8em;
	}
}
</style>
