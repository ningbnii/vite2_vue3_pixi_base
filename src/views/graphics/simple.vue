<template>
  <div class="canvas_box" ref="canvasBox">
    <div ref="myCanvas"></div>
  </div>
</template>
<script setup>
import * as PIXI from 'pixi.js'
import '@pixi/graphics-extras'
import { ref, onMounted, onUnmounted } from 'vue'

let myCanvas = ref(null)
let canvasBox = ref(null)
let app = {}

onMounted(() => {
  app = new PIXI.Application({
    width: canvasBox.value.clientWidth,
    height: canvasBox.value.clientHeight,
    backgroundColor: 0x1099bb,
    antialias: true,
    // resolution: window.devicePixelRatio || 1,
  })

  myCanvas.value.appendChild(app.view)

  const graphics = new PIXI.Graphics()

  // add to stage
  app.stage.addChild(graphics)

  // rectangle
  graphics.beginFill(0xff3300)
  graphics.drawRect(50, 50, 100, 100)
  graphics.endFill()

  // rectangle + line style 1
  graphics.lineStyle(2, 0x0000ff, 1)
  graphics.beginFill(0xff7007, 1)
  graphics.drawRect(50, 150, 100, 100)
  graphics.endFill()

  // rectangle + line style 2
  graphics.lineStyle(10, 0x0000ff, 1)
  graphics.beginFill(0xff7007, 1)
  graphics.drawRect(50, 250, 100, 100)
  graphics.endFill()

  // circle
  // draw a circle, set the lineStyle to 0 so it doesn't draw a line
  graphics.lineStyle(0)
  graphics.beginFill(0xff7007, 1)
  graphics.drawCircle(250, 100, 50)
  graphics.endFill()

  // circle + line style 1
  graphics.lineStyle(2, 0x0000ff, 1)
  graphics.beginFill(0xff7007, 1)
  graphics.drawCircle(250, 200, 50)
  graphics.endFill()

  // circle + line style 2
  graphics.lineStyle(10, 0x0000ff, 1)
  graphics.beginFill(0xff7007, 1)
  graphics.drawCircle(250, 300, 50)
  graphics.endFill()

  // ellipse + line style 1
  graphics.lineStyle(2, 0x0000ff, 1)
  graphics.beginFill(0xff7007, 1)
  graphics.drawEllipse(100, 420, 100, 50)
  graphics.endFill()

  // draw a triangle
  graphics.lineStyle(0)
  graphics.beginFill(0xffff0b, 1)
  graphics.moveTo(50, 450)
  graphics.lineTo(250, 450)
  graphics.lineTo(100, 600)
  graphics.lineTo(50, 450)
  graphics.endFill()

  // draw a rounded rectangle
  graphics.lineStyle(2, 0x0000ff, 1)
  graphics.beginFill(0xff7007, 1)
  graphics.drawRoundedRect(50, 650, 100, 100, 15)
  graphics.endFill()

  // draw star
  graphics.lineStyle(0)
  graphics.beginFill(0xffff0b, 1)
  graphics.drawStar(400, 100, 5, 50, 50)
  graphics.endFill()

  // draw polygon
  graphics.lineStyle(0)
  graphics.beginFill(0xffff0b, 1)
  graphics.drawPolygon([
    new PIXI.Point(400, 200),
    new PIXI.Point(500, 200),
    new PIXI.Point(400, 300),
    new PIXI.Point(300, 300),
    new PIXI.Point(400, 200),
  ])
  graphics.endFill()
})

onUnmounted(() => {
  // 销毁
  app.destroy()
})
</script>
<style lang="less" scoped>
.canvas_box {
  width: 100vw;
  height: 100vh;
}
</style>
