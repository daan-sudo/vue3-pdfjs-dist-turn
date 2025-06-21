<template>
  <div id="flipbook">
    <div class="hard">Turn.js</div>
    <div class="hard"></div>
    <div>Page 1</div>
    <div>Page 2</div>
    <div>Page 3</div>
    <div>Page 4</div>
    <div class="hard"></div>
    <div class="hard"></div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import "@/utils/turnjs4/lib/turn.js";
import $ from "jquery";
onMounted(() => {
  $("#flipbook").turn({
    width: 600,
    height: 400,
    autoCenter: true,
    elevation: 50, //卷起角度, 默认50
    duration: 500, //翻页速度(毫秒), 默认600ms
    gradients: true, //翻页时的阴影渐变, 默认true
    shadow: false, //翻页时的阴影, 默认true
    acceleration: true, //硬件加速, 默认true, 如果是触摸设备设置为true
    page: 1, //设置当前显示第几页
    // pages: pageInfo.totalPage, //总页数/
    turnCorners: "bl,br,tl,tr",
  });
});
</script>

<style lang="less" scoped>
#flipbook {
  // display: flex;
  // align-items: center;
  // justify-content: center;
  // width: 100vw;
  // height: 100vh;
  margin: 100px auto;
  div {
    background-color: #fff;
  }
}
</style>
