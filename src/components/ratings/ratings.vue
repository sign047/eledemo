<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <div class="split"></div>
      <ratingselect @select="selectRating" @toggle="toggleContent" :selectType="selectType" :onlyContent="onlyContent"
                    :ratings="ratings"></ratingselect>
      <div class="rating-wrapper">
        <ul>
          <li v-for="rating in ratings" v-show="needShow(rating.rateType, rating.text)" class="rating-item">
            <div class="avatar">
              <img  :src="rating.avatar">
            </div>
            <div class="content">
              <div class="name">
                 <h1 >{{rating.username}}</h1>
                  <div class="time">
                  {{rating.rateTime | formatDate}}
                  </div>
              </div>
              
              <div class="star-wrapper box">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                <span class="icon-thumb_up"></span>
                <div >
                   <span class="item" v-for="item in rating.recommend">{{item}}</span>
                </div>
               
              </div>
             
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import {formatDate} from 'common/js/date';
  import star from 'components/star/star';
  import ratingselect from 'components/ratingselect/ratingselect';
  
  const ALL = 2;
  const ERR_OK = 0;

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: false
      };
    },
    created() {
      this.$http.get('/api/ratings').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.ratings = response.data;
          this.$nextTick(() => {
           this._initScroll();
           console.log(this.scroll);
          });
        }
      });
    },
    mounted(){
       this.$nextTick(() => {
           this._initScroll();
           console.log(this.scroll);
          });
    },
    methods: {
      needShow(type, text) {
         if (this.onlyContent && !text) {
           return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
        }
      },
       _initScroll() {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.ratings, {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      },
      selectRating(type) {
        this.selectType = type;
        this.$nextTick(() => {
           this._initScroll();
        });
      },
      toggleContent() {
        this.onlyContent = !this.onlyContent;
        console.log(this.onlyContent);
        this.$nextTick(() => {
           this._initScroll();
        });
      }
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    components: {
      star,
      ratingselect
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
  .ratings{
      position:absolute;
      top:px2rem(348);
      bottom:0;
      left:0;
      display:block;
      width:100%;
      overflow: hidden;
      .ratings-content{
        // width:100%;
        // display:block;
        //  overflow: hidden;
        .overview{
          padding:px2rem(36) 0;
          display:flex;
          width:100%;
          .overview-left{
            box-sizing:border-box;
            padding:0 px2rem(40) 0 px2rem(48);
            width:px2rem(276);
            flex:0 0 px2rem(276);
            border-right:1px solid #e6e7e8;
            text-align:center;
            .score{
              margin-bottom: px2rem(12);
              @include dpr("font-size",48)
              color: #f90;
            }
            .title{
              margin-bottom: px2rem(16);
              @include dpr("font-size",24);
               @include dpr("line-height",36);
              color: #07111b;
            }
            .rank{
              @include dpr("font-size",20);
              @include dpr("line-height",24);
              color: #93999f;
            }
          }
          .overview-right{
            flex:1;
            padding-left:px2rem(40);
            .score-wrapper,.delivery-wrapper{
              display:flex;
              align-items:center;
              .title{
                    display:block;
                    line-height: px2rem(36);
                    margin-right:px2rem(24);
                    @include dpr("font-size",24);
                    color: #07111b;
              }
              .delivery{
                color: #93999f;
              }
              .score{
                  display:block;
                  margin-left:px2rem(24);
                  @include dpr("font-size",24);
                  color: #f90;
                }
              
            }
            .score-wrapper:nth-child(2){
              margin:px2rem(16) 0;
            }
          }
        }

      }
      .rating-wrapper{
        padding:0 px2rem(36);
        .rating-item{
          display:flex;
          padding: px2rem(36) 0;
          .avatar{
             height:px2rem(56);
            width:px2rem(56);
            border-radius:50%;
            overflow:hidden;
            margin-right:px2rem(24);
            img{
             display:block;
             height:100%;
             width:100%;
            }
          }
          .content{
            .star-wrapper{
              .delivery{
                margin-left:px2rem(12);
              }
            }
            .name{
              display:flex;
              justify-content:space-between;
              align-items:center;
              margin-bottom: px2rem(8);
              @include dpr("line-height",24);
              @include dpr("font-size",20);
              color: #07111b;
            }
            .text{
              margin:px2rem(12) 0 px2rem(16);
              @include dpr("line-height",36);
              color: #07111b;
              @include dpr("font-size",24);
            }
            .recommend{
              display:flex;
              .icon-thumb_up{
                color: #00a0dc;
                display:block;
                @include dpr("font-size",18);
                margin-top:px2rem(8);
              }
              >div{
                .item{
                  margin:0 0 px2rem(8) px2rem(16);
                  display:inline-block;
                  max-width:px2rem(124);
                  box-sizing:border-box;
                  border:1px solid #e6e7e8;
                  padding:px2rem(8) px2rem(12);
                  white-space: nowrap;
                  overflow: hidden;
                  text-overflow: ellipsis;
                }
              }
              

            }
          }
        }
      }
  }
</style>