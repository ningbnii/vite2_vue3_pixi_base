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
    antialias: true,
    autoDensity: true,
    resolution: window.devicePixelRatio,
  })

  myCanvas.value.appendChild(app.view)

  // create a texture from an image path
  const texture = PIXI.Texture.from('assets/flowerTop.png')

  // create a second texture
  const texture2 = PIXI.Texture.from('assets/eggHead.png')

  // create a new Sprite using the texture
  const bunny = new PIXI.Sprite(texture)

  // center the sprites anchor point
  bunny.anchor.set(0.5)

  // move the sprite to the center of the screen
  bunny.x = app.screen.width / 2
  bunny.y = app.screen.height / 2

  // add the bunny to the stage
  app.stage.addChild(bunny)

  // make the bunny interactive
  bunny.interactive = true
  // buttonMode will make the bunny behave like a button
  bunny.buttonMode = true

  // tap the bunny to change its texture
  let bol = false
  bunny.on('pointertap', () => {
    // change the texture
    bol = !bol
    if (bol) {
      bunny.texture = texture2
    } else {
      bunny.texture = texture
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
