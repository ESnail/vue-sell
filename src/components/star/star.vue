<template>
  <div class="star" :class="starType">
    <span v-for="itemClass in itemClasses" :class="itemClass" class="star-item"></span>
  </div>
</template>

<script type="text/ecmascript-6">
const LENGTH = 5;
const CLS_ON = 'on';
const CLS_HALF = 'half';
const CLS_OFF = 'off';

export default {
  name: 'star',
  props: {
    size: {
      type: Number
    },
    score: {
      type: Number
    }
  },
  computed: {
    starType () {
      return 'star-' + this.size;
    },
    itemClasses () {
      let result = [];
      // 5的倍数
      let score = Math.floor(this.score * 2) / 2;
      // 是否有小数
      let hasDecimal = score % 1 !== 0;
      let integer = Math.floor(score);
      for (let i = 0; i < integer; i++) {
        result.push(CLS_ON);
      }
      if (hasDecimal) {
        result.push(CLS_HALF);
      }
      while (result.length < LENGTH) {
        result.push(CLS_OFF);
      }
      return result;
    }
  }
};
</script>

<style type="text/css">
  .star {
  	font-size: 0;
  }
  .star .star-item {
    display: inline-block;
    background-repeat: no-repeat;
  }

  .star-48 .star-item {
    margin-right: 22px;
    width: 20px;
    height: 20px;
    background-size: 20px 20px;
  }
  .star-48 .star-item:last-child {
  	margin-right: 0;
  }
  .star-48 .star-item.on { /* 全星 */
    background-image: url('star48_on@2x.png');
  }
  .star-48 .star-item.half { /* 半星 */
    background-image: url('star48_half@2x.png');
  }
  .star-48 .star-item.off { /* 零星 */
    background-image: url('star48_off@2x.png');
  }

  .star-36 .star-item {
    margin-right: 6px;
    width: 15px;
    height: 15px;
    background-size: 15px 15px;
  }
  .star-36 .star-item:last-child {
  	margin-right: 0;
  }
  .star-36 .star-item.on { /* 全星 */
    background-image: url('star36_on@2x.png');
  }
  .star-36 .star-item.half { /* 半星 */
    background-image: url('star36_half@2x.png');
  }
  .star-36 .star-item.off { /* 零星 */
    background-image: url('star36_off@2x.png');
  }

  .star-24 .star-item {
    margin-right: 3px;
    width: 10px;
    height: 10px;
    background-size: 10px 10px;
  }
  .star-24 .star-item:last-child {
  	margin-right: 0;
  }
  .star-24 .star-item.on { /* 全星 */
    background-image: url('star24_on@2x.png');
  }
  .star-24 .star-item.half { /* 半星 */
    background-image: url('star24_half@2x.png');
  }
  .star-24 .star-item.off { /* 零星 */
    background-image: url('star24_off@2x.png');
  }
</style>

