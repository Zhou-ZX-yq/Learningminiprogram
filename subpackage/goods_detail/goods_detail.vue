<template>
	<view v-if="goods_info.goods_name" class="goods-detail-container">
   <!-- 渲染轮播图 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
		  <swiper-item v-for="(item,i) in goods_info.pics" :key="i">
        <image :src="item.pics_big" @click="preview(i)"></image>
		  </swiper-item>
		</swiper>
    <!-- 商品信息部分 -->
    <view class="goods-info-box">
      <!-- 价格 -->
      <view class="price">
        ￥{{goods_info.goods_price}}
      </view>
      <!-- 信息主体 -->
      <view class="goods-info-body">
        <!-- 商品名 -->
        <view class="name">
          {{goods_info.goods_name}}
        </view>
        <!-- 收藏 -->
        <view class="favi">
          <uni-icons type="star" size="18" color="gray"></uni-icons>
          <text>收藏</text>
        </view>
      </view>
      <!-- 运费 -->
      <view class="yf">
        快递：免运费
      </view>
    <!-- 详细商品图文信息 -->
    <rich-text :nodes="goods_info.goods_introduce"></rich-text>
    </view>
<!-- 商品导航栏 -->
<view class="goods-nav">
  <uni-goods-nav :fill="true"  :options="options" :buttonGroup="buttonGroup"  @click="onClick" @buttonClick="buttonClick" />
</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
        // 商品信息
				goods_info: {},
        options: [{
        			icon: 'shop',
        			text: '店铺',
        			infoBackgroundColor:'#007aff',
        			infoColor:"red"
        		}, {
        			icon: 'cart',
        			text: '购物车',
        			info: 2
        		}],
        	    buttonGroup: [{
        	      text: '加入购物车',
        	      backgroundColor: '#ff0000',
        	      color: '#fff'
        	    },
        	    {
        	      text: '立即购买',
        	      backgroundColor: '#ffa200',
        	      color: '#fff'
        	    }
        	    ]
			};
		},
    onLoad(options) {
      const goods_id=options.goods_id
      this.getGoodsDetails(goods_id);   
    },
    methods: {
      async getGoodsDetails(goods_id){
        const {data: res} = await uni.$http.get('/api/public/v1/goods/detail',{goods_id})
        if(res.meta.status !== 200)
        return uni.$showMsg();
        // 用正则替换的方式解决空白问题以及webp格式的图片无法再ios设备上显示的问题
        res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g, '<img style="display:block;" ').replace(/webp/g, 'jpg')
        
        this.goods_info = res.message;

      },
      preview(i){
        uni.previewImage({
          current:i,
          urls:this.goods_info.pics.map(x => x.pics_big)
        })
      },
      onClick(e){
        if(e.content.text === '购物车'){
          uni.switchTab({
             url: '/pages/cart/cart'
          })
        }
      },
      
    },
    
    
	}
</script>

<style lang="scss">
swiper{
    height: 750rpx;
    
    image{
      width: 100%;
      height: 100%;
    }
}

.goods-info-box{
  padding: 10px;
  padding-right: 0;
  
  .price{
    color: #C00000;
    font-size: 18px;
    margin: 10px 0;
  }
  .goods-info-body{
    display: flex;
    justify-content: space-between;
    .name{
      font-size: 13px;
      magin-right: 10px;
    }
    .favi{
      width: 120px;
      font-size: 12px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      border-left: 1px solid #efefef;
      color: gray;
    }
  }
  .yf{
    font-size: 12px;
    color: gray;
    margin: 10px 0;
  }
}
.goods-nav{
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%
}
.goods-detail-container{
  padding-bottom: 50px;
}

</style>
