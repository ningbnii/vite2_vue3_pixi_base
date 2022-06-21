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

  // particle container
  const sprites = new PIXI.ParticleContainer(10000, {
    scale: true,
    position: true,
    rotation: true,
    uvs: true,
    alpha: true,
  })

  app.stage.addChild(sprites)

  // create an array to store all the sprites
  const maggots = []

  const totalSprites = app.renderer instanceof PIXI.Renderer ? 10000 : 100

  for (let i = 0; i < totalSprites; i++) {
    // create a new Sprite
    const dude = new PIXI.Sprite.from('assets/bunny.png')

    // anchor the sprite's center
    dude.anchor.set(0.5)

    // scale
    dude.scale.set(Math.random() * 0.3 + 0.8)

    // set a random position
    dude.x = Math.random() * app.screen.width
    dude.y = Math.random() * app.screen.height

    // tint
    dude.tint = Math.random() * 0x808080

    // direction
    dude.direction = Math.random() * Math.PI * 2

    // creates a random speed between 0 and 2
    dude.speed = Math.random() * 2 + 1

    // this number will be used to modify the direction of the dude over time
    dude.turningSpeed = Math.random() - 0.8

    // offset
    dude.offset = Math.random() * 100

    // add the dude to the sprites array
    maggots.push(dude)

    // add the dude to the stage
    sprites.addChild(dude)
  }

  // create a bounding box box for the little dudes
  const dudeBoundsPadding = 100

  const dudeBounds = new PIXI.Rectangle(
    -dudeBoundsPadding,
    -dudeBoundsPadding,
    app.screen.width + dudeBoundsPadding * 2,
    app.screen.height + dudeBoundsPadding * 2
  )

  let tick = 0

  // listen for animate update
  app.ticker.add((delta) => {
    // loop through all the dudes and update their position
    for (let i = 0; i < maggots.length; i++) {
      const dude = maggots[i]

      // scale
      dude.scale.y = Math.sin(tick + dude.offset) * 0.05 + 0.95

      // increment the direction the dude is facing
      dude.direction += dude.turningSpeed * 0.01

      // move the dude in the direction of his direction, multiplied by the speed
      dude.x += Math.sin(dude.direction) * (dude.speed * dude.scale.y)
      dude.y += Math.cos(dude.direction) * (dude.speed * dude.scale.y)

      // rotation
      dude.rotation = -dude.direction + Math.PI

      // wrap the dudes position around the screen
      if (dude.x < dudeBounds.x) {
        dude.x += dudeBounds.width
      } else if (dude.x > dudeBounds.x + dudeBounds.width) {
        dude.x -= dudeBounds.width
      }

      if (dude.y < dudeBounds.y) {
        dude.y += dudeBounds.height
      } else if (dude.y > dudeBounds.y + dudeBounds.height) {
        dude.y -= dudeBounds.height
      }

      tick += 0.1
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
