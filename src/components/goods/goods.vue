<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item, index) in goods" :key="index" class="menu-item" :class="{'current':currentIndex === index}" @click="selectMenu(index, $event)">
          <span class="text">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>
            {{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foots-wrapper" ref="foodsWrapper">
      <ul>
        <li  v-for="(item, index) in goods" :key="index" class="food-list foodListHook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li  @click="selectFood(food, $event)" v-for="(food, index) in item.foods" :key="index" class="food-item">
              <div class="icon">
                <img :src="food.icon" width="57" height="57">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old " v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cart-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart :selectFoods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    <food :food="selectedFood" ref="food"></food>
  </div>
</template>

<script>
// 向外默认暴露这个对象
import BScroll from 'better-scroll'
import cartcontrol from '../cartcontrol/cartcontrol'
import shopcart from '../shopcart/shopcart'
import food from '../food/food'
const errno = 0
export default {
  props: {
    seller: Object
  },
  data () {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0,
      selectedFood: {}
    }
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i + 1]
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i
        }
      }
      return 0
    },
    selectFoods () {
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food)
          }
        })
      })
      return foods
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    this.$http.get(window.serverAddr + '/api/goods').then((response) => {
      // 响应成功回调
      response = response.body
      if (response.errno === errno) {
        this.goods = response.data
        this.$nextTick(() => {
          this._initScroll()
          this.calculateHeight()
        })
        // console.log(this.goods)
      }
    })
  },
  methods: {
    _initScroll () {
      this.menuScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      })
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
        click: true,
        probeType: 3
      })
      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      })
    },
    calculateHeight () {
      let footList = this.$refs.foodsWrapper.getElementsByClassName('foodListHook')
      let height = 0
      this.listHeight.push(height)
      for (let i = 0; i < footList.length; i++) {
        let item = footList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    selectMenu (index, event) {
      if (!event._constructed) {
        return
      }
      let footList = this.$refs.foodsWrapper.getElementsByClassName('foodListHook')
      let el = footList[index]
      this.foodsScroll.scrollToElement(el, 300)
    },
    selectFood (food, event) {
      if (!event._constructed) {
        return
      }
      this.selectedFood = food
      this.$refs.food.show()
    }
  },
  components: {
    cartcontrol,
    shopcart,
    food
  }
}
</script>

<style>
@import '../../common/stylus/icon.css';
.goods{
  width: 100%;
  display: flex;
  position: absolute;
  top:174px;
  bottom:46px;
  overflow: hidden;
}
.menu-wrapper{
  flex: 0 0 80px;
  width: 80px;
  background: #f3f5f7;
}
.menu-wrapper .menu-item{
  display: table;
  height: 54px;
  width: 56px;
  line-height: 14px;
  padding: 0 12px;
}
.menu-wrapper .menu-item .icon{
  display: inline-block;
  height: 12px;
  width: 12px;
  margin-right: 2px;
  background-repeat: no-repeat;
  vertical-align: middle;
}
.menu-wrapper .menu-item .decrease{
  background: url("decrease_3@2x.png");
  background-size: 12px 12px;
}
.menu-wrapper .current{
  position: relative;
  z-index: 10;
  margin-top: -1px;
  background: #fff;
  font-weight: 700;
}
.menu-wrapper .menu-item .discount{
  background: url("discount_3@2x.png");
  background-size: 12px 12px;
}
.menu-wrapper .menu-item .invoice{
  background: url("invoice_3@2x.png");
  background-size: 12px 12px;
}
.menu-wrapper .menu-item .special{
  background: url("special_3@2x.png");
  background-size: 12px 12px;
}
.menu-wrapper .menu-item .guarantee{
  background: url("guarantee_3@2x.png");
  background-size: 12px 12px;
}
.menu-wrapper .menu-item .text{
  display: table-cell;
  width: 56px;
  vertical-align: middle;
  font-size: 12px;
  border-bottom: 1px solid rgba(7,17,27,0.1);
}

.menu-wrapper .current .text{
  border-bottom: none;
}
.foots-wrapper{
  flex: 1;
}
/*.foots-wrapper .food-list{}*/
.foots-wrapper .food-list .title{
  padding-left: 14px;
  height: 26px;
  line-height: 26px;
  border-left: 2px solid #d9dde1;
  font-size: 12px;
  color: rgb(147,153,159);
  background: #f3f5f7;
  margin-top: 0;
}
.foots-wrapper .food-list .food-item{
  display: flex;
  margin: 18px;
  padding-bottom: 18px;
  border-bottom: 1px solid rgba(7,17,27,0.1);
}
.foots-wrapper .food-list .food-item:last-child{
  border-bottom:none;
  margin-bottom: 0;
}
.food-item .icon{
  flex: 0 0 57px;
  margin-right: 10px;
}
.food-item .content{
  flex: 1;
  position: relative;
}
.food-item .content .name{
  margin: 2px 0 8px 0;
  height: 14px;
  line-height: 14px;
  font-size: 14px;
  color: rgb(7,17,27);
}
.food-item .content .desc,
.food-item .content .extra{
  line-height: 10px;
  font-size: 10px;
  color: rgb(147,153,159);
}
.food-item .content .extra .count{
  margin-right: 2px;
}
.food-item .content .desc{
  margin-bottom: 8px;
  line-height: 12px;
}
.food-item .content .price{
  font-weight: 700;
  line-height: 24px;
}
.food-item .content .price .now{
  margin-right: 8px;
  font-size: 14px;
  color: rgb(240,20,20);
}
.food-item .content .price .old{
  text-decoration: line-through;
  font-size: 10px;
  color: rgb(147,153,159);
}
.food-item .content .cart-wrapper{
  position: absolute;
  right: 0;
  bottom:-6px;
}
</style>
