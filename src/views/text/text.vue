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

  const basicText = new PIXI.Text('Basic text in pixi', {
    fontFamily: 'Arial',
    fontSize: 36,
    fill: '#FFFFFF',
    align: 'center',
  })
  basicText.x = 50
  basicText.y = 100
  app.stage.addChild(basicText)

  const style = new PIXI.TextStyle({
    fontFamily: 'Arial',
    fontSize: 36,
    fontStyle: 'italic',
    fontWeight: 'bold',
    fill: ['#ffffff', '#00ff99'],
    stroke: '#4a1850',
    strokeThickness: 5,
    dropShadow: true,
    dropShadowColor: '#000000',
    dropShadowBlur: 4,
    dropShadowAngle: Math.PI / 6,
    dropShadowDistance: 6,
    wordWrap: true,
    wordWrapWidth: app.screen.width - 100,
    lineJoin: 'round',
    align: 'center',
  })

  const richText = new PIXI.Text(
    'Rich text with a lot of options and across multiple lines',
    style
  )
  richText.x = 50
  richText.y = 200

  app.stage.addChild(richText)

  const skewStyle = new PIXI.TextStyle({
    fontFamily: 'Arial',
    dropShadow: true,
    dropShadowAlpha: 0.8,
    dropShadowAngle: Math.PI / 6,
    dropShadowDistance: 6,
    dropShadowColor: '#000000',
    fill: ['#ffffff', '#00ff99'],
    stroke: '#4a1850',
    strokeThickness: 5,
    fontSize: 60,
    fontWeight: 'lighter',
    lineJoin: 'round',
    wordWrap: true,
    wordWrapWidth: app.screen.width - 100,
  })

  const skewText = new PIXI.Text('This text is skewed to the right', skewStyle)
  skewText.x = app.screen.width / 2
  skewText.y = 500
  skewText.skew.set(Math.PI, Math.PI)
  skewText.anchor.set(0.5)

  app.stage.addChild(skewText)
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
