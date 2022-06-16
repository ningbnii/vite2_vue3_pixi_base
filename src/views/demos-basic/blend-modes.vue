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
    // resolution: window.devicePixelRatio || 1,
  })

  myCanvas.value.appendChild(app.view)

  // create a new background sprite
  const background = PIXI.Sprite.from('assets/bg_rotate.jpg')
  // set background size
  background.width = app.screen.width
  background.height = app.screen.height
  // add background to stage
  app.stage.addChild(background)

  // create an array to store a reference to the dudes
  const dudeArray = []

  const totalDudes = 20

  for (let i = 0; i < totalDudes; i++) {
    // create a new Sprite using the texture
    const dude = PIXI.Sprite.from('assets/flowerTop.png')

    // set the dude anchor point to 0.5, 0.5
    dude.anchor.set(0.5)

    // set a random scale for the dude
    dude.scale.set(Math.random() * 0.8 + 0.3)

    // set a random position for the dude
    dude.x = Math.floor(Math.random() * app.screen.width)
    dude.y = Math.floor(Math.random() * app.screen.height)

    // the important bit of this example, this is how you change the default blend mode of the sprite
    dude.blendMode = PIXI.BLEND_MODES.ADD

    // create some extra properties that will control movement
    dude.direction = Math.random() * Math.PI * 2

    // this number will be used to modify the direction of the dude over time
    dude.turningSpeed = Math.random() - 0.8

    // create a random speed for the dude between 0 - 2
    dude.speed = Math.random() * 2 + 1

    // finally we push the dude into the array so it can be easily accessed later
    dudeArray.push(dude)

    // add the dude to the stage
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
    // loop through the dudes and update their position
    for (let i = 0; i < dudeArray.length; i++) {
      const dude = dudeArray[i]

      // increment the direction the dude is facing
      dude.direction += dude.turningSpeed * 0.01

      // position the dude
      dude.x += Math.sin(dude.direction) * dude.speed
      dude.y += Math.cos(dude.direction) * dude.speed

      // rotation
      dude.rotation = -dude.direction + Math.PI / 2

      // wrap the dudes by testing their bounds
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
