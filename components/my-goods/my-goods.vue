<template>
	<view>
	<!-- 最外层大容器 -->
	<view class="goods-item">
	  <!-- 左侧图片 -->
	  <view class="goods-list-left">
	      <image :src="goods.goods_small_logo || defaultPic" class="goods-image"></image>
	  </view>
	  <!-- 右侧信息 -->
	  <view class="goods-list-right">
	    <!-- 商品名 -->
	    <view class="goodsname">
	      {{goods.goods_name}}
	    </view>
	    <view class="goods-info-box">
	      <!-- 商品价格 -->
	      <view class="goodsprice">
	        ￥{{goods.goods_price | toFix}}
	      </view>
	    </view>
	  </view>
	</view>
	</view>
</template>

<script>
	export default {
    props: {
      goods: {
        type: Object,
        default:{}
      }
    },
		data() {
			return {
        // 默认图片
				defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
			};
		},
    methods:{
    },
    filters: {
      toFix(num){
        return Number(num).toFixed(2)
      }
    },
    

}
</script>

<style lang="scss">
.goods-container{
  padding: 0 5px;
  .goods-item{
    display: flex;
    pad: 5px 0;
    border-bottom: 1px solid #F1F1F1;
    .goods-list-left{
      margin-right: 5px;
      .goods-image{
        display: block;
        height: 100px;
        width: 100px;
      }
    }
    .goods-list-right{
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      .goodsname {
        font-size: 13px;
      }
      .goods-info-box {
        .goodsprice {
          color: #C00000;
        }
      }
    }
    
  }
  
}
</style>
