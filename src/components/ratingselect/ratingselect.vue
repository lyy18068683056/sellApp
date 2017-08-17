<template>
  <div class="ratingselect">
    <!--评价类型-->
    <div class="rating-type border-1px">
      <span @click="select(2,$event)" class="block positive" :class="{'active':selectType===2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
      <span @click="select(0,$event)" class="block positive" :class="{'active':selectType===0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
      <span @click="select(1,$event)" class="block negative" :class="{'active':selectType===1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
    </div>
    <!--只看有内容的评价-->
    <div @click="toggleCount" class="switch" :class="{'on':onlyContent}">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  const POSITIVE=0;
  const NEGATIVE=1;
  const ALL=2;

    export default{
      props:{
        ratings:{
          type:Array,
          default(){
            return [ ];
          }
        },
        selectType:{  //选择类型
          type:Number,
          default:ALL
        },
        onlyContent:{  //可读还是不可读
          type:Boolean,
          default:false
        },
        desc:{   //描述
          type:Object,
          default(){
            return {
              all:'全部',
              positive:'满意',
              negative:'不满意'
            }
          }
        }
      },
      computed:{
        positives(){   //满意的评论长度
          return this.ratings.filter((rating) => {
            return rating.rateType===POSITIVE;
          })
        },
        negatives(){
          return this.ratings.filter((rating) => {
            return rating.rateType===NEGATIVE;
          })
        }
      },
      methods:{
        select(type,event){   //点击选择全部/推荐/吐槽
          if(!event._constructed){
              return;
          }
          this.selectType=type;
          this.$dispatch('ratetype.select',type);  //监听  子组件告诉父组件的变化
        },
        toggleCount(event){
          if(!event._constructed){
            return;
          }
          this.onlyContent=!this.onlyContent;
          this.$dispatch('content.toggle',this.onlyContent);
        }
      }
    };
</script>

<style lang="stylus" type="text/stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl';

  .ratingselect
    .rating-type
      padding:18px 0
      margin:0 18px
      border-1px(rgba(7,17,27,0.1))
      font-size :0
      .block
        display :inline-block
        padding:8px 12px
        margin-right :8px
        border-radius :1px
        color:rgb(77,85,93)
        font-size :12px
        line-height :16px
        &.active
          color:#fff
        .count
          font-size :8px
          margin-left :2px
        &.positive
          background :rgba(0,160,220,0.2)
          &.active
            background :rgb(0,160,220)
        &.negative
          background :rgba(77,85,93,0.2)
          &.active
            background:rgb(77,85,93)
    .switch
      padding:12px 18px
      line-height :24px
      border-bottom :1px solid rgba(7,17,27,0.1)
      color:rgb(147,153,159)
      font-size :0
      &.on
        .icon-check_circle
          color:#00c850
      .icon-check_circle
        display :inline-block
        vertical-align :top
        font-size :24px
        margin-right :4px
      .text
        display :inline-block
        vertical-align :top
        font-size :12px
</style>
