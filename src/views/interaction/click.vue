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

  // scale mode for all textures, will retain pixelation
  PIXI.settings.SCALE_MODE = PIXI.SCALE_MODES.NEAREST

  // sprite
  const sprite = PIXI.Sprite.from('assets/bunny.png')

  // set the initial position
  sprite.x = app.screen.width / 2
  sprite.y = app.screen.height / 2
  // anchor
  sprite.anchor.set(0.5)

  // 可交互
  sprite.interactive = true
  sprite.buttonMode = true

  // 每次点击放大1.25倍
  // sprite.on('pointerdown', () => {
  //   sprite.scale.x *= 1.25
  //   sprite.scale.y *= 1.25
  // })

  app.renderer.plugins.interaction.on('pointerdown', (event) => {
    sprite.scale.x *= 1.25
    sprite.scale.y *= 1.25
  })

  // add the sprite to the stage
  app.stage.addChild(sprite)
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
