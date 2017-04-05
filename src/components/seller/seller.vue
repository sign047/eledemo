<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc border-1px">
          <star :size="36" :score="seller.score"></star>
          <span class="num">({{seller.ratingCount}})</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <div class="favorite" @click="toggleFavorite">
            <span class="icon-favorite" :class="{'active':favorite}"></span>
            <span class="text">{{favoriteText}}</span>
          </div>
        <ul class="remark">
          <li class="block">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
        
      </div>
      <div class="split"></div>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper border-1px">
          <p class="content">{{seller.bulletin}}</p>
        </div>
        <ul v-if="seller.supports" class="supports">
          <li class="support-item border-1px" v-for="(item,index) in seller.supports">
            <span class="icon" :class="classMap[seller.supports[index].type]"></span>
            <span class="text">{{seller.supports[index].description}}</span>
          </li>
        </ul>
      </div>
      <div class="split"></div>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic">
            </li>
          </ul>
        </div>
      </div>
      <div class="split"></div>
      <div class="info">
        <h1 class="title border-1px">商家信息</h1>
        <ul>
          <li class="info-item" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import {saveToLocal, loadFromLocal} from 'common/js/store';
  import star from 'components/star/star';
  
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        favorite: (() => {
          return loadFromLocal(this.seller.id, 'favorite', false);
        })()
      };
    },
    computed: {
      favoriteText() {
        return this.favorite ? '已收藏' : '收藏';
      }
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    watch: {
      'seller'() {
        this.$nextTick(() => {
          this._initScroll();
          this._initPics();
        });
      }
    },
    mounted() {
      this.$nextTick(() => {
        this._initScroll();
        this._initPics();
      });
    },
    methods: {
      toggleFavorite(event) {
        if (!event._constructed) {
          return;
        }
        this.favorite = !this.favorite;
        saveToLocal(this.seller.id, 'favorite', this.favorite);
      },
      _initScroll() {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.seller, {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      },
      _initPics() {
        if (this.seller.pics) {
          let picWidth = 240;
          let margin = 12;
          let width = (picWidth + margin) * this.seller.pics.length - margin;
          this.$refs.picList.style.width = width + 'px';
          this.$nextTick(() => {
            if (!this.picScroll) {
              this.picScroll = new BScroll(this.$refs.picWrapper, {
                scrollX: true,
                eventPassthrough: 'vertical'
              });
            } else {
              this.picScroll.refresh();
            }
            console.log(this.picScroll);
          });
        }
      }
    },
    components: {
      star
     
    }
  };
</script>
<style lang="scss" rel="stylesheet/scss">
  @import '../../common/scss/mixin.scss';
   .split{
    width: 100%;
    height: px2rem(32);
    border-top: 1px solid rgba(7, 17, 27, 0.1);
    border-bottom: 1px solid rgba(7, 17, 27, 0.1);
    background: #f3f5f7;
  }
  .seller{
    position:absolute;
    top:px2rem(348);
    bottom:0;
    left:0;
    display:block;
    width:100%;
    overflow: hidden;
    .overview{
      padding:px2rem(36);
      .title{
        
        margin-bottom:px2rem(16);

      }
      .remark{
        display:flex;
        padding-top:px2rem(36);
        .block{
          flex:1;
          display:flex;
          flex-direction:column;
          align-items:center;
          justify-content:center;
          h2{
            margin-bottom:px2rem(8);
            @include dpr("font-size",20);
            color:rgb(147,153,159);
          }
          .content{
            @include dpr("font-size",20);
            .stress{
              @include dpr("font-size",48);
              @include dpr("line-height",48);
              color:#07111b;
              
            }
          }
        }
        .block:nth-child(2){
          border-left:1px solid rgba(7,17,27,.1);
          border-right:1px solid rgba(7,17,27,.1);
        }
      }
      .favorite{
          position:absolute;
          top:px2rem(36);
          right:px2rem(40);
          .text{
            text-align:center;
            margin-top:px2rem(8);
            display:block;
            @include dpr("font-size",20);
            width:px2rem(66);
          }
          .icon-favorite{
            color: #d4d6d9;
            display:block;
            text-align:center;
            @include dpr("font-size",48);
            &.active{
              color:#f01414;
            }
          }
        }
      .desc{
        display:flex;
        border:1px solid rgba(7,17,27,.1);
        padding-bottom:px2rem(36);
        @include dpr("font-size",20);
        color: #4d555d;
        align-items:center;
        .num{
          margin-left:px2rem(16);
        }
        .text{
          margin-left:px2rem(24);
        }
      }
    }
    .bulletin{
      padding:px2rem(36);
      padding-bottom:0;
      .title{
        margin-bottom: px2rem(16);
        color: #07111b;
        @include dpr("font-size",28)
      }
      .content-wrapper{
        padding:0 px2rem(24);
        .content{
          @include dpr("font-size",24);
          @include dpr("line-height",48);
          color: #f01414;
          font-weight:500;
          border-bottom:1px solid rgba(7,17,27,0.1);
          padding-bottom:px2rem(28);
        }
      }
      .supports{
        
        .support-item{
          display:flex;
          align-items:center;
          padding:px2rem(32) px2rem(24);
          .icon{
            display: block;
            width: px2rem(32);
            height: px2rem(32);
            vertical-align: top;
            margin-right: px2rem(12);
            background-size: px2rem(32) px2rem(32);
            background-repeat: no-repeat;
            &.decrease{
              @include bgimg('decrease_4');
            }
            &.discount{
              @include bgimg('discount_4');
            }
            &.guarantee{
              @include bgimg('guarantee_4');
            }
            &.invoice{
              @include bgimg('invoice_4');
            }
            &.special{
              @include bgimg('special_4');
            }
          }  
          .text{
            display:block;
            @include dpr("font-size",24);
            color: rgb(7, 17, 27);
            
          }
        
        }
      }
    }
    .pics{
       padding: px2rem(36);
      .title{
        margin-bottom:px2rem(24);
        color: #07111b;
        @include dpr("font-size",28);
      }
      .pic-wrapper{
        width: 100%;
        overflow: hidden;
        white-space: nowrap;
        .pic-list{
         display:flex;

          .pic-item{
            flex:0 0 px2rem(240);
            width:px2rem(240);
            height:px2rem(180);
            margin-right:px2rem(12);

            img{
              display:block;
              height:100%;
              width:100%;
            }
          }
          .pic-item:last-child{
            border:none;
          }
        }
        
      }
    }
    .info{
      padding:px2rem(34)  px2rem(36) 0;
      .title{
        padding-bottom: px2rem(24);
        @include dpr("font-size",28);
        position: relative;
        border-bottom:1px solid rgba(7,17,27,.1);
      }
      .info-item{
        padding:px2rem(32) px2rem(24);
        position: relative;
        @include dpr("line-height",32);
        @include dpr("font-size",24);
        border-bottom:1px solid  rgba(7,17,27,.1);
        font-weight:200;
      }
      .info-item:last-child{
        border:none;
      }
    }
  }
</style>