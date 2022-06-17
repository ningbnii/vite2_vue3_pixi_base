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
    // resolution: window.devicePixelRatio || 1,
  })

  myCanvas.value.appendChild(app.view)

  // create a background
  const background = PIXI.Sprite.from('assets/bg_button.jpg')

  // size
  background.width = app.screen.width
  background.height = app.screen.height

  // add background to stage
  app.stage.addChild(background)

  // create some textures from an image path
  const textureButton = PIXI.Texture.from('assets/button.png')
  const textureButtonDown = PIXI.Texture.from('assets/button_down.png')
  const textureButtonOver = PIXI.Texture.from('assets/button_over.png')

  // store buttons
  const buttons = []

  // button position
  const buttonPositions = [
    { x: app.screen.width / 2 - 100, y: app.screen.height / 2 - 50 },
    { x: app.screen.width / 2 + 100, y: app.screen.height / 2 - 50 },
    { x: app.screen.width / 2 - 100, y: app.screen.height / 2 + 50 },
    { x: app.screen.width / 2 + 100, y: app.screen.height / 2 + 50 },
  ]

  // 循环
  for (let i = 0; i < buttonPositions.length; i++) {
    // create a button
    const button = new PIXI.Sprite(textureButton)

    // set the button position
    button.x = buttonPositions[i].x
    button.y = buttonPositions[i].y

    // 居中
    button.anchor.set(0.5)

    // set the button interactive state
    button.interactive = true

    // set	the button buttonMode state
    button.buttonMode = true

    // mouse & touch events are normalized into the pointers* events for handling different touch types
    button
      .on('pointerdown', onButtonDown)
      .on('pointerup', onButtonUp)
      .on('pointerover', onButtonOver)
      .on('pointerout', onButtonOut)
      .on('pointerupoutside', onButtonUp)

    // add the button to the stage
    app.stage.addChild(button)

    // store the button in the buttons array
    buttons.push(button)
  }

  // onButtonDown
  function onButtonDown(event) {
    // set the button state to be down
    event.currentTarget.texture = textureButtonDown
  }

  // onButtonUp
  function onButtonUp(event) {
    // set the button state to be up
    event.currentTarget.texture = textureButton
  }

  // onButtonOver
  function onButtonOver(event) {
    // set the button state to be over
    event.currentTarget.texture = textureButtonOver
  }

  // onButtonOut
  function onButtonOut(event) {
    // set the button state to be out
    event.currentTarget.texture = textureButton
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
