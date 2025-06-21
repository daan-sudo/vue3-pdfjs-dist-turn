<template>
  <div
    class="box"
    style="display: flex; align-items: center; justify-content: center"
  >
    <div
      id="flipbook"
      v-show="isShowBook"
      :style="{
        marginLeft: flipbookml,
      }"
    >
      <div
        class="page"
        :class="`page-${index + 1}`"
        v-for="(item, index) in pageInfo.totalPage"
        :key="index"
        :style="{ transform: `scale(${scale})` }"
      >
        <div class="leftShadow" v-if="index % 2 !== 0"></div>
        <div class="rightShadow" v-if="index % 2 === 0"></div>
        <canvas :id="`canvas${index + 1}`"></canvas>
        <div
          @click="nextPage"
          v-if="
            index % 2 === 0 ||
            (!pageInfo.isDoublePage &&
              pageInfo.currentPage !== pageInfo.pageNum)
          "
          class="next"
        >
          <el-icon color="#fff"><ArrowRight /></el-icon>
        </div>
        <div
          @click="prePage"
          v-if="
            index % 2 !== 0 ||
            (!pageInfo.isDoublePage && pageInfo.currentPage !== 1)
          "
          class="pre"
        >
          <el-icon color="#fff"><ArrowLeft /></el-icon>
        </div>
      </div>
    </div>
    <!-- <div id="flipbook" style="width: auto; height: auto; display: flex">
      <div
        class="page1"
        :class="`page-${index + 1}`"
        style="
          /* width: 400px;
          height: 400px; */
          background-color: pink;
          display: flex;
          align-items: center;
          justify-content: center;
          overflow: hidden;
        "
        v-for="(item, index) in 10"
      >
        <div
          id="test"
          :style="{ width: `${widthPage}px`, height: `${heightPage}px` }"
        >
          第{{ index }}页
        </div>
      </div>
    </div> -->
    <div
      style="width: 100vw; height: 500px"
      v-loading="true"
      v-show="!isShowBook"
    ></div>
    <footer>
      <el-icon
        style="position: absolute; left: 10px"
        @click="zoomOut"
        color="#fff"
        ><Operation
      /></el-icon>
      <el-icon @click="gotoTargetPage(1)" color="#fff"><DArrowLeft /></el-icon>
      <el-icon @click="prePage" color="#fff"><Back /></el-icon>
      <el-input
        @focus="pageNum = ''"
        @blur="pageBlur"
        v-model="pageNum"
        style="width: 140px; text-align: center"
      />
      <el-icon @click="nextPage" color="#fff"><Right /></el-icon>
      <el-icon @click="gotoTargetPage(pageInfo.totalPage)" color="#fff"
        ><DArrowRight
      /></el-icon>
    </footer>

    <!-- 去<input type="text" v-model="gotoPage" />页
    <button @click="gotoTargetPage(gotoPage)">去指定页面</button> -->
  </div>
  <el-drawer
    :size="pageInfo.isDoublePage ? '30%' : '50%'"
    v-model="drawer"
    title="目录"
    class="drawer"
    direction="ltr"
  >
    <el-menu
      default-active="2"
      class="el-menu-vertical-demo"
      @open="handleOpen"
      @close="handleClose"
    >
      <el-sub-menu
        v-for="(item, index) in menuList"
        :key="index"
        :index="index"
      >
        <template #title>
          <span>{{ item.name }}</span>
        </template>
        <el-menu-item v-for="(item1, index1) in item.children" :key="index1">{{
          item1.name
        }}</el-menu-item>
      </el-sub-menu>

      <!-- <el-sub-menu index="1">
        <template #title>
          <el-icon><location /></el-icon>
          <span>Navigator One</span>
        </template>
        <el-menu-item-group title="Group One">
          <el-menu-item index="1-1">item one</el-menu-item>
          <el-menu-item index="1-2">item two</el-menu-item>
        </el-menu-item-group>
        <el-menu-item-group title="Group Two">
          <el-menu-item index="1-3">item three</el-menu-item>
        </el-menu-item-group>
        <el-sub-menu index="1-4">
          <template #title>item four</template>
          <el-menu-item index="1-4-1">item one</el-menu-item>
        </el-sub-menu>
      </el-sub-menu> -->
      <!-- <el-menu-item index="2">
        <el-icon><icon-menu /></el-icon>
        <span>Navigator Two</span>
      </el-menu-item>
      <el-menu-item index="3" disabled>
        <el-icon><document /></el-icon>
        <span>Navigator Three</span>
      </el-menu-item>
      <el-menu-item index="4">
        <el-icon><setting /></el-icon>
        <span>Navigator Four</span>
      </el-menu-item> -->
    </el-menu>
  </el-drawer>
</template>

<script setup lang="ts">
import {
  ref,
  nextTick,
  onMounted,
  reactive,
  watchEffect,
  computed,
  h,
} from "vue";
import $ from "jquery";
// eslint-disable-next-line @typescript-eslint/no-unused-vars
import "@/utils/turnjs4/lib/turn.js";
import * as pdfjs from "pdfjs-dist";
import url_01 from "@/assets/pdf/1.pdf";
pdfjs.GlobalWorkerOptions.workerSrc = "/pdf.worker.js"; //这个文件地址一定要找对，我是放在public里面所以用 /
async function gotoTargetPage(pageNum: number) {
  // if (pageNum % 2 !== 0) {
  //   pageNum = pageNum + 1;
  // }
  if (pageNum < 1) {
    pageNum = 1;
  }
  $("#flipbook").turn("page", pageNum); //翻到指定页数
}
const menuList = ref([
  {
    name: "第一章",
    children: [
      {
        name: "第一节",
      },
      {
        name: "第二节",
      },
    ],
  },
  {
    name: "第二章",
    children: [
      {
        name: "第一节",
      },
      {
        name: "第二节",
      },
    ],
  },
  {
    name: "第三章",
    children: [
      {
        name: "第一节",
      },
      {
        name: "第二节",
      },
    ],
  },
  {
    name: "第四章",
    children: [
      {
        name: "第一节",
      },
      {
        name: "第二节",
      },
    ],
  },
  {
    name: "第五章",
    children: [
      {
        name: "第一节",
      },
      {
        name: "第二节",
      },
      {
        name: "第一节",
      },
      {
        name: "第二节",
      },
      {
        name: "第一节",
      },
      {
        name: "第二节",
      },
      {
        name: "第一节",
      },
      {
        name: "第二节",
      },
      {
        name: "第一节",
      },
      {
        name: "第二节",
      },
      {
        name: "第一节",
      },
      {
        name: "第二节",
      },
    ],
  },
]);
const scale = ref(1); // 缩放比例
const pageNum = ref("");
const drawer = ref(false);
const loadingTask = ref<any>(null);
const isShowBook = ref(false); // 是否显示书籍
const pageInfo = reactive({
  currentPage: 1,
  totalPage: 0,
  canvasWidth: 0,
  canvasHeight: 0,
  pageNum: 0,
  isDoublePage: computed(() => {
    return screenWidth.value / screenHeight.value > 1;
  }),
});
const flipbookml = computed(() => {
  if (pageInfo.isDoublePage) {
    return pageInfo.currentPage === 1 ? -pageInfo.canvasWidth + "px" : 0;
  } else {
    return 0;
  }
});
watchEffect(() => {
  pageNum.value = `${pageInfo.currentPage}-${pageInfo.currentPage + 1}/${
    pageInfo.totalPage
  }`;
});
const screenWidth = ref(window.innerWidth);
const screenHeight = ref(window.innerHeight - 80);
console.log(screenWidth.value, "screenWidth.value", screenHeight.value);
async function scaleNum(pdf: any) {
  const page1 = await pdf.getPage(1);
  const viewport = page1.getViewport({ scale: 1 });
  // console.log(viewport, "viewport");
  const width = viewport.width; //这是单页的宽度
  const height = viewport.height; //这是单页的宽度
  const pdfwidthHeightRatio = width / height; //pdf宽高比
  const pdfHeightWidthRatio = height / width; //pdf高宽比
  const widthHeightRatio = screenWidth.value / screenHeight.value; //宽高比
  const heightWidthRatio = screenHeight.value / screenWidth.value; //高宽比
  // console.log(pageInfo, "pageInfo.isDoublePage");
  if (pageInfo.isDoublePage) {
    // 双页
    if (widthHeightRatio > pdfwidthHeightRatio * 2) {
      // console.log(1);
      const result = screenHeight.value / height;
      return +result - 0.05;
    } else {
      // console.log(2);
      const result = screenWidth.value / (width * 2);
      // console.log(+result.toFixed(2), "result");
      return +result;
    }
  } else {
    // 单页
    if (widthHeightRatio > pdfwidthHeightRatio) {
      // console.log(3);
      const result = screenHeight.value / height;
      return +result - 0.05;
    } else {
      // console.log(4);
      const result = screenWidth.value / width;
      console.log(+result.toFixed(2), "result");
      return +result - 0.1;
    }
  }
}
function nextPage() {
  $("#flipbook").turn("next");
  // // $("#flipbook").turn("zoom", 0.8);
  // $("#flipbook").turn("size", 1000, 600);
  // scale.value += 0.1;
  // return;
  // if (pageInfo.currentPage === pageInfo.totalPage) return;
  // console.log(pageInfo.currentPage);
  // const skip = pageInfo.isDoublePage ? 2 : 1;
  // gotoTargetPage(pageInfo.currentPage + skip);
}
function prePage() {
  $("#flipbook").turn("previous");
  // if (pageInfo.currentPage === 1) return;
  // const skip = pageInfo.isDoublePage ? 2 : 1;
  // gotoTargetPage(pageInfo.currentPage - skip);
}
async function pdfInitSelf(url) {
  const pdfContainer = document.querySelector("#flipbook");
  if (!pdfContainer) {
    return;
  }
  loadingTask.value = pdfjs.getDocument({
    url: url,
  });
  const pdf = await loadingTask.value.promise;
  console.log(pdf, "pdf", pdf.numPages);
  pageInfo.totalPage = pdf.numPages;
  for (let i = 0; i < pdf.numPages; i++) {
    const page = await pdf.getPage(i + 1);
    const scale = await scaleNum(pdf);
    // console.log(scale, "scale");
    const viewport = page.getViewport({
      scale: scale,
    });
    // console.log(viewport, "viewport");
    const canvas = document.getElementById(
      `canvas${i + 1}`
    ) as HTMLCanvasElement;
    canvas.width = viewport.width;
    canvas.height = viewport.height;
    pageInfo.canvasWidth = viewport.width; // 双页显示时，宽度乘以2
    pageInfo.canvasHeight = viewport.height;
    const context = canvas.getContext("2d");
    if (!context) {
      return;
    }
    const renderContext = {
      canvasContext: context,
      viewport: viewport,
    };
    await page.render(renderContext).promise;
  }
}
function pageBlur() {
  gotoTargetPage(+pageNum.value);
  pageNum.value = `${pageInfo.currentPage}-${pageInfo.currentPage + 1}/${
    pageInfo.totalPage
  }`;
}
const widthPage = ref(400);
const heightPage = ref(600);
onMounted(async () => {
  window.onresize = async function () {
    // $("#flipbook").turn("destroy");
    // await pdfInitSelf(url_01); // 初始化PDF
    // isShowBook.value = true; // 是否显示书籍
    // await onTurn();
  };
  function resizeFlipbook() {
    var width = window.innerWidth; // 获取当前窗口宽度
    var height = window.innerHeight; // 获取当前窗口高度
    // // 根据需要调整宽高比例
    // var scale = Math.min(width / 800, height / 600); // 保持原始宽高比例
    var scale = 0.8; // 保持原始宽高比例
    // pageInfo.canvasWidth = pageInfo.canvasWidth * scale;
    // pageInfo.canvasHeight = pageInfo.canvasHeight * scale;
    widthPage.value = 400 * scale;
    heightPage.value = 600 * scale;
    console.log(widthPage.value, "widthPage.value");
    console.log(heightPage.value, "heightPage.value");
    // $("#flipbook").turn("size", width * scale, height * scale);
    // $("#flipbook").turn("size", 800 * scale, 600 * scale);
    // $("#flipbook").turn("size", 800, 600);
    // $("#flipbook").turn("size", width * scale, height * scale);
    // $("#flipbook").turn("size", 800 * scale, 600 * scale);
    // $("#flipbook").turn("size", 800, 600);
    $("#flipbook").turn("size", 800 * scale, 600 * scale);
  }
  // document.addEventListener("click", (e) => {
  //   console.log(e.target.className, "e.target.className");
  // });
  await pdfInitSelf(url_01); // 初始化PDF
  isShowBook.value = true; // 是否显示书籍
  await onTurn();
  // pageNum.value = `${pageInfo.currentPage}-${pageInfo.currentPage + 1}/${
  //   pageInfo.totalPage
  // }`;
});
function zoomOut() {
  drawer.value = true;
}
const handleOpen = (key: string, keyPath: string[]) => {
  drawer.value = true;
};
const handleClose = (key: string, keyPath: string[]) => {
  console.log(key, keyPath);
  drawer.value = false;
};
const onTurn = () => {
  $("#flipbook").turn({
    autoCenter: true, //自动居中, 默认false
    height: pageInfo.canvasHeight, //高度
    // width: pageInfo.canvasWidth * 2, //宽度
    width: pageInfo.isDoublePage
      ? pageInfo.canvasWidth * 2
      : pageInfo.canvasWidth, //宽度
    // width: 952 / 2, //宽度
    // width: 800,
    // heitht: 600,
    display: pageInfo.isDoublePage ? "double" : "single", //单页显示/双页显示  single/double
    // display: "single", //单页显示/双页显示  single/double
    elevation: 50, //卷起角度, 默认50
    duration: 500, //翻页速度(毫秒), 默认600ms
    gradients: true, //翻页时的阴影渐变, 默认true
    shadow: false, //翻页时的阴影, 默认true
    acceleration: true, //硬件加速, 默认true, 如果是触摸设备设置为true
    page: 1, //设置当前显示第几页
    // pages: pageInfo.totalPage, //总页数/
    pages: 10, //总页数/
    zoom: 1, //缩放比例
    turnCorners: "bl,br,tl,tr", // 设置可翻页的页角(都试过了，乱写 4个角都能出发卷起)， bl,br   tl,tr   bl,tr
    when: {
      //监听事件
      turning: async function (e, page, view) {
        // console.log(e, page, view);
        // const pageWrapper = $(".page-wrapper").eq(0).children()[0];
        // pageWrapper.style.background = "#fff";

        // console.log(pageWrapper, "pageWrapper");
        // return;
        // // console.log(e, page, view);
        // // 翻页前触发
        // // $(".page-content").css("background-color", "#fff");
        // // 获取上一页内容（假设当前页为page，上一页为page-1）
        // console.log(page, "page");
        const currentPageEl = $(`.page-${page - 1}`);
        console.log(currentPageEl, "currentPageEl");
        // 设置翻过去的页面为透明，并添加阴影效果
        currentPageEl.css({
          "background-color": "rgba(255, 255, 255, 0.5)",
          "box-shadow": "0 0 20px rgba(0, 0, 0, 0.5)",
        });
        // 在翻过去的页面上创建一个镜像层
        var mirrorPageEl = currentPageEl.clone();
        mirrorPageEl.addClass("mirror-page");
        currentPageEl.after(mirrorPageEl);

        // 设置镜像层的样式
        mirrorPageEl.css({
          position: "absolute",
          top: 0,
          left: 0,
          "pointer-events": "none",
          opacity: 0.7,
        });
        // console.log(prevPage, "prevPage");
        // // const prevPageClone = $(`.page-${page - 1}`)
        // // console.log(prevPageClone, "prevPageClone");
        // // if (!prevPage.length) return;

        // // 克隆上一页并创建镜像
        // const mirrorPage = prevPage
        //   .clone()
        //   .css({
        //     position: "absolute",
        //     width: "100%",
        //     height: "100%",
        //     transform: "scaleX(-1)", // 水平镜像
        //     opacity: 0.8, // 背面透明度
        //     filter: "brightness(0.7)", // 背面变暗
        //     backfaceVisibility: "hidden", // 避免穿透
        //     zIndex: -1, // 置于背面
        //   })
        //   .attr("id", "mirror-page");
        // console.log(mirrorPage);
        // // 插入到当前翻页动画层
        // $(`.page-${page}`).append(mirrorPage);
      },
      turned: function (e, page) {
        // 移除上一次翻页添加的镜像层
        $(".mirror-page").remove();

        // 恢复当前页面的背景色和阴影效果
        $('.page[data-pageno="' + page + '"]').css({
          "background-color": "",
          "box-shadow": "",
        });
        // console.log(page, "page");
        // $("#mirror-page").remove();
        // console.log(e, page, pageCav.value.length);
        pageInfo.currentPage = page;
        // 翻页后触发
      },
    },
  });
};
</script>

<style lang="less">
.box {
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  position: relative;

  padding: 0;
  margin: 0;
}
// .page-content,
.page {
  border: 1px solid #ccc;
  overflow: hidden;
  position: relative;
  // width: 100%;
  // height: 100%;
  transform-style: preserve-3d;
  transform-style: true;
  // opacity: 0.5;
}
#flipbook {
  position: relative;
  top: -10px;
  // overflow: hidden;
  transition: all 0.3s ease;
}
.next,
.pre {
  width: 30px;
  height: 30px;
  background-color: rgba(0, 0, 0, 0.6);
  position: absolute;
  top: 50%;
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}
.next {
  right: 0;
}
.pre {
  left: 0;
}
footer {
  width: 100vw;
  height: 60px;
  background-color: #4d5153;
  position: absolute;
  bottom: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  // color: #fff;
  font-size: 40px;
  gap: 5px;
}
.el-icon {
  border: 1px solid #fff;
  cursor: pointer;
  :hover {
    color: #4d5153;
    background-color: #fff;
  }
}
/* 翻页容器需启用3D空间 */
.page-content {
  transform-style: preserve-3d;
  perspective: 1500px; /* 增强立体感 */
}

/* 翻页时的阴影渐变（需配合turn.js的gradients参数） */
.page {
  box-shadow: -10px 0 20px rgba(0, 0, 0, 0.1); /* 左侧阴影模拟厚度 */
}
.rightShadow {
  position: absolute;
  width: 40px;
  left: 0;
  height: 100%;
  z-index: 9999;
  background-image: -webkit-linear-gradient(
    left,
    rgba(53, 53, 53, 0.5) 0,
    rgba(53, 53, 53, 0.2) 40%,
    rgba(53, 53, 53, 0.1) 60%,
    rgba(200, 200, 200, 0) 100%
  );
}
.leftShadow {
  position: absolute;
  width: 40px;
  right: 0;
  height: 100%;
  z-index: 9999;
  background-image: -webkit-linear-gradient(
    right,
    rgba(53, 53, 53, 0.5) 0,
    rgba(53, 53, 53, 0.2) 40%,
    rgba(53, 53, 53, 0.1) 60%,
    rgba(200, 200, 200, 0) 100%
  );
}
.el-drawer {
  // height: 100% !important;
  // width: 300px;
  /* 其他样式 */
}
.turnjs-page {
  background-color: white !important; /* 强制设置翻页时的页面背景颜色为不透明白色 */
}
.turnjs-over {
  opacity: 0 !important; /* 隐藏翻页过程中的背面内容 */
}
/* 可选：调整翻页时的阴影效果 */
.turnjs-shadow {
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.2) !important; /* 自定义翻页阴影效果 */
}
</style>
