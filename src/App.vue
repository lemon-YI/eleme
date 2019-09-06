<template>
  <div id="app">
    <v-header :seller="seller"/>
    <div class="tab">
      <div class="tab-item">
        <router-link to="/goods" class="linkActiveClass">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings" class="linkActiveClass">评价</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller" class="linkActiveClass">商家</router-link>
      </div>
    </div>
    <router-view :seller="seller"></router-view>
  </div>
</template>

<script>
import header from './components/header/header'
// import goods from './components/goods/goods'
let errno = 0
window.serverAddr = 'http://192.168.1.47:3000'
export default {
  data () {
    return {
      seller: {}
    }
  },
  created () {
    this.$http.get(window.serverAddr + '/api/seller').then((response) => {
      // 响应成功回调
      response = response.body
      if (response.errno === errno) {
        this.seller = response.data
        // console.log(response.errno)
      }
    })
  },
  components: {
    'v-header': header
  }
}
</script>

<style>
#app .tab{
  display: flex;
  width: 100%;
  height: 40px;
  line-height: 40px;
  position: relative;
}
.tab::after{
  display: block;
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  border-top:1px solid rgba(7,17,27,0.1);
  content: ' ';
}
.tab-item{
  flex: 1;
  text-align: center;
}
.tab-item a{
  display: block;
  text-decoration: none;
  font-size: 14px;
  color: rgb(77,85,93);
}
.tab-item .active{
  color: rgb(240,20,20);
}
</style>
