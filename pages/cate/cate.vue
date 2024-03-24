<template>
	<view>
    <!-- 使用搜索组件 -->
    <!-- <my-search :bgcolor="'black'" :radius="3"></my-search> -->
    <my-search @click="ToSearch"></my-search>
<!-- 实现分类页面 -->
<view class="scroll-view-container">
  
  <!-- 滑动组件左 ; 注意高度是怎么设置的-->
  <scroll-view scroll-y="true" :style="{height: wh + 'px'}" class="left-scroll">
    
    <view>
      <block v-for="(item,i) in cateList" :key="i">
       <view :class="['scroll-left-item', i === active ? ' active' : '']"  @click="activeChange(i)">{{item.cat_name}}</view>
       </block>
    </view>
    
  </scroll-view>

  <!-- 滑动组件右 -->
  <scroll-view scroll-y="true"  :style="{height: wh + 'px'}" class="right-scroll" :scroll-top="scrollTop">
    <!-- 二级分类列表 -->
    <view class="cate-lv2" v-for="(item2,i2) in cateList2" :key="i2">
    <view class="lv2-title">/**{{item2.cat_name}}**/</view>
    <!-- 三级分类列表 -->
    <view class="cate-lv3-list">
      <view class="catelv3-item" v-for="(item3,i3) in item2.children" :key="i3" @click="lv3Togoodslist(item3)">
        <!-- 图片 -->
        <image :src="item3.cat_icon" class="lv3-image" ></image>
        <!-- 文本 -->
        <text>{{item3.cat_name}}</text>
      </view>
      
    </view>
    </view>
  </scroll-view>

   </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title_list:[],
        //获取分类列表
        cateList:[],
        //分类下的二级分类列表
        cateList2:[],
        //记录窗口高度
        wh: 0,
        active:0,
        scrollTop: 0
			};
		},
    onLoad() {
      const sysinfo = uni.getSystemInfoSync();
      this.wh=sysinfo.windowHeight - 50
      this.getCateList();
    },
    methods:{
     async getCateList(){
        const {data:res} = await uni.$http.get('/api/public/v1/categories')
        // console.log(res)
        if(res.meta.status != 200)
        return uni.$showMsg()
        this.cateList=res.message
        //给二级分类列表赋值
        this.cateList2=res.message[0].children
      },
      activeChange(i){
        //改动选中一级列表
        this.active=i;
        //及时修改二级分类列表的值
        this.cateList2 = this.cateList[i].children;
        //修正滑动栏滑动后跳转不回顶端的问题
        this.scrollTop =this.scrollTop === 0 ? 1 : 0
      },
      lv3Togoodslist(item3){
        uni.navigateTo({
          url:'/subpackage/goods_list/goods_list?cid=' + item3.cat_id
        })
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
  .scroll-view-container{
    display: flex;
  }
  .left-scroll{
    width: 120px;
  }
  .scroll-left-item{
    background-color: #F7F7F7;
    text-align: center;
    line-height: 50px;
    font-size: 12;
    
    // 包含子类的大类，即含有子类名active的项会被渲染
    &.active{
      background-color: #FFFFFF;
      position: relative;
      // 为激活项设置更明显的标志，在前方加红色竖线
      &::before{
        content: ' ';
        display: block;
        width: 3px;
        height: 27px;
        background-color: #C00000;
        position: absolute;
        top: 50%;
        left: 0;
        transform: translateY(-50%);
      }
    }
  }
  
  .lv2-title{
    text-align: center;
    font-size: 14px;
    font-weight: bold;
    padding: 15px 0;
  }
.cate-lv3-list{
  display: flex;
  flex-wrap: wrap;
  .catelv3-item{
    width: 33.3%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin-bottom: 2px;
    image {
      width: 60px;
      height: 60px;
    }
    
    text {
      font-size: 12px;
    }
  }
}

</style>
