<template>
  <div class="header">
   <div class="background">
        <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <div class="content">
       <div class="avatar">
         <img :src="seller.avatar">
       </div>
      <div class="txt">
        <div class="title">
          <span class="brand"></span><span class="name">{{seller.name}}</span>
          
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div class="more" v-if="seller.supports" @click="showDetail">
        <span>{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="tips horizontal" @click="showDetail">
    <i></i><em>{{seller.bulletin}}</em><span class="icon-keyboard_arrow_right"></span>
    </div>
    <transition name="fade">
      <div v-show="detailShow" class="detail">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="name">{{seller.name}}</h1>
            <div class="star-wrapper">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-if="seller.supports" class="supports">
              <li class="support-item" v-for="(item,index) in seller.supports">
                <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                <span class="text">{{seller.supports[index].description}}</span>
              </li>
            </ul>
            <div class="title">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <div class="bulletin">
              <p class="content">{{seller.bulletin}}</p>
            </div>
          </div>
          <div class="detail-close" @click="hideDetail">
            <i class="icon-close"></i>
          </div>
        </div>
       
      </div>
    </transition>
  </div>
</template>

<script>
 import star from 'components/star/star';
 export default {
    name:'head',
    props: {
      seller:{
        type: Object
      }
    },
    methods:{
      showDetail() {
        this.detailShow = true;
      },
      hideDetail() {
        this.detailShow = false;
      }
    },
    data(){
      return {
        detailShow: false,
        classMap: ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      };
    },
    components:{star},
    computed:{
     
    }
  };
</script>
<style lang="scss" rel="stylesheet/scss">
  @import "../../common/scss/mixin";
  .header{
    background:rgba(7,17,27,0.5);
    position:relative;
    overflow:hidden;
    .background{
      position:absolute;
      height:100%;
      width:100%;
      filter: blur(10px);
      z-index:-1;
      top:0;
      left:0;
    }
    .tips{
      padding:0 px2rem(24);
      background:rgba(7,17,27,.7);
      color:#fff;
      @include dpr('font-size',20);
      font-weight:200;
      height:px2rem(56);
      line-height:px2rem(56);
      position:relative;
      z-index:2;
      i{
       display:block;
       @include bgimg(brand);
      height:px2rem(24);
      width:px2rem(44);
      background-size:px2rem(44) px2rem(24);
      background-repeat: no-repeat;
      }
      em{
        margin:0 px2rem(8);
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        display:inline-block;
        width:px2rem(620);
      }

    }
    .content{
      position:relative;
      z-index:2;
      display:flex;
      padding:px2rem(48) px2rem(24) px2rem(36) px2rem(48);
      background: rgba(7,17,27,0.5);
       .more{
            position:absolute;
            right:px2rem(24);
            bottom:px2rem(36);
            background:rgba(0,0,0,.2);
            @include dpr('font-size',20);
            padding:px2rem(14) px2rem(16);
            border-radius:px2rem(14);
            color:#fff;
            span{
              margin-right:px2rem(4);
            }
          }
        .avatar{
          height:px2rem(128);
          width:px2rem(128);
          border-radius:px2rem(4);
          overflow:hidden;
          img{
            display:block;
            height:100%;
            width:100%;
          }
        }

        .txt{
          margin-left:px2rem(32);
          color:#fff;
          position:relative;
          .title{
            .name{
              @include dpr("font-size",32);
              font-weight:bold;
              @include dpr('line-height',36);
            }
            .brand{
              display:inline-block;
              vertical-align:top;
              @include bgimg(brand);
              height:px2rem(36);
              width:px2rem(60);
              background-size:px2rem(60) px2rem(36);
              background-repeat: no-repeat;
              margin-right:px2rem(12);
            }
          }
         .description{
            @include dpr("font-size",24);
              font-weight:200;
            @include dpr('line-height',24);
            line-height:px2rem(24);
            margin:px2rem(16) 0 px2rem(20);
         }
         .support{
            .txt{
              @include dpr("font-size",20);
              font-weight:200;
              line-height:px2rem(24);
            }
         }
        }

    }
    .detail{
      position: fixed;
      z-index:400;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      overflow: auto;
      backdrop-filter: blur(10px);
      opacity: 1;
      background: rgba(7, 17, 27, 0.8);
      -webkit-overflow-scrolling: touch;
      &.fade-enter-active, &.fade-leave-active{
        transition: all 0.3s;
      }
      &.fade-enter, &.fade-leave-active{
        opacity: 0;
        background: rgba(7, 17, 27, 0);
      }
      .detail-wrapper{
        width: 100%;
        min-height: 100%;
        display:flex;
        flex-direction: column;
        .detail-main{
          flex:1;
          margin-top:px2rem(128);
          padding-bottom:px2rem(128);
          color:#fff;
           .name{
            text-align: center;
            @include dpr("font-size",32);
            font-weight: 700;
            }
            .title{
              display: flex;
              width: 80%;
              margin: 28px auto 24px auto;
              .line{
                flex: 1;
                position: relative;
                @include dpr("top",-14);
                border-bottom: 1px solid rgba(255, 255, 255, 0.2);
              }
              .text{
                padding: 0 px2rem(24);
                font-weight: 700;
                @include dpr("font-size",28);
                }
            }
          .star-wrapper{
            margin-top: px2rem(36);
            padding: px2rem(2) 0;
            text-align: center;
          }
          .supports{
               width: 80%;
            margin: 0 auto;
            .support-item{
              padding: 0 px2rem(24);
              margin-bottom: px2rem(24);
              &:last-child{
                margin-bottom: 0;
              }
              .icon{
                display:inline-block;
                width: px2rem(32);
                height: px2rem(32);
                vertical-align: top;
                margin-right: px2rem(12);
                background-size: px2rem(32) px2rem(32);
                background-repeat: no-repeat;
                &.decrease{
                  @include bgimg('decrease_2');
                }
                &.discount{
                  @include bgimg('discount_2');
                }
                &.guarantee{
                  @include bgimg('guarantee_2');
                }
                &.invoice{
                  @include bgimg('invoice_2');
                }
                &.special{
                  @include bgimg('special_2');
                }
              } 
            }
          }
          .bulletin{
             width: 80%;
            margin: 0 auto;
            .content{
              padding: 0 px2rem(24);
             @include dpr("font-size",24);
             line-height:px2rem(48);
             background: none;
            }
          }
        }
        .detail-close{
          position: relative;
          width: px2rem(64);
          height: px2rem(64);
          margin: px2rem(-128) auto 0;
          clear: both;

          @include dpr("font-size",68);
            color:#fff;
        }  
      }
      
    }
  }
 


</style>