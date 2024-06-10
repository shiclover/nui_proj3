<template>
	<view>
		<view class="search-box">
			<uni-search-bar @confirm="search" @input="input" :radius="100" cancelButton="none"></uni-search-bar>
		</view>
		
		<!-- 搜索建议列表 -->
		<!-- v-if="searchResults.length !== 0" 搜索历史和搜索列表的替换条件 -->
		<view class="sugg-list" v-if="searchResults.length !== 0">
			<view class="sugg-item" v-for="(item,i) in searchResults" :key="i" @click="gotoDetail(item)">
				<view class="goods-name">{{item.goods_name}}</view>
				<uni-icons type="arrowright" size="16"></uni-icons>
			</view>
		</view>
		
		<!-- 搜索历史 -->
		<view class="history-box" v-else>
			<!-- 标题区域 -->
			<view class="history-title">
				<text>历史搜索</text>
				<uni-icons type="trash" size="17" @click="clean"></uni-icons>
			</view>
			
			<!-- 列表区域 -->
			<view class="history-list">
				<uni-tag :text="item" v-for="(item,i) in histories" :key="i" @click="gotoGoodsList(item)"></uni-tag>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 延时器的 timerId
				timer:null,
				// 用来存储搜索关键词
				kw:'',
				// 搜索的结果列表
				searchResults:[],
				// 搜索历史的数组
				historyList:[]
			};
		},
		
		onLoad() {
			this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
		},
		
		methods:{
			// input 输入事件的处理函数
			input(e){
				// 清除 timer 对应的延时器
				clearTimeout(this.timer)
				// 重新启动一个延时器，并把 timerId 赋值给 this.timer
				this.timer = setTimeout(() =>{
					this.kw = e.value
					this.getSearchList()
				},500)
			},
			async getSearchList(){
				if(this.kw.length === 0){
					this.searchResults = []
					return
				}
				
				const {data:res} = await uni.$http.get('/api/public/v1/goods/qsearch',{query:this.kw})
				if(res.meta.status !== 200) return uni.$showMsg()
				this.searchResults = res.message
				
				
				this.saveSearchHistory()
			},
			
			gotoDetail(item){
				// console.log(item.goods_id)
				uni.navigateTo({
					url:'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
				})
			},
			
			saveSearchHistory(){
				// this.historyList.push(this.kw)
				
				// 解决重复的问题
				const set = new Set(this.historyList)
				set.delete(this.kw)
				set.add(this.kw)
				
				this.historyList = Array.from(set)
				
				// 对搜索历史数据，进行持久化的存储
				uni.setStorageSync('kw',JSON.stringify(this.historyList))
			},
			
			clean(){
				this.historyList = []
				uni.setStorageSync('kw','[]')
			},
			
			gotoGoodsList(kw) {
				uni.navigateTo({
					url:'/subpkg/goods_list/goods_list?query=' + kw
				})
			}
		},
		
		// 计算属性
		computed:{
			histories(){
				return [...this.historyList].reverse()
				// console.log([...this.historyList].reverse())
				
			}
		},
	}
</script>

<style lang="scss">
	.search-box{
		// 吸顶效果
		position: sticky;
		top: 0;
		z-index: 999;
	}
	
	.sugg-list{
		padding: 0 5px;
		.sugg-item{
			display: flex;
			align-items: center;
			justify-content: space-between;
			font-size: 12px;
			padding: 13px 0;
			border-bottom: 1px solid #efefef;
			.goods-name{
				// 文本不允许换行（单行文本）
				white-space: nowrap;
				// 溢出部分隐藏
				overflow: hidden;
				// 文本溢出后，使用 ... 代替
				text-overflow: ellipsis;
				margin-right: 3px;
			}
		}
	}
	
	.history-box{
		padding: 0 5px;
		
		.history-title{
			display: flex;
			justify-content: space-between;
			height: 40px;
			align-items: center;
			font-size:13px;
			border-bottom: 1px solid #efefef;
		}
		
		.history-list{
			display: flex;
			flex-wrap: wrap;
		}
		
		.uni-tag{
			margin-top: 5px;
			margin-right: 5px;
		}
	}
	
</style>
