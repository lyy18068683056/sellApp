<template>
  <!--header-->
  <div class="header">
    <div class="content-wrapper">
        <div class="avatar">
          <img width="64" height="64" :src="seller.avatar" >
        </div>
        <div class="content">
          <div class="title">
                <div class="brand"></div>
                <div class="name">{{seller.name}}</div>
          </div>
          <div class="description">
              {{seller.description}}/{{seller.deliveryTime}}分钟送达
          </div>
          <!--商品的优惠信息  满多少减-->
          <div class="support" v-if="seller.supports">
              <span class="icon" :class="classMap[seller.supports[0].type]"></span>
              <span class="text">{{seller.supports[0].description}}</span>
          </div>
        </div>
      <!--商品的数量-->
        <div v-if="seller.supports" class="support-count" @click="showDetail">
          <span class="count">{{seller.supports.length}}个</span>
          <i class="icon-keyboard_arrow_right"></i>
        </div>
    </div>
    <!--公告-->
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span>
      <span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <!--头部背景-->
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <!---------浮层----------------------浮层--------------------浮层------------------------->
    <div v-show="detailShow" class="detail" transition="fade">
      <div class="detail-wrapper clearfix">
        <div class="detail-main">
          <!--粥品香坊-->
          <h1 class="name">{{seller.name}}</h1>
          <!--五角星-->
          <div class="star-wrapper">
            <star :size="48" :score="seller.score"></star>
          </div>
          <div class="title">
            <div class="line"></div>
            <div class="text">优惠信息</div>
            <div class="line"></div>
          </div>
          <!--优惠列表-->
          <ul v-if="seller.supports" class="supports">
            <li class="support-item" v-for="item in seller.supports">
              <span class="icon" :class="classMap[seller.supports[$index].type]"></span>
              <span class="text">{{seller.supports[$index].description}}</span>
            </li>
          </ul>
          <!--商家公告-->
          <div class="title">
            <div class="line"></div>
            <div class="text">商家公告</div>
            <div class="line"></div>
          </div>
          <!--商家公告下面的文字-->
          <div class="bulletin">
            <p class="content">{{seller.bulletin}}</p>
          </div>
        </div>
      </div>
      <!--关闭按钮-->
      <div class="detail-close" @click="hideDetail">
        <i class="icon-close"></i>
      </div>
    </div>
  </div>
</template>

<script type="type/ecmascript-6">
import star from '../../components/star/star'
  export default{
    props:{
      seller:{
        type:Object
      }
    },
    data(){
      return {
        detailShow:false
      }
    },
    methods:{
      showDetail(){
        this.detailShow=true;
      },
      hideDetail(){
        this.detailShow=false;
      }
    },
    created(){
      this.classMap=['decrease','discount','guarantee','invoice','special'];
    },
   components:{
    star
   }
  };
</script>

<style lang="stylus" type="text/stylus" rel="stylesheet/stylus">
@import '../../common/stylus/mixin'
*
  padding:0
  margin:0
  /*头部*/
.header
  color:#fff
  position :relative
  background :rgba(7,17,27,0.5)
  overflow: hidden
  .content-wrapper
    padding:24px 12px 18px 24px
    font-size: 0
    position :relative
    .avatar
      display: inline-block
      vertical-align:top
      img
        border-radius:2px
    .content
      display: inline-block
      margin-left:16px
      .title
        margin:2px 0 8px 0
        .brand
          display:inline-block
          width:30px
          height:18px
          vertical-align:top
          bg-image('brand')
          background-size:30px 18px
          background-repeat:no-repeat
        .name
          margin-left 6px
          font-size: 16px
          font-weight:bold
          line-height:18px
          display:inline-block
      .description
        margin-bottom: 10px
        line-height: 12px
        font-size:12px
      .support
        .icon
          display :inline-block
          width:12px
          height:12px
          margin-right:4px
          background-size:12px 12px
          background-repeat :no-repeat
          vertical-align:top
          &.decrease
            bg-image('decrease_1')
          &.discount
            bg-image('discount_1')
          &.guarantee
            bg-image('guarantee_1')
          &.invoice
            bg-image('invoice_1')
          &.special
            bg-image('special_1')
        .text
          font-size: 10px
          line-height:12px
    .support-count
      position :absolute
      right: 12px
      bottom:14px
      padding:0 8px
      height:24px
      line-height :24px
      border-radius :14px
      background :rgba(0,0,0,0.2)
      text-align :center
      .count
        vertical-align :top
        font-size :10px
      .icon-keyboard_arrow_right
        font-size :10px
        line-height :24px
        margin-left :2px
  .bulletin-wrapper
    height: 28px
    line-height :28px
    padding:0 22px 0 12px
    white-space :nowrap
    overflow :hidden
    text-overflow :ellipsis
    position: relative
    background :rgba(7,17,27,0.2)
    .bulletin-title
      display :inline-block
      width: 22px
      height:12px
      bg-image('bulletin')
      background-size :22px 12px
      background-repeat :no-repeat
      vertical-align :top
      margin-top :8px
    .bulletin-text
      font-size :10px
      margin:0 4px
      vertical-align :top
    .icon-keyboard_arrow_right
      position :absolute
      right: 12px
      top: 8px
      font-size :10px
  .background
    width:100%
    height:100%
    position :absolute
    top:0
    left:0
    z-index:-1
    filter:blur(10px)
//浮层
  .detail
    position :fixed
    top:0
    left:0
    width:100%
    height:100%
    z-index:100
    overflow :auto
    transition :all 0.5s
    backdrop-filter:b
//    浮层运动出来的样式
    &.fade-transition
      opacity :1
      background :rgba(7,17,27,0.8)
    &.fade-enter,&.fade-leave
      opacity :0
      background :rgba(7,17,27,0)
    .detail-wrapper
      width:100%
      min-height :100%
      .detail-main
        margin-top :64px
        padding-bottom :64px
        .name
          font-size :16px
          font-weight :700
          line-height :16px
          text-align :center
          /*五角星*/
        .star-wrapper
          text-align :center
          margin-top :18px
          padding:2px 0
          /*优惠信息*/
        .title
          width:80%
          margin:28px auto 24px auto
          display :flex
          .line
            flex:1
            position :relative
            top: -6px
            border-bottom :1px solid rgba(255,255,255,0.2)
          .text
            padding:0 12px
            font-size :14px
            font-weight :700
        .supports
          width:80%
          margin:0 auto
          .support-item
            padding:0 12px
            margin-bottom :12px
            font-size :0
            &:last-child
              margin-bottom:0
              /*优惠信息里面的内容*/
            .icon
              display :inline-block
              width: 16px
              height: 16px
              vertical-align :top
              margin-right: 6px
              background-size :16px 16px
              background-repeat :no-repeat
              &.decrease
                bg-image('decrease_1')
              &.discount
                bg-image('discount_1')
              &.guarantee
                bg-image('guarantee_1')
              &.invoice
                bg-image('invoice_1')
              &.special
                bg-image('special_1')
            .text
              line-height :16px
              font-size :12px
              /*商家公告下面的文字*/
        .bulletin
          width:80%
          margin:0 auto
          .content
            font-size :12px
            line-height :24px
            padding:0 12px
    /*关闭按钮*/
    .detail-close
      position :relative
      width: 32px
      height: 32px
      margin:-64px auto 0 auto
      clear:both
      font-size:32px
</style>
