<template>
    <div class="shopcart">
      <div class="content" @click="toggleList">
        <div class="content-left">
         <div class="logo-wrapper">
           <div class="logo" :class="{'highlight': totalCount > 0}">
             <i class="icon-shopping_cart" :class="{'highlight': totalCount > 0}"></i>
           </div>
           <div class="num" v-show="totalCount>0">{{totalCount}}</div>
         </div>
          <div class="price" :class="{'highlight': totalPrice > 0}">￥{{totalPrice}}元</div>
          <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
        </div>
        <div class="content-right">
          <div class="pay" :class="payClass">
            {{payDesc}}
          </div>
        </div>
      </div>
      <transition name="fold"><div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="shopcart-title">购物车</h1>
            <span class="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li class="food" v-for="(food, index) in selectFoods" :key="index">
                <span class="name">{{food.name}}</span>
                <div class="price">
                  <span>¥{{food.price*food.count}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div></transition>
    </div>
</template>

<script>
// 向外默-认暴露这个对象
import BScroll from 'better-scroll'
import cartcontrol from '../cartcontrol/cartcontrol'
export default {
  components: {
    cartcontrol
  },
  data () {
    return {
      fold: true
    }
  },
  props: {
    selectFoods: {
      type: Array,
      default () {
        return [
          {
            price: 10,
            count: 1
          }
        ]
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  computed: {
    totalPrice () {
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    totalCount () {
      let count = 0
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    payDesc () {
      if (this.totalPrice === 0) {
        return `￥${this.deliveryPrice}元起送`
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice
        return `还差￥${diff}元起送`
      } else {
        return '去结算'
      }
    },
    payClass () {
      if (this.totalPrice < this.minPrice) {
        return 'not-enough'
      } else {
        return 'enough'
      }
    },
    listShow () {
      let vm = this
      if (!this.totalCount) {
        vm.fold = true
        return false
      }
      let show = !this.fold
      if (show) {
        this.$nextTick(() => {
          if (!this.scroll) {
            vm.scroll = new BScroll(this.$refs.listContent, {
              click: true
            })
          } else {
            vm.scroll.refresh()
          }
        })
      }
      return show
    }
  },
  methods: {
    toggleList () {
      if (!this.totalCount) {
        return
      }
      this.fold = !this.fold
    }
  }
}
</script>
<style>
@import "../../common/stylus/icon.css";
.shopcart{
  position: fixed;
  left: 0;
  bottom: 0;
  z-index: 50;
  width: 100%;
  height: 48px;
}
.shopcart .content{
  display: flex;
  background: #141d27;
  font-size: 0;
  color: rgba(255,255,255,0.4);
}
.content .content-left{
  flex: 1;
}
.content-left .logo-wrapper{
  display: inline-block;
  position: relative;
  top:-10px;
  margin: 0 12px;
  padding: 6px;
  width: 56px;
  height: 56px;
  box-sizing: border-box;
  vertical-align: top;
  border-radius: 50%;
  background: #141d27;
}
.content-left .logo-wrapper .logo{
  display: inline-block;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  background: #293137;
  text-align: center;
}
.content-left .logo-wrapper .highlight{
  background: rgb(0,160,220);
}
.content-left .logo-wrapper .num{
  position: absolute;
  top:0;
  right: 0;
  width: 24px;
  height: 16px;
  background:rgb(240,20,20) ;
  line-height: 16px;
  text-align: center;
  border-radius: 16px;
  font-size: 9px;
  font-weight: 700;
  color: #fff;
  box-shadow: 0 4px 8px 0 rgba(0,0,0,.4);
}
.content-left .icon-shopping_cart{
  color:#7D848B;
  font-size: 24px;
  line-height: 44px;
}
.content-left .logo .highlight{
  color: #fff;
}
.content-left .price{
  display: inline-block;
  vertical-align: top;
  line-height: 24px;
  margin-top: 12px;
  box-sizing: border-box;
  padding-right: 12px;
  border-right: 1px solid rgba(255,255,255,0.1);
  font-size: 16px;
  font-weight: 700;
}
.content-left .highlight{
  color: #fff;
}
.content-left .desc{
  display: inline-block;
  vertical-align: top;
  line-height: 24px;
  margin: 12px 0 0 12px;
  font-size: 10px;
}
.content-right{
  flex: 0 0 105px;
  width: 105px;
}
.content-right .pay{
  font-size: 12px;
  height: 48px;
  line-height:48px;
  text-align: center;
  font-weight: 700;
}
.content-right .not-enough{
  background: #293137;
}
.content-right .enough{
  background: #00b43c;
  color: #fff;
}
.shopcart-list{
  width: 100%;
  position: absolute;
  left: 0;
  bottom: 48px;
  z-index:-1;
}
.fold-transition{
  transition: all 0.5s;
  transform: translate3d(0,-100%,0);
}
.fold-enter,
.fold-leave{
  transform: translate3d(0,0,0);
}
.list-header{
  background: #f4f6f7;
  height: 40px;
  line-height: 40px;
  padding: 0 6px;
  border-bottom: 1px solid rgba(7,17,27,.1);
}
.list-header .shopcart-title{
  float: left;
  font-size: 14px;
  color: rgb(7,17,27);
}
.list-header .empty{
  float: right;
  color: rgb(0,160,220);
  font-size: 12px;
}
.list-content{
  max-height: 217px;
  overflow: hidden;
  background: #fff;
  width: 100%;
}
.list-content .food{
  position: relative;
  padding: 12px 0;
  box-sizing: border-box;
  border-bottom: 1px solid rgba(7,17,27,.1);
}
.list-content .food .name{
  line-height: 24px;
  font-size: 14px;
  color: rgb(7, 17, 27);
}
.list-content .food .price{
  position: absolute;
  right: 90px;
  bottom: 12px;
  font-size: 14px;
  line-height: 24px;
  font-weight: 700;
  color: rgb(240, 20, 20)
}
.list-content .cartcontrol-wrapper{
  position: absolute;
  right: 2px;
  bottom: 6px;
}
</style>
