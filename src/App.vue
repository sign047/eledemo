<template>
  <div id='app'>
    <v-header :seller="seller"></v-header>
    <div class="tab horizontal">
      <div class="tab-item flex">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item flex">
        <router-link to="/ratings">评论</router-link>
      </div>
      <div class="tab-item flex">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <router-view :seller="seller"></router-view>
  </div>
</template>

<script>
import head from 'components/header/head.vue';
const ERR_OK=0;
export default {
  name: 'app',
  data(){
    return{
     seller: {}
    }
  },
  components:{
    'v-header':head
  },
  created(){
    this.$http.get('/api/seller').then((res) => {
        res = res.body;
        if (res.errno === ERR_OK) {
          this.seller = Object.assign({}, this.seller, res.data);
        }
      });
  },
  methods(){

  },
  computed(){
     
  }
}
</script>

<style lang="scss" rel="stylesheet/scss">
   @import "./common/scss/mixin";
  .tab{
   border-bottom:1px solid rbga(7,17,27,.2);
   height:px2rem(80);
    .tab-item{
      text-align:center;
      @include dpr('font-size',28);
      @include dpr('line-height',28);
      a{
         color:rgb(77,85,93); 
      }
      .active{
        color:rgb(240,20,20);
      }
    }

  }
</style>
