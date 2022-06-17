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

  app.stage.interactive = true

  const bg = PIXI.Sprite.from('assets/bg_plane.jpg')
  app.stage.addChild(bg)

  const cells = PIXI.Sprite.from('assets/cells.png')
  // 放大1.5倍
  cells.scale.set(2)

  // sprite
  const mask = PIXI.Sprite.from('assets/flowerTop.png')
  mask.anchor.set(0.5)
  mask.x = app.screen.width / 2
  mask.y = app.screen.height / 2

  // 给cells设置遮罩
  cells.mask = mask

  app.stage.addChild(cells, mask)

  const target = new PIXI.Point()

  function reset() {
    // random point
    target.x = Math.floor(Math.random() * app.screen.width)
    target.y = Math.floor(Math.random() * app.screen.height)
  }

  reset()

  // animation
  app.ticker.add(() => {
    // move the mask to a random position each frame
    // 从当前位置移动到目标位置，有一个缓动效果
    mask.x += (target.x - mask.x) * 0.1
    mask.y += (target.y - mask.y) * 0.1

    // if the mask has arrived at the target, then reset
    if (Math.abs(mask.x - target.x) < 1 && Math.abs(mask.y - target.y) < 1) {
      reset()
    }
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
