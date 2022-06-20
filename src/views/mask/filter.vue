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

  // radius
  const radius = 100

  // blur size
  const blurSize = 32

  app.loader.add('grass', 'assets/bg_grass.jpg').load(setup)

  function setup(loader, resources) {
    // background
    const bg = new PIXI.Sprite(resources.grass.texture)
    app.stage.addChild(bg)
    // size
    bg.width = app.screen.width
    bg.height = app.screen.height
    // circle
    const circle = new PIXI.Graphics()
    circle.beginFill(0xff0000)
    circle.drawCircle(radius + blurSize, radius + blurSize, radius)
    circle.endFill()
    // blur
    const blurFilter = new PIXI.filters.BlurFilter(blurSize)
    circle.filters = [blurFilter]
    // 创建一个矩形区域
    const rect = new PIXI.Rectangle(
      0,
      0,
      (radius + blurSize) * 2,
      (radius + blurSize) * 2
    )
    // 生成纹理
    const texture = app.renderer.generateTexture(
      circle,
      PIXI.SCALE_MODES.NEAREST,
      1,
      rect
    )
    // 生成遮罩
    const mask = new PIXI.Sprite(texture)
    // 添加到舞台
    app.stage.addChild(mask)
    // 给bg设置遮罩
    bg.mask = mask

    app.stage.interactive = true
    app.stage.on('pointermove', (e) => {
      // 更新遮罩位置
      mask.x = e.data.global.x - mask.width / 2
      mask.y = e.data.global.y - mask.height / 2
    })
  }
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
