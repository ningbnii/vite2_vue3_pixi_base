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

  app.stop()

  app.loader
    .add('spriteSheet', 'assets/spritesheet/0123456789.json')
    .load(onAssetsLoaded)

  function onAssetsLoaded(loader, resources) {
    const frames = []

    for (let i = 0; i < 10; i++) {
      const frameKey = `0123456789 ${i}.ase`
      const texture = PIXI.Texture.from(frameKey)
      const time = resources.spriteSheet.data.frames[frameKey].duration
      frames.push({ texture, time })
    }

    console.log(frames)

    const scaling = 2

    // create a slow animation sprite
    const slow = new PIXI.AnimatedSprite(frames)
    slow.anchor.set(0.5)
    slow.scale.set(scaling)
    slow.animationSpeed = 0.5

    slow.x = app.screen.width / 2 - slow.width / 2
    slow.y = app.screen.height / 2
    slow.play()
    app.stage.addChild(slow)

    // create a fast animation sprite
    const fast = new PIXI.AnimatedSprite(frames)
    fast.anchor.set(0.5)
    fast.scale.set(scaling)
    fast.x = app.screen.width / 2 + fast.width / 2
    fast.y = app.screen.height / 2
    fast.play()
    app.stage.addChild(fast)

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
