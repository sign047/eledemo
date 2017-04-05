	<template>
	<div>
		<div class="cart box" @click="toggleList">
			<div class="cartleft flex horizontal">
				<div class="iconbox" :class="{online:totalCount>0}">
					<div><i class="icon-shopping_cart"></i></div>
					<div class="goods_num" v-show="totalCount>0">{{totalCount}}</div>
				</div>
				<span class="price" :class="{hight:totalPrice>0}">¥ {{totalPrice}}</span>
				<p>另需配送费¥4元</p>
				
			</div>
			<div class="cartright" :class="{enough:isEnough}">
				<p>{{payDesc}}</p>
			</div>
			<div class="ball-container">
        		<div v-for="ball in balls">
			          <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
			            <div class="ball" v-show="ball.show">
			              <div class="inner inner-hook"></div>
			            </div>
			          </transition>
        		</div>
      		</div>
			<transition name="fold">
		        <div class="shopcart-list" v-show="!isFold&&totalCount>0">
		          <div class="list-header">
		            <h1 class="title">购物车</h1>
		            <span class="empty" @click="empty">清空</span>
		          </div>
		          <div class="list-content" ref="listContent">
		            <ul>
		              <li class="food" v-for="food in selectFoods">
		                <span class="name">{{food.name}}</span>
		                <div class="price">
		                  <span>￥{{food.price*food.count}}</span>
		                </div>
		                <div class="cartcontrol-wrapper">
		                  <btn @add="addFood" :food="food"></btn>
		                </div>
		              </li>
		            </ul>
		          </div>
		        </div>
		    </transition>
		</div>
		<transition name="fade">
      			<div class="list-mask" @click="hideList" v-show="!isFold&&totalCount>0"></div>
    	</transition>
    </div>
	</template>
	<style lang="scss" rel="stylesheet/scss">
		@import '../../common/scss/mixin';

		.cart{
			width:100%;
			position:fixed;
			@include dpr("height",96);
			bottom:0;
			left:0;
			z-index:300;
			color:rgba(255,255,255,.4);
			.cartleft{
				background:#141d27;
				justify-content: flex-start;
				padding-left:px2rem(24);
				.iconbox.online{
					div:nth-child(1){
						background:#00a0dc;
						i{
							@include dpr("font-size",48);
							@include dpr("line-height",88);
							color:#fff;
						}
					}
				}
				.iconbox{

					background:#141d27;
					border-radius:50%;
					position:relative;
					@include dpr("top",-12);
					@include dpr("padding",12);
					div:nth-child(1){
						@include dpr("height",88);
						@include dpr("width",88);
						border-radius:50%;
						text-align:center;
						background:#2b343c;
						i{
							@include dpr("font-size",48);
							@include dpr("line-height",88);
							color:#80858a;
						}
					}
					.goods_num{
						position:absolute;
						width:px2rem(48);
						height:px2rem(36);
						line-height: px2rem(36);
						font-weight:700;
						color:#fff;
						top:0;
						right:0;
						@include dpr("font-size",18);
						box-shadow:0 px2rem(4) px2rem(8) 0 rgba(0,0,0,.4);
						background:rgb(240,20,20);
						border-radius:px2rem(36);
						text-align:center;
					}
				}
				.price{
					&.hight{
						color:#fff;
					}
					@include dpr("font-size",32);
					margin:0 px2rem(24) 0 px2rem(36);

				}
				p{
				  @include dpr("font-size",24);
				  @include dpr("line-height",96);
				  
				  font-weight:700;
				  padding-left:px2rem(24);
				  border-left:1px solid rgba(255,255,255,.1);
				}
			}
			.cartright{
				&.enough{
					p{
						background: #00b43c;
    					color: #fff;
					}
				}
				&>p{
					background:#2b333b;
					flex:0 0 px2rem(210);
					width:px2rem(210);
					@include dpr('font-size',24);
					@include dpr("line-height",96);
					color:rgba(255,255,255,.4);
					font-weight:700;
					text-align:center;
				}
				
			}
			.ball-container{
				.ball{
					
					position:fixed;
					left:px2rem(78);
					bottom:px2rem(56);
					z-index:10086;
					transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41);
					
					.inner{
						width:px2rem(40);
						height:px2rem(40);
						border-radius: 50%;
    					background: #00a0dc;
    					transition: all 0.4s linear;
					}
				}
			}

		}
		.list-mask{
			position: fixed;
		    top: 0;
		    left: 0;
		    width: 100%;
		    height: 100%;
		    z-index: 40;
		    transition: all 0.5s;
		    opacity: 1;
		    background: rgba(7,17,27,0.6);
		    &.fade-enter-active, &.fade-leave-active{
		     	transition: all 0.5s;
		    }
      			
		    &.fade-enter, &.fade-leave-active{
		    	opacity: 0;
		      background: rgba(7, 17, 27, 0);
		    }
		      
		}
		.shopcart-list{
			position:absolute;
			top:0;
			left:0;
			z-index:-1;
			width:100%;
			transform:translate3d(0,-100%,0);
			&.fold-enter-active, &.fold-leave-active{
        		transition: all 0.5s;
			}
		    &.fold-enter, &.fold-leave-active{
		        transform: translate3d(0, 0, 0);
		    }
			.list-content{
				background:#fff;
				max-height:px2rem(436);
    			overflow: hidden;
				li{
					padding:0 px2rem(36);
					box-sizing:border-box;
					position:relative;
					.controler{
						right:px2rem(36);
						top:50%;
						transform:translate3d(0,-50%,0);
					}
					.name{
						@include dpr('font-size',28);
						line-height:px2rem(48);
						color:rgb(7,17,27);
					}
				}
			}
			.list-header{
				padding:0 px2rem(36);
				box-sizing:border-box;
				background:#f3f5f7;
				height:px2rem(80);
				width:100%;
				border-bottom:1px solid rgba(7,17,27,.3);
				display:flex;
				align-items:center;
				justify-content:space-between;
				.title{
					@include dpr("font-size",28);
					font-weight:200;
					line-height:px2rem(80);
					color:rgb(7,17,27);
				}
				.empty{
					@include dpr ('font-size',24);
    			    color: #00a0dc;
				}
			}
		}
	</style>
	<script>
	import btn from 'components/cartcontrol/btn';
	import BScroll from 'better-scroll';
	export default {
	name:'cart',
    props: {
      selectFoods: {
        type: Array,
         default() {
          return [];
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    data() {
      return {
        balls: [
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          }
        ],
        dropBalls: [],
        isFold:true
      };
    },
    mounted:function(){
	 this.$nextTick(() => {
      	this._initScroll();
      });
    },
    computed: {
      totalPrice() {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total;
      },
      totalCount() {
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count;
        });
        return count;
      },

      payDesc() {
        if (this.totalPrice === 0) {
          return `￥${this.minPrice}元起送`;
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice;
          return `还差￥${diff}元起送`;
        } else {
          return '去结算';
        }
      },
      isEnough() {
        if (this.totalPrice < this.minPrice) {
        	return false;
        }else{
        	return true;
        }
        
      }
      
    },
    watch: {
	  'isFold'() {
		  this.$nextTick(() => {
		      this._initScroll();
		  });
    	}
	},
    methods: {
     _initScroll() {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.listContent, {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      },
      drop(el) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) {
            ball.show = true;
            ball.el = el;
            this.dropBalls.push(ball);
            return;
          }
        }
      },
      toggleList() {
        if (!this.totalCount) {
          return;
        }
        this.isFold = !this.isFold;
      },
      hideList() {
        this.isFold = true;
      },
      empty() {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        });
        this.isFold=true;
      },
      pay() {
        if (this.totalPrice < this.minPrice) {
          return;
        }
        window.alert(`支付${this.totalPrice}元`);
      },
      addFood(target) {
        this.drop(target);
      },
      beforeDrop(el) {
        let count = this.balls.length;
        while (count--) {
          let ball = this.balls[count];
          if (ball.show) {
            let rect = ball.el.getBoundingClientRect();
            let x = (rect.left - 32);
            let y = -(window.innerHeight - rect.top - 22);
            el.style.display = '';
            el.style.webkitTransform = `translate3d(0,${y}px,0)`;
            el.style.transform = `translate3d(0,${y}px,0)`;
            let inner = el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
            inner.style.transform = `translate3d(${x}px,0,0)`;
          }
        }
      },
      dropping(el, done) {
        /* eslint-disable no-unused-vars */
        let rf = el.offsetHeight;
        this.$nextTick(() => {
          el.style.webkitTransform = 'translate3d(0,0,0)';
          el.style.transform = 'translate3d(0,0,0)';
          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = 'translate3d(0,0,0)';
          inner.style.transform = 'translate3d(0,0,0)';
          el.addEventListener('transitionend', done);
        });
      },
      afterDrop(el) {
        let ball = this.dropBalls.shift();
        if (ball) {
          ball.show = false;
          el.style.display = 'none';
        }
      }
    },
    components: {btn}
  };
</script>
		