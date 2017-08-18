<template>
  <!--评价页-->
  <div class="ratings" v-el:ratings>
    <!--综合评分部分-->
    <div class="ratings-content">
      <div class="overview">
        <!--综合评分左边部分-->
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}</div>
        </div>
        <!--综合评分右边部分-->
        <div class="overview-right">
          <!--服务态度-->
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <!--商品评分-->
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <!--送达时间-->
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <!--两个边框-->
      <split></split>
      <!--评价点击选择部分-->
      <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="ratings">
      </ratingselect>
      <!--评价内容-->
      <div class="rating-wrapper">
        <ul>
          <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in ratings" class="rating-item border-1px">
            <!--头像-->
            <div class="avatar">
              <img width="28" height="28" :src="rating.avatar" >
            </div>
            <!--用户信息-->
            <div class="content">
              <!--用户名-->
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <!--评价内容-->
              <p class="text">{{rating.text}}</p>
              <!--比较赞的食物-->
              <div class="recommend" v-show=" rating.recommend && rating.recommend.length">
                <span class="icon-thumb_up"></span>
                <span class="item" v-for="item in rating.recommend">{{item}}</span>
              </div>
              <!--时间-->
              <div class="time">
                <!--需要过滤时间 在js中-->
                {{rating.rateTime | formatDate}}
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../../components/star/star.vue';
  import split from '../../components/split/split.vue';
  import ratingselect from '../../components/ratingselect/ratingselect.vue';   //评价点击选择的
  import {formatDate} from '../../common/js/date';
  import BScroll from 'better-scroll';

  const ALL=2;
  const ERR_OK=0;

  export default{
    props:{   //用props方法接受app.vue里面的seller
      seller:{
        type:Object
      }
    },
    data(){
      return{
        ratings:[],
        selectType:ALL,
        onlyContent:true,
      }
    },
    created(){
      this.$http.get('/api/ratings').then((response) => {
        response=response.body;
        if(response.errno===ERR_OK){
          this.ratings=response.data;
          this.$nextTick(() => {
//            console.log(this.$els.ratings)
            this.scroll=new BScroll(this.$els.ratings,{   //产生滚动条 为整个ratings页面
              click:true    //传入click:true就是为了让页面中可以存在点击事件
            });
          });
        }
      })
    },
    methods:{
      needShow(type,text){   //列表删选   把点击全部/满意/吐槽与下面的评论相匹配
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
      star,
      split,
      ratingselect
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl';
  .ratings
    position :absolute
    top: 174px
    bottom:0
    left:0
    width:100%
    overflow :hidden
    .overview
      display :flex
      padding :18px 0
      .overview-left
        flex :0 0 137px
        width:137px
        border-right :1px solid rgba(7,17,27,0.1)
        text-align :center
        padding:6px 0
        @media only screen and (max-width:320px)
          flex:0 0 120px
          width:120px
        .score
          line-height :28px
          font-size :24px
          color:rgb(255,153,0)
          margin-bottom :6px
        .title
          line-height :12px
          font-size :12px
          color:rgb(7,17,27)
          margin-bottom :8px
        .rank
          font-size :10px
          line-height :10px
          color:rgb(147,153,159)
      .overview-right
        flex:1
        padding:6px 0 6px 24px
        @media only screen and (max-width:320px)
          padding-left :6px
        .score-wrapper
          margin-bottom :8px
          font-size :0
          .title
            display :inline-block
            line-height :18px
            vertical-align :top
            font-size :12px
            color:rgb(7,17,27)
          .star
            display :inline-block
            vertical-align :top
            margin :0 12px
          .score
            display :inline-block
            vertical-align :top
            line-height :18px
            font-size :12px
            color:rgb(255,153,0)
        .delivery-wrapper
          font-size :0
          .title
            line-height :18px
            font-size :12px
            color:rgb(7,17,27)
          .delivery
            font-size :12px
            color:rgb(147,153,159)
            margin-left :12px
            /*评价内容*/
    .rating-wrapper
      padding :0 18px
      .rating-item
        display: flex
        padding:18px 0
        border-1px(rgba(7,17,27,0.1))
        .avatar
          flex :0 0 28px
          width: 28px
          margin-right :12px
          img
            border-radius :50%
        .content
          position :relative
          flex :1
          .name
            line-height :12px
            font-size :10px
            color:rgb(7,17,27)
            margin-bottom :4px
          .star-wrapper
            margin-bottom :6px
            font-size :0
            .star
              display :inline-block
              vertical-align :top
              margin-right :6px
            .delivery
              display :inline-block
              vertical-align :top
              line-height :12px
              font-size :10px
              color:rgb(147,153,159)
//              评价文本样式
          .text
            font-size :12px
            line-height :18px
            color:rgb(7,17,27)
            margin-bottom :8px
          .recommend
            line-height :16px
            font-size :0
            .icon-thumb_up,.item
              display :inline-block
              margin :0 8px 4px 0
              font-size :9px
            .icon-thumb_up
              color:rgb(0,160,220)
            .item
              padding :0 6px
              border:1px solid rgba(7,17,27,0.1)
              border-radius :1px
              color:rgb(147,153,159)
              background :#fff

          /*时间*/
          .time
            position :absolute
            top:0
            right:0
            line-height :12px
            font-size :10px
            color:rgb(147,153,159)
</style>
