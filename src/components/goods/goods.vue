<template>
  <div>
    <div class="goods">
      <div class="menu-wrapper" ref="menuWrapper">
        <ul>
          <li v-for="(item, index) in goods" class="menu-item border-1px" :class="[currentIndex===index ? 'current' : '']" @click="selectMenu(index, $event)">
            <span class="text">
              <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
            </span>
          </li>
        </ul>
      </div>
      <div class="foods-wrapper" ref="foodsWrapper">
        <ul>
          <li v-for="item in goods" class="food-list food-list-hook">
            <h1 class="title">{{item.name}}</h1>
            <ul>
              <li @click="selectFood(food, $event)" v-for="food in item.foods" class="food-item border-1px">
                <div class="icon">
                  <img width="57px" :src="food.icon">
                </div>
                <div class="content">
                  <h2 class="name">{{food.name}}</h2>
                  <p class="desc">{{food.description}}</p>
                  <div class="extra">
                    <span class="count">月售{{food.sellCount}}</span><span>好评率{{food.rating}}%</span>
                  </div>
                  <div class="price">
                    <span class="now">¥{{food.price}}</span>
                    <span class="old" v-show="food.oldPrice">¥{{food.oldPrice}}</span>
                  </div>
                  <div class="cartcontrol-wrapper">
                    <cartcontrol @add="addFood" :food="food"></cartcontrol>
                  </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <shopcart ref="shopcart" :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    </div>
    <food @add="addFood" :food="selectedFood" ref="food"></food>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import shopcart from '@/components/shopcart/shopcart';
  import cartcontrol from '@/components/cartcontrol/cartcontrol';
  import food from '@/components/food/food';

  const ERR_OK = 0;

  export default {
    name: 'goods',
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      };
    },
    computed: {
      currentIndex () {
        for (let i = 0, ilen = this.listHeight.length; i < ilen; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i;
          }
        }
        return 0;
      },
      selectFoods () {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];

      this.$http.get('/api/goods').then((response) => {
        response = response.data;
        if (response.errno === ERR_OK) {
          this.goods = response.data;
          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
          });
        }
      });
    },
    methods: {
      _initScroll () {
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true // 允许点击
        });
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
          click: true, // 允许点击
          probeType: 3 // scroll 滚动时能返回滚动的位置
        });
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      _calculateHeight () {
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0, ilen = foodList.length; i < ilen; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        }
      },
      selectMenu (index, event) {
        // 点击左侧menu时，在pc端会执行两次：浏览器原生、BScroll派生各一次
        // 解决：pc浏览器原生点击event事件无_constructed return 不执行 即可
        if (!event._constructed) {
          return;
        }
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el, 300);
      },
      selectFood (food, event) {
        if (!event._constructed) {
          return;
        }
        this.selectedFood = food;
        this.$refs.food.show();
      },
      addFood (target) {
        this._drop(target);
      },
      _drop (target) {
        // 体验优化，异步执行下落动画
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target);
        });
      }
    },
    components: {
      shopcart,
      cartcontrol,
      food
    }
  };
</script>

<style lang="less" rel="stylesheet">
  @import "../../common/css/index.css";
  
  .goods {
  	display: flex;
  	position: absolute;
  	top: 174px;
  	bottom: 46px;
  	overflow: hidden;
    width: 100%;
    .menu-wrapper {
      flex: 0 0 80px;
      width: 80px;
      background-color: #f3f5f7;
      .menu-item {
        display: table;
        padding: 0 12px;
        width: 56px;
        height: 54px;
        line-height: 14px;
        cursor: pointer;
        &.current {
          position: relative;
          z-index: 10;
          margin-top: -1px;
          background-color: #fff;
          .text {
            border: 0;
            font-weight: 700;
          }
        }
        .icon {
          margin-right: 2px;
          display: inline-block;
          width: 12px;
          height: 12px;
          background-size: 12px 12px;
          background-repeat: no-repeat;
          vertical-align: top;
          &.decrease {
            background-image: url('decrease_3@2x.png');
          }
          &.discount {
            background-image: url('discount_3@2x.png');
          }
          &.guarantee {
            background-image: url('guarantee_3@2x.png');
          }
          &.invoice {
            background-image: url('invoice_3@2x.png');
          }
          &.special {
            background-image: url('special_3@2x.png');
          }
        }
        .text {
          display: table-cell;
          width: 56px;
          vertical-align: middle;
          font-size: 12px;
         /*  border-1px(rgba(7, 17, 27, .1)); */
        }
      }
    }
    .foods-wrapper {
      flex: 1;
      .title {
        margin: 0;
        padding-left: 14px;
        height: 26px;
        width: 100%;
        line-height: 26px;
        background-color: #f3f5f7;
        border-left: 2px solid #d9dde1;
        font-size: 12px;
        color: rgb(147, 153, 159);
      }
      .food-item {
        display: flex;
        margin: 18px; 
        padding-bottom: 18px;  
        &:last-child {
          border: 0;
          margin-bottom: 0;
        }
        .icon {
          flex: 0 0 57px;
          margin-right: 10px;
          width: 57px;
        } 
        .content {
          flex: 1;
          .name {
            margin: 2px 0 8px 0;
            height: 14px;
            line-height: 14px;
            font-size: 14px;
            color: rgb(7, 17, 27);
          }
          .desc,
          .extra {
            line-height: 10px;
            font-size: 10px;
            color: rgb(147, 153, 159);
          }
          .desc {
            margin-bottom: 8px;
            line-height: 12px;
          }
          .extra .count {
            margin-right: 12px;
          }
          .price {
            font-weight: 700;
            line-height: 24px;
            .now {
              margin-right: 8px;
              font-size: 14px;
              font-weight: 700;
              color: rgb(240, 20, 20);
            }
            .old {
              text-decoration: line-through;
              font-size: 10px;
              color: rgb(147, 153, 159);
            }  
          }
          .cartcontrol-wrapper {
            position: absolute;
            right: 0;
            bottom: 12px;
          }
        }
      }
    }
  }
</style>

