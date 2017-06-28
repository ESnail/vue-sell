<template>
  <div class="title">
    <div class="line"></div>
    <div class="text">{{text}}</div>
    <div class="line"></div>
  </div>
</template>

<script type="text/ecmascript-6">

export default {
  name: 'title',
  // props: ['text']
  props: {
    text: {
      type: String
    }
  }
};
</script>

<style type="text/css">
  .title {
  	display: flex;
  	margin: 28px auto 24px auto;
  	width: 80%;
  }
  .title .line {
	flex: 1;
	position: relative;
	top: -6px;
	border-bottom: 1px solid rgba(255, 255, 255, .3);
  }
  .title .text {
  	padding: 0 12px;
  	font-size: 14px;
  	font-weight: 700;
  }
</style>

