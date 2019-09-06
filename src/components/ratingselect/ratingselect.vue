<template>
    <div class="ratingselect">
      <div class="rating-type">
        <span @click="select(2, $event)" class="block position" :class="{'active1':selectType===2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
        <span @click="select(0, $event)" class="block position" :class="{'active1':selectType===0}">{{desc.position}}<span class="count">{{positives.length}}</span></span>
        <span @click="select(1, $event)" class="block negative" :class="{'active2':selectType===1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
      </div>
      <div @click="toggleContent" class="switch">
        <span class="icon-check_circle" :class="{'activeFont':onlyContent===true}"></span>
        <span class="text">只看内容的评价</span>
      </div>
      <div class="rating-wrapper">
        <ul v-show="food.ratings && food.ratings.length">
          <li v-for="(rating, index) in food.ratings" :key="index" class="rating-item">
            <div class="user">
              <span class="name">{{rating.username}}</span>
              <img class="avatar" :src="rating.avatar" width="12" height="12">
            </div>
            <div class="time">{{rating.rateTime}}</div>
            <p class="text">
              <span :class="{'icon-thumb_up': rating.rateType === 0, 'icon-thumb_down': rating.rateType ===1}"></span>
              {{rating.text}}
            </p>
          </li>
        </ul>
        <div class="no-rating" v-show="!food.ratings || !food.ratings.length"></div>
      </div>
    </div>
</template>

<script>
const POSITIVE = 0
const NEGATIVE = 1
const ALL = 2
export default {
  props: {
    food: {},
    ratings: {
      type: Array,
      default () {
        return []
      }
    },
    selectType: {
      type: Number,
      default: ALL
    },
    onlyContent: {
      type: Boolean,
      default: false
    },
    desc: {
      type: Object,
      default () {
        return {
          all: '全部',
          position: '满意',
          negative: '不满意 '
        }
      }
    }
  },
  data () {
    return {

    }
  },
  computed: {
    positives () {
      return this.ratings.filter((rating) => {
        return rating.rateType === POSITIVE
      })
    },
    negatives () {
      return this.ratings.filter((rating) => {
        return rating.rateType === NEGATIVE
      })
    }
  },
  methods: {
    select (type, event) {
      if (!event._constructed) {
        return
      }
      this.$parent.$emit('ratingtype', type)
      // this.$dispatch('ratingtype.select',type)
    },
    toggleContent (event) {
      if (!event._constructed) {
        return
      }
      this.$parent.$emit('content', this.onlyContent)
    }
  }
}
</script>

<style>
.rating-type{
  padding: 18px;
  /*margin: 0 18px;*/
  border-bottom: 1px solid rgba(7,17,27,0.1);
  font-size: 0;
}
.rating-type .block{
  display: inline-block;
  padding: 8px 12px;
  margin-right: 8px;
  border-radius: 1px;
  color: rgb(77,85,93);
  font-size: 12px;
  line-height: 16px;
}
.rating-type .active1,
.rating-type .active2{
  color: #fff;
}
.rating-type .count{
  font-size: 8px;
  margin-left: 8px;
}
.rating-type .position{
  background-color: rgba(0,160,220,0.2);
}
.rating-type .active1{
  background-color: rgb(0,160,220);
}
.rating-type .negative{
  background-color: rgba(77,85,93,0.2);
}
.rating-type .active2{
  background-color: rgb(77,85,93);
}
.switch{
  padding: 12px 18px;
  line-height: 24px;
  border-bottom: 1px solid rgba(7,17,27,0.1);
  color: rgb(147,153,159);
  font-size: 0;
}
.switch .icon-check_circle{
  font-size: 24px;
  margin-right: 4px;
  vertical-align: middle;
}
.switch .activeFont{
  color: #059b53;
}
.switch .text{
  font-size: 12px;
  vertical-align: middle;
}
.rating-wrapper{
  padding: 0 18px;
}
.rating-wrapper .rating-item{
  position: relative;
  padding: 16px 0;
  border-bottom: 1px solid rgba(7,17,27,0.1);
}
.rating-item .user{
  position: absolute;
  right: 0;
  top: 16px;
  font-size: 0;
  line-height: 12px;
}
.rating-item .user .name{
  font-size: 10px;
  color: rgb(147,153,159);
  display: inline-block;
  vertical-align: middle;
  margin-right: 6px;
}
.rating-item .user .avatar{
  vertical-align: middle;
  border-radius: 50%;
}
.rating-item .time{}
.rating-item .text{}
</style>
