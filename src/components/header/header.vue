<template>
  <div class="header">
  	<div class="content-wrapper">
  	  <div class="avatar">
  	  	<img width="64" height="64" :src="seller.avatar">
  	  </div>
  	  <div class="content">
  	  	<div class="title">
  	  	  <span class="brand"></span>
  	  	  <span class="name">{{seller.name}}</span>
  	  	</div>
  	  	<div class="description">
  	  	  {{seller.description}}/{{seller.deliveryTime}}分钟送达
  	  	</div>
  	  	<div v-if="seller.supports" class="support">
  	  	  <span class="icon" :class="classMap[seller.supports[0].type]"></span>
  	  	  <span class="text">{{seller.supports[0].description}}</span>
  	  	</div>
  	  </div>
  	  <div v-if="seller.supports" class="support-count" @click="showDetail">
  	  	<span class="count">{{seller.supports.length}}个</span>
  	  	<i class="icon-keyboard_arrow_right"></i>
  	  </div>
  	</div>
  	<div class="bulletin-wrapper" @click="showDetail">
  	  <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
  	  <i class="icon-keyboard_arrow_right"></i>
  	</div>
  	<div class="background">
  	  <img :src="seller.avatar" width="100%" height="100%"/>
  	</div>

  	<!-- detail begin -->
    <transition name="fade">
      <div v-show="detailShow" class="detail">
        <div class="detail-wrapper clearfix">
        	<div class="detail-main">
        	  <h1 class="name">{{seller.name}}</h1>
        	  <div class="star-wrapper">
        	    <star :size="48" :score="seller.score"></star>
        	  </div>
        	  <div class="title">
        	  	<div class="line"></div>
        	  	<div class="text">优惠信息</div>
        	  	<div class="line"></div>
        	  </div>
        	  <ul v-if="seller.supports" class="supports">
        	  	<li class="support-item" v-for="(item, index) in seller.supports">
        	  	  <span class="icon" :class="classMap[seller.supports[index].type]"></span>
        	  	  <span class="text">{{seller.supports[index].description}}</span>
        	  	</li>
        	  </ul>
        	  <v-title text="商家公告"></v-title>
        	  <div class="bulletin">
        	  	<p class="content">{{seller.bulletin}}</p>
        	  </div>
        	</div>
        </div>
        <div class="detail-close" @click="hideDetail">
        	<i class="icon-close"></i>
        </div>
      </div>
    </transition>
  	<!-- detail end -->
  </div>
</template>

<script type="text/ecmascript-6">
import star from '@/components/star/star';
import title from '@/components/title/title';

export default {
  name: 'header',
  data () {
    return {
      detailShow: false
    };
  },
  props: {
    seller: {
      type: Object
    }
  },
  methods: {
    showDetail () {
      this.detailShow = true;
    },
    hideDetail () {
      this.detailShow = false;
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  },
  components: {
    star,
    'v-title': title
  }
};
</script>

<style lang="less" rel="stylesheet">
  .title {
    display: initial;
    margin: initial;
    width: initial;
  }
  .header {
  	position: relative;
  	overflow: hidden;
  	background-color: rgba(7, 17, 27, .2);
  	color: #fff;
    .content-wrapper {
      position: relative;
      padding: 24px 12px 18px 24px;
      font-size: 0;
      .avatar {
        display: inline-block;
        vertical-align: top;
        img {
          border-radius: 2px;
         }
      }
      .content {
        display: inline-block;
        margin-left: 14px;
        width: calc(100% - 22%);
        .title {
          margin: 2px 0 8px 0;
          .brand {
            display: inline-block;
            width: 30px;
            height: 18px;
            background-image: url("brand@2x.png");
            background-size: 30px 18px;
            background-repeat: no-repeat;
            vertical-align: top;
          }
          .name {
            margin-left: 6px;
            font-size: 16px;
            font-weight: bold;
            line-height: 18px;
          }
        }
        .description {
          margin-bottom: 10px;
          font-size: 12px;
          line-height: 12px;
        }
        .support {
          .icon {
            margin-right: 4px;
            display: inline-block;
            width: 12px;
            height: 12px;
            background-size: 12px 12px;
            background-repeat: no-repeat;
            &.decrease {
              background-image: url('decrease_1@2x.png');
            }
            &.discount {
              background-image: url('discount_1@2x.png');
            }
            &.guarantee {
              background-image: url('guarantee_1@2x.png');
            }
            &.invoice {
              background-image: url('invoice_1@2x.png');
            }
            &.special {
              background-image: url('special_1@2x.png');
            }
          }
          .text {
            line-height: 12px;
            vertical-align: top;
            font-size: 10px;
          }
        }
      }
      .support-count {
        position: absolute;
        right: 12px;
        bottom: 14px;
        padding: 0 8px;
        height: 24px;
        line-height: 24px;
        border-radius:  14px;
        background-color: rgba(0, 0, 0, .2);
        text-align: center;
        .count {
          vertical-align: top;
          font-size: 10px;
        }
        .icon-keyboard_arrow_right{
          margin-left: 2px;
          font-size: 10px;
          line-height: 24px;
        }
      }
    }
    .bulletin-wrapper {
      position: relative;
      padding: 0 22px 0 12px;
      overflow: hidden;
      height: 28px;
      background-color: rgba(7, 17, 27, .2);
      line-height: 28px;
      text-overflow: ellipsis;
      white-space: nowrap;
      .bulletin-title {
        margin-top: 8px;
        display: inline-block;
        width: 22px;
        height: 12px;
        background-image: url('bulletin@2x.png');
        background-size: 22px 12px;
        background-repeat: no-repeat;
      }
      .bulletin-text {
        margin: 0 4px;
        vertical-align: top;
        font-size: 10px;
      }
      .icon-keyboard_arrow_right{
        position: absolute;
        right: 12px;
        top: 8px;
        font-size: 10px;
       }
    }
    .background {
      position: absolute;
      top: 0;
      left: 0;
      z-index: -1;
      width: 100%;
      height: 100%;
      filter: blur(10px);
    }
    /* detail begin */
    .detail {
      position: fixed;
      top: 0;
      left: 0;
      z-index: 100;
      width: 100%;
      height: 100%;
      overflow: hidden;
      transition: all .5s linear;
      background-color: rgba(7, 17, 27, .7);
      backdrop-filter: blur(10px);
      &.fade-enter-active,
      &.fade-leave {
        background-color: rgba(7, 17, 27, .7);
        opacity: 1;
      }
      &.fade-enter,
      &.fade-leave-active {
        background-color: rgba(7, 17, 27, 0);
        opacity: 0;
      }
      .detail-wrapper {
        min-height: 100%;
        width: 100%;
        .detail-main {
          margin-top: 64px; /* 必须使用和footer相同的高度 */
          padding-bottom: 64px; /* 必须使用和footer相同的高度 */
          .name {
            font-size: 16px;
            font-weight: 700;
            text-align: center;
            line-height: 16px;
          }
          .star-wrapper {
            margin-top: 18px;
            padding: 2px 0;
            text-align: center;
          }
          .title {
            display: flex;
            margin: 28px auto 24px auto;
            width: 80%;
            .line {
              flex: 1;
              position: relative;
              top: -6px;
              border-bottom: 1px solid rgba(255, 255, 255, .3);
            }
            .text {
              padding: 0 12px;
              font-size: 14px;
              font-weight: 700;
            }
          }
          .supports {
            margin: 0 auto;
            width: 80%;
            .support-item {
              padding: 0 12px;
              margin-bottom: 12px;
              font-size: 0;
              &:last-child {
                margin-bottom: 0;
              }
              .icon {
                display:  inline-block;
                margin-right: 6px;
                width: 16px;
                height: 16px;
                background-size: 16px 16px;
                background-repeat: no-repeat;
                vertical-align: top;
                &.decrease {
                  background-image: url('decrease_1@2x.png');
                }
                &.discount {
                  background-image: url('discount_1@2x.png');
                }
                &.guarantee {
                  background-image: url('guarantee_1@2x.png');
                }
                &.invoice {
                  background-image: url('invoice_1@2x.png');
                }
                &.special {
                  background-image: url('special_1@2x.png');
                }
              }
              .text {
                line-height: 16px;
                font-size: 12px;
              }
            }
          }
          .bulletin {
            margin: 0 auto;
            width: 80%;
            .content {
              padding: 0 12px;
              line-height: 24px;
              font-size: 12px;
            }
          }
        }
      }
      .detail-close {
        position: relative;
        margin: -64px auto 0 auto; /* footer高度的负值 */
        width: 32px;
        height: 32px;
        clear: both;
        font-size: 32px;
      }
    }
  }
</style>

