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
      </div>
      <!--加入购物车-->
      <div class="cartcontrol-wrapper">
        <cartcontrol :food="food"></cartcontrol>
      </div>
      <div @click="addFirst" class="buy" v-show="!food.count || food.count===0" transition="fade">加入购物车</div>
    </div>
  </div>
</template>

<script>
  import BScroll from 'better-scroll';
  import Vue from 'vue';
  import cartcontrol from '../../components/cartcontrol/cartcontrol.vue';
  export default {
    props:{
      food:{
        type:Object
      }
    },
    data(){
      return{
        showFlag:false
      }
    },
    methods:{
      show(){
        this.showFlag=true;
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
      }
    },
    components:{
        cartcontrol
    }
  }
</script>

<style lang="stylus" type="text/stylus" rel="stylesheet/stylus">
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
</style>
