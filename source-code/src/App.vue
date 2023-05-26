<template>
  <h5>功能</h5>
  客制化節點(圖片,顏色,DOM隨意寫), 滑鼠Hover顯示更多資訊, 放大縮小,
  單一節點點擊可以收合組織<br />

  <button @click="zoomIn">+</button>
  <button @click="zoomOut">-</button>
  <button @click="restoreScale">reset</button>

  <div class="container">
    <vue-tree
      style="width: 1000px; height: 500px; border: 1px solid gray"
      :dataset="richMediaData"
      :config="treeConfig"
      ref="tree"
      @wheel="handleWheel"
    >
      <template v-slot:node="{ node, collapsed }">
        <div
          class="rich-media-node"
          :style="{ border: collapsed ? '2px solid grey' : '' }"
          @mousemove="handleMouseMove($event, node.name)"
          @mouseout="handleMouseOut"
        >
          <img
            :src="node.avatar"
            style="width: 48px; height: 48px; border-raduis: 4px"
          />
          <!-- <span style="padding: 4px 0; font-weight: bold"
            >名稱:{{ node.name }}</span
          > -->
          <span style="padding: 4px 0; font-weight: bold"
            >$${{ node.value }}</span
          >
          <!-- <button class="inline w-100">Hover Me</button> -->
        </div>
      </template>
    </vue-tree>
  </div>
  <div
    class="c-img"
    :style="{ top: imgTop, left: imgLeft, display: imgDisplay }"
  >
    <!-- <img src="https://fakeimg.pl/50/" /> -->
    <div class="c-description">
      <p style="color: white">{{ description }}</p>
    </div>
  </div>
  <h5>參考</h5>
  https://www.twblogs.net/a/5b8953162b71775d1ce135a6 <br />
  https://github.com/ssthouse/tree-chart/blob/master/docs/vue-tree-chart-CN.md
  <br />
</template>

<script>
import { richMediaData } from "@/dta/index";
import VueTree from "./vue-tree/VueTree.vue";
export default {
  name: "treemap",
  components: { VueTree },
  data() {
    return {
      richMediaData,
      treeConfig: { nodeWidth: 120, nodeHeight: 80, levelHeight: 200 },
      imgTop: "",
      imgLeft: "",
      imgDisplay: "none",
      description: "",
      zoom: {
        zoomInMax: 5,
        zoomCounter: 0,
        zoomOutMax: -5,
      },
    };
  },
  methods: {
    handleMouseMove(event, param) {
      // console.log(event, param);
      this.imgTop = event.clientY + 10 + "px";
      this.imgLeft = event.clientX + 10 + "px";
      this.imgDisplay = "inline";
      this.description = param;
    },
    handleMouseOut() {
      this.imgDisplay = "none";
    },
    zoomIn() {
      if (this.zoom.zoomCounter <= this.zoom.zoomInMax) {
        this.zoom.zoomCounter++;
        this.$refs.tree.zoomIn();
      }
    },
    zoomOut() {
      if (this.zoom.zoomCounter >= this.zoom.zoomOutMax) {
        this.zoom.zoomCounter--;
        this.$refs.tree.zoomOut();
      }
    },
    restoreScale() {
      this.zoom.zoomCounter = 0;
      this.$refs.tree.restoreScale();
    },
    handleWheel(event) {
      event.preventDefault();
      if (event.deltaY < 0) {
        this.zoomIn();
      } else if (event.deltaY > 0) {
        this.zoomOut();
      }
    },
    /**
    缩小: this.$refs.tree.zoomIn()
    放大: this.$refs.tree.zoomOut()
    恢复原始大小: this.$refs.tree.restoreScale()
    */
  },
};
</script>

<style scoped lang="less">
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.rich-media-node {
  width: 100px;
  padding: 8px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: center;
  color: white;
  background-color: #f7c616;
  border-radius: 4px;
}

.c-img {
  display: none;
  position: fixed;
  z-index: 10;
  opacity: 0.5;
}
.c-description {
  background-color: black;
  padding: 10px;
}
</style>
