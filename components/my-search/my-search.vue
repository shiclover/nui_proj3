<template>
	
	<view class="my-search-container" :style="{'background-color':bgcolor}" @click="searchBoxHandler">
		<view class="my-search-box" :style="{'border-radius':radius + 'px'}">
			<uni-icons type="search" size="17"></uni-icons>
			<text class="placeholder">搜索</text>
		</view>
	</view>
	
</template>

<script>
	export default {
		props:{
			// 背景颜色
			bgcolor:{
				type:String,
				default:'#EFE4B0',
			},
			
			// 圆角尺寸
			radius:{
				type:Number,
				default:18
			},
		},
		
		
		
		data() {
			return {
				
			};
		},
		
		methods:{
			searchBoxHandler(){
				this.$emit('click')
			}
		}
	}
</script>

<style lang="scss">
	.my-search-container{
		height: 50px;
		// background-color: #EFE4B0;
		display: flex;
		align-items: center;
		padding: 0 10px;
		
		.my-search-box{
			width: 100%;
			height: 36px;
			background-color: #FFFFFF;
			// border-radius: 18px;
			display: flex;
			justify-content: center;
			align-items: center;
			
			.placeholder{
				font-size: 15px;
				margin-left: 5px;
			}
		}
	}
</style>
