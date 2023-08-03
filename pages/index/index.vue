<template>
	<view class="home">
		<view class="scrollNav">
			<scroll-view scroll-x class="navscroll">
				<view class="item" :class="index==navIndex ? 'active' : ''
				" v-for="(item,index) in navArr" 
				@click="clickNav(index,item.id)" 
				:key="item.id">{{item.classname}}</view>
			</scroll-view>
		</view>
		
		<view class="content">
			<view class="row" v-for="item in newsArr" :key="item.id">
				<newsbox :item="item"></newsbox>
			</view>
		</view>
		
		<view class="nodate" v-if="!newsArr.length">
			<image src="../../static/image/nodate .png" mode="widthFix"></image>
		</view>
		
		<view class="loading" v-if="newsArr.length">
			<view v-show="loading==0"></view>
			<view v-show="loading==1">加载中...</view>
			<view v-show="loading==2">没有更多了</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				navIndex:0,//根据索引值给指定导航栏item加上active类名
				navArr:[],//导航栏数组
				newsArr:[],//新闻数组
				currentPage:1,//当前页码
				loading:0//0是默认状态，1是加载中，2是没有更多了
			}
		},
		onLoad() {
			this.getTab();
			this.getNewsDate();
		},
		onReachBottom(){
			//console.log("到底了(；′⌒`)")
			//到底部后再次触底不再发送网络请求，用于优化
			if(this.loading==2){
				return;
			}
			this.currentPage++;
			this.loading=1;
			uni.showLoading({
				title: '加载中'
			});					//显示加载框
			this.getNewsDate();//增加页码后再次加载
		},
		methods: {
			//点击导航切换
			clickNav(index,id){
				this.navIndex=index;
				//console.log(id);
				this.currentPage=1;
				this.newsArr=[];
				this.loading=0;//切换导航后将loading还原为默认值
				this.getNewsDate(id);//给新闻列表传参，展示不同类别新闻
			},
			//获取导航栏数据
			getTab(){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/navlist.php",
					success:Tabres=>{
						//console.log(Tabres)
						this.navArr=Tabres.data
					}
				})
			},
			
			//获取新闻列表数据
			getNewsDate(id=50){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/newslist.php",
					data:{
						cid:id,//必填，新闻类别
						num:8,//非必填，限制显示新闻的数量
						page:this.currentPage//非必填，页码，用于下拉加载更多
					},
					success:Newsres=>{
						//console.log(Newsres)
						if(Newsres.data.length==0 & this.currentPage>1 ){
							this.loading=2
						}
						this.newsArr=[...this.newsArr,...Newsres.data];
						uni.hideLoading();//关闭加载框
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped >
.navscroll{
	height: 100rpx;
	background: #f7f8fa;
	white-space: nowrap;
	//导航栏顶部吸附
	position: fixed;
	top:var(--window-top); //H5调试时顶部默认为浏览器顶部,此为重新设置
	//top: 0;
	left: 0;
	z-index: 10;//权值高的会显示在上面,防止被item遮挡
	.item{
		font-size: 40rpx;
		display: inline-block;
		line-height: 100rpx;
		padding: 0 30rpx;
		color: #333;
		&.active{
			color: #31c27c;
		}
	}
}

.content{
	padding: 30rpx;
	padding-top: 100rpx;//设置顶部高度,防止被导航栏遮挡
	.row{
		border-bottom: 1px dashed #efefef;
		padding: 20rpx 0;
	}
}
.nodate{
	display: flex;
	justify-content: center;
	image{
		width: 360rpx;
	}
}
.loading{
	text-align: center;
	font-size: 26rpx;
	color: #888;
	line-height: 2em;
}
</style>
