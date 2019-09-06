<template>
  <transition name="move">
    <div v-show="showFlag" class="food" ref="food" @show="show">
      <div class="food-content">
        <div class="image-header">
          <img :src="food.image">
          <div class="back" @click="hide">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="food-detail">
            <span class="sell-count">
              月售{{food.sellCount}}份
            </span>
            <span class="rating">
              好评率{{food.rating}}%
            </span>
          </div>
          <div class="price">
            <span class="now">￥{{food.price}}</span>
            <span class="old " v-show="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol :food="food"></cartcontrol>
          </div>
          <div class="buy" v-show="!food.count || food.count === 0" @click.stop.prevent="addFirst($event)">加入购物车</div>
        </div>
        <split v-show="food.info"></split>
        <div class="info" v-show="food.info">
          <h1 class="info-title">商品信息</h1>
          <p class="info-text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="product-rating">
          <h1 class="rating-title">商品评价</h1>
          <ratingselect :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="food.ratings" :food="food"></ratingselect>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import BScroll from 'better-scroll'
import Vue from 'vue'
import cartcontrol from '../cartcontrol/cartcontrol'
import ratingselect from '../ratingselect/ratingselect'
import split from '../split/split'
// 向外默认暴露这个对象
// const POSTTIVE = 0
// const NEGATIVE = 1
const ALL = 2
export default {
  props: {
    food: Object
  },
  data () {
    return {
      showFlag: false,
      selectType: ALL,
      onlyContent: true,
      desc: {
        all: '全部',
        position: '推荐',
        negative: '吐槽 '
      }
    }
  },
  mounted () {
    this.$on('ratingtype', this.ratingT)
    this.$on('content', this.toggle)
  },
  methods: {
    show () {
      this.showFlag = true
      this.selectType = ALL
      this.onlyContent = false
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.food, {
            click: true
          })
        } else {
          this.scroll.refresh()
        }
      })
    },
    hide () {
      this.showFlag = false
      this.selectType = ALL
      this.onlyContent = true
    },
    addFirst ($event) {
      if (!event._constructed) {
        return
      }
      Vue.set(this.food, 'count', 1)
    },
    ratingT (type) {
      this.selectType = type
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    toggle (onlyContent) {
      this.onlyContent = !onlyContent
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    }
  },
  components: {
    cartcontrol,
    ratingselect,
    split
  }
}
</script>

<style>
.food{
  position: fixed;
  left: 0;
  top:0;
  bottom: 48px;
  z-index: 30;
  width: 100%;
  background: #fff;
}
.move-enter-active, .move-leave-active {
  transition: all 0.2s linear;
}
.move-enter, .move-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
  transform: translateX(30px);
}
.image-header{
  width: 100%;
  position: relative;
  height: 0;
  padding-top: 100%;
}
.image-header img{
  position: absolute;
  top:0;
  left: 0;
  width: 100%;
  height: 100%;
}
.back{
  position: absolute;
  top:10px;
  left:0;
}
.back .icon-arrow_lift{
  display: block;
  padding: 10px;
  font-size:20px;
  color: #fff;
}
.food-content .content{
  padding: 18px;
  position: relative;
}
.content .title{
  line-height: 14px;
  margin-bottom: 8px;
  font-size: 14px;
  font-weight: 700;
  color: rgb(7,17,27);
}
.content .food-detail{
  line-height: 10px;
  margin-bottom: 18px;
  height: 10px;
  font-size: 0;
}
.content .food-detail .sell-count,
.content .food-detail .rating{
  font-size: 10px;
  color: rgb(147,153,159);
}
.food-detail .sell-count{
  margin-right: 12px;
}
.content .price{
  font-weight: 700;
  line-height: 24px;
}
.content .price .now{
  margin-right: 8px;
  font-size: 14px;
  color: rgb(240,20,20);
}
.content .price .old{
  text-decoration: line-through;
  font-size: 10px;
  color: rgb(147,153,159);
}
.cartcontrol-wrapper {
   position: absolute;
   right: 12px;
   bottom: 12px;
 }
.buy{
  position: absolute;
  right: 18px;
  bottom: 16px;
  z-index: 10;
  height: 24px;
  line-height: 24px;
  padding: 0 12px;
  box-sizing: border-box;
  border-radius: 12px;
  font-size: 10px;
  color: #fff;
  background-color: rgb(0,160,220);
}
.food-content .info{
  padding: 18px;
}
.food-content .info-title{
  line-height: 14px;
  margin-bottom: 16px;
  font-size: 14px;
  color: rgb(7,17,27);
}
.food-content .info-text{
  color: rgb(77,85,93);
  line-height: 24px;
  padding: 0 8px;
  font-size: 12px;
}
.product-rating{
  padding-top: 18px;
}
.product-rating .rating-title{
  line-height: 14px;
  margin-left: 16px;
  font-size: 14px;
  color: rgb(7,17,27);
}
</style>
