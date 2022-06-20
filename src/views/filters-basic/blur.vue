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
    // backgroundColor: 0x1099bb,
    antialias: true,
    // resolution: window.devicePixelRatio || 1,
  })

  myCanvas.value.appendChild(app.view)

  // bg sprite
  const bg = PIXI.Sprite.from('/assets/bg_depth_blur.jpg')
  // add to stage
  app.stage.addChild(bg)
  // set size
  bg.width = app.screen.width
  bg.height = app.screen.height

  const littleDudes = PIXI.Sprite.from('/assets/depth_blur_dudes.jpg')
  // add to stage
  app.stage.addChild(littleDudes)
  // set position center
  littleDudes.x = app.screen.width / 2
  littleDudes.y = app.screen.height / 2
  // set anchor
  littleDudes.anchor.set(0.5)

  // 缩放到适合屏幕尺寸
  littleDudes.scale.x = littleDudes.scale.y = app.screen.width / 650

  const littleRobot = PIXI.Sprite.from('/assets/depth_blur_moby.jpg')
  app.stage.addChild(littleRobot)
  littleRobot.x = app.screen.width / 2
  littleRobot.y = app.screen.height / 2
  littleRobot.anchor.set(0.5)
  littleRobot.scale.set(0.5)

  const blurFilter1 = new PIXI.filters.BlurFilter()
  const blurFilter2 = new PIXI.filters.BlurFilter()

  littleDudes.filters = [blurFilter1]
  littleRobot.filters = [blurFilter2]

  // animation
  let count = 0
  app.ticker.add(() => {
    count += 0.01
    blurFilter1.blur = Math.sin(count) * 10
    blurFilter2.blur = Math.cos(count) * 10
  })
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
