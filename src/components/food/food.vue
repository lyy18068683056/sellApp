<template>
  <!--商品详情页-->
  <div class="food" v-show="showFlag" transition="move" v-el:food>
    <div class="food-content">
      <!--图片-->
      <div class="image-header">
        <img :src="food.image">
        <div class="back" @click="hide">
          <i class="icon-arrow_lift"></i>
        </div>
      </div>
      <!--商品详情内容-->
      <div class="content">
        <h1 class="title">{{food.name}}</h1>
        <div class="detail1">
          <span class="sell-count">月售{{food.sellCount}}份</span>
          <span class="rating">好评率{{food.rating}}</span>
        </div>
        <!--价格-->
        <div class="price">
          <span class="now">￥{{food.price}}</span>
          <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
        </div>
        <!--加入购物车-->
        <div class="cartcontrol-wrapper">
          <cartcontrol :food="food"></cartcontrol>
        </div>
        <div @click="addFirst" class="buy" v-show="!food.count || food.count===0" transition="fade">加入购物车</div>
      </div>
      <!--商品介绍-->
      <split v-show="food.info"></split>
      <div class="info" v-show="food.info">
        <h1 class="title">商品介绍</h1>
        <p class="text">{{food.info}}</p>
      </div>
      <!--商品评价-->
      <split></split>
      <div class="rating">
        <h1 class="title">商品评价</h1>
        <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings"></ratingselect>
        <!--商品评价的内容-->
        <div class="rating-wrapper">
          <ul v-show="food.ratings && food.ratings.length">
            <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-item border-1px">
              <!--用户信息-->
              <div class="user">
                <span class="name">{{rating.username}}</span>
                <img class="avatar" width="12" height="12" :src="rating.avatar">
              </div>
              <!--时间-->
              <div class="time">{{rating.rateTime | formatDate}}</div>
              <!---->
              <p class="text">
                <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}">
                  {{rating.text}}
                </span>
              </p>
            </li>
          </ul>
          <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import Vue from 'vue';
  import cartcontrol from '../../components/cartcontrol/cartcontrol.vue';
  import split from '../../components/split/split.vue';
  import ratingselect from '../../components/ratingselect/ratingselect.vue';
  import {formatDate} from '../../common/js/date';

  const ALL=2;

  export default {
    props:{
      food:{
        type:Object
      }
    },
    data(){
      return{
        showFlag:false,
        selectType:ALL,
        onlyContent:true,
        desc:{
          all:'全部',
          positive:'推荐',
          negative:'吐槽'
        }
      }
    },
    methods:{
      show(){
        this.showFlag=true;
        this.selectType=ALL;
        this.onlyContent=true;
        this.$nextTick(() => {
          if(!this.scroll){
              this.scroll=new BScroll(this.$els.food,{
                  click:true
              });
          }else{
              this.scroll.refresh();  //否则就重新刷新
          }
        });
      },
      hide(){
        this.showFlag=false;
      },
      addFirst(event){
        if(!event._constructed){
          return;
        }
        this.$dispatch('cart.add',event.target);
        Vue.set(this.food,'count',1);
      },
      needShow(type,text){   //把点击全部/满意/吐槽与下面的评论相匹配
        if(this.onlyContent && !text){
            return false;
        }
        if(this.selectType===ALL){
            return true;
        }else{
            return type===this.selectType;
        }
      }
    },
    events:{    //监听点击选择的
      'ratetype.select'(type){
        this.selectType=type;
        this.$nextTick(() => {
          this.scroll.refresh();
        })
      },
      'content.toggle'(onlyContent){
        this.onlyContent=onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        })
      }
    },
    filters:{
      formatDate(time){  //定义时间转换
        let date=new Date(time);
        return formatDate(date,'yyyy-MM-dd hh:mm');
      }
    },
    components:{
        cartcontrol,
        split,
        ratingselect
    }
  }
</script>

<style lang="stylus" type="text/stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl';
  .food
    position:fixed
    left:0
    top:0
    bottom:48px
    z-index:30
    width:100%
    background: #fff
    &.move-transition
      transition:all 0.2s linear
      transform:translate3d(0,0,0)
    &.move-enter,&.move-leave
      transform:translate3d(100%,0,0)
    .image-header
      position:relative
      width:100%
      height:0
      padding-top :100%
      img
        position :absolute
        top:0
        left:0
        width:100%
        height:100%
      .back
        position :absolute
        top:10px
        left:0
        .icon-arrow_lift
          display :block
          padding:10px
          font-size :20px
          color:#fff
    .content
      position :relative
      padding:18px
      .title
        line-height :14px
        font-size :14px
        margin-bottom :8px
        font-weight :700
        color:rgb(7,17,27)
//    详细内容
      .detail1
        margin-bottom :18px
        height:10px
        line-height :10px
        font-size :0
        .sell-count,.rating
          font-size :10px
          color:rgb(147,153,159)
          line-height :10px
        .sell-count
          margin-right :12px
          /*价格*/
      .price
        font-weight :700
        line-height :24px
        .now
          margin-right :8px
          font-size :14px
          color:rgb(240,20,20)
        .old
          text-decoration :line-through
          font-size :10px
          color:rgb(147,153,159)
      .cartcontrol-wrapper
        position :absolute
        right: 12px
        bottom:12px
      .buy
        position :absolute
        right: 18px
        bottom: 18px
        z-index :10
        height:24px
        line-height :24px
        padding:0 12px
        box-sizing :border-box
        border-radius :12px
        font-size :10px
        color: #fff
        background :rgb(0,160,220)
        &.fade-transition
          transition :all 0.2s
          opacity :1
        &.fade-enter,&.fade-leave
          opacity :0
    .info
      padding: 18px
      .title
        line-height :14px
        margin-bottom :6px
        font-size :14px
        color:rgb(7,17,27)
      .text
        line-height :24px
        padding:0 8px
        font-size :12px
        color:rgb(77,85,93)
    .rating
      padding-top: 18px
      .title
        line-height :14px
        margin-left :18px
        font-size :14px
        color:rgb(7,17,27)
      .rating-wrapper
        padding:0 18px
        .rating-item
          position :relative
          padding:16px 0
          border-1px(rgba(7,17,27,0.1))
          .user
            position :absolute
            right:0
            top:16px
            line-height :12px
            font-size :0
            .name
              display :inline-block
              vertical-align :top
              font-size :10px
              margin-right :6px
              color:rgb(147,153,159)
          .time
            line-height :12px
            font-size :10px
            color:rgb(147,153,159)
            margin-bottom :6px
          .text
            line-height :16px
            font-size :12px
            color:rgb(7,17,27)
            .icon-thumb_up,.icon-thumb_down
              line-height :16px
              margin-right :4px
              font-size :12px
            .icon-thumb_up
              color:rgb(0,160,220)
            .icon-thumb_down
              color:rgb(147,153,159)
        .no-rating
          padding :16px 0
          font-size :12px
          color:rgb(147,153,159)
</style>
