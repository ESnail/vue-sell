<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" @click.stop.prevent="decreaseCart" v-show="food.count>0">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
import Vue from 'vue';

export default {
  name: 'cartcontrol',
  props: {
    food: {
      type: Object
    }
  },
  methods: {
    addCart (event) {
      if (!event._constructed) {
        return;
      }
      if (!this.food.count) {
        // this.food.count = 1;
        // 未定义过的参数直接使用不能检测到 count
        Vue.set(this.food, 'count', 1);
      } else {
        this.food.count++;
      }
      this.$emit('add', event.target);
    },
    decreaseCart (event) {
      if (!event._constructed) {
        return;
      }
      if (this.food.count) {
        this.food.count--;
      }
    }
  }
};
</script>

<style lang="less" rel="stylesheet">
  .cartcontrol {
    font-size: 0;
    .cart-decrease,.cart-add {
      display: inline-block;
      padding: 6px;
    }
    .cart-decrease {
      opacity: 1;
      transition: all .4s linear;
      .inner {
        display: inline-block;
        line-height: 24px;
        font-size: 24px;
        color: rgb(0, 160, 220);
      }
    }
    .move-enter-active,.move-leave {
      transform: translate3d(0, 0, 0) rotate(0);
      opacity: 1;
    }
    .move-enter,.move-leave-active {
      transform: translate3d(24px, 0, 0) rotate(180deg);
      opacity: 0;
    } 
    .cart-count {
      display: inline-block;
      padding-top: 6px;
      vertical-align: top;
      width: 12px;
      line-height: 24px;
      text-align: center;
      font-size: 10px;
      color: rgb(147, 153, 159);
    }
    .cart-add {
      line-height: 24px;
      font-size: 24px;
      color: rgb(0, 160, 220);
    }
  }
</style>

