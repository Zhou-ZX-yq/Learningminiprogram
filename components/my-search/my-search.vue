<template>
	<view class="search-container" :style=" {'background-color':bgcolor }" @click="searchboxHandler">
		<view class="search-box" :style=" {'border-radius' : radius + 'px'}">
      <uni-icons type="search" size="20"></uni-icons>
		  <text class="placeholder">搜索</text>
		</view>
	</view>
</template>

<script>
	export default {
    props:{
      bgcolor:{
        type: String,
        default: '#C00000'
      },
      
      radius:{
        type: Number,
        default: 18//px
      }
    },
    
		data() {
			return {
				
			};
		},
    methods:{
      searchboxHandler(){
        this.$emit('click')
      }
    }
	}
</script>

<style lang="scss">
.search-container{
  display: flex;
  align-items: center;
  height: 45px;
  padding: 0,10px;
  .search-box{
  background-color: #FFFFFF;
  height: 35px;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  .placeholder{
    font-size: 15px;
    margin-left: 5px;
  }
}
  
}


</style>
