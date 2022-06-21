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

  // load resources
  app.loader.add('bg_grass', 'assets/bg_grass.jpg').load(build)

  // build
  function build() {
    // create a new texture
    const texture = PIXI.Texture.from('bg_grass')

    // create the SimplePlane object
    const plane = new PIXI.SimplePlane(texture, 10, 10)

    // set the size of the plane
    plane.width = app.screen.width
    plane.height = app.screen.height

    // set the position of the plane
    // plane.x = 100
    // plane.y = 100

    // add the plane to the stage
    app.stage.addChild(plane)

    // get the buffer for vertices positions
    const buffer = plane.geometry.getBuffer('aVertexPosition')

    // listen for animate update
    app.ticker.add((delta) => {
      // randomize the vertices positions a bit to create movement
      for (let i = 0; i < buffer.data.length; i += 2) {
        buffer.data[i] += Math.random() * 2 - 1
        buffer.data[i + 1] += Math.random() * 2 - 1
      }
      // update
      buffer.update()
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
