	<template>
	<div>
		<div class="goods">
			<div class="menu" ref="menuWrapper">
				<ul>
					<li v-for="(item,index) in goods" :class="{active:currentIndex==index}" @click="changeMenu(index,$event)">
						<span class="kind">{{item.name}}</span>
					</li>
				</ul>
			</div>
			<div class="foods" ref="foodsWrapper">
			    <ul>
			     <li v-for="(item,index) in goods" ref="foodList">       
			     	<h3>{{item.name}}</h3>
					<ul>
						<li @click="selectFood(food,$event)" v-for="food in item.foods" class="food_wraper box">
						  <div class="food_img">
						  	<img  :src="food.icon">
						  </div>
						  <div class="goods_info">
						      <h2 class="name">{{food.name}}</h2>
			                  <p class="desc">{{food.description}}</p>
			                  <div class="extra">
			                    <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
			                  </div>
  							  <div class="price">
  							  <span class="now"><i>￥</i>{{food.price}}</span>
			                    <span class="old" v-if="food.oldPrice">￥{{food.oldPrice}}</span>
                  			  </div>	
						  </div>
						  <btn :food="food" @add="addAction"></btn>
						</li>
					</ul>
			     </li>
			    </ul>
				
			</div>
			<cart ref="shopcart" :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></cart>
		</div>
		 <food @add="addAction" :food="selected" ref="food"></food>
	</div>	
	</template>
	<style lang="scss" rel="stylesheet/scss">
		@import '../../common/scss/mixin';
		.goods{
			position:absolute;
			top:px2rem(348);
			@include dpr('bottom',96);
			overflow: hidden;
			display:flex;
			width:100%;
			.menu{
				flex:0 0 px2rem(160);
				background:#f3f5f7;
				li{
				 background:#f3f5f7;
				 padding:px2rem(40) px2rem(24);
				 border-right:1px solid #ededed;
				 border-bottom: 1px solid #ededed;
				  .kind{
				    overflow: hidden;
				    text-overflow: ellipsis;
				    display: flex;
				    -webkit-line-clamp: 3;
				    -webkit-box-orient: vertical;
				     color:#666;
				     @include dpr("font-size",24);
				     @include dpr("line-height",28);
				     font-weight:200;
				    }
				  
				}
				li.active{
				   background:#fff;
				   position:relative;
			       border-right-color: #fff;
				   &::after{
				        content: "";
					    position: absolute;
					    left: 0;
					    top: 0;
					    bottom: 0;
					    width: .08rem;
					    background-color: #3190e8;
				    }
				}
			}
			.foods{
				flex:1;
				h3{
					border-left:px2rem(4) solid #d9dde1;
					color:#93999f;
					@include dpr('font-size',26);
					background:#f3f5f7;
					padding:px2rem(12) 0 px2rem(12) px2rem(24);
				}
				.food_wraper{
					padding:px2rem(36) 0;
					margin:0 px2rem(36);
					position:relative;
					border-bottom:1px solid rgba(7,17,27,.1);
					.food_img{
					    margin-right:px2rem(20);
						height:px2rem(114);
						width:px2rem(114);
						img{
						 display:block;
						 height:100%;
						 width:100%;
						}
					}
					.goods_info{
					    @include dpr("font-size",20);
					    color:rgb(147,153,159);
					   .name{
					     margin-top:px2rem(4);
					     color:rgb(7,17,27);
					     font-weight:600;
					     @include dpr("font-size",28);
					     @include dpr("line-height",28);
					   }
					   .desc{
					    margin:px2rem(16) 0;
					   }
					   .extra{
					     
					     .count{
					       margin-right:px2rem(24);
					     }
					   }
					   .price{
					   	  .now {
					   	  	color:#f01414;
					   	  	@include dpr("font-size",28);
					   	  	@include dpr("line-height",48);
					   	  	margin-right:px2rem(16);
					   	  	i{
					   	  		@include dpr("font-size",20);
					   	  		font-style:normal;
					   	  	}
					   	  }
					   	  .old{
					   	  	color:#93999f;
					   	  	@include dpr("font-size",20);
					   	  	text-decoration:line-through;
					   	    @include dpr("line-height",48);
					   	  }
					   }
					}
				}
			}
		}
	</style>
	<script>
	    import cart from 'components/shopcart/cart';
	    import btn from 'components/cartcontrol/btn';
	    import BScroll from 'better-scroll';
	    import food from 'components/food/food';
	    const ERR_OK=0;
		export default{
		 name:'goods',
		 data(){
		  return{
		  	goods:[],
		  	listHeight:[],
		  	scrollY:0,
		  	selected:{}
		  }
		 },
		 created(){
		 	 this.$http.get('/api/goods').then((response) => {
        		response = response.body;
		        if (response.errno === ERR_OK) {
		          this.goods = response.data;
		          this.$nextTick(() => {
		            this._initScroll();
		            this._calculateHeight();
		          });
		        }
      		});
		 },
		 methods:{

		 	//选择menu
		 	changeMenu(index,event){
		 		 if (!event._constructed) {
         			 return;
		        }
		        let foodList = this.$refs.foodList;
		        let el = foodList[index];
		        this.foodsScroll.scrollToElement(el, 300);
		 	},
		 	//初始化 scroll组件
		 	_initScroll() {
		        this.meunScroll = new BScroll(this.$refs.menuWrapper, {
		          click: true
		        });

		        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
		          	click: true,
		          	probeType: 3
		        });

		        this.foodsScroll.on('scroll', (pos) => {
		          this.scrollY = Math.abs(Math.round(pos.y));

		        });
		    },
		    //计算滚动区间
		    _calculateHeight() {
		        let foodList = this.$refs.foodList;
		        let height = 0;
		        this.listHeight.push(height);
		        for (let i = 0; i < foodList.length; i++) {
		          let item = foodList[i];
		          height += item.clientHeight;
		          this.listHeight.push(height);
        		}
      		},
      		selectFood(food, event) {
		        if (!event._constructed) {
		          return;
		        }
		        this.selected = food;
		        this.$refs.food.show();
      		},
      		addAction(ele){

      			this._drop(ele);
      		},
      		_drop(ele){

      			 // 体验优化,异步执行下落动画
      			
		        this.$nextTick(() => {
		        	
		          this.$refs.shopcart.drop(ele);
		        });
      		}

		 },
		 computed:{
		 	currentIndex(){
		 		for(let i=0;i<this.listHeight.length;i++){
		 			 let h1=this.listHeight[i],
		 			     h2=this.listHeight[i+1];
		 			if(!h2 ||this.scrollY>=h1&&this.scrollY<h2) return i;
		 		}
		 	},
		 	 selectFoods() {
		        let foods = [];
			    this.goods.forEach((good) => {
			        good.foods.forEach((food) => {
			            if (food.count) {
			              foods.push(food);
			            }
			        });
        		});
        		return foods;
      		}
		 },
		 props:{
		   seller:{
        		type: Object
      		}
		 },
		 components:{cart,btn,food}
		}
	</script>	