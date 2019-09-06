<template>
    <div class="cartcontrol">
      <transition name="move">
        <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decreaseCart">
          <span class="inner icon-remove_circle_outline"></span>
        </div>
      </transition>
      <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
      <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
    </div>
</template>

<script>
// 向外默认暴露这个对象
import Vue from 'vue'
export default {
  props: {
    food: Object
  },
  methods: {
    addCart (event) {
      if (!event._constructed) {
        return
      }
      console.log('click')
      if (!this.food.count) {
        Vue.set(this.food, 'count', 1)
      } else {
        this.food.count++
      }
      // this.$dispatch('cart.add', event.target)
    },
    decreaseCart (event) {
      if (!event._constructed) {
        return
      }
      if (this.food.count) {
        this.food.count--
      }
    }
  }
}
</script>

<style>
.cartcontrol{
  font-size: 0;
}
.cartcontrol .cart-decrease{
  display: inline-block;
  padding: 3px;
  transition: all 0.4s linear;
}
.move-transition{
  opacity: 1;
  transform: translate3D(0,0,0);
}
.cartcontrol .cart-decrease .inner{
  font-size: 24px;
  line-height: 24px;
  color: rgb(0,160,220);
  display: inline-block;
  transition: all 0.4s linear;
  transform: rotate(0);
}
.move-enter,
.move-leave{
  opacity: 0;
  transform: translate3d(24px,0 ,0);
}
.move-transition .move-enter .inner,
.move-transition .move-leave .inner{
  transform: rotate(180deg);
}
.cartcontrol .cart-add{
  font-size: 24px;
  line-height: 24px;
  color: rgb(0,160,220);
  display: inline-block;
  padding: 3px;
}
.cartcontrol .cart-count{
  display: inline-block;
  width: 24px;
  padding-top: 3px;
  vertical-align: top;
  line-height: 24px;
  text-align: center;
  font-size: 10px;
  color: rgb(147,153,159);
}
.cartcontrol .cart-add{
  display: inline-block;
}
</style>
