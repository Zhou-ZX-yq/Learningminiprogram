<template>
	<view>
    <!-- 搜索框 -->
    <view class="searchbox">
      <uni-search-bar :radius="100" @confirm="search" @input="input" cancelButton="none"></uni-search-bar>
    </view>
    <!-- 列表结构 -->
    <view class="List-box"  v-if="SearchList.length !== 0">
      <view class="list-item" v-for="(item,i) in SearchList" :key="i" @click="gotoDetail(item)">
       <view class="nametext">{{item.goods_name}}</view> 
       <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>
    
    <!-- 搜索历史 -->
    <view class="history" v-else>
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="16"@click="cleanhistory"></uni-icons>

      </view>
      <view class="history-list">
        <view class="history-item" v-for="(item2,i2) in histories" :key="i2" @click="Togoodslist(item2)">
          <uni-tag :text="item2"></uni-tag>
        </view>
      </view>
    </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				timer:null,
        kw: '',
        //搜索建议列表
        SearchList:[],
        //搜索历史列表
        Historylist:[]
			};
		},
    onLoad(){
      this.historyList=JSON.parse(uni.getStorageSync('kw') || '[]')
    },
    methods:{
      input(e){
        clearTimeout(this.timer)
        this.timer = setTimeout(()=>{
          this.kw=e.value
          this.getSearchList();
        },500) 
      },
      async getSearchList(){
        if(this.kw.length===0){
        this.SearchList = []
        return
        }
        const {data:res} = await uni.$http.get('/api/public/v1/goods/qsearch',{query:this.kw})
        if(res.meta.status !== 200) return uni.$showMsg()
        this.SearchList=res.message
        this.gethistoryList();
      },
      gotoDetail(item){
        uni.navigateTo({
          url:'/subpackage/goods_detail/goods_detail?goods_id=' + item.goods_id
        })
      },
      gethistoryList(){
        // this.Historylist.push(this.kw)
        
        //数组转化为set对象
        const set=new Set(this.Historylist)
        //删除所有与kw相同的数据
        set.delete(this.kw)
        //添加kw到末尾
        set.add(this.kw)
        //转换回数组
        this.Historylist=Array.from(set)
        //对数据进行本地存储
        uni.setStorageSync('kw',JSON.stringify(this.Historylist)) 
      },
      cleanhistory(){
        this.Historylist = []
        uni.setStorageSync('kw','[]')
      },
      Togoodslist(kw){
        uni.navigateTo({
          url:'/subpackage/goods_list/goods_list?query=' + kw
        })
      }
    },
    computed: {
      histories(){
        return [...this.Historylist].reverse()
      }
    }
    
    
	}
</script>

<style lang="scss">
.searchbox{
position: sticky;
top:0;
z-index: 999;
}

.List-box{
  padding: 0,5px;
  .list-item{
    font-size: 12px;
    padding:13px 0;
    border-bottom: 1px solid #efefef;
    display: flex;
    align-items: center;//纵向居中
    justify-content: space-between;//两端贴边对其
    
  .nametext{
      white-space: nowrap;//不允许换行
      overflow: hidden;//溢出隐藏
      text-overflow: ellipsis;//溢出部分用省略号代替
      margin-right:3px
   }
  }
}



.history{
    padding: 0 8px;
  .history-title{
  display: flex;
  justify-content: space-between;
  height: 40px;
  align-items: center;
    font-size: 14px;
  border-bottom: 1px solid #F5F5F5;
}
.history-list{
  display: flex;
  flex-wrap: wrap;
  .history-item{
    margin-top: 5px;
    margin-right: 5px;
    font-size: 12px;
  }
}
}

</style>
