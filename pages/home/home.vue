<template>


	<view>
    <!-- 顶端搜索框 -->
  <view class="searchBox">
    <my-search @click="ToSearch"></my-search>
  </view>
    
		<!-- 轮播图结构 (快速生成：uswiper)-->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item,i) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpackage/goods_detail/goods_detail?goods_id=' + item.goods_id">
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
    
    <!-- 分类导航区域 -->
    <view class="navi-list">
      <view v-for="(item,i) in naviList" :key="i" @click="navClickHandler(item)">
        <image :src="item.image_src" class="navi-item"></image>
      </view>
    </view>
    
    <!-- 渲染楼层 -->
    <view class="Floor">
    <!-- 楼层item -->
    <view class="floor-list" v-for="(item,i) in floorList" :key="i">
      <!-- 楼层标题 -->
        <image class="floor_title" :src="item.floor_title.image_src"></image>
        <!-- 楼层产品 -->
        <view class="floor-box">
          <!-- 左侧的大图片 -->
          <navigator class="left-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
          </navigator>
          <!-- 右侧图片 -->
          <view class="right-box">
            <navigator class="right-item-box" v-for="(item2,i2) in item.product_list" :key="i2" v-if = "i2 !== 0" :url="item2.url">
              <image :src="item2.image_src" mode="widthFix" :style="{width:item2.image_width + 'rpx'}"></image>
              
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
				//轮播图数据
				swiperList:[],
        //分类导航数据
        naviList:[],
        //楼层数据
        floorList:[]
			};
		},
    onLoad(){
      this.getSwiperList();
      this.getnaviList();
      this.getfloorList();
    },
    methods: {
      //这里的uni.$http.get返回的是一个promise类型的数据，用await去修饰以便使用，同时要用async修饰该方法
       async getSwiperList(){
         const {data:res} = await uni.$http.get('/api/public/v1/home/swiperdata')
         // 如果数据请求错误
         if(res.meta.status != 200)
           return uni.$showMsg()
         //请求成功
         this.swiperList=res.message
       },
       async getnaviList(){
         const {data:res} = await uni.$http.get('/api/public/v1/home/catitems');
         if(res.meta.status != 200)
           return uni.$showMsg()
           this.naviList=res.message
       },
       navClickHandler(item){
         // console.log(item)
         if(item.name === "分类"){
           uni.switchTab({
             url:'/pages/cate/cate'
           })
         }
       },
      async getfloorList(){
        const {data:res} =await uni.$http.get('/api/public/v1/home/floordata');
        if(res.meta.status != 200)
        return uni.$showMsg()
        //获取到的数据有自己的url地址，但并不是我们想要的地址，所以先对数据进行处理
        res.message.forEach(floor => {
          floor.product_list.forEach(prod => {
           prod.url = '/subpackage/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
          })
        })
        this.floorList=res.message
      },
      ToSearch(){
        uni.navigateTo({
          url: '/subpackage/search/search'
        })
      }
 
    }
	}
</script>

<style lang="scss">
  swiper {
    height: 330rpx;
    .swiper-item,
    image {
      width: 100%;
      height: 100%;
    }
  }
  .navi-list{
    display: flex;
    justify-content: space-around;
    margin: 15px,0;
  }
  .navi-item{
    width: 128rpx;
    height: 140rpx;
  }
  .floor_title{
    width: 100%;
    height: 60rpx;
    display: flex;
  }
  .right-box{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }
  .floor-box{
    display: flex;
    padding-left: 10rpx;
  }
  .searchBox{
    position: sticky;
    
    top: 0;
    
    z-index: 999;//防止轮播图覆盖
  }

</style>
