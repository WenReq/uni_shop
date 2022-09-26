<template>
	<view>
		<!-- 搜索组件 -->
		<view class="search-box">
			<my-search @click="gotoSearch"></my-search>
		</view>
		<!-- 轮播图区域 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<!-- 循环渲染轮播图的 item 项 -->
			<swiper-item v-for="(item, index) in swiperList" :key="index">
				<navigator class="swiper-item" :url="'/subpkg/goods_details/goods_details?goods_id='+item.goods_id">
					<!-- 动态绑定图片的 src 属性 -->
					<image :src="item.image_src"></image>
				</navigator>
			</swiper-item>
		</swiper>
		<!-- 分类导航区域 -->
		<view class="nav-list">
			<view class="nav-item" v-for="(item, index) in navList" :key="index" @click="navClickHandle(item)">
				<image :src="item.image_src" class="nav-img"></image>
			</view>
		</view>
		<!-- 楼层区域 -->
		<view class="floor-list">
			<!-- 楼层 item -->
			<view class="floor-item" v-for="(item, index) in floorList" :key="index">
				<!-- 楼层 title -->
				<image :src="item.floor_title.image_src" class="floor-title"></image>
				<!-- 楼层 content -->
				<view class="floor-img-box">
					<!-- 左侧大图片的盒子 -->
					<navigator class="left-img-box" :url="item.product_list[0].url">
						<image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
					</navigator>
					<!-- 右边 4 个小图片的盒子 -->
					<view class="right-img-box">
						<navigator class="right-img-item" v-for="(item2, index2) in item.product_list" :key="index2" v-if="index2 !== 0" :url="item2.url">
							<image :src="item2.image_src" mode="widthFix" :style="{width: item2.image_width + 'rpx'}"></image>
						</navigator>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 轮播图数据列表
				swiperList: [],
				// 分类数据列表
				navList: [],
				// 楼层的数据列表
				floorList: [],
			};
		},
		onLoad() {
			// 调用方法，获取轮播图的数据
			this.getSwiperList()
			this.getNavList()
			this.getFloorList()
		},
		methods: {
			async getSwiperList() {
				const { data: res } = await uni.$http.get('/api/public/v1/home/swiperdata')
				if(res.meta.status !== 200) return uni.$showMsg()
				this.swiperList = res.message || []
			},
			async getNavList() {
				const { data: res } = await uni.$http.get('/api/public/v1/home/catitems')
				if(res.meta.status !== 200) return uni.$showMsg()
				this.navList = res.message || []
			},
			// nav-item 项被点击时候的事件处理函数
			navClickHandle(item) {
				// 判断点击的是哪个 nav
				if(item.name === '分类') {
					uni.switchTab({
						url: "/pages/cate/cate"
					})
				}
			},
			async getFloorList() {
				const { data: res } = await uni.$http.get('/api/public/v1/home/floordata')
				if(res.meta.status !== 200) return uni.$showMsg()
				res.message.forEach(floor => {
					floor.product_list.forEach(prod => {
						prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
					})
				})
				this.floorList = res.message || []
			},
			gotoSearch() {
				uni.navigateTo({
					url: '/subpkg/search/search'
				})
			},
		}
	}
</script>

<style lang="scss">
swiper {
	height: 330rpx;
	.swiper-item, image {
		width: 100%;
		height: 100%;
	}
}
.nav-list {
	display: flex;
	justify-content: space-around;
	margin: 30rpx 0;
	.nav-img {
		width: 128rpx;
		height: 140rpx;
	}
}
.floor-list {
	.floor-title {
		height: 60rpx;
		width: 100%;
		display: flex;
	}
	.floor-item {
		.right-img-box {
		  display: flex;
		  flex-wrap: wrap;
		  justify-content: space-around;
		}
		
		.floor-img-box {
		  display: flex;
		  padding-left: 10rpx;
		}
	}
}
.search-box {
	position: sticky;
	top: 0;
	z-index: 9;
}
</style>
