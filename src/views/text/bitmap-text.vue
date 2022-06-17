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

  app.loader.add('Desyrel', 'assets/bitmap-font/desyrel.xml').load(() => {
    const bitmapText = new PIXI.BitmapText('Vue.js', {
      fontName: 'Desyrel',
      align: 'center',
      fontSize: 55,
    })
    bitmapText.anchor.set(0.5)
    bitmapText.x = app.screen.width / 2
    bitmapText.y = app.screen.height / 2
    app.stage.addChild(bitmapText)
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
