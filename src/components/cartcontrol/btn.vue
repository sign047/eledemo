	<template>
		<div class="controler">
			<transition name="move">
				<span class="min icon-remove_circle_outline" v-show="food.count>0" @click.stop.prevent="min($event)"></span>	
			</transition>
			<span class="num" v-show="food.count>0">{{food.count}}</span>
			<span class="plus icon-add_circle" @click.stop.prevent="plus($event)"></span>
		</div>
	</template>
	<script>
	    import Vue from 'vue';
		export default{
			name:'btn',
			data(){
				return {

				}
			},
			methods:{
				plus(event){
					if (!event._constructed) {
			          return;
			        }
			        if (!this.food.count) {
			          Vue.set(this.food, 'count', 1);
			        } else {
			          this.food.count++;
			        }
			      
			        let srcEle=event.target ||event.srcElement;
			        
			        this.$emit('add', srcEle);
				},
				min(event){
					if (!event._constructed) {
			          return;
			        }
			        this.food.count--;
				}
			},
			props:{
			  food:{
		        type: Object
		      }
			},
			computed:{
				// Vue.set(this.food, 'count', 0);
			}
		}
	</script>
	<style lang="scss" rel="stylesheet/scss">
	  @import '../../common/scss/mixin';
		.controler{
			position:absolute;
			right:0;
			bottom:px2rem(20);
			display:flex;
			align-items:center;
			
			.num{
				display:block;
				@include dpr("font-size",22);
				@include dpr("line-height",48);
				color:#93999f;
				width:px2rem(48);
				text-align: center;

			}
			.min,.plus{
				display:block;
				color:#00a0dc;
				
				@include dpr("font-size",48);
				@include dpr("line-height",48);
				transform:translate3d(0,0,0) rotate(0);
				opacity:1;
			}
			.min{padding:px2rem(18) 0 px2rem(18) px2rem(18);}
			.plus{padding:px2rem(18) px2rem(18) px2rem(18) 0;}
			.move-enter-active, .move-leave-active {
			  transition: all ease .5s;
			}
			.move-enter, .move-leave-active {
			  opacity: 0;
			  transform:translate3d(px2rem(60),0,0) rotate(180deg);
			}
		}
	</style>