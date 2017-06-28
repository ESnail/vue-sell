<template>
  <div class="ratingselect">
    <div class="rating-type">
      <span @click="select(2, $event)" class="block positive" :class="{'active':selectType===2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
      <span @click="select(0, $event)" class="block positive" :class="{'active':selectType===0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
      <span @click="select(1, $event)" class="block negative" :class="{'active':selectType===1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
    </div>
    <div @click="toggleContent" class="switch" :class="{'on': onlyContent}">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  /* 评价类型 */
  // 正向评价
  const POSITIVE = 0;
  // 负向评价
  const NEGATIVE = 1;
  // 所有评价
  const ALL = 2;

  export default {
    props: {
      ratings: {
        type: Array,
        default () {
          return [];
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
            positive: '满意',
            negative: '不满意'
          };
        }
      }
    },
    computed: {
      positives () {
        return this.ratings.filter((rating) => {
          return rating.rateType === POSITIVE;
        });
      },
      negatives () {
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE;
        });
      }
    },
    methods: {
      select (type, event) {
        if (!event._constructed) {
          return;
        }
        this.$emit('select', type);
      },
      toggleContent () {
        if (!event._constructed) {
          return;
        }
        this.$emit('toggle');
      }
    }
  };
</script>

<style lang="less" rel="stylesheet">
  .ratingselect {
    .rating-type {
      padding: 18px 0;
      margin: 0 18px;
      border-bottom: 1px solid rgba(7, 17, 27, .2);
      font-size: 0;
      .block {
        display: inline-block;
        padding: 8px 12px;
        margin-right: 8px;
        border-radius: 1px;
        color: rgb(77, 85, 93);
        font-size: 12px;
        line-height: 16px;
        &.active {
          color: #fff;
        }
        &.positive {
          background-color: rgba(0, 160, 220, .2);
        }
        &.positive.active {
          background-color: rgb(0, 160, 220);
        }
        &.negative {
          background-color: rgba(77, 85, 93, 0.2);
        }
        &.negative.active {
          background-color: rgb(77, 85, 93);
        }
        .count {
          margin-left: 12px;
          font-size: 8px;
        }
      }
    }
    .switch {
      padding: 12px 18px;
      line-height: 24px;
      border-bottom: 1px solid rgba(7, 17, 27, .1);
      color: rgb(147, 153, 159);
      font-size: 0;
      .icon-check_circle {
        display: inline-block;  
        vertical-align: top;
        margin-right: 4px;
        font-size: 24px;
      }
      &.on .icon-check_circle {
        color: #00c850;
      }
      .text {
        display: inline-block;
        font-size: 12px;
      }
    }
  }
</style>

