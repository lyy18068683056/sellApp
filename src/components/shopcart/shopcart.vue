<template>
  <!--购物车页-->
  <div class="shopcart">
    <div class="content" @click.stop.prevent="toggleList">
      <!--左侧-->
      <div class="content-left">
        <div class="logo-wrapper">
          <!--logo-->
          <div class="logo" :class="{'highlight':totalCount>0}">
            <i class="icon-shopping_cart"  :class="{'highlight':totalCount>0}"></i>
          </div>
          <!--选择商品的数量-->
          <div class="num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <!--配送费-->
        <div class="price"  :class="{'highlight':totalPrice>0}">￥{{totalPrice}}</div>
        <!--配送价-->
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <!--右侧-->
      <div class="content-right" @click="pay">
        <div class="pay" :class="payClass">
          {{payDesc}}
        </div>
      </div>
    </div>
    <!--添加商品到购物车产生一个动画小球-->
    <div class="ball-container">
      <div transition="drop" v-for="ball in balls" v-show="ball.show" class="ball">
        <div class="inner inner-hook"></div>
      </div>
    </div>
    <!--购物车详情效果-->
    <div class="shopcart-list" v-show="listShow" transition="fold">
      <div class="list-header">
        <h1 class="title">购物车</h1>
        <span class="empty" @click="empty">清空</span>
      </div>
      <!--购物车内的商品-->
      <div class="list-content" v-el:list-content>
        <ul>
          <li class="food" v-for="food in selectFoods">
            <span class="name">{{food.name}}</span>
            <div class="price">
              <span>￥{{food.price*food.count}}</span>
            </div>
            <div class="cartcontrol-wrapper">
              <cartcontrol :food="food"></cartcontrol>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
  <div class="list-mask" v-show="listShow" transition="fade" @click="hideList"></div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from '../../components/cartcontrol/cartcontrol.vue'
  export default{
    props: {
        selectFoods:{   //        选择的食物作为一个数组
          type:Array,
          default(){
            return [
//                用来存放数组里每一个物品的价格和数量
              {
                  price:60,
                count:1
              }
            ];
          }
        },
      deliveryPrice: {   //      另需配送费
        type: Number,
        default: 0
      },
      minPrice: {  //      多少钱起送
        type: Number,
        default: 0
      }
    },
    data(){
      return{
        balls:[
          {
            show:false
          },
          {
            show:false
          },
          {
            show:false
          },
          {
            show:false
          },
          {
            show:false
          }
        ],
        dropBalls:[],
        fold:true
      }
    },
    computed:{
        totalPrice(){   //        计算总价
            let total=0;
           this.selectFoods.forEach((food) => {
              total += food.price * food.count
           });
           return total;
        },
        totalCount(){   //计算总数量
            let count=0;
            this.selectFoods.forEach((food) => {
                count += food.count;
            })
          return count;
        },
        payDesc(){   //      右边的还差多少起送
        if(this.totalPrice === 0){
           return `￥${this.minPrice}元起送`;
        }else if(this.totalPrice < this.minPrice){
            let diff=this.minPrice - this.totalPrice;
            return `还差￥${diff}元起送`;
        }else{
            return '去结算';
        }
      },
        payClass(){   //计算价格来添加相应的样式
          if(this.totalPrice < this.minPrice){
              return 'no-enough';
          }else{
              return 'enough';
          }
      },
        listShow(){
            if(!this.totalCount){
                this.fold=true;
                return false;
            }
            let show=!this.fold;
//            定义一个滚动
            if(show){
                this.$nextTick(() => {
                    if(!this.scroll){
                      this.scroll=new BScroll(this.$els.listContent,{
                        click:true
                      });
                    }else{
                        this.scroll.refresh();
                    }
                });
            }
              return show;
        }
    },
    methods:{
      drop(el){   //      添加商品抛物线的动画效果
        for(let i=0;i<this.balls.length;i++){
          let ball=this.balls[i];
          if(!this.ball){
            ball.show=true;
            ball.el=el;
            this.dropBalls.push(ball);
            return;
          }
        }
      },
      toggleList(){   //      点击出现购物车里面内容
        if(!this.totalCount){
          return;
        }
        this.fold=!this.fold;
      },
      empty(){   //      清空数据
        this.selectFoods.forEach((food) => {
          food.count=0;
        })
      },
      hideList(){   //点击透明区域 购物车隐藏
        this.fold=true;
      },
      pay(){  //结算
        if(this.totalPrice < this.minPrice){
            return;
        }
        window.alert(`支付${this.totalPrice}元`);
      }
    },
//    transition的动画效果
    transitions:{
      drop:{
        beforeEnter(el) {
          let count =this.balls.length;
          while(count--){
            let ball=this.balls[count];
            if(ball.show){
              let rect=ball.el.getBoundingClientRect();
              let x=rect.left-32;
              let y=-(window.innerHeight - rect.top - 22);
              el.style.display=' ';
              el.style.webkitTransform=`translate3d(0,${y}px,0)`;
              el.style.transform=`translate3d(0,${y}px,0)`;
              let inner=el.getElementsByClassName('inner-hook')[0];
              inner.style.webkitTransform=`translate3d(${x}px,0,0)`;
              inner.style.transform=`translate3d(${x}px,0,0)`;
            }
          }
        },
        enter(el) {
          let rf=el.offsetHeight;
          this.$nextTick(() => {
            el.style.webkitTransform='translate3d(0,0,0)';
            el.style.transform='translate3d(0,0,0)';
            let inner=el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform='translate3d(0,0,0)';
            inner.style.transform='translate3d(0,0,0)';
          });
        },
        afterEnter(el) {
          let ball=this.dropBalls.shift();
          if(ball){
            ball.show=false;
            el.style.display='none';
          }
        }
      }
    },
    components:{
        cartcontrol
    }
  };
</script>

<style lang="stylus" type="text/stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'
  .shopcart
    position:fixed
    left:0
    bottom:0
    z-index :50
    width:100%
    height:48px
    /*底部*/
    .content
      display:flex
      background :#1d1427
      font-size :0
      color:rgba(255,255,255,0.4)
      /*底部左*/
      .content-left
        flex :1
        .logo-wrapper
          display :inline-block
          position :relative
          top: -10px
          margin:0 12px
          padding:6px
          width:56px
          height:56px
          box-sizing :border-box
          vertical-align :top
          border-radius :50%
          background :#1d1427
          /*底部左logo*/
          .logo
            width:100%
            height:100%
            border-radius :50%
            background :#2b343c
            text-align :center
            /*购物车有数量的时候logo亮*/
            &.highlight
              background :rgb(0,160,220)
            .icon-shopping_cart
              font-size :24px
              line-height :44px
              color:#80858a
              &.highlight
                color:rgb(255,255,255)
              /*商品的数量*/
          .num
            position :absolute
            top:0
            right:0
            width: 24px
            height:16px
            line-height :16px
            font-weight :700
            text-align :center
            border-radius :16px
            font-size :9px
            background:rgb(240,20,20)
            box-shadow:0 4px 8px 0 rgba(0,0,0,0.4)
        .price
          display :inline-block
          font-size :16px
          line-height :24px
          font-weight :700
          vertical-align :top
          margin-top:12px
          box-sizing :border-box
          padding-right:12px
          border-right:1px solid rgb(255,255,255,0.1)
          color:rgba(255,255,255,0.4)
          &.highlight
            color:#fff
        .desc
          display :inline-block
          vertical-align :top
          font-size :10px
          line-height :24px
          font-weight :700
          margin:12px 0 0 12px
      .content-right
        flex:0 0 105px
        width:105px
        .pay
          height: 48px
          line-height :48px
          text-align :center
          font-size :12px
          font-weight :700
          background :#2b333b
          /*去结算的样式*/
          &.not-enough
            background :#2b333b
          &.enough
            background :#00b43c
            color:#fff
    .ball-container
      .ball
        position:fixed
        left:32px
        bottom:22px
        z-index:200
        &.drop-transition
          transition:all 0.4s cubic-bezier(0.49,-0.29,0.75,0.41)
          .inner
            width:16px
            height:16px
            border-radius:50%
            background:rgb(0,160,220)
            transition:all 0.4s linear
    .shopcart-list
      position :absolute
      top:0
      left:0
      z-index: -1
      width:100%
      &.fold-transition
        transition:all 0.5s
        transform:translate3d(0,-100%,0)
      &.fold-enter,&.fold-leave
        transform:translate3d(0,0,0)
      .list-header
        height: 40px
        line-height: 40px
        padding:0 18px
        background :#f3f5f7
        border-bottom:1px solid rgba(7,17,27,0.1)
        .title
          float :left
          font-size :14px
          color:rgb(7,17,27)
        .empty
          float :right
          font-size :12px
          color:rgb(0,160,220)

      .list-content
        padding:0 18px
        max-height :217px
        overflow :hidden
        background :#fff
        .food
          position: relative
          padding:12px 0
          box-sizing :border-box
          border-1px(rgba(7,17,27,0.1))
          .name
            line-height :24px
            font-size :14px
            color:rgb(7,17,27)
          .price
            position :absolute
            right: 90px
            bottom :12px
            line-height :24px
            font-size :14px
            font-weight :700
            color:rgb(240,20,20)
          .cartcontrol-wrapper
            position :absolute
            right:0
            bottom:6px
  .list-mask
    position :fixed
    top:0
    left:0
    width:100%
    height:100%
    z-index:40
    backdrop-filter:blur(10px)
    &.fade-transition
      transition:all 0.5s
      opacity :1
      background :rgba(7,17,27,0.6)
    &.fade-enter,&.fade-leave
      opacity :0
      background :rgba(7,17,27,0)

</style>
