<template>
  <div class="canvas_box" ref="canvasBox">
    <div ref="myCanvas"></div>
  </div>
</template>
<script setup>
import * as PIXI from 'pixi.js'
import '@pixi/graphics-extras'
import { SmoothGraphics } from '@pixi/graphics-smooth'
import { ref, onMounted, onUnmounted } from 'vue'

let myCanvas = ref(null)
let canvasBox = ref(null)
let app = {}

onMounted(() => {
  app = new PIXI.Application({
    width: canvasBox.value.clientWidth,
    height: canvasBox.value.clientHeight,
    // backgroundColor: 0x1099bb,
    antialias: true,
    autoDensity: true,
    resolution: window.devicePixelRatio,
  })

  myCanvas.value.appendChild(app.view)

  const graphics = new SmoothGraphics()

  graphics.pivot = { x: 300, y: 300 }
  graphics.position = { x: 300, y: 300 }

  // Rectangle
  graphics.lineStyle(0.1, 0xffffff, 1)

  graphics.drawRect(150.5, 150.5, 250, 250)
  graphics.endFill()

  graphics.moveTo(0, 0)
  graphics.lineTo(300, 300)
  graphics.lineTo(0, 300)
  graphics.lineTo(0, 0)
  graphics.endFill()

  app.stage.addChild(graphics)

  // app.ticker.add((delta) => {
  //   graphics.rotation -= 0.004 * delta
  // })
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
