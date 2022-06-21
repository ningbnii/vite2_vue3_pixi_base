<template>
  <div class="canvas_box" ref="canvasBox">
    <div ref="myCanvas"></div>
  </div>
</template>
<script setup>
import * as PIXI from 'pixi.js'
import { ref, onMounted, onUnmounted } from 'vue'
import bunny from '../../assets/bunny.png'
let myCanvas = ref(null)
let canvasBox = ref(null)
let app = {}

onMounted(() => {
  app = new PIXI.Application({
    width: canvasBox.value.clientWidth,
    height: canvasBox.value.clientHeight,
    backgroundColor: 0x1099bb,
    antialias: true,
    autoDensity: true,
    resolution: window.devicePixelRatio,
  })

  myCanvas.value.appendChild(app.view)

  const container = new PIXI.Container()

  app.stage.addChild(container)

  // create a new texture
  const texture = PIXI.Texture.from(bunny)

  // create a 5x5 grid of bunnies
  for (let i = 0; i < 25; i++) {
    const bunny = new PIXI.Sprite(texture)
    bunny.anchor.set(0.5)
    bunny.x = (i % 5) * 40
    bunny.y = Math.floor(i / 5) * 40
    container.addChild(bunny)
  }

  // move the container to the center of the screen
  container.x = app.screen.width / 2
  container.y = app.screen.height / 2

  console.log(container.pivot.x, container.pivot.y)
  // center bunny sprite in local container coordinates
  container.pivot.x = container.width / 2
  container.pivot.y = container.height / 2

  console.log(container.pivot.x, container.pivot.y)

  // listen for animate update
  app.ticker.add((delta) => {
    // rotate the container!
    // use delta to create frame-independent transform
    container.rotation -= 0.01 * delta
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
