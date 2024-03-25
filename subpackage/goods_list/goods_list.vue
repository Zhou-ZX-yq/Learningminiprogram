<template>
	<view>
  <!-- 渲染商品列表 -->
  <view class="goods-container" >
      <view v-for="(goods,i) in goodsList" :key = "i"  @click="TogoodsDetail(goods)">
       <my-goods :goods="goods"></my-goods>
      </view>
   </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
        //参数请求对象
        queryObj:{
          //查询关键字
          query: '',
          //分类id
          cid: '',
          //页面
          pagenum: 1,
          //页面大小
          pagesize: 10,
        },
          // 商品列表数组
          goodsList: [],
          //为分页实现而获取的总数
          total: 0,
          // 节流阀
          isloading: false
			};
		},
    onLoad(options){
      // console.log(options)
      this.queryObj.cid=options.cid || ''
      this.queryObj.query=options.query || ''
      //获取商品数据
      this.getgoodsList()
    },
    onReachBottom() {
      // 判断是否所有数据都已经展示
      if(this.queryObj.pagenum * this.queryObj.pagesize>=this.total) return uni.$showMsg("数据加载完毕！")
      // 判断是否有正在加载的数据
      if(this.isloading) return
      this.queryObj.pagenum += 1
      
      this.getgoodsList()
    },
    methods:{
      async getgoodsList(cb){
        // 打开节流阀
        this.isloading = true
          const {data:res} = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
          // 关闭节流阀
          this.isloading = false
          // console.log(res)
          if(res.meta.status !== 200) return uni.$showMsg()
          this.goodsList=[...this.goodsList, ...res.message.goods]
          this.total=res.message.total
          cb&&cb()
        },
        TogoodsDetail(item){
          uni.navigateTo({
            url:'/subpackage/goods_detail/goods_detail?goods_id=' + item.goods_id
          })
        }
        
      },
      onPullDownRefresh() {
        // 重置数据
        this.isloading=false
        this.goodsList=[]
        this.queryObj.pagenum=1
        this.total=0
        // 获取新数据
        this.getgoodsList(()=>uni.stopPullDownRefresh())
      }
    }
</script>

<style lang="scss">

</style>
