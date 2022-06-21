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
    // backgroundColor: 0x1099bb,
    antialias: true,
    autoDensity: true,
    resolution: window.devicePixelRatio,
  })

  myCanvas.value.appendChild(app.view)

  app.loader.add('assets/spritesheet/fighter.json').load(onAssetsLoaded)

  function onAssetsLoaded(loader, resources) {
    const frames = []

    for (let i = 0; i < 30; i++) {
      const val = i < 10 ? `0${i}` : i
      frames.push(PIXI.Texture.from(`rollSequence00${val}.png`))
    }

    // create an AnimatedSprite (brings back memories from the days of Flash, right ?)
    const anim = new PIXI.AnimatedSprite(frames)

    /**
     * an animated sprite inherits all the properties of a PIXI sprite
     * so you can change its position, its anchor, its scale, etc
     */
    anim.x = app.screen.width / 2
    anim.y = app.screen.height / 2
    anim.anchor.set(0.5)
    anim.scale.set(1)

    // animation speed
    anim.animationSpeed = 0.5

    // play the animation
    anim.play()

    // add the sprite to the stage
    app.stage.addChild(anim)

    // start the animation
    app.ticker.add(() => {
      anim.rotation += 0.01
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
