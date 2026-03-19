<!-- 虚拟列表+高度相同瀑布流演示(内置列表写法)(vue) -->

<!-- 请注意1：内置列表写法在微信小程序中部分较高版本调试库会报More than one slot named "cell" are found...的警告并导致开发者工具卡顿，将基础库版本调到2.18.0以下即可。因线上没有控制台打印，因此不会影响线上版本。 -->
<!-- 在微信小程序中如果是vue2推荐使用虚拟列表兼容写法(virtual-list-compatibility-demo)，如果是vue3推荐使用虚拟列表非内置列表写法(virtual-list-no-inner-demo.vue) -->
<!-- 写法简单，通过slot=cell插入所需cell，页面中无直接的for循环，在vue2中兼容性良好 -->
<!-- 在各平台兼容性请查阅https://z-paging.zxlee.cn/module/virtual-list.html -->
<!-- 虚拟列表+高度相同瀑布流演示的其他虚拟列表写法请参考其他虚拟列表写法修改 -->

<template>
	<view class="content">
		<!-- :paging-style="{padding:'10rpx',background:'#f5f5f5'}"用于设置列表上下左右间距和背景色，突出瀑布流展示效果 -->
		<!-- use-virtual-list 代表开启虚拟列表，等同:use-virtual-list="true" -->
		<!-- cell-height-mode="fixed" 代表设置高度相同的虚拟列表，请注意虚拟列表+瀑布流仅支持高度相同虚拟列表模式-->
		<!-- :virtual-list-col="2" 用于告知z-paging当前虚拟列表渲染是2列的，z-paging会根据这个值计算虚拟列表占位区域 -->
		<!-- 请注意，其实高度相同瀑布流本质就是列表容器设置:display: flex;flex-wrap: wrap，然后每个子cell设置宽度50%，这样就可以达到左右换行排列的等高瀑布流效果 -->
		<!-- :inner-list-style="{'display':'flex','flex-wrap':'wrap'}"是内置列表写法独有的，也就是相当于上方的设置列表容器设置:display: flex;flex-wrap: wrap  -->
		<!-- :inner-cell-style="{width:'50%'}"是内置列表写法独有的，也就是相当于上方的给每个子cell设置宽度50%  -->
		<!-- 综上，可以举一反三：在非内置列表写法的虚拟列表中，只要给cell外层包一个list容器设置display: flex;flex-wrap: wrap，然后给cell设置width:50%，即可达成相同效果 -->
		<z-paging 
		ref="paging" 
		:paging-style="{padding:'10rpx',background:'#f5f5f5'}"
		use-virtual-list 
		cell-height-mode="fixed" 
		:virtual-list-col="2" 
		:inner-list-style="{'display':'flex','flex-wrap':'wrap'}" 
		:inner-cell-style="{width:'50%'}" 
		@query="queryList"
		>
			<template #cell="{item,index}">
				<view class="item-container">
					<view class="item" @click="itemClick(item,index)">
						<image class="item-image" mode="aspectFit" src="@/static/boji1.png"></image>
						<view class="item-content">
							<text class="item-title">第{{item.title}}行</text>
							<text style="color: red;margin-left: 10rpx;">虚拟列表</text>
							<view class="item-detail">{{item.detail}}</view>
						</view>
					</view>
				</view>
			</template>
			
		</z-paging>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			queryList(pageNo, pageSize) {
				// 组件加载时会自动触发此方法，因此默认页面加载时会自动触发，无需手动调用
				// 这里的pageNo和pageSize会自动计算好，直接传给服务器即可
				// 模拟请求服务器获取分页数据，请替换成自己的网络请求
				const params = {
					pageNo: pageNo,
					pageSize: pageSize
				}
				this.$request.queryListLong(params).then(res => {
					// 将请求的结果数组传递给z-paging
					this.$refs.paging.complete(res.data.list);
				}).catch(res => {
					// 如果请求失败写this.$refs.paging.complete(false);
					// 注意，每次都需要在catch中写这句话很麻烦，z-paging提供了方案可以全局统一处理
					// 在底层的网络请求抛出异常时，写uni.$emit('z-paging-error-emit');即可
					this.$refs.paging.complete(false);
				})
			},
			itemClick(item, index) {
				console.log('点击了', item.title);
			},
		}
	}
</script>

<style scoped>
	.item-container {
		padding: 10rpx;
	}
	
	.item {
		background-color: white;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: space-between;
		padding: 20rpx 30rpx;
	}
	
	.item-content {
		margin-top: 20rpx;
	}
	
	.header {
		background-color: red;
		font-size: 24rpx;
		text-align: center;
		padding: 20rpx 0rpx;
		color: white;
	}
	
	.item-image {
		height: 150rpx;
		width: 150rpx;
		background-color: #eeeeee;
		border-radius: 10rpx;
	}
	
	.item-title {
		background-color: red;
		color: white;
		font-size: 26rpx;
		border-radius: 5rpx;
		padding: 5rpx 10rpx;
	}
	
	.item-detail {
		margin-top: 10rpx;
		border-radius: 10rpx;
		font-size: 28rpx;
		color: #aaaaaa;
		word-break: break-all;
	}

	.item-line {
		position: absolute;
		bottom: 0rpx;
		left: 0rpx;
		height: 1px;
		width: 100%;
		background-color: #eeeeee;
	}
</style>
