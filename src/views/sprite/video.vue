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
    transparent: true,
    // backgroundColor: 0x1099bb,
    // resolution: window.devicePixelRatio || 1,
  })

  myCanvas.value.appendChild(app.view)

  // create play button that can be used to trigger the video
  const button = new PIXI.Graphics()
    .beginFill(0x0, 0.5)
    .drawRoundedRect(0, 0, 100, 100, 10)
    .endFill()
    .beginFill(0xffffff)
    .moveTo(36, 30)
    .lineTo(36, 70)
    .lineTo(66, 50)
    .endFill()

  // position the button
  button.x = (app.screen.width - button.width) / 2
  button.y = (app.screen.height - button.height) / 2
  button.interactive = true
  button.buttonMode = true

  app.stage.addChild(button)

  // tap the button to play the video
  button.on('pointertap', () => {
    button.destroy()
    // create a video texture from a video
    const texture = PIXI.Texture.from('assets/video.mp4')

    // create a new Sprite using the video texture (yes it's a Sprite, not an Image)
    const video = new PIXI.Sprite(texture)

    // sketch the full screen video
    video.width = app.screen.width
    video.height = app.screen.height

    // add the video to the stage
    app.stage.addChild(video)
  })

  // listen for animate update
  // app.ticker.add((delta) => {
  //   // update the video texture
  //   video.texture.update()
  // })
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
