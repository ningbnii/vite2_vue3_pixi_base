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
    autoDensity: true,
    resolution: window.devicePixelRatio,
  })

  myCanvas.value.appendChild(app.view)
  app.stage.interactive = true

  // sprite
  const bg = PIXI.Sprite.from('assets/bg_rotate.jpg')
  bg.x = app.screen.width / 2
  bg.y = app.screen.height / 2
  bg.anchor.set(0.5)
  app.stage.addChild(bg)

  // container
  const container = new PIXI.Container()
  container.x = app.screen.width / 2
  container.y = app.screen.height / 2

  // sprites
  const bgFront = PIXI.Sprite.from('assets/bg_scene_rotate.jpg')
  bgFront.anchor.set(0.5)

  const light1 = PIXI.Sprite.from('assets/light_rotate_1.png')
  light1.anchor.set(0.5)

  const light2 = PIXI.Sprite.from('assets/light_rotate_2.png')
  light2.anchor.set(0.5)

  const panda = PIXI.Sprite.from('assets/panda.png')
  panda.anchor.set(0.5)

  // add to container
  container.addChild(bgFront, light1, light2, panda)

  // add to stage
  app.stage.addChild(container)

  // create a moving shape
  const mask = new PIXI.Graphics()
  app.stage.addChild(mask)
  // position
  mask.x = app.screen.width / 2
  mask.y = app.screen.height / 2
  mask.lineStyle(0)

  // container mask
  container.mask = mask

  // pointertap
  app.stage.on('pointertap', () => {
    if (!container.mask) {
      container.mask = mask
    } else {
      container.mask = null
    }
  })

  // animation
  let count = 0
  app.ticker.add(() => {
    bg.rotation += 0.01
    bgFront.rotation -= 0.01
    light1.rotation += 0.02
    light2.rotation += 0.01

    // panda scale
    panda.scale.x = 1 + Math.sin(count) * 0.02
    panda.scale.y = 1 + Math.cos(count) * 0.02

    count += 0.1
    // update the shape
    mask.clear()
    mask.beginFill(0x0, 0.5)
    mask.moveTo(-120 + Math.sin(count) * 20, -100 + Math.cos(count) * 20)
    mask.lineTo(120 + Math.cos(count) * 20, -100 + Math.sin(count) * 20)
    mask.lineTo(120 + Math.sin(count) * 20, 100 + Math.cos(count) * 20)
    mask.lineTo(-120 + Math.cos(count) * 20, 100 + Math.sin(count) * 20)

    // rotation
    mask.rotation = count * 0.1
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
