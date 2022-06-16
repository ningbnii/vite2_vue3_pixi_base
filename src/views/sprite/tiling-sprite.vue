<template>
  <div class="canvas_box" ref="canvasBox">
    <div ref="myCanvas"></div>
  </div>
</template>
<script setup>
import * as PIXI from 'pixi.js'
import { ref, onMounted, onUnmounted } from 'vue'

let myCanvas = ref(null)
let canvasBox = ref(null)
let app = {}

onMounted(() => {
  app = new PIXI.Application({
    width: canvasBox.value.clientWidth,
    height: canvasBox.value.clientHeight,
    backgroundColor: 0x1099bb,
    // resolution: window.devicePixelRatio || 1,
  })

  myCanvas.value.appendChild(app.view)

  // create a texture from an image path
  const texture = PIXI.Texture.from('assets/p2.jpeg')

  // create a tiling sprite
  const tilingSprite = new PIXI.TilingSprite(
    texture,
    app.screen.width,
    app.screen.height
  )

  // add the sprite to the stage
  app.stage.addChild(tilingSprite)

  // animation
  app.ticker.add(() => {
    tilingSprite.tilePosition.x += 1
    tilingSprite.tilePosition.y += 1

    // scale the sprite
    tilingSprite.tileScale.x = 2 + Math.sin(app.ticker.lastTime / 1000)
    tilingSprite.tileScale.y = 2 + Math.cos(app.ticker.lastTime / 1000)
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
