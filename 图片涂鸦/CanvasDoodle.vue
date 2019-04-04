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
      <span class="el-icon-check" title="确定" @click="ok"></span>
    </div>
    <div class="edit-setting" v-if="edit">
      <ul class="sizes">
        <li :class="{active:sizes.current == item}" v-for="(item,index) in sizes.data" :key="index" @click="sizes.current = item"><span :style="{width:item+'px',height:item+'px'}"></span></li>
      </ul>
      <ul class="colors">
        <li :class="{active:colors.current == item}" v-for="(item,index) in colors.data" :key="index" @click="colors.current = item"><span :style="{borderColor:item,background:item}"></span></li>
      </ul> 
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
      sizes:{
        current:6,
        data:[6,10,14]
      },
      colors:{
        current:"#ff0f00",
        data:["#ff0f00","#ffbe00","#1a9bff","#1aad19","#4d4d4d"]
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
    // 鼠标在画布上按下
    canvasDown(e) {
      this.flag = true;
      // this.ctx.shadowBlur = 10;
      // this.ctx.shadowColor = "red";
      this.ctx.lineWidth = this.sizes.current;
      this.ctx.strokeStyle = this.colors.current;
      console.log(e.offsetX, e.offsetY);
      this.ctx.beginPath();
      this.ctx.moveTo(e.offsetX, e.offsetY);
    },
    // 鼠标在画布上松开
    canvasUp() {
      this.flag = false;
      this.imgData.push(this.ctx.getImageData(0, 0, this.width, this.height));
    },
    // 鼠标在画布上移动
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
    // 启用编辑模式
    toolEdit() {
      this.edit = true;
      this.move = false;
    },
    // 启用拖动模式
    toolMove() {
      this.move = true;
      this.edit = false;
    },
    // 鼠标在拖拽层按下
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
    // 鼠标在拖拽层拖动
    layerMove(e) {
      if (this.moveFlag) {
        let x = this.offset.x - e.offsetX + this.offset.oldx;
        let y = this.offset.y - e.offsetY + this.offset.oldy;
        canvasBox.scroll(x, y);
      }
    },
    // 鼠标在拖拽层松开
    layerUp() {
      this.moveFlag = false;
    },
    ok(){
      var _url = canvas.toDataURL('image/png');
      console.log(_url);
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
.edit-setting{
  display: inline-block;
  padding: 0 40px;
  background: #f5f5f5;
  ul{
    margin: 0 10px;
    padding:5px 0 10px;
  }
  ul,li{
    display: inline-block;
    vertical-align: middle;
  }
  li{
    width: 20px;
    margin:0 2px;
    cursor: pointer;
  }
  .sizes{
    span{
      background: #ccc;
      display: block;
      border-radius: 50%;
    }
    li.active{
      span{
        background: #9083ED !important;
      }
    }
  }
  .colors {
    span{
      display: block;
      width:16px;
      height: 16px;
      border-style: solid;
      border-width: 4px;
      box-sizing: border-box;
    }
    li.active{
      span{
        background: #fff !important;
      }
    }
  }
}
</style>
