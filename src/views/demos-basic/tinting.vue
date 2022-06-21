<template>
  <div class="canvas_box" ref="canvasBox">
    <div ref="myCanvas"></div>
  </div>
</template>
<script setup>
import * as PIXI from 'pixi.js'
import { ref, onMounted, onUnmounted } from 'vue'
import eggHeadImg from '../../assets/eggHead.png'
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

  // holder to store the aliens
  const aliens = []

  const totalDudes = 20

  for (let i = 0; i < totalDudes; i++) {
    // create a new sprite that uses the image name that we just generated as its source
    const dude = new PIXI.Sprite.from(eggHeadImg)

    // set the anchor point so the texture is centerd on the sprite
    dude.anchor.set(0.5)

    // set a random scale for the dude - no point them being the same size!
    dude.scale.set(Math.random() * 0.8 + 0.3)

    // finally lets set the dude to be at a random position..
    dude.x = Math.random() * app.screen.width
    dude.y = Math.random() * app.screen.height

    // set color value
    dude.tint = Math.random() * 0xffffff

    // create a random direction in radians
    dude.direction = Math.random() * Math.PI * 2

    // this number will be used to modify the direction of the dude over time
    dude.turningSpeed = Math.random() - 0.8

    // create a random speed for the dude between 2 - 4
    dude.speed = Math.random() * 3 + 1

    // finally we push the dude into the aliens array so it can be easily accessed later
    aliens.push(dude)

    // add to the stage
    app.stage.addChild(dude)
  }

  // create a bounding box box for the little dudes
  const dudeBoundsPadding = 100

  const dudeBounds = new PIXI.Rectangle(
    -dudeBoundsPadding,
    -dudeBoundsPadding,
    app.screen.width + dudeBoundsPadding * 2,
    app.screen.height + dudeBoundsPadding * 2
  )

  // listen for animate update
  app.ticker.add((delta) => {
    // loop through all the dudes and update their position
    for (let i = 0; i < aliens.length; i++) {
      const dude = aliens[i]

      // increment the direction the dude is facing
      dude.direction += dude.turningSpeed * 0.01

      // move the dude in the direction of his direction, multiplied by the speed
      dude.x += Math.sin(dude.direction) * dude.speed
      dude.y += Math.cos(dude.direction) * dude.speed

      // rotation
      dude.rotation = -dude.direction + Math.PI / 2

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
