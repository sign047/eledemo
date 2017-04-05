<template>
  <div class="star" :class="starType">
    <span v-for="(itemClass,index) in itemClasses" :class="itemClass" class="star-item" key="index"></span>
  </div>
</template>

<script type="text/ecmascript-6">
  const LENGTH = 5;
  const CLS_ON = 'on';
  const CLS_HALF = 'half';
  const CLS_OFF = 'off';

  export default {
    props: {
      size: {
        type: Number
      },
      score: {
        type: Number
      }
    },
    computed: {
      starType() {
        return 'star-' + this.size;
      },
      itemClasses() {
        let result = [];
        let score = Math.floor(this.score * 2) / 2;
        let hasDecimal = score % 1 !== 0;
        let integer = Math.floor(score);
        for (let i = 0; i < integer; i++) {
          result.push(CLS_ON);
        }
        if (hasDecimal) {
          result.push(CLS_HALF);
        }
        while (result.length < LENGTH) {
          result.push(CLS_OFF);
        }
        return result;
      }
    }
  };
</script>

<style lang="scss" rel="stylesheet/scss">
  @import "../../common/scss/mixin.scss";
  .star{
    font-size: 0;
    .star-item{
      display: inline-block;
      background-repeat: no-repeat;
    }
    
    &.star-48{
      .star-item{
        width: px2rem(40);
        height: px2rem(40);
        margin-right: px2rem(38);
        background-size: px2rem(40) px2rem(40);
        &:last-child{
          margin-right: 0;
        }
        &.on{
           @include bgimg('star48_on');
        }
        &.half{
           @include bgimg('star48_half');
        }
        &.off{
           @include bgimg('star48_off');
        }
      }
    }
    &.star-36{
      .star-item{
        width: px2rem(30);
        height: px2rem(30);
        margin-right: px2rem(14);
        background-size: px2rem(30) px2rem(30);
        &:last-child{
          margin-right: 0;
        }
        &.on{
          @include bgimg('star36_on');
        }
        &.half{
           @include bgimg('star36_half');
        }
        &.off{
          @include bgimg('star36_off');
        }
      }
    }
    &.star-24{
      .star-item{
        width: px2rem(20);
        height: px2rem(20);
        margin-right: 3px;
        background-size: px2rem(20) px2rem(20);
        &:last-child{
          margin-right: 0;
        }
        &.on{
           @include bgimg('star24_on');
        }
        &.half{
           @include bgimg('star24_half');
        }
        &.off{
           @include bgimg('star24_off');
        }
      }
    }
  }       
</style>