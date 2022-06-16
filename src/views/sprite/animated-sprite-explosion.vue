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

  app.stop()

  app.loader
    .add('spritesheet', 'assets/spritesheet/mc.json')
    .load(onAssetsLoaded)

  function onAssetsLoaded(loader, resources) {
    // create an array to store the textures
    const frames = []
    for (let i = 0; i < 26; i++) {
      frames.push(PIXI.Texture.from(`Explosion_Sequence_A ${i + 1}.png`))
    }

    for (let i = 0; i < 50; i++) {
      // create an explosion animated sprite
      const explosion = new PIXI.AnimatedSprite(frames)

      // set the anchor point so animation faces the right way
      explosion.anchor.set(0.5)

      // random position
      explosion.x = Math.random() * app.screen.width
      explosion.y = Math.random() * app.screen.height

      // random rotation
      explosion.rotation = Math.random() * Math.PI * 2

      // random scaling
      explosion.scale.set(Math.random() * 0.5 + 0.75)

      // goto and play a random frame
      explosion.gotoAndPlay(Math.random() * 27)

      // add the explosion to the stage
      app.stage.addChild(explosion)
    }
    // start
    app.start()
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
