<template>
  <div>
    <div class="shopcart">
      <div class="content" @click="toggleList">
        <div class="content-left">
        	<div class="logo-wrapper">
        	  <div class="logo" :class="{'highlight': totalCount > 0}">
        	  	<i class="icon-shopping_cart" :class="{'highlight': totalCount > 0}"></i>

        	  </div>
            <div class="num" v-show="totalCount">{{totalCount}}</div>
        	</div>
        	<div class="price" :class="{'highlight': totalPrice > 0}">¥ {{totalPrice}}</div>
        	<div class="desc">另需配送费 ¥ {{deliveryPrice}} 元</div>
        </div>
        <div class="content-right" @click.stop.prevent="pay">
          <div class="pay" :class="payClass">{{payDesc}}</div>
        </div>
      </div>
    
      <div class="ball-container">
        <div v-for="ball in balls">
          <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
            <div v-show="ball.show" class="ball">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div> 
      </div>
      
      <transition name="fold">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li class="food" v-for="food in selectFoods">
                <span class="name">{{food.name}}</span>
                <div class="price">
                  <span>¥ {{food.price*food.count}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol @add="addFood" :food="food"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>

    <transition name="fade">
      <div class="list-mask" @click="hideList" v-show="listShow"></div>
    </transition>

   </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from '@/components/cartcontrol/cartcontrol';

  export default {
    name: 'shopcart',
    props: {
      selectFoods: {
        type: Array,
        // 在props中如果 type是 数组、对象，那么default就是函数
        default () {
          return [];
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
    data () {
      return {
        balls: [
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          }
        ],
        dropBalls: [],
        // fold 折叠
        fold: true
      };
    },
    computed: {
      totalPrice () {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total;
      },
      totalCount () {
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count;
        });
        return count;
      },
      payDesc () {
        if (this.totalPrice === 0) {
          return `¥ ${this.minPrice} 元起送`;
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice;
          return `还差¥ ${diff} 元起送`;
        } else {
          return '去结算';
        }
      },
      payClass () {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough';
        } else {
          return 'enough';
        }
      },
      listShow () {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        let show = !this.fold;
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.listContent, {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
        return show;
      }
    },
    methods: {
      drop (el) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) {
            ball.show = true;
            ball.el = el;
            this.dropBalls.push(ball);
            return;
          }
        }
      },
      beforeDrop (el) {
        let count = this.balls.length;
        while (count--) {
          let ball = this.balls[count];
          if (ball.show) {
            // getBoundingClientRect用于获取某个元素相对于视窗的位置集合。集合中有top, right, bottom, left等属性
            let rect = ball.el.getBoundingClientRect();
            let x = rect.left - 32;
            let y = -(window.innerHeight - rect.top - 22);
            el.style.display = '';
            el.style.webkitTransform = `translate3d(0, ${y}px, 0)`;
            el.style.transform = `translate3d(0, ${y}px, 0)`;
            let inner = el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform = `translate3d(${x}px, 0, 0)`;
            inner.style.transform = `translate3d(${x}px, 0, 0)`;
          }
        }
      },
      dropping (el, done) {
        /* eslint-disable no-unused-vars */
        let rf = el.offsetHeight; // 主动触发浏览器重绘
        this.$nextTick(() => {
          el.style.display = '';
          el.style.webkitTransform = 'translate3d(0, 0, 0)';
          el.style.transform = 'translate3d(0, 0, 0)';
          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = 'translate3d(0, 0, 0)';
          inner.style.transform = 'translate3d(0, 0, 0)';
          el.addEventListener('transitionend', done);
        });
      },
      afterDrop (el) {
        let ball = this.dropBalls.shift();
        if (ball) {
          ball.show = false;
          el.style.display = 'none';
        }
      },
      toggleList () {
        if (!this.totalCount) {
          return;
        }
        this.fold = !this.fold;
      },
      hideList () {
        this.fold = true;
      },
      addFood (target) {
        this.drop(target);
      },
      empty () {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        });
      },
      pay () {
        if (this.totalPrice < this.minPrice) {
          return;
        }
        window.alert(`支付${this.totalPrice}元`);
      }
    },
    components: {
      cartcontrol
    }
  };
</script>

<style lang="less" rel="stylesheet">
  .shopcart {
  	position: fixed;
  	left: 0;
  	bottom: 0;
  	z-index: 50;
  	width: 100%;
  	height: 48px;
    .content {
      display: flex;
      background-color: #141d27;
      font-size: 0;
      color: rgba(255, 255, 255, .4);
      .content-left {
        flex: 1;
        .logo-wrapper {
          display: inline-block;
          position: relative;
          top: -10px;
          margin: 0 12px;
          padding: 6px;
          width: 56px;
          height: 56px;
          box-sizing: border-box;
          background-color: #141d27;
          border-radius: 50%;
          vertical-align: center;
          .logo {
            width: 100%;
            height: 100%;
            border-radius: 50%;
            background-color: #2b343c;
            text-align: center;
            &.highlight {
              background-color: rgb(0, 160, 220);
            }
            .icon-shopping_cart {
              color: #80808a;
              font-size: 24px;
              line-height: 44px;
              &.highlight {
                color: #fff;
              }
            }
          }
          .num {
            position: absolute;
            top: 0;
            right: 0;
            width: 24px;
            height: 16px;
            line-height: 16px;
            text-align: center;
            border-radius: 16px;
            font-size: 9px;
            font-weight: 700;
            color: #fff;
            background-color: rgb(240, 20, 20);
            box-shadow: 0 4px 8px 0 rgba(0, 0, 0, .4);
          }
        }
        .price {
          display: inline-block;
          margin-top: 12px;
          padding-right: 12px;
          box-sizing: border-box;
          vertical-align: top;
          line-height: 24px;
          box-sizing: border-box;
          border-right: 1px solid rgba(255, 255, 255, .1);
          font-size: 16px;
          font-weight: 700;
          &.highlight {
            color: #fff;
          }
        }
        .desc {
          display: inline-block;
          margin: 12px 0 0 12px;
          line-height: 24px;
          vertical-align: top;
          font-size: 10px;
        }
      }
      .content-right {
        flex: 0 0 105px;
        width: 105px;
        .pay {
          height: 48px;
          line-height: 48px;
          text-align: center;
          background-color: #2b333b;
          font-size: 12px;
          font-weight: 700;
          &.not-enough {
            background-color: #2b333b;
          }
          &.enough {
            background-color: #00b43c;
            color: #fff;
          }
        }
      }
    }
    .ball-container {
      .ball {
        position: fixed;
        left: 32px;
        bottom: 22px;
        z-index: 200;
        transition: all .4s cubic-bezier(.49, -.29, .75, .41);
        .inner {
          width: 16px;
          height: 16px;
          border-radius: 50%;
          background-color: rgb(0, 160, 220);
          transition: all .4s linear;
        }
      }
    }
    .shopcart-list {
      position: absolute;
      top: 0;
      left: 0;
      z-index: -1;
      width: 100%;
      transform: translate3d(0, -100%, 0);
      &.fold-enter-active,
      &.fold-leave-active {
        transition: all .5s linear;
      }
      &.fold-enter,
      &.fold-leave-active {
        transform: translate3d(0, 0, 0);
      }
      .list-header {
        height: 40px;
        line-height: 40px;
        padding: 0 18px;
        background-color: #f3f5f7;
        border-top: 1px solid rgba(7, 17, 27, .1);
        border-bottom: 1px solid rgba(7, 17, 27, .1);
        overflow: hidden;
        .title {
          margin: initial;
          float: left;
          font-size: 14px;
          color: rgb(7, 17, 27);
        }
        .empty {
          float: right;
          font-size: 12px;
          color: rgb(0, 160, 220);
          cursor: pointer;
        }
      }
      .list-content {
        padding: 0 18px;
        max-height: 217px;
        overflow: hidden;
        background-color: #fff;
        .food {
          position: relative;
          padding: 12px .0;
          box-sizing: border-box;
          border-bottom: 1px solid rgba(7, 17, 27, .1);
          .name {
            line-height: 24px;
            font-size: 14px;
            font-weight: 700;
            color: rgb(7, 17,27);
          }
          .price {
            position: absolute;
            right: 90px;
            bottom: 12px;
            font-size: 14px;
            line-height: 24px;
            color: rgb(240, 20, 20);
            span {
              font-weight: 700;
            }
          }
          .cartcontrol-wrapper {
            position: absolute;
            bottom: 6px;
            right: 0;
          }
        }
      }
    }
  }

  .list-mask {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 40;
    backdrop-filter: blur(10px);
    background-color: rgba(7, 17, 27, .6);
    opacity: 1;
    &.fade-enter-active,
    &.fade-leave {
      transition: all .5s linear;
    }
    &.fade-enter,
    &.fate-leave {
      opacity: 0;
      background-color: rgba(7, 17, 27, 0);
    }
  }
</style>
