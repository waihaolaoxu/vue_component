<template>
  <div class="tc">
    <div class="pr">
      <div class="canvas-box" ref="canvasBox">
        <canvas ref="canvas" :width="width" :height="height" :style="{'width':`${width}px`,'height':`${height}px`}" @mousedown="canvasDown($event)" @mouseup="canvasUp($event)" @mousemove="canvasMove($event)">
        </canvas>
      </div>
      <div class="move-layer" v-if="move" @mousedown="layerDown($event)" @mouseup="layerUp($event)" @mousemove="layerMove($event)">按住鼠标可拖动图片</div>
    </div>
    <div class="toolbar">
      <span class="el-icon-rank" title="移动" @click="toolMove" :class="{active:move}"></span>
      <span class="el-icon-edit" title="编辑" @click="toolEdit" :class="{active:edit}"></span>
      <span class="el-icon-back" title="回退" @click="revoke"></span>
      <span class="el-icon-check" title="确定"></span>
    </div>
  </div>
</template>
<script>
var canvas = null;
var canvasBox = null;
export default {
  data() {
    return {
      //工具栏
      move: false,
      edit: true,
      moveFlag: false,
      offset: {
        x: 0,
        y: 0
      },

      // canvas
      width: 200,
      height: 200,
      ctx: null,
      imgData: [],
      flag: false
    };
  },
  props: {
    img: {
      type: String
    }
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      canvas = this.$refs.canvas;
      canvasBox = this.$refs.canvasBox;
      this.ctx = canvas.getContext("2d");
      var img = new Image();
      img.src = this.img;
      img.onload = () => {
        this.width = img.width;
        this.height = img.height;
        setTimeout(() => {
          this.ctx.drawImage(img, 0, 0, this.width, this.height);
          this.imgData.push(
            this.ctx.getImageData(0, 0, this.width, this.height)
          );
        }, 100);
      };
    },
    canvasDown(e) {
      this.ctx.lineWidth = 10;
      this.ctx.shadowBlur = 10;
      this.ctx.shadowColor = "red";
      this.ctx.strokeStyle = "red";
      this.flag = true;
      console.log(e.offsetX, e.offsetY);
      this.ctx.beginPath();
      this.ctx.moveTo(e.offsetX, e.offsetY);
    },
    canvasUp() {
      this.flag = false;
      this.imgData.push(this.ctx.getImageData(0, 0, this.width, this.height));
    },
    canvasMove(e) {
      if (this.flag) {
        this.ctx.lineTo(e.offsetX, e.offsetY);
        this.ctx.stroke();
      }
    },
    // 撤销一步操作
    revoke() {
      let data = this.imgData,
        len = data.length;
      if (len > 1) {
        this.ctx.clearRect(0, 0, this.width, this.height);
        this.ctx.putImageData(data[len - 2], 0, 0);
        this.imgData.pop();
      }
    },

    toolMove() {
      this.move = true;
      this.edit = false;
    },
    toolEdit() {
      this.edit = true;
      this.move = false;
    },
    layerDown(e) {
      this.moveFlag = true;
      this.offset = {
        x: e.offsetX,
        y: e.offsetY,
        oldx:canvasBox.scrollLeft,
        oldy:canvasBox.scrollTop
      };
      console.log("left", canvasBox.scrollLeft);
      console.log("top", canvasBox.scrollTop);
    },
    layerMove(e) {
      if (this.moveFlag) {
        let x = this.offset.x - e.offsetX + this.offset.oldx;
        let y = this.offset.y - e.offsetY + this.offset.oldy;
        canvasBox.scroll(x, y);
      }
    },
    layerUp() {
      this.moveFlag = false;
    }
  }
};
</script>
<style lang="scss" scoped>
.tc {
  text-align: center;
}
.pr {
  position: relative;
}
.canvas-box {
  height: 500px;
  overflow: hidden;
}
.move-layer {
  position: absolute;
  background: rgba(0,0,0,.2);
  width: 100%;
  height: 100%;
  line-height: 500px;
  left: 0;
  top: 0;
  cursor: move;
  color:rgba(255,255,255,.3);
  user-select: none;
  font-size: 50px;
}
.toolbar {
  height: 40px;
  line-height: 40px;
  font-size: 20px;
  background: #f5f5f5;
  margin: 20px 0;
  span {
    padding: 0 10px;
    color: #999;
    cursor: pointer;
    &:hover {
      color: #000;
    }
    &.active {
      color: #000;
    }
  }
}
</style>
