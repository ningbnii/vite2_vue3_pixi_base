<template>
  <div class="canvas_box" ref="canvasBox">
    <div ref="myCanvas"></div>
  </div>
</template>
<script setup>
import * as PIXI from 'pixi.js'
import { ref, onMounted, onUnmounted } from 'vue'
import bunnyImg from '../../assets/bunny.png'
let myCanvas = ref(null)
let canvasBox = ref(null)
let app = {}

onMounted(() => {
  app = new PIXI.Application({
    width: canvasBox.value.clientWidth,
    height: canvasBox.value.clientHeight,
    transparent: true,
    // resolution: window.devicePixelRatio || 1,
  })

  myCanvas.value.appendChild(app.view)

  // create a new sprite from an image path
  const bunny = new PIXI.Sprite.from(bunnyImg)

  // center the sprite's anchor point
  bunny.anchor.set(0.5)

  // move the sprite to the center of the screen
  bunny.x = app.screen.width / 2
  bunny.y = app.screen.height / 2

  // add the bunny to the stage
  app.stage.addChild(bunny)

  // listen for animate update
  app.ticker.add((delta) => {
    // rotate the bunny around its center
    bunny.rotation -= 0.01 * delta
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
