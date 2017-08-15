<template>
  <div class="goods">
    <!--左侧菜单-->
    <div class="menu-wrapper" v-el:menu-wrapper>
      <ul>
        <li v-for="item in goods" class="menu-item" :class="{'current':currentIndex === $index}"
        @click="selectMenu($index,$event)">
          <span class="text border-1px">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>
            {{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <!--右侧菜单-->
    <div class="foods-wrapper" v-el:foods-wrapper>
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item border-1px">
              <div class="icon">
                <img :src="food.icon" width="57" height="57">
              </div>
              <div class="content">
                <!--商品名字-->
                <h2 class="name">{{food.name}}</h2>
                <!--商品描述-->
                <p class="desc">{{food.description}}</p>
                <!--月售  好评率-->
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <!--现价-->
                  <span class="now">￥{{food.price}}</span>
                  <!--原价-->
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <!--数量加减-->
                <div class="cartcontroll-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <!--购物车-->
    <shopcart v-ref:shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
  </div>
</template>

<script type="text/ecmascript-6">
import BScroll from 'better-scroll';
import shopcart from 'components/shopcart/shopcart'
import cartcontrol from '../../components/cartcontrol/cartcontrol.vue'

const ERR_OK=0;

  export default{
    props:{
      seller:{
        type:Object
      }
    },
    data(){
      return {
        goods:[],
        listHeight:[],
        scrollY:0
      };
    },
    computed:{
//        计算左边栏的索引   让右边的scrollY与左边的索引相对应
      currentIndex(){
        for(let i=0;i<this.listHeight.length;i++){
            let height1 = this.listHeight[i];
            let height2 = this.listHeight[i+1];
//            console.log('height1'+ height1)
//            console.log('height2'+ height2)
            if(!height2 || (this.scrollY >= height1 && this.scrollY < height2)){
                return i;
            }
        }
        return 0;
      },
//      购物车里面的商品
      selectFoods(){
        let foods=[];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if(food.count){
              foods.push(food);
            }
          })
        });
        return foods;
      }
    },
    created(){
      this.classMap=['decrease','discount','guarantee','invoice','special'];
      this.$http.get('/api/goods').then((response) => {
        response=response.body;
        if(response.errno === ERR_OK){
          this.goods=response.data;
          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
          })
        }
      });
    },
    methods:{
//        点击左边栏对应的索引
  		//在PC端晕倒点击触发两次的问题,在参数里添加event 写一个判断
      selectMenu(index,event){
          if(!event._constructed){
              return;
          }
          let foodList = this.$els.foodsWrapper.getElementsByClassName('food-list-hook');
          let el = foodList[index];
          this.foodsScroll.scrollToElement(el,300);
//            console.log(index)
      },
      _initScroll(){
//          初始化BScroll  让menuwrapper和foodswrapper可以滚动
        this.menuScroll=new BScroll(this.$els.menuWrapper,{
//            点击左侧的时候传入的属性
            click:true
        });
        this.foodsScroll=new BScroll(this.$els.foodsWrapper,{
//            scroll在滚动过程中告诉实时的位置
						click:true,
            probeType:3
        });
//        用foodsScroll去监听一个事件
        this.foodsScroll.on('scroll',(pos) => {
            this.scrollY = Math.abs(Math.round(pos.y));
//            console.log(this.scrollY)
        })
      },
//      每个区块对应的高度
      _calculateHeight(){
//          找到每个li  每个区块的
        let foodList= this.$els.foodsWrapper.getElementsByClassName('food-list-hook');
        let height=0;
        this.listHeight.push(height);
        for(let i=0;i<foodList.length;i++){
            let item=foodList[i];
            height += item.clientHeight;
          this.listHeight.push(height);
        }
      },
//      添加商品产生抛物线小球
//      体验优化,异步执行下落动画
      _drop(target) {
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target);
        })
      }
    },
    components:{
      shopcart,
      cartcontrol
    },
    events:{
      'cart.add'(target) {
        this._drop(target);
      }
    }
  }
</script>

<style lang="stylus" type="text/stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .goods
    position:absolute
    top: 174px
    bottom:46px
    width:100%
    display :flex
    overflow :hidden
    /*左侧菜单*/
    .menu-wrapper
      flex:0 0 80px
      width:80px
      background :#f3f5f7
      .menu-item
        display :table
        height: 54 px
        width:56px
        line-height: 14px
        padding:0 12px
        &.current
          position :relative
          z-index:10
          margin-top :-1px
          background :#fff
          font-weight :700
          .text
            border-none()
        .icon
          display :inline-block
          width:12px
          height:12px
          margin-right:2px
          background-size:12px 12px
          background-repeat :no-repeat
          vertical-align:top
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          display :table-cell
          width: 56px
          font-size:12px
          vertical-align :middle
          border-1px(rgba(7,17,27,0.1))
    .foods-wrapper
      flex:1
      .title
        padding-left :14px
        height: 26px
        line-height :26px
        border-left:2px solid #d9dde1
        font-size :12px
        color:rgb(147,153,159)
        background:#f3f5f7
      .food-item
        display :flex
        margin:18px
        border-1px(rgba(7,17,27,0.1))
        padding-bottom:18px
        &:last-child
          border-none()
          margin-bottom :0
        .icon
          flex:0 0 57px
          margin-right :10px
        .content
          flex:1
          .name
            font-size :14px
            line-height :14px
            color:rgb(7,17,27)
            margin:2px 0 8px 0
          .desc
            font-size :10px
            line-height:12px
            color:rgb(147,153,159)
            margin-bottom :8px
          .extra
            font-size :10px
            line-height:10px
            color:rgb(147,153,159)
            .count
              margin-right :12px
          .price
            font-weight :700
            line-height :24px
          .cartcontroll-wrapper
            position :absolute
            right:0
            bottom:12px
            .now
              margin-right :8px
              font-size :14px
              color:rgb(240,20,20)
            .old
              text-decoration :line-through
              font-size :10px
              color:rgb(147,153,159)
</style>
